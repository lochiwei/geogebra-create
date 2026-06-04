# Learned GeoGebra Techniques

## Polyline for Connected Segments and Closed Outlines

When drawing a connected chain of segments, use `Polyline` instead of creating individual segments one by one.

Useful forms:

```geogebra
Polyline({A, B, C, D})
Polyline(A, B, C, D)
```

For a closed outline, repeat the first point at the end:

```geogebra
Polyline({A, B, C, D, A})
Polyline(A, B, C, D, A)
```

When vertices follow a regular geometric structure, use listable object behavior to generate the vertices compactly:

```geogebra
Polyline(A + {(0, 0), (w, 0), (w, h), (0, h), (0, 0)})
```

Use this when constructing boundaries, polygonal outlines, regular repeated vertex patterns, or closed guide paths where a polyline is clearer than many separate segment objects.

## Polygon from a Point List Without Extra Segment Objects

When only the filled polygon region is needed, and the boundary segments do not need to exist as separate objects, pass the vertices as a list:

```geogebra
Polygon({A, B, C, D})
```

This creates the polygon itself without also creating separate side segment objects. Use this for filled regions where extra boundary objects would clutter the Algebra View or violate the principle of not creating unnecessary objects.

## Custom Tools with Point List Inputs

Use Custom Tools when a repeated geometric pattern can be expressed as a reusable transformation. A Custom Tool can accept a point list as an input parameter. The reference file `lessons/CustomTools.ggb` demonstrates this with `NextGen`.

Reference file:

```text
lessons/CustomTools.ggb
SHA-256: 8e02554b66d696203d10bd6e591e837c418d4e36fd117e41f0e3e98896f1750a
```

Practical workflow:

```geogebra
points = {(1, 1), (1, 2), (2, 2)}
```

Create dependent objects from this list, then use the Custom Tool creation workflow to select the dependent output and the point list as one of the inputs. This makes the resulting tool callable with any compatible point list.

The `NextGen` tool in the reference file has this structure:

```geogebra
n = Length[v0]
l1 = Sequence[Dilate[v0(1 + Mod[k, n]), t, v0(k)], k, 1, n]
```

Here `v0` is the point list, `t` is the affine ratio, and `l1` is the transformed point list. This pattern is useful for polygon vertex transformations, edge-wise interpolation, contraction patterns, and list-to-list geometric operations.

## Layered Custom Tools for Repeated Patterns

When a complex construction contains a pattern that repeats at several levels, split the logic into layered Custom Tools. Keep each tool responsible for one level of complexity.

The reference file demonstrates this layering:

```geogebra
NextGen(<PointList>, <param>)
NestedSpiral(<point list>, <affine ratio>, <iterations>)
```

`NextGen` handles one generation of transformed points. `NestedSpiral` builds on `NextGen` and turns repeated generations into polygons:

```geogebra
nested = IterationList[NextGen[L, t], L, {v0}, m]
spiral = Zip[Polygon[L], L, nested]
```

Use this approach to keep the Algebra View concise. Prefer a small number of meaningful high-level tools over many intermediate objects, especially when intermediate objects are only implementation details.

## Recursive Patterns with IterationList

For recursive or generation-by-generation patterns, prefer `Iteration` or `IterationList` instead of manually creating each generation.

Use `IterationList` when the full history is needed:

```geogebra
nested = IterationList[NextGen[L, t], L, {v0}, m]
```

Use this for nested polygons, spirals, repeated affine transformations, iterative midpoint constructions, fractal-like constructions, and any pattern where each stage depends on the previous stage.

Combine `IterationList` with `Zip` to map each generated stage into a visible object:

```geogebra
spiral = Zip[Polygon[L], L, nested]
```

This avoids explicit one-by-one object creation and keeps the construction parameter-driven.

## Symmetry-First Blocks for Rotational Nested Spirals

When a construction is made from one local motif, its reflected partner, and rotated copies around a center, build the motif once and transform the resulting objects as a group. Do not expand every sector into separate point lists unless each sector is genuinely different.

Recommended workflow for octagonal or radial nested-spiral figures:

```geogebra
n = 8
R = 5
alpha = (360°) / n
theta_{A} = 90°
O = (0, 0)
A = (R; theta_{A})
B = (R; theta_{A} + alpha)
E = (A + B) / 2
C = O + q Vector[O, A]
D = O + q Vector[O, B]
F = O + q_{mid} Vector[O, E]

blueQuadRight = NestedSpiralLines[{F, C, A, E}, t, iterations]
blueTriRight = NestedSpiralLines[{O, F, C}, t, iterations]
blueQuadLeft = Reflect[blueQuadRight, Line[O, E]]
blueTriLeft = Reflect[blueTriRight, Line[O, E]]
rotations = Sequence[Rotate[{blueQuadLeft, blueQuadRight, blueTriLeft, blueTriRight}, alpha k, O], k, 1, n - 1]
```

This keeps the construction aligned with the geometric structure:

- create the right-half motif from the actual base points,
- create the left-half motif using `Reflect[object, line]`,
- create all remaining sectors using `Sequence[Rotate[group, angle, center], ...]`.

This is usually clearer and shorter than generating full vertex lists for every sector and calling `Zip` over each sector separately. It also reduces the risk of using the wrong sector index or accidentally placing the highlighted reference motif on the neighboring octagon side.

GeoGebra uses the command name `Reflect`, not `Mirror`. If unsure about a GeoGebra command name or syntax, verify it in the official manual before writing XML or commands. The official Reflect command supports forms such as:

```geogebra
Reflect[ <Object>, <Line> ]
Reflect[ <Object>, <Point> ]
Reflect[ <Object>, <Circle> ]
```

## Direct XML Macro Authoring

GeoGebra `.ggb` files are ZIP archives. Custom Tool definitions live in `geogebra_macro.xml` as `<macro>` blocks. When it is more reliable than driving the UI, a `.ggb` can be generated or modified directly by writing the XML macro definition.

Useful macro fields from `lessons/CustomTools.ggb`:

```xml
<macro cmdName="NextGen" toolName="NextGen" toolHelp="NextGen(&lt;PointList&gt;,&lt;param&gt;)">
  <macroInput a0="v0" a1="t"/>
  <macroOutput a0="l1"/>
</macro>
```

Only use direct XML authoring when the generated file can be validated by opening it in GeoGebra or by inspecting the archive contents. Keep a small known-good example such as `lessons/CustomTools.ggb` available as a template.

## Opening 3D Files with the Correct GeoGebra Panes

When directly authoring a `.ggb` file that should open into a 3D construction, write a complete GUI perspective. Do not rely on GeoGebra to infer the visible panes from `<euclidianView3D>` alone.

Important view ids observed in GeoGebra Classic:

- `view id="512"` is the 3D Graphics View that should be visible for 3D constructions.
- `view id="2"` is the Algebra View.
- `view id="1"` is the 2D Graphics View.
- `view id="4097"` is not the 3D Graphics View in this context; making it visible can open a settings/properties pane instead of the drawing area.

Recommended 3D file setup:

```xml
<perspective id="tmp">
  <panes>
    <pane location="" divider="0.18" orientation="1"/>
    <pane location="1" divider="0.50" orientation="1"/>
  </panes>
  <views>
    <view id="2" visible="true" inframe="false" stylebar="false"
          location="3" size="240" tab="ALGEBRA"/>
    <view id="512" visible="true" inframe="false" stylebar="true"
          location="1,3" size="920"/>
    <view id="1" visible="false" inframe="false" stylebar="false"
          location="1,3" size="220"/>
    <view id="4097" visible="false" inframe="false" stylebar="true"
          location="1,1" size="300"/>
  </views>
  <toolbar show="true" position="1" help="true"/>
  <input show="true" cmd="true" top="algebra"/>
</perspective>
```

Also include both a minimal `<euclidianView>` and a full `<euclidianView3D>`, and set:

```xml
<kernel>
  <uses3D val="true"/>
  ...
</kernel>
```

For Custom Tools intended for 3D output, set the macro `viewId` to `512`, not `4097`.

After packaging, validation should include opening the file in GeoGebra when possible and checking that the Algebra View and 3D Graphics View appear immediately, without the user having to open panes manually. Static XML validation alone is not enough to catch wrong view ids.

## CircularArc for Great-Circle Style Routes

When drawing the shortest-looking route between two 3D points around a chosen center, prefer GeoGebra's native arc command:

```geogebra
CircularArc(O, P, Q)
```

Use this instead of a manually parameterized spherical linear interpolation curve when the intended construction is the circular arc from `P` to `Q` around center `O`. `P` and `Q` do not need to be pre-constrained to the same circle or sphere; GeoGebra is able to determine the corresponding circular arc from the center point and the two endpoints. This keeps the construction shorter, more readable, and usually more robust than expanding the arc into coordinate formulas.

For a globe-style construction, if `O` is the globe center and `P`, `Q` are surface points or route endpoints, the route can be:

```geogebra
greatCirclePath = CircularArc(O, P, Q)
```

## Longitude-Latitude Point Lists with Zip and GeoPoint

When drawing map outlines or any polyline whose source data is a sequence of longitude-latitude pairs, keep the source coordinates as a readable point list and transform them with `Zip`. Do not expand every coordinate into long repeated trigonometric formulas in the visible construction.

Recommended pattern:

```geogebra
coords = {(1, 2), (3, 4), ..., (1, 2)}
Polyline(Zip(GeoPoint(P, drawR), P, coords))
```

Here `coords` stores `(longitude, latitude)` pairs as ordinary 2D points. `GeoPoint` should be a Custom Tool that converts one longitude-latitude point and a radius into the corresponding 3D point on the sphere. This keeps geographic data readable, makes the conversion semantic, and avoids flooding the Algebra View with repeated `cos`/`sin` coordinate formulas.

Use this pattern for continent outlines, coastlines, route datasets, spherical grids derived from coordinate lists, or any repeated longitude-latitude conversion. Close an outline by repeating the first coordinate at the end of `coords`.

## Prefer Native GeoGebra Commands Before Expanding Formulas

Before manually deriving coordinate formulas or vector expressions for a geometric quantity, check whether GeoGebra already provides a native command for that construction or measurement. Native commands are usually shorter, clearer, more dynamic, and less error-prone than hand-expanded formulas.

If no native command expresses the needed construction clearly, prefer creating or reusing a Custom Tool before expanding a large formula directly in the construction. Custom Tools keep commands shorter, make the geometric meaning explicit, and avoid confusing users with long coordinate-level formulas in the Algebra View. Use direct formula expansion only after checking native commands and Custom Tools, or when the expression is genuinely small and self-explanatory.

For example, to measure the angle with apex `O` between points `P` and `Q`, use:

```geogebra
Angle(P, O, Q)
```

Prefer this over manually computing:

```geogebra
acos((x(P) x(Q) + y(P) y(Q) + z(P) z(Q)) / r^2)
```

Use the manual dot-product formula only when the native `Angle` command cannot express the intended construction, or when a specialized signed/oriented/numeric variant is explicitly needed and has been checked against GeoGebra's available commands and the Custom Tool option.

## Additional Command Patterns from CustomTools.ggb

The reference file also demonstrates several compact GeoGebra command techniques:

- Use `Length[v0]` to make point-list logic independent of the number of vertices.
- Use `Mod[k, n]` for cyclic indexing over polygon vertices.
- Use list indexing such as `v0(k)` inside `Sequence`.
- Use `Dilate[target, ratio, center]` for affine point movement along an edge or toward a target point.
- Use `Zip[Polygon[L], L, nested]` to convert a list of point lists into a list of polygons.
- Hide intermediate lists such as `nested` when only the final generated geometry should remain visible.
