# GeoGebra Custom Tools Index

本目錄保存 `geogebra-create` skill 可重用的 GeoGebra 自製工具。完整說明、範例與限制請見 `custom/manual.md`。

| 指令名稱 | 引用參數 | 功能簡述 |
| --- | --- | --- |
| `MidpointOnSphere` | `<Point>, <Point>` | 回傳兩個同心球面 3D 點之間的大圓弧球面中點，球心固定為原點 `(0, 0, 0)`。 |
| `ArcOnSphere` | `<Point>, <Point>` | 回傳通過兩個球面端點與其球面中點的大圓弧物件，需與 `MidpointOnSphere` 一起載入。 |
| `PolylineClosed` | `<point list>` | 將點列畫成封閉折線；工具會自動把第一點接到點列尾端後建立 `Polyline`。 |
