Annotation Consensus
====================

Note that the `consensus` function is imported into the pylidc namespace
so that the `utils` module does not need to be imported for its use::

    import pylidc as pl
    
    # consensus function:
    pl.consensus( ... )


A usage example can be found `here <tuts/consensus.html>`_.

-------------

.. currentmodule:: pylidc
.. automethod:: utils.consensus(anns, clevel=0.5, pad=None, ret_masks=True, verbose=True)
