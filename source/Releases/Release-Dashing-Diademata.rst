
ROS 2 Dashing Diademata (codename 'dashing'; May 31st, 2019)
============================================================

.. contents:: Table of Contents
   :depth: 2
   :local:

*Dashing Diademata* will be the fourth release of ROS 2.

Supported Platforms
-------------------

To be determined.


New features in this ROS 2 release
----------------------------------

During the development the `Dashing meta ticket <https://github.com/ros2/ros2/issues/607>`__ on GitHub contains an up-to-date state of the ongoing high level tasks as well as references specific tickets with more details.


Timeline before the release
---------------------------

A few milestone leading up to the release:

    Mon. Apr 8th (alpha)
        First releases of core packages available.
        Testing can happen from now on (some features might not have landed yet).

    Thu. May 2nd
        API freeze for core packages

    Mon. May 6th (beta)
        Updated releases of core packages available.
        Additional testing of the latest features.

    Thu. May 16th
        Feature freeze.
        Only bug fix releases should be made after this point.
        New packages can be released independently.

    Mon. May 20th (release candidate)
        Updated releases of core packages available.

    Wed. May 29th
        Freeze rosdistro.
        No PRs for Dashing on the `rosdistro` repo will be merged (reopens after the release announcement).


Changes since the Crystal release
---------------------------------

ament_cmake
~~~~~~~~~~~

The CMake function ``ament_index_has_resource`` was returning either ``TRUE`` or ``FALSE``.
As of `this release <https://github.com/ament/ament_cmake/pull/155>`_ it returns either the prefix path in case the resource was found or ``FALSE``.

If you are using the return value in a CMake condition like this:

.. code-block:: cmake

   ament_index_has_resource(var ...)
   if(${var})

you need to update the condition to ensure it considers a string value as ``TRUE``:

.. code-block:: cmake

   if(var)

rosidl
~~~~~~

Until Crystal each message generator package registered itself using the ``ament_cmake`` extension point ``rosidl_generate_interfaces`` and was passed a set of ``.msg`` / ``.srv`` / ``.action`` files.
As of Dashing the message generation pipeline is based on ``.idl`` files instead.

Any message generator package needs to change and register itself using the new extension point ``rosidl_generate_idl_interfaces`` which passes only ``.idl`` files instead.
The message generators for the commonly supported languages C, C++, and Python as well as the typesupport packages for introspection, FastRTPS, Connext and OpenSplice have already been updated (see `ros2/rosidl#334 <https://github.com/ros2/rosidl/pull/334/files>`__).
The CMake code calling ``rosidl_generate_interfaces()`` can either pass ``.idl`` files directly or pass ``.msg`` / ``.srv`` / ``.action`` which will then internally be converted into ``.idl`` files before being passed to each message generator.

The format of ``.msg`` / ``.srv`` / ``.action`` files is not being evolved in the future.
The mapping between ``.msg`` / ``.srv`` / ``.action`` files and ``.idl`` files is described in `this design article <http://design.ros2.org/articles/legacy_interface_definition.html>`__.
A `second design article <http://design.ros2.org/articles/idl_interface_definition.html>`__ describes the supported features in ``.idl`` files.
In order to leverage any of the new features existing interfaces need to be converted (e.g. using the command line tools  ``msg2idl`` / ``srv2idl`` / ``action2idl``).

rosidl_generator_cpp
~~~~~~~~~~~~~~~~~~~~

The C++ data structures generated for messages, services and actions provide setter methods for each field.
Until Crystal each setter returned a pointer to the data structure itself to enable the named parameter idiom.
As of Dashing these setters `return a reference <https://github.com/ros2/rosidl/pull/353>`__ instead since that seems to be the more common signature as well as it clarifies that the returned value can't be a ``nullptr``.

launch
~~~~~~

The ``launch_testing`` package caught up with the ``launch`` package redesign done in Bouncy Bolson.
The legacy Python API, already moved into the ``launch.legacy`` submodule, has thus been deprecated and removed.

See ``launch`` `examples <https://github.com/ros2/launch/tree/master/launch/examples>`__ and `documentation <https://github.com/ros2/launch/tree/master/launch/doc>`__ for reference on how to use its new API.

See `demos tests <https://github.com/ros2/demos>`__ for reference on how to use the new ``launch_testing`` API.

rmw
~~~

Changes since the `Crystal Clemmys <Release-Crystal-Clemmys>` release:

* New API in ``rmw``, a fini function for ``rmw_context_t``:

 * `rmw_context_fini <https://github.com/ros2/rmw/blob/c518842f6f82910482470b40c221c268d30691bd/rmw/include/rmw/init.h#L111-L136>`_

* Modification of ``rmw``, now passes ``rmw_context_t`` to ``rmw_create_wait_set``:

 * `rmw_create_wait_set <https://github.com/ros2/rmw/blob/c518842f6f82910482470b40c221c268d30691bd/rmw/include/rmw/rmw.h#L522-L543>`_

Known Issues
------------

None yet.
