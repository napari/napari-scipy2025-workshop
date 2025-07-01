# Create custom image visualization and analysis tools with napari

This is a three-part workshop that takes you begins with an introduction to napari, its interface and its layers using
a simple image analysis exercise. We then look at extending napari's functionality with custom event handlers,
keybindings and widgets. Finally, we look at how your new custom functionality can be packaged into a pip-installable
napari plugin.

## Getting Started

Use the [installation guide](installation) to prepare your environment and install napari and
its required dependencies locally. Running napari locally is likely to give you the best experience, but you can also run napari and the workshop materials in the cloud using the SciPy 2025 Nebari instance. See our [Nebari instructions](nebari) for details.

## Part 1 - Using Python and napari to view and analyze imaging data

In this section we introduce napari's interface, opening and exploring images and other napari layer types, and
the bi-directional interactions between the napari viewer and a Jupyter notebook. [The napari application](intro-gui)
document will walk you through the basics.

The next step is interactive image analysis. Follow through the [interactive analysis](interactive-analysis) notebook
to learn how to use napari, a Jupyter notebook and scikit-image to perform an image segmentation.

## Part 2 - Customizing your analysis workflow by extending napari’s functionality

We explore three easy ways to add custom functionality to napari to support interactive analysis:

- **Event handlers** are functions that are executed when different viewer and layer events occur
- **Keybinding/mouse handlers** are custom functions that are executed when you press a key sequence or click the mouse
- **Widgets** are custom GUI elements like checkboxes, dropdown menus, buttons and sliders, that you can add to the viewer with simple Python code

Follow through the [event handlers and widget](event-handlers-widget) notebook to learn how to 
use these different elements to customize your analysis.

## Part 3 - Distributing your customized functionality with plugins

Now that you've learned how to use napari and built our own custom elements, you might want to share
them with your coworkers or with the napari community at large! This section walks you through
packaging your functionality into a pip-installable napari plugin. Follow through the [Creating a napari plugin](simple-plugin)
document to learn how to declare and install a plugin.
