pylidc
======

`pylidc <https://github.com/pylidc/pylidc>`_ is an `Object-relational mapping
<https://en.wikipedia.org/wiki/Object-relational_mapping>`_ 
(using `SQLAlchemy <https://www.sqlalchemy.org/>`_) for the data provided 
in the `LIDC dataset 
<https://wiki.cancerimagingarchive.net/display/Public/LIDC-IDRI>`_. This 
means that the data can be queried in SQL-like fashion, and that 
the data are also `objects 
<https://en.wikipedia.org/wiki/Object-oriented_programming>`_ that add 
additional functionality via functions that act on 
instances of data obtained by querying for particular attributes.

.. toctree::
    :maxdepth: 1

    meta

Jump to:

* `Installation`_
* `Tutorials`_
* `Full API`_

----------------------------------

.. raw:: html
    
    <table style="width:100%; max-width: 500px;">
        <tr>
            <td style="width: 50%;">
                <a href="tuts/scan.html#scan-visualization">
                    <img alt="Interactive scan visualization" title="Interactive scan visualization" src="_images/scan_visualize.png">
                </a>
            </td>
            <td style="width: 50%;">
                <a href="tuts/annotation.html#surface-visualization">
                    <img alt="3D nodule surface visualization" title="3D nodule surface visualization" src="_images/annotation3d.png">
                </a>
            </td>
        </tr>

        <tr>
            <td style="width: 50%;">
                <a href="tuts/consensus.html">
                    <img alt="Consensus consolidation of multiple annotations" title="Consensus consolidation of multiple annotations" src="_images/consensus.png">
                </a>
            </td>
            <td style="width: 50%;">
                <a href="tuts/annotation.html#annotation-visualization">
                    <img alt="Annotation contour conversion to boolean mask" title="Annotation contour conversion to boolean mask" src="_images/mask_bbox.png">
                </a>
            </td>
        </tr>
    </table>


----------------------------------

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

The following tutorials sections provide a quick introduction to various 
aspects of the `pylidc` module:

.. toctree::
    :caption: Tutorials
    :maxdepth: 1

    tuts/scan
    tuts/annotation
    tuts/consensus
    tuts/advanced-queries

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
    consensus
