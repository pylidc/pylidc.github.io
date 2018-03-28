pylidc
======

`pylidc` is an `Object-relational mapping <https://en.wikipedia.org/wiki/Object-relational_mapping>`_ for the data provided in the `LIDC dataset <https://wiki.cancerimagingarchive.net/display/Public/LIDC-IDRI>`_. This means that the data can be queried in SQL-like fashion, and also that the data are also `objects <https://en.wikipedia.org/wiki/Object-oriented_programming>`_ that add additional functionality via functions that act on instances of the queried data.

.. toctree::
    :maxdepth: 1

    meta


.. |img1| image:: /images/scan_visualize.png    
    :scale: 20%

.. |img2| image:: /images/consensus.png
    :scale: 50%

.. |img3| image:: /images/annotation3d.png
    :scale: 50%

+---------+---------+
| |img1|  | |img2|  |
+---------+---------+
| |img3|  |         |
+---------+---------+

Installation
------------
The module and its dependencies can be installed via `pip install pylidc`.

Additonal details regarding setting the external data path 
for CT DICOM data can be found here:

.. toctree::
    :maxdepth: 1

    install

Tutorials
---------

.. toctree::
    :caption: Tutorials
    :maxdepth: 1

    tuts/scan
    tuts/annotation
    tuts/consensus

Full API
--------

A detailed listing of the classes and their respective member functions and 
attributes are given here:

.. toctree::
    :caption: Full API
    :maxdepth: 1

    scan
    annotation
    contour
