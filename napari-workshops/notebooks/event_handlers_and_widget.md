---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.16.7
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

(event-handlers-widget)=

```{code-cell} ipython3
import napari
import numpy as np

from enum import Enum
from functools import partial
from magicgui import magic_factory
from skimage.filters import (
    threshold_isodata,
    threshold_li,
    threshold_otsu,
    threshold_triangle,
    threshold_yen,
)
from skimage.measure import label
from scipy.ndimage import center_of_mass
DATA_PATH = '~/PhD/data/toy_data/easy-no-divide-swaps/Fluo-N2DL-HeLa.tif'
```

```{code-cell} ipython3
# very simple thresholding based segmentation widget
class Threshold(Enum):
    isodata = partial(threshold_isodata)
    li = partial(threshold_li)
    otsu = partial(threshold_otsu)
    triangle = partial(threshold_triangle)
    yen = partial(threshold_yen)

@magic_factory
def segment_by_threshold(
    img_layer: "napari.layers.Image", threshold: Threshold
) -> "napari.types.LayerDataTuple":

    seg_labels = np.zeros_like(img_layer.data)
    for i in range(len(img_layer.data)):
        frame = img_layer.data[i]
        # need to use threshold.value to get the function from the enum member
        threshold_val = threshold.value(frame)
        binarised_im = frame > threshold_val
        seg_labels[i] = label(binarised_im)

    seg_layer = (seg_labels, {"name": f"{img_layer.name}_seg"}, "labels")

    return seg_layer
```

```{code-cell} ipython3
viewer = napari.Viewer()
cells_image = viewer.open(DATA_PATH)[0]

widget = segment_by_threshold()
viewer.window.add_dock_widget(widget)
```

```{code-cell} ipython3
cell_labels = viewer.layers[-1]

def move_point_to_label_center(event):
    points_layer = event.source
    layer_data = event.value
    # round latest point so we can find the label value underneath
    latest_point = np.round(layer_data[-1]).astype(cell_labels.data.dtype)
    label_val_at_point = cell_labels.data[tuple(latest_point)]
    # pass through just the frame we added the point to, so we can get center of mass
    data_slice = latest_point[0]
    label_center = center_of_mass(cells_image.data[data_slice], cell_labels.data[data_slice], label_val_at_point)
    new_point = [data_slice, *label_center]
    # block data event when we're changing the point to avoid an infinite loop
    with points_layer.events.data.blocker():
        points_layer.data[-1] = new_point
    points_layer.refresh()
```

```{code-cell} ipython3
points_layer = viewer.add_points(ndim=3)
points_layer.events.data.connect(move_point_to_label_center)
```

```{code-cell} ipython3
# add keybinding on labels layer to add point with same color as the label underneath
@cell_labels.mouse_drag_callbacks.append
def add_point_with_label_color(layer, event):
    if (len(event.modifiers) == 1
            and event.modifiers[0].name == 'Alt'):
        # rounding mouse position to integer coordinates
        data_pos = np.round(event.position).astype(layer.data.dtype)
        label_color = layer.get_color(layer.data[tuple(data_pos)])   
        # add points layer if it doesn't already exist
        if 'cell_points' not in viewer.layers:
            points_layer = viewer.add_points(name='cell_points', ndim=3)
            points_layer.events.data.connect(move_point_to_label_center)
            viewer.layers.selection.active = cell_labels
        # add a point to the (potentially newly created points layer)
        points_layer = viewer.layers['cell_points']
        points_layer.current_face_color = label_color
        points_layer.add(event.position)
        points_layer.selected_data.clear()
```

```{code-cell} ipython3

```

```{code-cell} ipython3

```
