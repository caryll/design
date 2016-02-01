<p align="center"><img src="https://raw.githubusercontent.com/caryll/design/master/caryll-logo.png" width=200 height=200></p><h1 align="center">Caryll/Design</h1>

**This repository contains the design proposals of Caryll, the parametric font-design software.**

## Origin

After the creation of [Iosevka](https://github.com/be5invis/Iosevka) I found that, if we introduce some of the “procedural” / “parametric” methods to the conventional font designing software, it will significantly help type designers. Type designers maintain the metrics across the glyphs by moving the points, and I think such workflow can be improved.

By introducing some concepts in the CAD software like Solidworks, the maintaince can be much simpler and clearer. In the proposed workflow, type designers explicitly add metric or geometric constraints into the font, and a constraint solver will move the points of the outline automatically to make all the constraints satisfied.

## Overview

Caryll is a font-design software supports

* Constraint solving.
* <del>Constructive geometry for glyphs' outline.</del> Overcomplicated. Discarded.
* Advanced curve-modeling methods including Hobby's splines.
* Automated glyph building for composites, small caps, superscripts, etc.
* Advanced multiple-master interpolation (may be powered by Kriging method).

## File format

BSON-based I think.

Glyph structure:

``` yaml
name: 'E'
local-equations: 
    - name: stem
      expr: whatever
variables:
	- master: Bold
      parameters: [...]
    - master: Thin
      parameters: [...]
free-variables: [t1] # variable used for proportions, etc.
outlines:
    - type: bez3
      render: true # will be used as glyph outline
      points: 
          - [smooth p1 p2] # smooth on-curve
          - [implicit tension] # implicit off-curve
          - [directional tension] # direction-constrained implicit off-curve
          - [smooth ...] # smooth on-curve
          - [corner ...] # corner on-curve
          - [explicit ...] # explicit off-curve
          - [explicit ...]
          - [cycle] # close contour
    - type: include # include a glyph
      id: a
      points:
        origin: [corner p1 p2]
        ......
      transform: [1 0 0 1]
constraints:
    - type: ppdistance
      points: z1 z2
      value: p1 # a variable
    - type: pldistance
      points: z1 z2 z3
      value: t1 # .. and free variable used for proportions
```

The multiple-master interpolation follows this workflow:

* Solve constraints for all masters.
* Interpolate parameters for the specified instance.
* Solve constraints for this instance.

(Removing the third step will give ability to assign different constraints for different masters.)