# Changelog

## 1.0.0

- first release

Converts Ordnance Survey grid references to latitude and longitude, as the
JavaScript package ospoint (MIT, Ideal Postcodes) does: British National
Grid eastings and northings to OSGB36, ETRS89 and WGS84, and Irish Grid ones
to ETRS89 and WGS84. Every test in ospoint's suite is ported, with its test
vectors held far tighter than ospoint holds them. Two of ospoint's results
are corrected rather than copied: a transposed rotation in its Helmert
transformation, which put ETRS89 and WGS84 up to 0.7 m out, and the British
datum shift it used on the Irish Grid.
