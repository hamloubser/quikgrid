# Introduction #

Quikgrid provides an easy interface to contouring height-field matrices (i.e. 2.5D terrains).  Results from the algorithm are comparable to commercial tools when tested against the same data.

The python bindings provide a pythonic interface to the library via the ctypes library.  Refer to qgcapi.cpp for the C interface.

# Building #

On `*`nix, build from the top-level Makefile:
```
# make
# make install
```

This will build **core/** and install the libquikgrid.so file into /usr/local/lib, and run the setup.py file in **python/**.

# Usage #

For a detailed example, refer to the test case: http://code.google.com/p/quikgrid/source/browse/trunk/python/quikgrid/capi_test.py

A simple example:
```
# create the grid: matrix xsize, matrix ysize, SW corner coordinate tuple, NE corner coordinate tuple
sg = SurfaceGrid(100, 100, (11,11), (21,21))

# create a list of points with height values: 
testdata = [
      (11,11,11),
      (14,11.5,12),
      (11.2,15.4,12),
      ...]

# adjust the heights in the matrix to reflect the scatter points not 
# tied to matrix nodes (done via interpolation, clustering etc.)
sg.expand(testdata)

# get the expanded matrix back
mat = sg.to_matrix()
for row in mat:
   for tup in row: # x, y, height
      ...

# find the contour(s) at 14.0
contours = sg.contour(14.0)
for c in contours:
    for p in c: # an x,y point tuple
        ...

# return the bi-linear interpolated value from the height field matrix
val = sg.interpolate(15.5, 11.9)

# set a height value at a matrix node
sg.setZ(1,1,numpy.NaN)

# check the value of the node
assert numpy.isnan(sg.get(1,1).z)

# return contours as polygon or linestring features via shapely
# closure_tolerance is used for reconciling linestrings into polygons.
contours = sg.contour(14.0, as_geometry=True, closure_tolerance=0.1)
```


# Known issues #
The current implementation is not thread safe for contouring.