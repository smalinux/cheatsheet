
```python
# hex format, Interesting for me
>>> for f in bv.functions:
        print(f"{f.start:#x}  {f.name}")
                         #x
>>> for s in bv.strings:
        print(f"{s.start:#x}  {s.value!r}")
                                      !r

# Search by name
>>> [f.name for f in bv.functions if "check" in f.name]


# Move the cursor
here = 0x401176


# bv
# Surprise #4: magic variables exist only in the console. here, current_function and even bv are created by the console for you. Later, when you write a plugin (a script in a file), these names do not exist. The plugin receives bv as a parameter instead. We will see this in a later lesson.


# rename functions ~ easy
f.name = "new_name"


# Loop over the blocks
>>> for bb in sorted(f.basic_blocks, key=lambda b: b.start):
...     print(f"{bb.start:#x} - {bb.end:#x}   {bb.instruction_count} instructions")
...
0x40118e - 0x4011b8   12 instructions
0x4011b8 - 0x4011bf   2 instructions
0x4011bf - 0x4011c4   1 instructions
0x4011c4 - 0x4011c6   2 instructions
```


Online Plugin manager:
[https://extensions.binary.ninja/](https://extensions.binary.ninja/)
[https://zerotistic.github.io/binja-resources/](https://zerotistic.github.io/binja-resources/)


## Dealing with +100 nested functions
- Tanto binary ninja?
	- Great example to steal from: https://github.com/Vector35/tanto/blob/master/helpers.py
- Reflog Plugin?


## Write your own Plugin
[examples/hellosidebar.py](https://github.com/Vector35/binaryninja-api/blob/dev/python/examples/hellosidebar.py)

## APIs involved next to binja APIs itself

The stack has three layers:

**1. Binary Ninja API (analysis side)**
- Python API reference: https://api.binary.ninja/
- Plugin dev guide (loading, `plugin.json`, packaging): https://docs.binary.ninja/dev/plugins.html
- Official Python examples: https://github.com/Vector35/binaryninja-api/tree/dev/python/examples

**2. `binaryninjaui` (Binja's UI layer on top of Qt)**
This is where `SidebarWidget`, `UIContext`, `View` and `UIActionHandler` live. The Python bindings are barely documented, so read the C++ headers. The Python names match them 1:1.
- UI headers (start with `sidebar.h`, `uicontext.h`, `action.h`): https://github.com/Vector35/binaryninja-api/tree/dev/ui
- C++ API docs, which include the UI classes: https://api.binary.ninja/cpp/
,
**3. Qt 6 via PySide6 (the widgets themselves)**
Binary Ninja ships its own PySide6, so you don't need to `pip install` it. Learn these in this order:
- Qt for Python (PySide6) docs: https://doc.qt.io/qtforpython-6/
- Signals & slots (the core concept): https://doc.qt.io/qt-6/signalsandslots.html
- Layouts: https://doc.qt.io/qt-6/layout.html
- Qt Widgets (`QWidget`, `QLabel`, `QTableWidget`, `QTreeView`…): https://doc.qt.io/qt-6/qtwidgets-index.html
- Model/View (needed for tables and trees like Tanto's): https://doc.qt.io/qt-6/model-view-programming.html
- Full Qt 6 C++ reference (more complete than the PySide pages, and the names are the same): https://doc.qt.io/qt-6/

- unicorn              Unicorn is a lightweight, multi-platform, multi-architecture CPU emulator framework based on QEMU.
- capstone


## MCP

```bash
# Claude
! claude mcp add --transport http binja http://127.0.0.1:24642/mcp
```

