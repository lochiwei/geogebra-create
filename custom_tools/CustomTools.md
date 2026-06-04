# GeoGebra Custom Tools 登錄

本目錄保存 `geogebra-create` skill 可重用的 GeoGebra 自製工具。建立物件時，如果沒有已知 GeoGebra 內建指令可以直接套用，必須先搜尋本目錄與本文件，確認是否已有適用工具。

## `slerp.ggb`

來源：`CustomTools/slerp.ggb`

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
