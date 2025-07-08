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

# Mouse Callbacks & Layer Events

+++

We have seen how `napari`'s functionality can be extended with new GUI elements (widgets), and new keybindings for executing operations.

We will now look at extending `napari`'s functionality with two other features:

- Layer event callbacks: these are functions that will be executed when an event is **emitted** by a `napari` layer. Events are usually emitted when something changes about the layer e.g. the data, the colormap, or other layer properties.
- Mouse callbacks: these are functions that will be executed when the user interacts with the canvas by clicking, dragging or releasing the mouse button.

We will illustrate these two features using image warping as an example.

+++

## A Motivating Use-case

A common precursor to image analysis is image registration, or the alignment of two images into the same coordinate system. For example, imagine you have a biological sample that needs to be imaged by a microscope, then treated somehow (e.g. by applying a different staining agent to expose different features), and then imaged again by the same microscope. It's unlikely that the sample will be placed in the exact same position after being removed, and so the resulting images from the microscope will also be misaligned.

Typically, image registration involves linear transformations like translation, scaling, or rotation. However, in more complex cases, we may wish to **warp** the image e.g. if it's exhibiting barrel distortion, also known as the "fisheye" effect. Let's use `napari` to distort an image when the user moves one of four anchor points away from its original location.

## Warping an Image using a Layer `data` Event

We begin by setting up our layers. We will need an `Image` layer to warp, a `Points` layer that contains our original anchor points, and a second `Points` layer that contains the points the user will move to distort the image.

```{code-cell} ipython3
# this cell is required to run these notebooks in the cloud. Make sure that you also have a desktop tab open.
import os
if 'BINDER_SERVICE_HOST' in os.environ or 'NEBARI_JUPYTERHUB_SSH_SERVICE_HOST' in os.environ:
    os.environ['DISPLAY'] = ':1.0'
```

```{code-cell} ipython3
import napari
import numpy as np
import skimage as ski

image = ski.data.checkerboard()
src = np.array(
    [
        [ 66, 66],
        [133,  66],
        [ 66, 133],
        [133, 133]
    ]
)

viewer = napari.Viewer()
checkerboard_image_layer = viewer.add_image(image, name='checkerboard')
source_points_layer = viewer.add_points(src, name='source_points', symbol='+', face_color='red', size=5)
moving_points_layer = viewer.add_points(src.copy(), name='moving_points')
```

We now need a function to do the warping! Inspired by this excellent [scikit-image example](https://scikit-image.org/docs/stable/auto_examples/transform/plot_tps_deformation.html), we will use thin-plate splines to deform our image. We want to take the original image data, the unmoved source points, and the new coordinates of the destination points. Using the two different sets of points, we'll estimate the required transformation, and then apply this transformation to the original image data. Once we've done the transformation, we'll change the data of the image layer **in-place**, so that the image layer is always showing the latest warped version.

```{code-cell} ipython3
def warp(im_layer: 'napari.layers.Image', src: 'np.ndarray', dst: 'np.ndarray') -> None:
    """
    Warp image using thin-plate spline transformation.

    Estimate the warping required to transform the
    destination points into the source points, and then
    apply the warping to the original image data, replacing
    the image layer data in-place.

    Parameters
    ----------
    im_layer: napari Image layer to display the warped data
    src: array of source points
    dst: array of destination points
    """
    tps = ski.transform.ThinPlateSplineTransform()
    tps.estimate(dst, src)
    warped = ski.transform.warp(image, tps)
    # warped will be in 0..1 floats, so we need to
    # multiple by 255 to get it back to the same range
    # as the original data
    im_layer.data = (warped * 255).astype(image.dtype)
```

We can run this function without any GUI interaction by passing in the `checkerboard_image_layer`, the `src` points array and then a copy of the `src` array with some modified points. Note that this will warp our image, but won't actually move the points in the viewer!

```{code-cell} ipython3
dest_points = src.copy()
dest_points[0] = [40, 40]
warp(checkerboard_image_layer, src, dest_points)
```

Let's reset our image layer data for the next step.

```{code-cell} ipython3
checkerboard_image_layer.data = image
```

Now that we have the function that does the warping, and replaces the layer data, we want to call this function whenever a point's location in the layer has changed. To know when a point has changed, we want to connect to the layer's `data` event. This event will be emitted whenever the data of layer has seen any kind of change: a point has been added or deleted, the data has been replaced with a new array, and, importantly for us, a point has been moved from its original location.

In `napari`, you can connect functions or *callbacks* to layer events. These functions will be called by napari whenever the layer event is emitted. `napari` will pass the `event` object to the callback. The `event` object contains information about what triggered the `event`. For our purposes, we only care about the `changed` event, which indicates one or more points have just been moved. We can check which action caused this event to be emitted using `event.action`.

We also want to know the new position of all the points, as this will be the 'destination' array we use to estimate our warping. `event.value` gives us a reference to the updated `layer.data`, which is exactly the array of coordinates we need.

The final piece of the puzzle is our image layer. Because the original `warp` function takes three arguments, but our callback only gets the `event` as an argument, we define a `warp_checkerboard` function using `partial`, that satisfies the `image_layer` argument of the original `warp` function with our checkerboard image layer, and the `src` argument of the original function with our `src` coordinates array.

```{code-cell} ipython3
from functools import partial

warp_checkerboard = partial(warp, checkerboard_image_layer, src)
```

```{code-cell} ipython3
def warp_on_point_changed(event):
    # check that this event was fired after a point moved
    # rather than e.g. `added` or `removed`
    if event.action == 'changed':
        # event.value is our updated array of points coordinates
        new_dst = event.value
        # we already bound the image layer and the source coordinates
        # so we now only need to pass the destination coordinates
        warp_checkerboard(new_dst)
```

Now that we have our callback, we just need to hook it up to the layer event. This is a pretty simple one-liner. Run the cell below, select your `moving_points` layer, click the `Select points` button in the layer controls (third from the left), and then move one of the points. Once you release the mouse button, you should see your image warp. Pretty slick!

```{code-cell} ipython3
moving_points_layer.events.data.connect(warp_on_point_changed)
```

But it could be slicker... What if we could see the image warp as we moved the point, rather than just on mouse release? This is possible in `napari` with mouse callbacks, which we'll cover in the next example. However before we move on, let's disconnect our callback from the data event. It's always good practice to clean up your callbacks once you're done with them. Leaving them connected can lead to unintended side effects, and potentially cause a slow-down in the viewer.

```{code-cell} ipython3
moving_points_layer.events.data.disconnect(warp_on_point_changed)
```

## Warping an Image using a Mouse Callback

Mouse callbacks in `napari` have a specific structure that allows them to perform different tasks when the mouse is pressed, while it is dragging, and when it is released. They are typically attached to a specific layer (so that the user can still mouse around the viewer and click on buttons without interruption). They therefore always receive the interacted-with layer and the mouse event itself as arguments.

Mouse callbacks are essentially a *generator*, making use of `yield`s to split up the execution into separate code for handling the mouse press, the mouse drag, and the mouse release events. The dummy function below describes the structure of a mouse callback.

```{code-cell} ipython3
def some_mouse_callback(interacted_layer, event):
    # here, we put any code that needs to be executed when the mouse is first pressed
    print("Mouse has been pressed")
    # once this code is executed, we yield to the calling function to execute any other 
    # code that needs to run when the mouse is pressed
    yield

    # now, we handle the mouse drag. Because the drag can last indefinitely, and we
    # don't want our viewer to freeze while we drag, we put our dragging code into
    # a while loop and yield after each iteration
    while event.type == 'mouse_move':
        print(f"Mouse is being dragged. Current mouse position is {event.position}.")
        yield

    # the while loop will end when the mouse is no longer being dragged i.e. when
    # the mouse button is released
    print("Mouse has been released!")
```

The callback above doesn't do anything useful, but let's see what happens if we attach it to the image layer. Run the code below, select the `checkerboard` image layer in the viewer, and click and drag the image around. You should see the information printed in the terminal. Note that if some other layer is selected, you don't get anything printed. Also note that if you just make a single click, we don't get the information about the drag. Looks like our structure is correct!

```{code-cell} ipython3
checkerboard_image_layer.mouse_drag_callbacks.append(some_mouse_callback)
```

Remember to disconnect your callback! For mouse callbacks, we call `remove` on the `mouse_drag_callbacks` and pass in our function reference.

```{code-cell} ipython3
checkerboard_image_layer.mouse_drag_callbacks.remove(some_mouse_callback)
```

Let's edit our callback to pass the correct information to the `warp` function when the mouse is dragged.

To pass the right information to the callback, we inspect the `selected_data` attribute of our `points_layer`, which contains the indices of all currently selected points. This example only works when one point is selected, so we just grab the last one (since that's probably what the mouse is hovering over!). We make a copy of the `dst` points before updating the coordinates of the selected point to the current mouse position, to avoid messing with the layer data itself. Finally, we call the same warping function as in our layer event example.

```{code-cell} ipython3
def warp_on_move(points_layer, event):
    # we do nothing here, as we don't care about the mouse press
    # however, if we *don't* yield here, we'll never get a chance
    # to do things on drag!
    yield

    # while the mouse is moving, we call our warp function
    while event.type == 'mouse_move':
        # find the index into the points data of the currently selected point
        # we use the last selected point as that's likely what the mouse is hovering
        # over
        moved_point_index = list(points_layer.selected_data)[-1]
        # make a copy of the moving_points so we don't change the original
        # data array
        dst = points_layer.data.copy()
        # assign the current mouse position into the correct index to
        # update the location of the point
        dst[moved_point_index] = event.position
        # warp our image
        warp_checkerboard(dst)
        yield

    # we do nothing on mouse release
```

Now that we've defined our function, we add it to the `moving_points_layer` mouse drag callbacks. After running the cell below, select your `moving_points` layer in the viewer, and move a point as before. Now, the image warps as you drag!

```{code-cell} ipython3
moving_points_layer.mouse_drag_callbacks.append(warp_on_move)
```

Once you're done, don't forget to remove your callback!

```{code-cell} ipython3
moving_points_layer.mouse_drag_callbacks.remove(warp_on_move)
```

## Suggestions for Further Exploration

- Right now our warping only works on one image, which we've bound to our warping function using a `partial`. Try making a widget that allows you to select any image layer and then warp it. How will you ensure the correct image gets warped? Will you need to update the source and destination points depending on the size of the image?
- The mouse drag example doesn't work with multiple points selected (the way our layer event example does), because it's only updating the position of the last selected point. Try tweaking the example to make dragging work with multiple points. You'll need to figure out how to update the position of selected points that are *not* directly under the mouse.
- Try adding a keybinding that warps the image, instead of it warping on point move or drag.
- Rather than warping the image in-place, keep a stack of the different stages of warping as the mouse moves, then add this stack to the viewer as a new layer that the user can scroll through. This stack will grow very quickly... you might need a way to limit how many individual frames you keep.
