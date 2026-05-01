Advanced Container Config Options
=================================
.. _Options:

The following is a list of all the current options that can be used in the container config files along with 
a brief description of what the option is used for.

Required options:
-----------------
The following are strictly required for all containers to function.

- **description** (str): Human readable description of the container.
- **groups** list(str): List of Plain Text label(s) to help group containers with the list command.
- **container_definition** (str): Path to container definition (.def) file or a valid URI link to a container registry. 
  Currently supports `docker://`, `ORAS://` and `Library://` however all must include a tag. e.g. `docker://ubuntu:latest`.

Optional options:
-----------------

The following are optional and all have sensible defaults.

-   **image_file** (str): Path to the container image (.sif) file. If omitted this defaults to `Images/"ModelName".sif`.
- **shared_directories** List[str]: List of extra directories to grant the Container access to. 
  By default Apptainer only grants containers access to the users home directory, the current working directory,
  and the ML_Toolkit directory [#SharedDir]_. 
  You can use this option to provide a list of the full paths to any other directories you wish to allow your 
  container to access.

.. [#SharedDir] Apptainer also grants containers access to a few other select system directories. see `the docs for more details <https://apptainer.org/docs/user/main/bind_paths_and_mounts.html#system-defined-bind-paths>`_

Note:

1. these directories must exist
2. access is also automatically granted to all subdirectories
3. symbolic links are ignored and can sometimes cause issues, ask me how I know.

- **dont_mount** List[str]: list of directories not to mount automatically. 
  Currently can be any of `[]`, `['home']`, `['cwd']`` or `['home','cwd']``
- **Device** (str):  string to control access to cpu/gpu. 
  Can be any of: `"cuda"`: for nvidia (default), `"rocm"`: for AMD or `"cpu"`: for cpu only.
- **build_options** (dict) Dictionary of key-value pairs to pass though to the Apptainer build script 
  to replace `{{ variable }}` entries in build definition (.def) files. [#BuildOptions]_
- **output_file** (str) file to use for container stdout and stderr when running as background process. default is ModelName.out/ModelName.err
- **CASTEP** (bool) flag to say the model interfaces with CASTEP (default is False)
- **available_tasks** List[str] list of tasks to use with --task flag.

.. [#BuildOptions] These can be very useful by allowing you to use a single definition file to build multiple similar containers. See the 
  `Apptainer build docs`_ for more information.


.. _Apptainer build docs: https://apptainer.org/docs/user/main/definition_files.html#templating-how-to-pass-values-into-definition-files

Experimental Options:
---------------------

The following are considered experimental options. whilst they have been tested they may not 
work on all machines as they require certain permissions and as such depend on how 
Apptainer has been configured on your machine. They also do not work on Bede, USE WITH CAUTION.

- **encrypted** (bool): **EXPERIMENTAL**, defaults to False. If set to True will ask the user for a password when trying to 
  build/run the container. Note: there is no backup so if you loose/forget the password/keyfile your data is as good as gone. 
- **encryption_key** (str):  Path to encryption keyfile. Only applicable if encrypted=True. This tells Apptainer to use a 
  pemkey file instead of a password.

