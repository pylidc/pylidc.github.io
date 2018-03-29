Advanced queries
================

`pylidc` is built on `SQLAlchemy <https://www.sqlalchemy.org/>`_, using a 
SQLite database for storage, and so, if you are familar with a SQL-like query
language, you will find familiar capabilities in `pylidc`. This short tutorial 
is not a comprehensive guide to everything you can do with 
SQLAlchemy --- if you'd like to explore this aspect further you should 
visit the SQLAlchemy documentation directly.

Jump to:

* `Get a random result`_
* `Query multiple model parameters with a join`_
* `Filtering with an in clause`_
* `Grouping nodules with similar attributes`_
 

Get a random result
-------------------

One common objective for data exploration is to grab a random instance from 
some query. You can accomplish this by import `func` from `sqlalchemy`, 
ordering the results by `random`, and taking the first result::

    from sqlalchemy import func
    
    # Fetch all highly suspicious nodules
    anns = pl.query(pl.Annotation).filter(pl.Annotation.malignancy == 5)

    ann = anns.order_by(func.random()).first()
    print(ann.id, ann.Malignancy)
    # => 2516, 'Highly Suspicious'

    ann = anns.order_by(func.random()).first()
    print(ann.id, ann.Malignancy)
    # => 4749, 'Highly Suspicious'

Query multiple model parameters with a `join` 
---------------------------------------------

Another common objective is to query for an `Annotation` object that is 
constrained by the queryable parameters of its corresponding `Scan` in 
some way. This can be accomplished with using a `join`. For example::

    # Fetch all annotations with non-indeterminate malignancy value
    # and whose respective scan has slice thickness less than 1 mm.
    anns = pl.query(pl.Annotation).join(pl.Scan)\
             .filter(pl.Scan.slice_thickness < 1, 
                     pl.Annotation.malignancy != 3)
    print(anns.count())
    # => 181
    
Meanwhile, the number of non-indeterminate annotations is::

    anns = pl.query(pl.Annotation)\
             .filter(pl.Annotation.malignancy != 3)
    print(anns.count())
    # => 4253
    
And, the number of scans with slice thickness less than 1 mm is::

    scans = pl.query(pl.Scan)\
              .filter(pl.Scan.slice_thickness < 1)
    print(scans.count())
    # => 39
    
.. note:: When querying with a join, be careful what object type you
    actually would like returned. In the `join` example above, we joined on
    `Annotation` and `Scan` objects, but returned all `Annotation` objects
    with the desired properties.
    
Filtering with an `in` clause
-----------------------------

Suppose we wanted to count the total number of annotations belonging to the 
scans in the query above. One way is to loop through all the scans and sum 
up the number of annotations belonging to each::

    scans = pl.query(pl.Scan)\
              .filter(pl.Scan.slice_thickness < 1)
    n_anns = sum([len(scan.annotations) for scan in scans])
    print(n_anns)
    # => 300

Alternatively, a more SQL-like approach is to query for all Annotations whose 
`scan_id` attribute is an id from the ids from `scans`::

    sids = [scan.id for scan in scans]
    anns = pl.query(pl.Annotation).filter(pl.Annotation.scan_id.in_( sids ))
    print(anns.count())
    # => 300

Grouping nodules with similar attributes
----------------------------------------

Let's suppose we'd like to know which `Annotation` s share identical 
characteristic values (i.e., lobulation, spiculation, etc.).

This can be accomplished by making use of the 
`SQLite aggregate functions <https://www.sqlite.org/lang_aggfunc.html>`_, 
such as `count` and `group_concat`, as well as the `group_by` and
`having` functions::

    from sqlalchemy import func

    # Get a list of the attributes. Alternatively,
    # we could list them all out in the query, i.e.,
    # pl.Annotation.spiculation, pl.Annotation.malignancy, etc...
    all_features = [getattr(pl.Annotation, fname)
                        for fname in pl.annotation_feature_names]

    groups = pl.query(func.group_concat(pl.Annotation.id))\
               .group_by( *all_features )\
               .having(func.count('*') > 1)

The previous query fetches all annotations that share identical numerical 
feature sets. This is accomplished by using the SQL `group by` statement 
and filtering to including groups with size strictly greater than 1 using 
the SQL `having` statement.

Note that in the query itself, we query for the `Annotation` ids, not the 
`Annotation` objects themselves as we would normally do. If `pl.Annotation` 
is used as the query argument, then only a single member of the group is 
returned. `group_concat` returns the entire group in the query, rather 
than a single member; however, a SQL literal must be used inside of 
`group_concat`, so we query for the `Annotation` id instead (the `Annotation`
object can be obtained from the id, anyway).

So what exactly is the result?::

    print(groups.count())
    # => 837

This means there are 837 nodule annotation groups whose group members have 
identical characteristic feature values. The individual results can be 
inspected::
    
    print(groups[0], groups[1])
    # => (u'6764,6766',), (u'5781,5782,5937',)

Each result is a 1-tuple whose element is a string of `Annotation` ids, 
separated by commas. We can assert that the feature values are indeed identical
in a particular group::

    for id in groups[0].split(','):
        ann = pl.query(pl.Annotation).get(id)
        print(ann.feature_vals())
    # => [1 1 3 3 5 1 1 5 1]
    # => [1 1 3 3 5 1 1 5 1]

Let's look at the largest such group::

    group_sizes = [len(g[0].split(',')) for g in groups]
    print(max(group_sizes))
    # => 296

So, there is a group of annotations of size 296, all having identical 
feature values, and this is the biggest such group. Let's look at what the 
feature values are::

    import numpy as np
    
    # Get the location of the maximum sized group.
    i = np.argmax(group_sizes)
    
    # Grab the first id in the maximum sized group, for no particular reason.
    id = int(groups[i][0].split(',')[0])
    
    ann = pl.query(pl.Annotation).get(id)
    ann.print_formatted_feature_table()
    # => Feature              Meaning                    # 
    # => -                    -                          - 
    # => Subtlety           | Fairly Subtle            | 3 
    # => Internalstructure  | Soft Tissue              | 1 
    # => Calcification      | Absent                   | 6 
    # => Sphericity         | Round                    | 5 
    # => Margin             | Sharp                    | 5 
    # => Lobulation         | No Lobulation            | 1 
    # => Spiculation        | No Spiculation           | 1 
    # => Texture            | Solid                    | 5 
    # => Malignancy         | Moderately Unlikely      | 2

There are 296 annotations with values identical to the above.

We can confirm that the feature values for all these annotations by computing 
the variance of the each feature value computed over all annotations in the 
group::

    # Fetch the feature values for all annotations in the group
    ids = [int(id) for id in groups[i][0].split(',')]
    
    fvals = pl.query(*all_features)\
              .filter(pl.Annotation.id.in_( ids )).all()
    fvals = np.array(fvals)
    
    print(fvals.shape)
    # => (296, 9)
    
    print(fvals.var(axis=0))
    # => [0. 0. 0. 0. 0. 0. 0. 0. 0.]    
