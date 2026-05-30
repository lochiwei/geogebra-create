# geogebra-create

`geogebra-create` is a Codex skill for creating GeoGebra constructions, `.ggb` files, and geometry diagrams with GeoGebra Commands, tools, scripting, custom tools, and the GeoGebra Classic app.

This skill is written primarily in Traditional Chinese and is intended for workflows where the user asks Codex to create or inspect GeoGebra constructions.

## Install

Clone this repository into your Codex skills directory:

```bash
git clone https://github.com/lochiwei/geogebra-create.git ~/.codex/skills/geogebra-create
```

Restart Codex after installation if the skill is not detected immediately.

## Usage

Invoke the skill with:

```text
/geogebra-create [task description]
```

Example:

```text
/geogebra-create 製作一個展示正五邊形遞迴縮放的 GeoGebra 檔案
```

## Contents

- `SKILL.md`: main Codex skill instructions.
- `agents/openai.yaml`: display metadata for the skill.
- `references/learned-techniques.md`: accumulated GeoGebra construction notes.
- `lessons/CustomTools.ggb`: a compact reference file demonstrating custom tools such as `NextGen` and `NestedSpiral`.

## Notes

The included `.ggb` file is a GeoGebra archive and should be opened with GeoGebra Classic or compatible GeoGebra tooling.

