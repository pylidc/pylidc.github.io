Annotation consensus
====================

The :meth:`pylidc.utils.consensus` utility accepts a list of annotations to 
produce a single boolean-valued volume of those annotations. It also returns 
the individual boolean masks for each Annotation, placed in a common frame of 
reference -- i.e., a common bounding box, which is also returned.

In the following example, we first compute the 50% consensus consolidation of 
the annotation contours, then we plot them along with the original 4 contours
from the 4 different annotations of the nodule:

.. literalinclude:: /code/consensus_example.py
    :language: python

.. image:: /images/consensus.png
    :target: ../_images/consensus.png
