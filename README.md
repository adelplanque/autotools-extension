# Autotools-Extension

`autotools-extension` allows you to add a configuration step before building a python extension with distutil or setuptools. It is done with the well-known `autoconf`.
According to the autotools philosophy, only the developer uses `autoconf`. The `configure` script is then embedded in the source distribution.

## Getting Started

### Prerequisites

`autotools-extension` work with python2 (tested with python2.6) or python3.

It requires `setuptools`.

### Installing

* From sources:

  ~~~bash
  python setup.py install
  ~~~

* From pip:

  ~~~bash
  pip install autotools-extension
  ~~~

Please refer to [alternate installation](https://docs.python.org/3/install/index.html#inst-alt-install) for alternative installation schema.

## Example

Suppose we want to package an extension `my_ext.cpp` using [boost-python](https://www.boost.org/doc/libs/1_69_0/libs/python/doc/html/index.html])
The name of the library required for link editing depends on the system on which the installation is performed.
boost_python, boost_python37, boost_python3-py36, ... are possible names, depending on the OS, the python version, ...
By adding an autoconf script, the name of the library will be determined at the time of installation.

~~~python
from setuptools import setup, Extension

from autotools_extension.autoconf import Distribution

setup(
    distclass=Distribution,
    name='my_package,
    packages=['my_extension],
    ext_modules=[Extension(
        'my_extension,
        sources=['my_ext.cpp],
        libraries=['@BOOST_PYTHON_LIB@']
    )],
    configure_ac="""
        dnl Check for python
        AX_PYTHON_DEVEL

        dnl Boost
        AX_BOOST_BASE([1.41], [],
            AC_MSG_ERROR([Needs Boost but it was not found in your system])
        )

        dnl Boost Python
        AX_BOOST_PYTHON
        if test "x$ac_cv_boost_python" != "xyes" -o "x$BOOST_PYTHON_LIB" == "x"; then
            AC_MSG_ERROR([Boost Python needed])
        fi
    """,
    configure_options = [
        ('with-boost=', None, "Boost install prefix"),
    ],
)
~~~

`configure_options` are thrown to configure script, so you can specify custom boost location by:

~~~bash
python setup.py configure --with-boost /path/to/boost install
~~~

A full example is provided at https://github.com/adelplanque/autotools-extension-example

## License

[MIT License](https://github.com/adelplanque/autotools-extension-example/blob/master/LICENSE)
