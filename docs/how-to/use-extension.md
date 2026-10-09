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

# Work with record metadata

Use named accessors on `OGCRecord` to edit common discovery metadata:

```python
import pystac
from pystac.extensions.ogc_record import OGCRecord

record = OGCRecord(id="ocean-temperature")
record.title = "Ocean temperature"
record.keywords = ["ocean", "temperature"]
record.language = {"code": "en"}
record.keywords = None
assert "keywords" not in record.properties

record.add_link(pystac.Link(
    rel="related", target="https://example.org/datasets/ocean-temperature",
))
assert record.record_metadata.title == record.title
```

Getters return `None` for absent metadata, and assigning `None` removes the property. `record.record_metadata` is a live view of the same properties. Its `to_dict()` returns a deep copy.

Use `pystac.Link` to connect related resources. Metadata assignments do not create links automatically. Nested metadata dictionaries are not schema-validated by setters; see the [record reference](../reference/ogc-records.md) for validation and serialization behavior.
