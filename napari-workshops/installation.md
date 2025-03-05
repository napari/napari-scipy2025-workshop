(installation=)

# napari installation

napari is a Python application and package, so it requires a working Python installation.
There are multiple ways that Python installations and packages can be managed. For a detailed
breakdown, we recommend [this excellent guide and tutorial by Talley Lambert (Harvard Medical School)](https://hackmd.io/@talley/SJB_lObBi).
For the purposes of this workshop, for consistency, we will use [`conda`](https://docs.conda.io/en/latest/) as the Python, package, and environment manager.

## Installing Python using `conda`

In this tutorial, we will install Python via miniforge, a distribution of
Python based in the [conda package manager](https://docs.conda.io/en/latest/).

````{important}
If you already have anaconda, miniconda, or miniforge installed, those will work
as well. You can check using:

```bash
conda info
```

However, we recommend you that the conda version is >23.10 and that you are using 
[`conda-forge` channels](https://conda-forge.org/docs/user/introduction/). You can ensure this using:

```bash
conda update conda
conda config --add channels conda-forge
```

Once you have that set, you can skip to [the next section](setting-up-your-napari-workshop-environment).
````

1. In your web browser,  go to:  
[https://conda-forge.org/download/](https://conda-forge.org/download/)
2. Click on the tile corresponding to your platform (e.g., Windows, macOS, or Linux) to download the installer. If you are unsure about your macOS or Linux architecture, open a command line terminal and run:

   ```bash
   uname -m
   ```

   Alternately, you can also download the installer using the command line on macOS or Linux by running:

   ```bash
   curl -L -O "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
   ```

3. Once you have downloaded miniforge installer, run it to install conda. Note: you may need 
   admin permissions during the installation process.
   
   ### Windows

   1. Find the file you downloaded (e.g. in your Downloads directory), it should look like `Miniforge3-Windows-x86_64.exe`. 
   2. Double-click with the left mouse button to execute it. 
   3. Follow the instructions to complete the installation. We recommend checking the options to "Create start menu shortcuts" and "Add Miniforge3 to my PATH environment variable".
   4. Once the installation has completed, you can verify it was correctly installed by searching for the “miniforge prompt” in your Start menu.

   ### macOS & Linux

   1. Find the file you downloaded (e.g. in your Downloads directory), it should look like `Miniforge3-MacOSX-arm64.sh` or `Miniforge3-Linux-x86_64.sh`.
   2. Using the command line, navigate to the location of the installer. For example, if it's in the Downloads directory, you can run:

   ```bash
   cd ~/Downloads
   ```

   3. Run the installer by typing:

   ```bash
   bash Miniforge3-$(uname)-$(uname -m).sh
   ```

   4. The installer will walk you through a series of prompts to complete the installation. We recommend that you use the default installation location, but you can change it to a different location in your user directory if you prefer. 

   5. Once the installation finishes, the installer will ask:  
   `Do you wish to update your shell profile to automatically initialize conda?`  
   We recommend you answer `yes` to this question. This will add the necessary lines to your shell profile to ensure that `conda` is added to your PATH variable and properly initialized when you open a new shell. Note: For changes to take effect, you will need to close and re-open your current shell.

   6. Once the installation has completed, you can verify it was correctly installed by opening a new command line. You should see `(base)` next to your command line prompt. Additionally, the following command:

      ```
      echo $CONDA_PREFIX
      ```

      should return the path to your `miniforge` installation, by default this is `~/miniforge3` (i.e., `miniforge3` in your home directory), so for example:


      ```bash
      /Users/username/miniforge3
      ```

      or

      ```bash
      /home/username/miniforge3
      ```

   #### Manual initialization

   If you did not have the installer initialize your shell, you can manually initialize it. Note: the command depends on installation location of `miniforge`, by default this is `~/miniforge3`. Then, run:

   ```bash
   ~/miniforge3/condabin/conda init
   ```

   Again, for changes to take effect, you will need to close and re-open your current shell.

## Setting up your napari workshop environment

1. Open your terminal.
   - **Windows**: Open the "miniforge prompt" from your start menu
   - **Mac OS**: Open Terminal (you can search for it in spotlight - `cmd` +
     `space`)
   - **Linux**: Open your terminal application
2. We will use a virtual environment to encapsulate the Python tools used for this workshop.
   This ensures that the requirements for this workshop do not interfere with
   any other Python projects. To create the environment (named
   `napari-workshop`) with Python 3.11 in it, enter the following command:

    ```bash
    conda create -n napari-workshop -c conda-forge python=3.12 napari pyqt jupyterlab
    ```

3. Once the environment setup has finished, activate the environment:

    ```bash
    conda activate napari-workshop
    ```

    If you successfully activated the environment, you should now see
   `(napari-workshop)` to the left of your command prompt.

   ```{important}
   Remember to activate the right environment for what you need! And always 
   double-check that the activated environment is the one you want before installing any 
   packages—you never want to install into the `base` environment.
   ```

4. Test that your notebook installation is working. We will be using notebooks
   for interactive analysis. Enter the command below and it should launch the
   `jupyter lab` application in a web browser. Once you've confirmed it
   launches, close the web browser and press `ctrl+c` in the terminal window to
   stop the notebook server.

    ```bash
    jupyter notebook
    ```

5. Test your napari installation. Enter the command below and an empty napari
   viewer should open. You can close the window after it opens using the File menu. 
   Please note that it takes a bit of extra time (as much as 60 s) to launch napari 
   the first time, particularly if your computer has security and virus scan applications
   running.
    
    ```bash
    napari
    ```

```{admonition} Errors launching?
Contact the workshop instructors or you can reach out to the napari community on the [napari zulip chat](https://napari.zulipchat.com/#narrow/stream/212875-general)
```

This installation guide has been reproduced and edited from an [introductory napari tutorial](https://github.com/TheJacksonLaboratory/intro-napari-workshop) at the Jackson Laboratory