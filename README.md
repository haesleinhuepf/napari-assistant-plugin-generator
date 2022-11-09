# napari-assistant-plugin-generator
[![License](https://img.shields.io/pypi/l/napari-assistant-plugin-generator.svg?color=green)](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/raw/main/LICENSE)
[![PyPI](https://img.shields.io/pypi/v/napari-assistant-plugin-generator.svg?color=green)](https://pypi.org/project/napari-assistant-plugin-generator)
[![Python Version](https://img.shields.io/pypi/pyversions/napari-assistant-plugin-generator.svg?color=green)](https://python.org)
[![tests](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/workflows/tests/badge.svg)](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/actions)
[![codecov](https://codecov.io/gh/haesleinhuepf/napari-assistant-plugin-generator/branch/main/graph/badge.svg)](https://codecov.io/gh/haesleinhuepf/napari-assistant-plugin-generator)
[![Development Status](https://img.shields.io/pypi/status/napari-assistant-plugin-generator.svg)](https://en.wikipedia.org/wiki/Software_release_life_cycle#Alpha)
[![napari hub](https://img.shields.io/endpoint?url=https://api.napari-hub.org/shields/napari-assistant-plugin-generator)](https://napari-hub.org/plugins/napari-assistant-plugin-generator)
[![DOI](https://zenodo.org/badge/563886413.svg)](https://zenodo.org/badge/latestdoi/563886413)


The napari-assistant-plugin-generator is a [napari](https://github.com/napari/napari) plugin to generate python code which can be pip-installed and serve as napari-plugin compatible with the [napari-assistant](https://www.napari-hub.org/plugins/napari-assistant).

## Usage

For demonstrating how one can generate a Napari plugin from an existing workflow, we demonstrate the procedure by reusing function from [napari-segment-blobs-and-things-with-membranes](https://www.napari-hub.org/plugins/napari-segment-blobs-and-things-with-membranes) (nsbatwm) for generating a plugin.

* After installing nsbatwm you can start the assistant from the menu `Tools > Utilities > Assistant (na)` or run `naparia` from the command line. 

* Open the blobs example image `Blobs`, e.g. after downloading it from [here](https://github.com/clEsperanto/napari_pyclesperanto_assistant/blob/master/napari_pyclesperanto_assistant/data/blobs.tif).

* In the Assistant, click on the `Remove noise` button and select `Gaussian (scikit-image, nsbatwm)` from the operation pulldown.
* Click the `Binarize` button and select `Threshold (Otsu 1979, scikit-image, nsbatwm)` operation.
* Click the `Label` button and select 'Connected component labeling (scikit-image, nsbatwm)' from the operation pulldown.

Afterwards, your Napari with the configured workflow should look like this:

![img.png](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/raw/main/docs/screenshot_workflow.png)

### Plugin generation

Before running the plugin-generator, make sure you are connected to the internet, 
because a [plugin template](https://github.com/haesleinhuepf/cookiecutter-napari-assistant-plugin-generator-plugin) will be downloaded. 
The plugin generator can be found in the menu `Tools > Utilities > Generate Napari plugin from workflow (na)` and also 
in the `Generate code...` of the Assistant:

![img.png](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/raw/main/docs/generate_napari_plugin1.png)

In the plugin generator dialog, please enter this information:
* **output dir:** Folder where the Napari plugin code should be saved. If not specified, the plugin will be stored in the current directory Napari was started from.
* **plugin name:** Name of the plugin. A folder with this name will be generated in the folder specified above. Plugin names must not contain special characters or spaces. Use `_` instead.
* **developer name:** Your name as it will be displayed later on the [napari-hub](https://napari-hub.org) in case you decide to publish your plugin.
* **developer email:** Your email as it will be stored in the configuration of your plugin. This email-address is visible to the public.
* **github username:** Your username on [github](https://github.com). URLs in the plugin documentation will point to your profile on github.
* **short description:** Please write one sentence explaining what the plugin is doing.
* **license:** Choose the open-source license your plugin code will be licensed. If you are not sure which one to use, consult [choosealicense.com](https://choosealicense.com).
* **tools menu** The menu under `Tools` where your plugin will be found after installing it.
* **menu name** The menu entry will have this title.

![img.png](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/raw/main/docs/generate_napari_plugin2.png)

After the Napari plugin code has been generated, open it in the integrated development environment (IDE) of your choice. 
Go through the files in the directory and search for `TODO` entries. Start with the `readme.md` and the `requirements.txt` files:

![img.png](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/raw/main/docs/generate_napari_plugin4.png)

Highly relevant might be the python file `my_napari_assistant_plugin/_function.py`. It contains a function with python code representing the workflow we designed by clicking above:

```python
from napari_plugin_engine import napari_hook_implementation
from napari_tools_menu import register_function
from napari_time_slicer import time_slicer

@napari_hook_implementation
def napari_experimental_provide_function():
    return [process_image]

@register_function(menu="Segmentation / labeling > Segment image")
@time_slicer
def process_image(image0_b: "napari.types.ImageData", gaussian_blur_sigma_2: float = 1.0, connected_component_labeling_exclude_on_edges_3: bool = False) -> "napari.types.LabelsData":
    """
    Short plugin description
    
    # TODO: Provide more detailed documentation here. E.g. specify the parameters and what values users should enter.
    """
    # TODO: Check the list of parameters of the function definition above. 
    # If there are parameters that should not be editable by the end user, move their definition and values here instead.
    
    import napari_segment_blobs_and_things_with_membranes as nsbatwm  # version 0.3.3
    
    
    # gaussian blur
    image1_G = nsbatwm.gaussian_blur(image0_b, sigma=gaussian_blur_sigma_2)
    
    # threshold otsu
    image2_T = nsbatwm.threshold_otsu(image1_G)
    
    # connected component labeling
    image3_C = nsbatwm.connected_component_labeling(
        image2_T, exclude_on_edges=connected_component_labeling_exclude_on_edges_3)
    return image3_C
```

It is recommended to inspect the generated code and rename variables to be more meaningful.
For renaming variables, make use of your IDE's tools. 
For example variables can be renamed conveniently in [pycharm](https://www.jetbrains.com/pycharm/) using the right-click menu:

![img.png](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/raw/main/docs/generate_napari_plugin3.png)

The `readme.md` file also contains instructions for how to install and distribute your plugin. 
TL:DR: As a plugin developer you typically execute this command from the terminal within your plugin's root directory to install your plugin in an `editable`. 
This command allows you to modify the code and test it without the need for re-installing your plugin.

```
pip install -e .
```

If installation was successful, you will find your plugin in the menu you specified and a dialog will open requesting the parameters of the generated Python function in `<your_plugin_folder>/<your_plugin_folder>/_function.py`:

![img.png](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/raw/main/docs/generate_napari_plugin5.png)

![img.png](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/raw/main/docs/generate_napari_plugin6.png)


## Installation

It is recommended to install [devbio-napari](https://www.napari-hub.org/plugins/devbio-napari#installation) first. It comes with many image processing functions that can be combined in workflows and where it is easy to generate plugins from.

Afterwards, the plugin generator can be installed using `pip`:
```
pip install napari-assistant-plugin-generator
```

Also make sure you have `git` installed. E.g. using `mamba`/`conda`:

```
mamba install git
```

## Feedback welcome!

The napari-assistant is developed in the open because we believe in the open source community. Feel free to drop feedback as [github issue](https://github.com/haesleinhuepf/napari-assistant-plugin-generator/issues) or via [image.sc](https://image.sc)

## Contributing

Contributions are very welcome. Please ensure
the test coverage at least stays the same before you submit a pull request.

## License

Distributed under the terms of the [BSD-3] license,
"napari-assistant-plugin-generator" is free and open source software

## Acknowledgements
This project was supported by the Deutsche Forschungsgemeinschaft under Germany’s Excellence Strategy – EXC2068 - Cluster of Excellence "Physics of Life" of TU Dresden. 
This project has been made possible in part by grant number [2021-240341 (Napari plugin accelerator grant)](https://chanzuckerberg.com/science/programs-resources/imaging/napari/improving-image-processing/) from the Chan Zuckerberg Initiative DAF, an advised fund of the Silicon Valley Community Foundation.

[BSD-3]: http://opensource.org/licenses/BSD-3-Clause

