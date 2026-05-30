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

## Additional Command Patterns from CustomTools.ggb

The reference file also demonstrates several compact GeoGebra command techniques:

- Use `Length[v0]` to make point-list logic independent of the number of vertices.
- Use `Mod[k, n]` for cyclic indexing over polygon vertices.
- Use list indexing such as `v0(k)` inside `Sequence`.
- Use `Dilate[target, ratio, center]` for affine point movement along an edge or toward a target point.
- Use `Zip[Polygon[L], L, nested]` to convert a list of point lists into a list of polygons.
- Hide intermediate lists such as `nested` when only the final generated geometry should remain visible.
