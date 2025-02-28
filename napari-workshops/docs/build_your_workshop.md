# Build your own workshop

```{note}
This section is about building your own workshop - it should probably be removed
from your workshop material once you have adapted it to your needs.
```

## Build locally

To build this workshop locally and preview the generated materials in html format (as a webpage), follow the steps below.

### 1. Install all required packages

First, [fork this repository](https://docs.github.com/en/get-started/quickstart/fork-a-repo) and clone it to your local system by

```
git clone git@github.com:<your-username>/napari-workshop-template.git
```

In a console/terminal, after installing python on your system using the
distribution of your choice, and ideally
[using a virtual environment](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/), navigate to the napari-workshop-template folder,
run

```
python -m pip install -r requirements.txt
```

### 2. Update the configuration files for the website

You will want to update `napari-workshops/_toc.yml` and `napari-workshops/_config.yml` to 
suit the needs of your workshop. These two files will determine how the website is rendered.

In `_toc.yml` you can put the Markdown files you want to be rendered in the order you want them.

```{important}
You don't use the `.md` extension, just the name of the file.
```

Use the existing file as a template and adjust it to suit your needs. For example, we recommend
removing *this section* on building the workshop from your final workshop materials.
For more information, see the [official Jupyter Book Table of Contents documentation](https://jupyterbook.org/customize/toc.html).

In `_config.yml` you can customize the build of the website. Be sure and take a look at, at a minimum:
- `title` and `author` information at the top: adjust the title as needed and add your name to the authors!
- the `repository` section towards the bottom: replace the URL of the template repository with your repository—this will be what the GitHub logo links to in your website—and ensure the `path_to_book` matches what you are using—you don't need to change this if you didn't change the layout of the repository.

If you're curious about the other settings already in the file or the range of available options, see [the official official Jupyter Book documentation](https://jupyterbook.org/customize/config.html).

### 3. Building your book locally

To render the html version of your book, including autogenerating figures or
running Jupyter notebooks, run

```
jupyter book build napari-workshops
```

where `napari-workshops` is the name of the folder where your book is stored.
The rendered version of the workshop materials will be under
`napari-workshops/_build/html`. For more information, see the
[JupyterBook documentation](https://jupyterbook.org/).

**Note:** The html files built by the `jupyter book` commands do not need to be
stored on GitHub, so we added the following path to our `.gitignore` file:

```
napari-workshops/_build/
```

## Publish and share your workshop
### Publish your workshop materials as a static web page

If you want to set up this material to be published as a website from your fork, using [GitHub pages](https://pages.github.com/), follow the steps below.

```{important}
For this to work, you need to have GitHub pages enabled on your GitHub account. To do this, follow the [deployment guide](deployment_guide).
```

1. Edit the `.github/workflows/pages.yml` file in your workshop folder to
   include any installation or setup requirements. By default, the following
   commands are run as a setup and build step:

    ```
    - name: Build workshop
      run: |
        python -m pip install -r requirements.txt
        jupyter book build napari-workshops
    ```

2. Edit the `.github/workflows/pages.yml` file in your workshop folder to
   set up the desired folder there the (html) built artifacts are stored. By
   default, the artifacts are in the following path:

    ```
    - name: Upload artifact
      uses: actions/upload-pages-artifact@v1
      with:
        path: 'napari-workshops/_build/html'
    ```

As currently configured, whenever a new commit is added to the `main` branch of the repository, a new deployment is made: the Jupyter book is built and the
generated pages are uploaded to [GitHub pages](https://docs.github.com/en/pages/quickstart). 

After GitHub pages is set up for your repository, you will find the workshop materials at

```
https://<your-username>.github.io/<workshop-name>
```

For this template, the built site is at

https://napari.github.io/napari-workshop-template

## Index your workshop using Zenodo (for citation purposes)

If you want people to be able to cite your workshop materials, you can create
a DOI for it using [Zenodo](https://docs.github.com/en/repositories/archiving-a-github-repository/referencing-and-citing-content).

Select Creative Commons Attribution 4.0 International (CC-BY 4.0) license if you want attribution.

## Enable a citation file for your repository

Check [the GitHub documentation](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-citation-files) for more details on how to use the `citation.cff` file to enable others to cite your repository.

## Upstream your ideas!

If you spot something that can be improved in this template, or if you want to
suggest adding a notebook to the base materials, feel free to reach out! You can:

- [Create an issue](https://github.com/napari/napari-workshop-template/issues/new)
  with suggestions for improvement;
- [Submit a Pull Request](https://docs.github.com/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request) to the workshop template repository with your suggested changes;
- [Reach out on Zulip chat](https://napari.zulipchat.com/) to tell us about your workshop and give feedback on the template or napari itself;
- Add your workshop materials (including video, if available) to [the Workshops page in the napari documentation website](https://napari.org/stable/further-resources/napari-workshops.html).
