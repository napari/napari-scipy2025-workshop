# napari Plugins

Plugins are Python packages that allow developers to extend napari functionality.
Currently, plugins can:
* Add file format support with readers and writers
* Add custom widgets and user interface elements for Python functions, etc.
* Provide sample data
* Change the look of napari with a theme

[The napari hub](https://www.napari-hub.org) is a repository for searching/discovering plugins.
Plugins can be installed as Python packages using conda or pip as normal or from within the
napari GUI using the Plugin menu.

```{note}
The built-in [napari Plugin Manager](https://napari.org/napari-plugin-manager/) by default will attempt to 
install plugins in the same fashion that napari was installed. This means that if you installed napari 
via `conda`, it will attempt to install plugins via `conda` and if your napari was installed via `pip`, 
it will attempt to install plugins via `pip`. You can control this using the `Installation Info` dropdown 
in the Plugin Manager.  

Also keep in mind that plugins with complex or compiled dependencies or those that require non-Python components may not install properly. In those cases, you will need to check the documentation of the plugin for information regarding installation.
```

For more information on plugins, please see the [official Plugin documentation](https://napari.org/stable/plugins/index.html).

## Install Your First Plugins  
* (Optional) Navigate to [napari-hub.org](https://www.napari-hub.org)—this is the platform to discover and share plugins. Actual plugin installation will be done via napari--we detail this step below.
* **napari-bio-sample-data** : some additional sample data (File menu)
    * Search for **sample**.
    * Scroll down until you see **napari-bio-sample-data**.  
    * Select **napari-bio-sample-data**.  
    * Read the documentation on the plugin page. 
* **napari-skimage-regionprops** : widget to access [scikit-image `measure.regionprops`](https://scikit-image.org/docs/stable/api/skimage.measure.html#skimage.measure.regionprops)
    * Search for **regionprops**.
    * Scroll down until you see **napari-skimage-regionprops**.  
    * Select **napari-skimage-regionprops**.  
    * Read the documentation on the plugin page. 
* In napari, open  
**Plugins**>**Install/Uninstall Plugins...**   
and wait for the plugin list to populate.  
* Search for **sample** in the top search/filter bar, and click the install button next to **napari-bio-sample-data**.
* Once the install completes, search for **regionprops** and install that plugin as well.  

    ```{note}
    - After clicking the install button, you can monitor the **installing…** process by clicking `Show status`. This can be helpful in trouble-shooting installation issues.
    - After installing, you will typically need to close napari and reopen it, then  continue.
    ``` 

* Restart napari. 
* Open **File**>**Open Sample**. **napari-bio-sample-data** should be a menu option.  
    * Select: **File**>**Open Sample**>**napari-bio-sample-data**>**3D nuclei**  

    This sample data includes the same `nuclei` data as before, but this time a 3D labels and surface layer
    are present. You can explore these in the 3D view. Delete the surface layer once you're ready to move
    on.
* Open **Tools**>**Measurement Tables**>**Regionprops (scikit-image, nsr)**
    * Ensure that `nuclei (data)` is selected for the `image` layer and `nuclei label (data)` is selected in the `image` and `labels` dropdown menus for the Regionprops plugin, respectively.
    for the `labels` layer.
    * Use the checkboxes to select some properties to measure, such as `perimeter` or `position`. Refer to the [scikit-image `measure.regionprops` documentation](https://scikit-image.org/docs/stable/api/skimage.measure.html#skimage.measure.regionprops) for more information
    * Click run, which should yield a table of measurements—depending how many you checked, this could take
    a moment to run.

    ```{tip}
    Grab the titlebar of the new widget `Properties of nuclei label` and drag it off the napari
    viewer to undock. Then you can resize the table for easier browsing.
    You can re-dock the widget by dragging it on the existing side dock  or the bottom of the viewer,
    by dragging the widget title bar from below the viewer onto the canvas (left of the `Activity` button).
    ```

    * In the labels layer controls widget, check the box `show selected` to only view the currently
    selected label. Now in the table, when you click a cell you will be shown the corresponding label.
    
    ```{tip}
    You can double-click a table header to generate a heat map of that property.
    ```

## A few other handy plugins

1. [napari-skimage](https://github.com/guiwitz/napari-skimage#napari-skimage): a plugin that gives easy access 
   to some scikit-image image processing functions in napari.
1. [napari-animation](https://napari.org/napari-animation/): a plugin that lets you make
   (and export) animations in napari by simply setting key frames.
1. [napari-threedee](https://napari-threedee.github.io): a plugin to make interacting and annotating in 3D easier.
1. [napari-ome-zarr](https://www.napari-hub.org/plugins/napari-ome-zarr): a reader plugin for for `zarr` backed
   OME-NGFF images (local or remote).
1. [napari-aicsimageio](https://www.napari-hub.org/plugins/napari-aicsimageio): a reader plugin that uses
   `aicsimageio` as a backend. Provides access to proprietary formats (e.g. `.lif`, `.czi`) via drag-n-drop. 
   Can also give access to `bioformats` if `bioformats_jar` is installed from conda-forge (`conda install -c conda-forge bioformats_jar`).
1. [napari-matplotlib](https://napari-matplotlib.github.io): a plugin providing widgets with `matplotlib` based
   plots, such as histogram, scatter plot, line plot, etc. 
   
