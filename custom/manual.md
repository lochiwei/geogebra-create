# GeoGebra Custom Tools Manual

本文件保存 `geogebra-create` skill 可重用的 GeoGebra 自製工具完整說明。快速查找工具時先看 `custom/index.md`；需要使用範例、限制與內部邏輯時再看本文件。

## `tools/Slerp/Slerp.ggb`

來源：`custom/tools/Slerp/Slerp.ggb`

可載入工具檔：`custom/tools/Slerp/Slerp.ggt`

XML 展開定義：`custom/tools/Slerp/Slerp.xml`

用途：提供以原點 `(0, 0, 0)` 為球心的球面插值／大圓弧相關工具。這組工具適合建立單位球或同心球上的球面中點與大圓弧，特別是 GeoGebra 內建 `CircumcircleArc` / `CircularArc` 在 3D 球面情境不易直接得到預期結果時。

### `MidpointOnSphere[<Point>, <Point>]`

指令格式：

```geogebra
MidpointOnSphere[A, B]
```

輸入：

- `A`：球面上的 3D 點。
- `B`：球面上的 3D 點。

輸出：

- 回傳 `A` 與 `B` 之間大圓弧的球面中點。

使用方式：

```geogebra
M = MidpointOnSphere[P, Q]
```

前提與限制：

- 球心固定為原點 `(0, 0, 0)`。
- 輸入點應位於同一個以原點為球心的球面上。
- 兩點不應互為對跖點；若夾角為 180 度，球面中點不唯一，公式中的 `cos(theta / 2)` 會退化。

內部邏輯：

```geogebra
theta = Angle[A, (0, 0, 0), B]
M = (A + B) / (2 * cos(theta / 2))
```

### `ArcOnSphere[<Point>, <Point>]`

指令格式：

```geogebra
ArcOnSphere[A, B]
```

輸入：

- `A`：球面上的 3D 點。
- `B`：球面上的 3D 點。

輸出：

- 回傳通過 `A`、球面中點與 `B` 的大圓弧物件。

使用方式：

```geogebra
arcAB = ArcOnSphere[A, B]
```

前提與限制：

- 球心固定為原點 `(0, 0, 0)`。
- 輸入點應位於同一個以原點為球心的球面上。
- 此工具內部呼叫 `MidpointOnSphere[A, B]`，因此 `ArcOnSphere` 與 `MidpointOnSphere` 必須一起載入。
- 兩點不應互為對跖點，原因同 `MidpointOnSphere`。

內部邏輯：

```geogebra
arc = CircumcircleArc[A, MidpointOnSphere[A, B], B]
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
