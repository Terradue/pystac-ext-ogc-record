<!--
Copyright 2026 Terradue

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

# Install and build

## Install the library

The package declares Python 3.10 or later and PySTAC `>=1,<2`. The test matrix covers Python 3.10–3.14.

```bash
python -m pip install pystac-ext-ogc-record
python -c "from pystac.extensions.ogc_record import OGCRecord; print(OGCRecord(id='example').to_dict())"
```

## Install from a checkout

```bash
git clone https://github.com/Terradue/pystac-ext-ogc-record.git
cd pystac-ext-ogc-record
python -m pip install -e .
```

If an existing editable installation predates a packaging change, repeat the editable install in that environment.

## Preview the documentation

Install the package and documentation tools in the same environment. Use a regular install for documentation: static API discovery does not reliably find editable modules inside the shared PySTAC namespace. Reinstall after changing source code:

```bash
python -m pip install . "mkdocs<2" mkdocs-material "mkdocstrings[python]"
mkdocs serve
```

Build with warnings treated as errors:

```bash
mkdocs build --strict
```

## Run repository checks

With Hatch installed, run these commands from the checkout:

```bash
hatch run dev:check
hatch run dev:typecheck
hatch run dev:security
hatch run test:test
```
