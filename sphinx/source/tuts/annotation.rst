The Annotation class
====================

A :class:`pylidc.Annotation` object belongs to a :class:`pylidc.Scan` object::

    import pylidc as pl

    ann = pl.query(pl.Annotation).first()
    print(ann.scan.patient_id)
    # => LIDC-IDRI-0078

Querying and assigned characteristic values
-------------------------------------------

The queryable attributes for `Annotation` objects are the characteristic
values assigned by the particular annotating radiologist::

    anns = pl.query(pl.Annotation).filter(pl.Annotation.spiculation == 5,
                                          pl.Annotation.malignancy == 5)
    print(anns.count())
    # => 91

.. note:: Not all of the class attributes are queryable! Some attributes 
    are actually *computed* properties. You can determine this by, for example,
    examining the object type for the class::

        print(type(pl.Annotation.lobulation))
        # => <class 'sqlalchemy.orm.attributes.InstrumentedAttribute'>
        # => ^^^ queryable because it's an sqlalchemy attribute.
        
        # Whereas ...
        print(type(pl.Annotation.Lobulation))
        # => <type 'property'>
        # ^^^ not queryable because it's a computed property

    The `lobulation` attribute is the numerical value assigned by the 
    annotating radiologist, whereas the `Lobulation` attribute is a property 
    that encodes the numerical value to the semantic string representation.

Each of the characteristic values are the numerical values assigned by 
the annotating radiologist. Alongside each of these attributes is a 
corresponding computed property that gives the semantic interpretation 
of the numerical value for a given characteristic::
    
    ann = pl.query(pl.Annotation)\
          .filter(pl.Annotation.malignancy == 5).first()
    print(ann.malignancy, ann.Malignancy)
    # => 5, 'Highly Suspicious'

    print(ann.margin, ann.Margin)
    # => 2, 'Near Poorly Defined'
    
The names of these characteristics are found in
`pl.annotation_feature_names`::

    ('subtlety',
     'internalStructure',
     'calcification',
     'sphericity',
     'margin',
     'lobulation',
     'spiculation',
     'texture',
     'malignancy')

All of these characteristics values and strings can be quickly viewed by::

    ann.print_formatted_feature_table()
    # => Feature              Meaning                    # 
    # => -                    -                          - 
    # => Subtlety           | Obvious                  | 5 
    # => Internalstructure  | Soft Tissue              | 1 
    # => Calcification      | Absent                   | 6 
    # => Sphericity         | Ovoid/Round              | 4 
    # => Margin             | Near Poorly Defined      | 2 
    # => Lobulation         | Near Marked Lobulation   | 4 
    # => Spiculation        | No Spiculation           | 1 
    # => Texture            | Solid                    | 5 
    # => Malignancy         | Highly Suspicious        | 5

We can also query directly for the attributes themselves, rather 
than the entire Annotation object::
    
    svals = pl.query(pl.Annotation.spiculation)
    svals = anns.filter(pl.Annotation.spiculation > 3)

    print(svals[0])
    # => (4,)

    print(all([s[0] > 3 for s in sval]))
    # => True

Contour data
------------

The :class:`pylidc.Contour` class is almost never used directly, but only 
through the :class:`pylidc.Annotation` object to which the contours belong.
They can be accessed directly, however, via::

    ann = pl.query(pl.Annotation).first()
    contours = ann.contours

    print(contours[0])
    # => Contour(id=21,annotation_id=4)

The `diameter`, `surface_area`, and `volume` attributes are
all, for example, computed properties that use the contour data
for a particular Annotation::

    # units: mm, mm^2, mm^3
    print("%.2f, %.2f, %.2f" % (ann.diameter, ann.surface_area, ann.volume))
    # => 32.81, 2228.53, 5230.34

The contour data are also used to compute the bounding box indices of an 
annotation. The `bbox` returns a tuple of slices corresponding to the nodule
bounding box indices. This can be used to easily index into the 
scan NumPy volume::

    bbox = ann.bbox()
    print(bbox)
    # => (slice(151, 185, None), slice(349, 376, None), slice(44, 50, None))

    vol = ann.scan.to_volume()
    print(vol[bbox].shape)
    # => (34, 27, 6)

The :meth:`pylidc.Annotation.bbox` function accepts a `pad` argument which 
is quite flexible for adding context about the nodule.

We can also get the physical dimensions of the bounding box::

    print(ann.bbox_dims())
    # => [21.45, 16.90, 15.0]

Annotation visualization
------------------------

3D visualization
~~~~~~~~~~~~~~~~

The nodule surface (as obtained by a single annotator) can be visualized in 
three dimensions by invoking the following::
    
    ann = pl.query(pl.Annotation)\
            .filter(pl.Annotation.lobulation == 5).first()
    ann.visualize_in_3d()

which appears as:

.. image:: /images/annotation3d.png
    :target: ../_images/annotation3d.png

CT visualization
~~~~~~~~~~~~~~~~

The contours can be visualized interactively on top of the CT image data::

    ann = pl.query(pl.Annotation).first()
    ann.visualize_in_scan()

which appears as:

.. image:: /images/annotation_viz_in_scan.png
    :target: ../_images/annotation_viz_in_scan.png

