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

# Create a workflow record

[Install the package](../how-to/install.md), then run these blocks in order. The example uses application-specific workflow metadata and requires no catalog service.

## Create and connect the record

```python
from datetime import datetime, timezone

import pystac
from pystac.extensions.ogc_record import OGCRecord

workflow = OGCRecord(id="crop-mapping-workflow")
workflow.type = "workflow"
workflow.title = "Crop mapping workflow"
workflow.description = "A reproducible workflow for mapping cropland."
workflow.license = "proprietary"
workflow.keywords = ["cropland", "classification"]
workflow.formats = [{"name": "openEO process graph"}]
workflow.created = workflow.updated = datetime.now(timezone.utc)
workflow.add_link(pystac.Link(
    rel="related", target="https://example.org/projects/crop-mapping",
    media_type="application/json", title="Project: Crop mapping",
))
```

The adapter supplies the Feature envelope and null geometry. Common properties use named accessors; `pystac.Link` handles link serialization. The `created` and `updated` metadata timestamps do not supply a STAC Item `datetime`.

## Round-trip and copy

```python
document = workflow.to_record_dict()
assert document["type"] == "Feature"
assert document["geometry"] is None
assert document["properties"]["type"] == "workflow"
assert "stac_version" not in document

restored = OGCRecord.from_dict(document)
assert restored.title == workflow.title
assert restored.to_record_dict() == document

working_copy = restored.clone()
working_copy.keywords = None
assert "keywords" not in working_copy.properties
assert restored.keywords == ["cropland", "classification"]
```

## Write a file

This block writes `workflow_record.json` in the current directory, replacing it if it already exists:

```python
import json
from pathlib import Path

output_path = Path("workflow_record.json")
output_path.write_text(
    json.dumps(workflow.to_record_dict(), indent=2) + "\n",
    encoding="utf-8",
)
loaded = OGCRecord.from_dict(json.loads(output_path.read_text(encoding="utf-8")))
assert loaded.title == workflow.title
```

Creating and serializing the object does not establish schema conformance. `workflow.validate()` requires an explicit OGC validator. If the resource has spatial or temporal coverage, supply `geometry`, `bbox`, or OGC `time` explicitly. Continue with the [OGC Record reference](../reference/ogc-records.md) and [workflow/experiment guide](../how-to/ogc-records.md).
