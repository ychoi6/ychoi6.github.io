---
title: Elementary Flux Mode
tag: 
layout: post
author: Yoonmi Choi
published: true
categories: modeling
---

<!-- ##### Concept -->
#### Download 
[Download efmtool from this website!](https://csb.ethz.ch/tools/software/efmtool.html){:target="_blank"}  

or **Install** using pip.
```python
pip install efmtool
```
#### Documentation
See an [official documentation](https://csb.ethz.ch/tools/software/efmtool/documentation.html){:target="_blank"} here.

For **Python** users, refer to this [site (pypi.org/project/efmtool/0.2.1/)](https://pypi.org/project/efmtool/0.2.1/){:target="_blank"} and [code](https://gitlab.com/csb.ethz/efmtool/-/tree/main/python){:target="_blank"}.



#### Usage
```python  
efms = efmtool.calculate_efms(
    stoichiometry : np.array,
    reversibilities : List[int],
    reaction_names : List[str],
    metabolite_names : List[str],
    options : Dict = None,
    jvm_options : List[str] = None)
```
`efms` returns a `Numpy array`.

#### Understand `efmtool.py`

Let's understand the each function defined in `efmtool.py`


- Import libraries
```python
import glob
import logging
import multiprocessing as mp
import os
import tempfile
import traceback
from ctypes import c_char_p
from typing import Dict, List

import jpype
import numpy as np
import scipy.io

logger = logging.getLogger(__name__)
package_directory = os.path.dirname(os.path.abspath(__file__))
```

- 
```python
def _relative_filename(folder: str, file: str) -> str:
    return os.path.relpath(os.path.join(folder, file), ".")

```

```python
def _read_efms_from_mat(folder: str) -> np.array:
    # efmtool stores the computed EFMs in one or more .mat files. This function
    # finds them and loads them into a single numpy array.
    efm_parts: List[np.array] = []
    files_list = sorted(glob.glob(os.path.join(folder, "efms_*.mat")))
    for f in files_list:
        mat = scipy.io.loadmat(f, verify_compressed_data_integrity=False)
        efm_parts.append(mat["mnet"]["efms"][0, 0])

    return np.concatenate(efm_parts, axis=1)


def calculate_efms(
    stoichiometry: np.array,
    reversibilities: List[int],
    reaction_names: List[str],
    metabolite_names: List[str],
    options: Dict = None,
    jvm_options: List[str] = None,
) -> np.array:
    """Calculates all the Elementary Flux Modes (EFMs) in a reaction network.

    Parameters
    ----------
    stoichiometry : np.array
        m-by-n array describing the stoichiometry of the network.
    reversibilities : List[int]
        n-elements list specifying whether each reaction is reversible (1) or
        not (0).
    reaction_names : List[str]
        n-elements list of reaction names.
    metabolite_names : List[str]
        m-elements list of metabolite names.
    options : Dict, optional
        List of options to pass to efmtool. Defaults can be obtained with
        get_default_options().
    jvm_options : List[str], optional
        List of arguments to be passed to the JVM.

    Returns
    -------
    np.array
        n-by-e array containing the e EFMs found in the network.
    """
    options = options or get_default_options()

    # Create temporary directory for efmtool and input/output files.
    tmp_dir = tempfile.TemporaryDirectory()
    options["tmpdir"] = tmp_dir.name
    options["stoich"] = "stoich.txt"
    options["rev"] = "revs.txt"
    options["meta"] = "mnames.txt"
    options["reac"] = "rnames.txt"
    out_file = "efms.mat"

    # Convert options dictionary to list of flags.
    options_list = [["-" + k, v] for _, (k, v) in enumerate(options.items())]
    args = [s for pair in options_list for s in pair]
    args += ["-out", "matlab", out_file]

    # Write inputs to temporary files.
    np.savetxt(os.path.join(options["tmpdir"], options["stoich"]), stoichiometry)
    with open(os.path.join(options["tmpdir"], options["rev"]), "w") as file:
        file.write(" ".join(str(x) for x in reversibilities))
    with open(os.path.join(options["tmpdir"], options["meta"]), "w") as file:
        file.write(" ".join('"' + x + '"' for x in metabolite_names))
    with open(os.path.join(options["tmpdir"], options["reac"]), "w") as file:
        file.write(" ".join('"' + x + '"' for x in reaction_names))

    # Call efmtool, read result and clean up temporary directory.
    try:
        call_efmtool(args, jvm_options)
        efms = _read_efms_from_mat(tmp_dir.name)
    except:
        logger.error("EFM computation failed.")
        raise
    finally:
        try:
            tmp_dir.cleanup()
        except:
            logger.warning(f"Unable to clean up temporary directory {tmp_dir.name}")

    return efms


def call_efmtool(args: List[str], jvm_options: List[str] = None):
    """Wrapper for the ch.javasoft.metabolic.efm.main.CalculateFluxModes.matlab
    call. Note that we use matlab() instead of main() because the latter would
    terminate the process. This function is meant for advanced usage. It does not
    perform any initialization and only calls efmtool with the given arguments.

    Parameters
    ----------
    args : List[str]
        List of arguments to be passed to efmtool.
    jvm_options : List[str], optional
        List of arguments to be passed to the JVM.
    """

    manager = mp.Manager()
    result = manager.Value(c_char_p, "", lock=False)
    efmtool_process = mp.Process(target=_efmtool_process, args=(result, args, jvm_options))
    efmtool_process.start()
    efmtool_process.join()

    if result.value != "success":
        logger.error("Error running the efmtool Java process:")
        logger.error(result.value)
        raise RuntimeError("Error running the efmtool Java process")


def _efmtool_process(result, args: List[str], jvm_options: List[str] = None):
    # Read the tmpdir argument and change working directory to it.
    tmpdir = args[args.index("-tmpdir") + 1]
    cwd = os.getcwd()
    os.chdir(tmpdir)

    try:
        # Start JVM adding efmtool and its dependencies to the classpath
        jvm_options = jvm_options or []
        libs_path = os.path.join(package_directory, "lib/*")
        jpype.startJVM(*jvm_options, classpath=[libs_path])
        CalculateFluxModes = jpype.JClass(
            "ch.javasoft.metabolic.efm.main.CalculateFluxModes"
        )

        # Call efmtool. We use the matlab call so that it doesn't kill the process.
        CalculateFluxModes.matlab(args)

        # Close JVM and restore the original working directory.
        jpype.shutdownJVM()
    except Exception:
        result.value = traceback.format_exc()
    else:
        result.value = "success"

    os.chdir(cwd)



def get_default_options() -> Dict:
    """Gets a dictionary containing default options for efmtool.

    Returns
    -------
    Dict
        The default options.
    """
    options = {
        "kind": "stoichiometry",
        "arithmetic": "double",
        "zero": "1e-10",
        "compression": "default",
        "log": "console",
        "level": "INFO",
        "maxthreads": "-1",
        "normalize": "min",
        "adjacency-method": "pattern-tree-minzero",
        "rowordering": "MostZerosOrAbsLexMin",
    }
    return options

```