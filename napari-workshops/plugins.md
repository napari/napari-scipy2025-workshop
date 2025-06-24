# napari Plugins

Plugins are Python packages that allow developers to extend napari functionality with new features.
Currently, plugins can:
* Add file format support with readers and writers
* Add custom widgets and user interface elements for Python functions, etc.
* Provide sample data
* Change the look of napari with a theme

[The napari hub](https://www.napari-hub.org) is a repository for searching/discovering plugins.
Plugins can be installed as Python packages using `pip` or, in most cases, `conda` or from within the
napari GUI using the Plugin menu.

For you as a user, plugins are a way to extend the functionality of napari and access features that are not part of the core napari viewer experience. Often, they provide GUI access to complex Python functions or workflows. For a developer, plugins are a way to share imaging-related code in a robust way with the broader scientific community, using napari for the user interface and visualization capabilities.

For more information on plugins, please see the [official Plugin documentation](https://napari.org/stable/plugins/index.html).

```{important}
Most plugins are developed by the broader napari scientific community. Plugins and their functionalities may be very specific and not be robust enough for every use case! Also, maintaining a plugin can be a burden and plugin developers may not keep up with napari or the broader scientific Python ecosystem.  
If you encounter issues with a napari plugin, please check the plugin's documentation and/or GitHub repository for more information or post on the [image.sc forum](https://forum.image.sc/).
```

## Install Your First Plugins  

* In napari, open  
**Plugins**>**Install/Uninstall Plugins...**   
* Search for **sample** in the top search/filter bar, and then, in the bottom pane, you will see all of the plugins that match this search term. 
* Click on the name of the plugin (underlined) to open a webpage with more information about the plugin, such as the documentation or the code repository (e.g. GitHub).
* Click the install button next to **napari-bio-sample-data**. If you want to know more about the version and installer used, you can click the `Installation Info` menu. 
* Once the install completes, search for **skimage** and install the **napari-skimage** plugin as well.  

    ```{tip}
    - After clicking the install button, you can monitor the **installing…** process by clicking `Show status` at the bottom left. This can be helpful in trouble-shooting installation issues.
    - You can queue multiple plugins to install at once, by searching for a new plugin and clicking the install button.
    ``` 
* After installing plugins, it is recommended that you close napari and reopen it to continue.

## Using Plugins

Plugins that provide reader functionality will automatically be used when you open a file type that the plugin supports. You may be prompted to select a reader plugin if there are multiple plugins that can read the same file type. Sample data provided by Plugins will be available in the **File**>**Open Sample** menu. Plugins that provide widgets to access functionality will be available in the **Plugins** menu, as well as in other declared menu locations, such as the **Layers** menu.

```{tip}
A good way to get a sense of the functionalities offered by a plugin is to use the napari Command Palette. You can open it by pressing `Ctrl+Shift+P` (or `Cmd+Shift+P` on macOS). In the floating text entry bar, you can type the name--or part of it--of a plugin. This will show you all the commands available from that plugin.
```

* Open **File**>**Open Sample**. **napari-bio-sample-data** should be a menu option.  
    * Select: **File**>**Open Sample**>**napari-bio-sample-data**>**3D nuclei**  

    This sample data includes the same `nuclei` data as before, but it also provides segmentations as a 3D Labels layer. Additionally, a Surface layer, which can be used to visualize precomputed surface meshes, is also present. You can explore these in the 3D view. Delete the surface layer once you're ready to move on, as we will not be using it.

* Open **Layers**>**Measure**>**Regionprops (labels) (napari-skimage)**
    * Ensure that `nuclei label` is selected for the `Label Layer` and `nuclei` is selected
    for the `Intensity Image Layer`.
    * Select some properties to measure, you can use shift or control/command to multiselect. Refer to the [scikit-image `measure.regionprops` documentation](https://scikit-image.org/docs/stable/api/skimage.measure.html#skimage.measure.regionprops) for more information
    * Click `Analyze`, which should yield a table of measurements—depending how many you checked, this could take a moment to run. You can save the results to a CSV file by clicking the `Save Results` button.

    ```{tip}
    Grab the titlebar of the Table widget, it's on the left edge, and drag it off the napari
    viewer to undock. Then you can resize the table for easier browsing.
    You can re-dock the widget by dragging it on the existing side dock or the bottom of the viewer,
    e.g. where the viewer buttons are, to dock it beneath the canvas.
    ```

    * Select the `nuclei labels` layer in the layer list and in the layer controls widget at the top, check the box `show selected` to only view the currently selected label. Now in the table, when you click a cell you will be shown the corresponding label. You could now use the brush and eraser tools to edit the segmentation and then re-run the analysis.

* Hide the Labels layer and close any open widgets.
* Explore a few of the other functions offered by the **napari-skimage** plugin. You can find them in the **Layers** menu (or the **Plugins** menu), or by using the Command Palette. For example, you can try **Layers**>**Filter**>**Filtering**>**Gaussian filter**. This open a widget that will allow you to apply a Gaussian filter to a specified Image layer and create a new Image layer with the output. The widget even provides a link to the [documentation](https://scikit-image.org/docs/stable/api/skimage.filters.html#skimage.filters.gaussian). You can also try one of the options in the **Layers**>**Filter**>**Thresholding** menu, which will allow you to apply a threshold to a specified Image layer.

## A few other handy plugins

1. [napari-animation](https://napari.org/napari-animation/): a plugin that lets you make
   (and export) animations in napari by simply setting key frames.
1. [napari-ome-zarr](https://www.napari-hub.org/plugins/napari-ome-zarr): a reader plugin for for `zarr` backed
   OME-NGFF images (local or remote).
1. [napari-tiff](https://www.napari-hub.org/plugins/napari-tiff): a reader plugin that can open `tiff` files and variants like `svs` or `ndpi` and load them as multi-scale data.
1. [napari-matplotlib](https://napari-matplotlib.github.io): a plugin providing widgets with `matplotlib` based
   plots, such as histogram, scatter plot, line plot, etc. 
