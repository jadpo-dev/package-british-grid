# BritishGrid

Call `convert.to_wgs84` with an `EastingNorthing` to get a `Point` you can
store, measure with `geo.distance` and send as GeoJSON. Its `crs` says which
grid: `EPSG:27700` for the British National Grid, `EPSG:29902` for the Irish
Grid. Ordnance Survey's Code-Point Open and most UK address data give the
British one, sometimes with leading zeros on the northing, which a number
drops harmlessly.

Use `convert.to_osgb36` only where something else works in OSGB36 degrees,
such as old Ordnance Survey maps; it never mixes with a `Point`.

Both failures are about the input, never the method: `UnsupportedGrid` when
the `Crs` is one this package doesn't convert, `OutsideGrid` when an easting
or northing is off its grid. Report either as a bad reference.

Results are good to a few metres, not survey grade. For survey work, convert
with Ordnance Survey's OSTN15 instead.
