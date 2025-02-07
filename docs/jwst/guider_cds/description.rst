Description
============

:Class: `jwst.guider_cds.GuiderCdsStep`
:Alias: guider_cds

The ``guider_cds`` step computes countrate images from the
Correlated Double Sampling (CDS) detector readouts used in FGS
guiding mode data. The exact way in which the countrate images
are computed depends on the guiding mode (ID, ACQ1, ACQ2,
TRACK, FineGuide) in use.

ID mode
-------
The ID mode uses 2 integrations (NINTS=2) with 2 groups per
integration (NGROUPS=2). For this mode the ``guider_cds`` step
first computes a difference image for each integration by
subtracting group 1 from group 2. A final difference image is
then computed by taking the minimum value at each pixel from
the 2 integrations. The minimum difference image is then divided
by the group time to produce a countrate image. The output
data array is 3D, with dimensions of (ncols x nrows x 1).

ACQ1, ACQ2, and TRACK modes
---------------------------
These modes use multiple integrations (NINTS>1) with 2 groups
per integration (NGROUPS=2). For these modes the ``guider_cds``
step computes a countrate image for each integration, by
subtracting group 1 from group 2 and dividing by the group time.
The output data array is 3D, with dimensions of
(ncols x nrows x nints).

FineGuide mode
--------------
The FineGuide mode uses many integrations (NINTS>>1) with 4
groups at the beginning and 4 groups at the end of each
integration. The ``guider_cds`` step computes a countrate
image for each integration by subtracting the average of the
first 4 groups from the average of the last 4 groups and
dividing by the group time. The output data array is
3D, with dimensions of (ncols x nrows x nints).

For all modes, the values for the output ERR array are calculated for
each segment in each integration from the two variances of the slope
of the segment. The ERR array has the same dimensions as the output
data array.

The segment's variance due to read noise is:

.. math::
   var^R = \frac{2 \ R^2 }{tgroup^2 } \,,

where :math:`R` is the noise (from the READNOISE reference file) in the
difference between 2 groups (or, for the FineGuide mode, the difference
between the averages of the
4 groups at the beginning and the 4 groups at the end of the integration)
and :math:`tgroup` is the group time in seconds (from the keyword TGROUP).


The segment's variance due to Poisson noise is: 

.. math::
   var^P = \frac{ slope }{ tgroup \times gain }  \,,

where :math:`gain` is the gain for the pixel (from the GAIN reference file),
in e/DN. The :math:`slope` is the slope of the pixel.

The square-root of the sum of the Poisson variance and read noise variance is
written to the ERR extension.

After successful completion of the step, the "BUNIT" keyword in
the output data is updated to "DN/s" and the "S_GUICDS"
keyword is set to "COMPLETE".
