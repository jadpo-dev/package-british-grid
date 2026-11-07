# @jadpo/british-grid

Converts Ordnance Survey grid references to latitude and longitude. A Jadpo
library package, called `BritishGrid.v1` in your app.

```jadpo
var reference: EastingNorthing = EastingNorthing {
    easting: 529404.0
    northing: 184524.0
    crs: "EPSG:27700"
}
var point: Point = attempt BritishGrid.convert.to_wgs84(reference)
```

| Function | From | Gives |
|---|---|---|
| `convert.to_osgb36` | British National Grid, `EPSG:27700` | `Osgb36Position`, degrees on OSGB36 |
| `convert.to_etrs89` | British National Grid, or Irish Grid `EPSG:29902` | `Etrs89Position`, degrees on ETRS89 |
| `convert.to_wgs84` | either grid | `Point`, the ETRS89 figures |

Each fails `UnsupportedGrid` for any other `Crs`, and `OutsideGrid` for an
easting or northing off its grid.

It lives in its own repository so each release is this repository at a tag,
which is how jadpo.dev's package registry publishes every package. Once the
registry opens:

```sh
jadpo package add @jadpo/british-grid
```

## Accuracy

The grid to OSGB36 step is Ordnance Survey's transverse Mercator equations,
good to well under a millimetre. ETRS89 and WGS84 then come through a
seven-parameter Helmert transformation, typically within a few metres
across Great Britain. That suits mapping and finding what is near a
postcode. It isn't survey grade: that needs Ordnance Survey's OSTN15 grid,
which this package doesn't carry. WGS84 is given as ETRS89, which it drifts
from by about 2.5 cm a year.

Against ospoint's own test vectors, its OSGB36 figures are matched to
10⁻⁹ degrees and its ETRS89 ones on the British grid to 10⁻⁸ degrees, about a
millimetre. Those vectors come from the same Helmert method, so they check
the port, not the method's accuracy. The Irish Grid vectors are matched to
within 0.3 m.

## Where it differs from ospoint

The method, its constants and its test vectors come from
[ospoint](https://github.com/ideal-postcodes/ospoint) by Ideal Postcodes,
under the MIT licence (see NOTICE). This package isn't theirs. It copies
ospoint's behaviour, its stopping tolerances and its WGS84-as-ETRS89 included,
except where that would be wrong:

- **The Helmert rotation.** ospoint's x line uses the x rotation where the
  z rotation belongs, which puts its ETRS89 and WGS84 up to 0.72 m from its
  own test vectors. This package uses Ordnance Survey's formula, which
  matches them to a millimetre.
- **The Irish Grid's datum shift.** ospoint applies OSGB36's Helmert
  parameters to Irish Grid positions. This package uses TM65's to ETRS89,
  which lands within 0.23 m of ospoint's Irish vectors, where ospoint's own
  answers are 0.76 m out. Irish Grid positions aren't given on OSGB36, which
  ospoint's `toOSGB36("irish_national_grid")` would label wrongly.
- **Fractions of a metre are kept.** ospoint reads eastings and northings
  with `parseInt`, dropping anything after the point. An `EastingNorthing`
  keeps them, so Ordnance Survey's worked example lands on its answer.
- **Off-grid positions fail.** ospoint converts any number to some place.
  This package fails `OutsideGrid` past the grid's extent, and gives up
  with the same failure if an iteration hasn't settled in 20 steps, where
  ospoint would loop.

Licensed under Apache 2.0.
