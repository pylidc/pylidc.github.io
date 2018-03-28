Installation and setup
======================

`pylidc` works on Linux, Mac, and Windows, and on Python 2 and 3.

The package can be installed via `pip`::

    pip install pylidc

Dicom file directory configuration
----------------------------------

While `pylidc` has many functions for analyzing and querying only annotation 
data (which do not require DICOM image data access), `pylidc` also has many 
functions that do require access to the DICOM files associated with the 
LIDC dataset. `pylidc` looks for a special configuration file that tells it 
where DICOM data is located on your system. You can use `pylidc` without 
creating this configuration file, but of course, any functions that depend 
on CT image data will not be usable.

`pylidc` looks in your home folder for a configuration file called, 
`.pylidcrc` on Mac and Linux, or `pylidc.conf` on Windows. You must create 
this file. On Linux and Mac, the file should be located at 
`/home/[user]/.pylidcrc`. On Windows, the file should be located at 
`C:\Users\[User]\pylidc.conf`.

The configuration file should be formatted as follows::

    [dicom]
    path = /path/to/big_external_drive/datasets/LIDC-IDRI
    warn = True

If you want to use `pylidc` without utilizing the DICOM data (for say, 
querying annotation attributes, etc.), you can remove `path` and set 
`warn` to `False` and leave the path undefined,  i.e.,::

    [dicom]
    warn = False

and the module won't bother you about it each time you import the module.

The DICOM files should be stored in a folder with corresponding `PatientID`
(i.e., a string of the form `LIDC-IDRI-dddd`, where `dddd` is a string of 4 
integers). `pylidc` looks for a subfolder within::

    /path/to/big_external_drive/datasets/LIDC-IDRI/LIDC-dddd

for DICOM files that match the `SeriesInstanceUID` and 
`StudyInstanceUID` of the particular scan being requested.
