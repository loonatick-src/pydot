<!--
SPDX-FileCopyrightText: 2024 pydot contributors

SPDX-License-Identifier: MIT
-->

![CI](https://github.com/pydot/pydot/actions/workflows/CI.yml/badge.svg)
[![Coverage](https://raw.githubusercontent.com/pydot/pydot/python-coverage-comment-action-data/badge.svg)](https://htmlpreview.github.io/?https://github.com/pydot/pydot/blob/python-coverage-comment-action-data/htmlcov/index.html)
[![PyPI](https://img.shields.io/pypi/v/pydot.svg)](https://pypi.org/project/pydot/)
[![Code style: ruff](https://img.shields.io/badge/code%20style-ruff-purple.svg)](https://github.com/astral-sh/ruff)

# Pydot

`pydot` is a Python interface to Graphviz and its DOT language. You can use `pydot` to create, read, edit, and visualize graphs.

- It's made in pure Python, with only one dependency – pyparsing – other than Graphviz itself.
- It's compatible with `networkx`, which can convert its graphs to `pydot`.

To see what Graphviz is capable of, check the [Graphviz Gallery](https://graphviz.org/gallery/)!

## Dependencies

- [`pyparsing`][pyparsing]: used only for *loading* DOT files, installed automatically during `pydot` installation.
- GraphViz: used to render graphs in a variety of formats, including PNG, SVG, PDF, and more.
  Should be installed separately, using your system's [package manager][pkg], something similar (e.g., [MacPorts][mac]), or from [its source][src].

## Installation

- Latest release, from [PyPI][pypi]:

  ```bash
  pip install pydot
  ```

- Current development code, from this repository:

  ```bash
  pip install git+https://github.com/pydot/pydot.git
  ```

- Development installation, to modify the code or contribute changes:

  ```bash
  # Clone the repository
  git clone https://github.com/pydot/pydot
  cd pydot

  # (Optional: create a virtual environment)
  python3 -m venv _venv
  . ./_venv/bin/activate

  # Make an editable install of pydot from the source tree
  pip install -e .
  ```

## Quickstart

### 1. Input

No matter what you want to do with `pydot`, you'll need some input to start with. Here are the common ways to get some data to work with.

#### Import a graph from an existing DOT file

Let's say you already have a file `example.dot` (based on an [example from Wikipedia][wiki_example]):

```dot
graph my_graph {
    bgcolor="yellow";
    a [label="Foo"];
    b [shape=circle];
    a -- b -- c [color=blue];
}
```

You can read the graph from the file in this way:

```python
import pydot

graphs = pydot.graph_from_dot_file("example.dot")
graph = graphs[0]
```

#### Parse a graph from an existing DOT string

Use this method if you already have a string describing a DOT graph:

```python
import pydot

dot_string = """graph my_graph {
    bgcolor="yellow";
    a [label="Foo"];
    b [shape=circle];
    a -- b -- c [color=blue];
}"""

graphs = pydot.graph_from_dot_data(dot_string)
graph = graphs[0]
```

#### Create a graph from scratch using pydot objects

This is where the cool stuff starts. Use this method if you want to build new graphs with Python code.

```python
import pydot

graph = pydot.Dot("my_graph", graph_type="graph", bgcolor="yellow")

# Add nodes
my_node = pydot.Node("a", label="Foo")
graph.add_node(my_node)
# Or, without using an intermediate variable:
graph.add_node(pydot.Node("b", shape="circle"))

# Add edges
my_edge = pydot.Edge("a", "b", color="blue")
graph.add_edge(my_edge)
# Or, without using an intermediate variable:
graph.add_edge(pydot.Edge("b", "c", color="blue"))
```

You can use these basic building blocks in your Python program
to dynamically generate a graph. For example, start with a
basic `pydot.Dot` graph object, then loop through your data
as you add nodes and edges. Use values from your data as labels to
determine shapes, edges and so on. This allows you to easily create
visualizations of thousands of related objects.

#### Convert a NetworkX graph to a pydot graph

NetworkX has conversion methods for pydot graphs:

```python
import networkx
import pydot

# See NetworkX documentation on how to build a NetworkX graph.
graph = networkx.drawing.nx_pydot.to_pydot(my_networkx_graph)
```

### 2. Edit

You can now further manipulate your graph using pydot methods:

Add more nodes and edges:

```python
graph.add_edge(pydot.Edge("b", "d", style="dotted"))
```

Edit attributes of graphs, nodes and edges:

```python
graph.set_bgcolor("lightyellow")
graph.get_node("b")[0].set_shape("box")
```

### 3. Output

Here are three different output options:

#### Generate an image

If you just want to save the image to a file, use one of the `write_*` methods:
```python
graph.write_png("output.png")
```

If you need to further process the image output, the `create_*` methods will get you a Python `bytes` object:
```python
output_graphviz_svg = graph.create_svg()
```

#### Retrieve the DOT string

There are two different DOT strings you can retrieve:

- The "raw" pydot DOT: This is generated the fastest and will
  usually still look quite similar to the DOT you put in. It is
  generated by pydot itself, without calling Graphviz.

  ```python
  # As a string:
  output_raw_dot = graph.to_string()
  # Or, save it as a DOT-file:
  graph.write_raw("output_raw.dot")
  ```

- The Graphviz DOT: You can use it to check how Graphviz lays out
  the graph before it produces an image. It is generated by
  Graphviz.

  ```python
  # As a bytes literal:
  output_graphviz_dot = graph.create_dot()
  # Or, save it as a DOT-file:
  graph.write_dot("output_graphviz.dot")
  ```

#### Convert to a NetworkX graph

NetworkX has a conversion method for pydot graphs:

```python
my_networkx_graph = networkx.drawing.nx_pydot.from_pydot(graph)
```

### More help

For more help, see the docstrings of the various pydot objects and
methods. For example, `help(pydot)`, `help(pydot.Graph)` and
`help(pydot.Dot.write)`.

More [documentation contributions welcome][contrib].

## Troubleshooting

### Enable logging

`pydot` uses Python's standard `logging` module. To see the logs,
assuming logging has not been configured already:

    >>> import logging
    >>> logging.basicConfig(level=logging.DEBUG)
    >>> import pydot
    DEBUG:pydot:pydot initializing
    DEBUG:pydot:pydot <version>
    DEBUG:pydot.core:pydot core module initializing
    DEBUG:pydot.dot_parser:pydot dot_parser module initializing

**Warning**: When `DEBUG` level logging is enabled, `pydot` may log the
data that it processes, such as graph contents or DOT strings. This can
cause the log to become very large or contain sensitive information.

### Advanced logging configuration

- Check out the [Python logging documentation][log] and the
  [`logging_tree`][log_tree] visualizer.
- `pydot` does not add any handlers to its loggers, nor does it setup
  or modify your root logger. The `pydot` loggers are created with the
  default level `NOTSET`.
- `pydot` registers the following loggers:
  - `pydot`: Parent logger. Emits a few messages during startup.
  - `pydot.core`: Messages related to pydot objects, Graphviz execution
                  and anything else not covered by the other loggers.
  - `pydot.dot_parser`: Messages related to the parsing of DOT strings.


## License

Distributed under the [MIT license][MIT].

The module [`pydot._vendor.tempfile`][tempfile]
is based on the Python 3.12 standard library module
[`tempfile.py`][tempfile-src],
Copyright © 2001-2023 Python Software Foundation. All rights reserved.
Licensed under the terms of the [Python-2.0][Python-2.0] license.


## Contacts

Current maintainer(s): 
- Łukasz Łapiński <lukaszlapinski7 (at) gmail (dot) com>

Past maintainers:
- Sebastian Kalinowski <sebastian (at) kalinowski (dot) eu> (GitHub: @prmtl)
- Peter Nowee <peter (at) peternowee (dot) com> (GitHub: @peternowee)

Original author: Ero Carrera <ero (dot) carrera (at) gmail (dot) com>


[wiki_dot]: https://en.wikipedia.org/wiki/DOT_%28graph_description_language%29
[networkx]: https://github.com/networkx/networkx
[pydot_gh]: https://github.com/pydot/pydot
[wiki_example]: https://en.wikipedia.org/w/index.php?title=DOT_(graph_description_language)&oldid=1003001464#Attributes
[contrib]: https://github.com/pydot/pydot/issues/130
[pypi]: https://pypi.org/project/pydot/
[pyparsing]: https://github.com/pyparsing/pyparsing
[pkg]: https://en.wikipedia.org/wiki/Package_manager
[mac]: https://www.macports.org
[src]: https://gitlab.com/graphviz/graphviz
[log]: https://docs.python.org/3/library/logging.html
[log_tree]: https://pypi.org/project/logging_tree/
[MIT]: https://github.com/pydot/pydot/blob/main/LICENSES/MIT.txt
[Python-2.0]: https://github.com/pydot/pydot/blob/main/LICENSES/Python-2.0.txt
[tempfile]: https://github.com/pydot/pydot/blob/main/src/pydot/_vendor/tempfile.py
[tempfile-src]: https://github.com/python/cpython/blob/8edfa0b0b4ae4235bb3262d952c23e7581516d4f/Lib/tempfile.py
