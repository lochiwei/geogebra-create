# GeoGebra Custom Tools Manual

本文件保存 `geogebra-create` skill 可重用的 GeoGebra 自製工具完整說明。快速查找工具時先看 `custom/index.md`；需要使用範例、限制與內部邏輯時再看本文件。

## `tools/Slerp/Slerp.ggb`

來源：`custom/tools/Slerp/Slerp.ggb`

可載入工具檔：`custom/tools/Slerp/Slerp.ggt`

XML 展開定義：`custom/tools/Slerp/Slerp.xml`

SHA-256：

- `Slerp.ggb`：`42197af9b3d3c3e16a3c025b6e86174160a66d6015b450e9aa233e42e2ab558d`
- `Slerp.ggt`：`8cadaa1c3049b172aa32e6ca18235529225b0451b7f2a5024566c59a30936b48`

用途：提供以原點 `(0, 0, 0)` 為球心的球面線性插值、球面中點與測地線弧工具。這組工具適合建立同心球上的插值點、大圓弧中點與兩點間的大圓弧，特別是需要以參數 `t` 控制球面路徑上的動點時。

### `Slerp[<start point>, <end point>, <parameter>]`

指令格式：

```geogebra
Slerp[P, Q, t]
```

輸入：

- `P`：球面插值的起點。
- `Q`：球面插值的終點。
- `t`：插值參數；通常使用 `0 <= t <= 1`，其中 `0` 回到 `P`，`1` 回到 `Q`，`0.5` 回到球面中點。

輸出：

- 回傳從 `P` 到 `Q` 的大圓弧上，比例為 `t` 的 3D 點。

使用方式：

```geogebra
t = Slider[0, 1, 0.01]
R = Slerp[A, B, t]
```

前提與限制：

- 球心固定為原點 `(0, 0, 0)`。
- 輸入點應位於同一個以原點為球心的球面上。
- `P` 與 `Q` 不應重合或互為對跖點；若夾角為 `0` 或 `180` 度，公式中的 `sin(theta)` 會退化或測地線不唯一。
- `t` 可超出 `[0, 1]` 做延伸插值，但一般測地線段用途建議限制在 `[0, 1]`。

內部邏輯：

```geogebra
theta = Angle[P, (0, 0, 0), Q]
R = (sin((1 - t) theta) P + sin(t theta) Q) / sin(theta)
```

### `GeoMidpoint[<Point>, <Point>]`

指令格式：

```geogebra
GeoMidpoint[A, B]
```

輸入：

- `A`：球面上的起點。
- `B`：球面上的終點。

輸出：

- 回傳 `A` 與 `B` 之間大圓弧上的球面中點。

使用方式：

```geogebra
M = GeoMidpoint[A, B]
```

前提與限制：

- 球心固定為原點 `(0, 0, 0)`。
- 輸入點應位於同一個以原點為球心的球面上。
- 此工具內部呼叫 `Slerp[A, B, 0.5]`，因此 `GeoMidpoint` 與 `Slerp` 必須一起載入。
- 兩點不應重合或互為對跖點，原因同 `Slerp`。

內部邏輯：

```geogebra
M = Slerp[A, B, 0.5]
```

### `Geodesic[<Point>, <Point>]`

指令格式：

```geogebra
Geodesic[A, B]
```

輸入：

- `A`：球面上的起點。
- `B`：球面上的終點。

輸出：

- 回傳通過 `A`、`GeoMidpoint[A, B]` 與 `B` 的大圓弧物件，代表兩點之間的測地線弧。

使用方式：

```geogebra
arcAB = Geodesic[A, B]
```

前提與限制：

- 球心固定為原點 `(0, 0, 0)`。
- 輸入點應位於同一個以原點為球心的球面上。
- 此工具內部呼叫 `GeoMidpoint[A, B]`，因此 `Geodesic`、`GeoMidpoint` 與 `Slerp` 必須一起載入。
- 兩點不應重合或互為對跖點，原因同 `Slerp`。

內部邏輯：

```geogebra
M = GeoMidpoint[A, B]
c = CircumcircleArc[A, M, B]
```

## `tools/PolylineClosed/PolylineClosed.ggb`

來源：`custom/tools/PolylineClosed/PolylineClosed.ggb`

可載入工具檔：`custom/tools/PolylineClosed/PolylineClosed.ggt`

XML 展開定義：`custom/tools/PolylineClosed/PolylineClosed.xml`

SHA-256：`16b9d987fc2e3369034eaac58c668d8a44012794e3bb6f0010fbb3295e34b134`

用途：提供將「點列」直接畫成封閉折線的工具。GeoGebra 內建 `Polyline` 會建立開放折線，也就是第一個點和最後一個點不會自動相連；`PolylineClosed` 會自動把點列的第一點接到尾端，再建立折線，適合畫外框、路徑邊界、閉合導引線、地圖輪廓、用 `Sequence` / `Zip` 生成的規則點列。

### `PolylineClosed[<point list>]`

指令格式：

```geogebra
PolylineClosed[pts]
```

輸入：

- `pts`：點列，例如 `{A, B, C, D}` 或 `Sequence[(1; 60° k), k, 1, 6]`。

輸出：

- 回傳一個封閉的 `polyline` 物件。

使用方式：

```geogebra
pts = {A, B, C, D}
outline = PolylineClosed[pts]
```

也可以直接搭配動態生成的點列：

```geogebra
hexPts = Sequence[(1; 60° k), k, 1, 6]
hexOutline = PolylineClosed[hexPts]
```

內部邏輯：

```geogebra
closed = Polyline[Join[{pts, {pts(1)}}]]
```

相關指令用法：

- `Polyline[<List of Points>]`：GeoGebra 內建開放折線指令，會依照點列順序連接相鄰點，但不會自動閉合。
- `Join[<List of Lists>]`：把多個 list 接成一個 list；此工具使用 `Join[{pts, {pts(1)}}]` 將第一點加到點列尾端。
- `Sequence[<Expression>, <Variable>, <Start>, <End>]`：可用來生成規則點列，再交給 `PolylineClosed` 建立封閉折線。

前提與限制：

- `pts` 應是點物件組成的 list。
- 若要形成有面積的閉合輪廓，通常至少需要三個不同點。
- 此工具只建立折線邊界，不建立填色區域；若需要填滿區域，優先使用 `Polygon[pts]`。
- 輸入點列不需要手動重複第一點；工具會自動閉合。若輸入點列已經在尾端重複第一點，會產生一段零長度收尾段。
