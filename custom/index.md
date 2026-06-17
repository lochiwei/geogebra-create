# GeoGebra Custom Tools Index

本目錄保存 `geogebra-create` skill 可重用的 GeoGebra 自製工具。完整說明、範例與限制請見 `custom/manual.md`。

| 指令名稱 | 引用參數 | 功能簡述 |
| --- | --- | --- |
| `Slerp` | `<start point>, <end point>, <parameter>` | 以原點 `(0, 0, 0)` 為球心做球面線性插值，回傳從起點到終點在比例 `t` 的 3D 點。 |
| `GeoMidpoint` | `<Point>, <Point>` | 呼叫 `Slerp[A, B, 0.5]`，回傳兩點之間大圓弧上的球面中點。 |
| `Geodesic` | `<Point>, <Point>` | 透過 `GeoMidpoint` 建立通過兩端點與球面中點的大圓弧物件，代表兩點間的測地線弧。 |
| `PolylineClosed` | `<point list>` | 將點列畫成封閉折線；工具會自動把第一點接到點列尾端後建立 `Polyline`。 |
