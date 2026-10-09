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

# Create your first record

[Install the package](../how-to/install.md) before running this complete example. A record describes a resource using discovery metadata.

```python
import pystac
from pystac.extensions.ogc_record import OGCRecord

record = OGCRecord(id="ocean-temperature")
record.type = "dataset"
record.title = "Ocean temperature"
record.description = "Sea surface temperature observations."
record.keywords = ["ocean", "temperature"]
record.language = {"code": "en"}
record.license = "CC-BY-4.0"
record.add_link(pystac.Link(
    rel="related", target="https://example.org/datasets/ocean-temperature",
))

document = record.to_record_dict()
assert document["type"] == "Feature"
assert document["geometry"] is None
assert document["properties"]["title"] == "Ocean temperature"
assert "stac_version" not in document

restored = OGCRecord.from_dict(document)
assert restored.keywords == ["ocean", "temperature"]
assert restored.to_record_dict() == document
```

Common metadata lives in `properties`. The top-level `type` is always `Feature`; `record.type` describes the resource. Creating or serializing a record does not validate it against a schema. See [validation boundaries](../explanation/architecture.md#validation-boundaries).
