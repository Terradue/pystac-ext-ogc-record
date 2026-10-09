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

# Create workflow and experiment records

Use `OGCRecord` for workflow and experiment discovery metadata, and PySTAC links to connect related resources. The relationship names in this example are application choices.

```python
import pystac
from pystac.extensions.ogc_record import OGCRecord

workflow = OGCRecord(
    id="ocean-workflow",
    properties={
        "type": "workflow",
        "title": "Ocean temperature workflow",
        "description": "Processing steps for sea surface temperature",
    },
)
workflow.add_link(pystac.Link(
    rel="related", target="https://example.org/projects/ocean-science.json",
))
workflow.add_link(pystac.Link(
    rel="child", target="https://example.org/experiments/experiment-42.json",
))

experiment = OGCRecord(
    id="experiment-42",
    properties={
        "type": "experiment",
        "title": "Ocean workflow execution 42",
        "description": "An execution producing the ocean-temperature product",
    },
)
experiment.add_link(pystac.Link(
    rel="related", target="https://example.org/workflows/ocean-workflow.json",
))
experiment.add_link(pystac.Link(
    rel="child", target="https://example.org/products/ocean-temperature.json",
))
experiment.add_link(pystac.Link(
    rel="environment", target="https://example.org/experiments/environment.yml",
))
experiment.add_link(pystac.Link(
    rel="input", target="https://example.org/experiments/parameters.json",
))

experiment.keywords = ["ocean", "temperature"]
experiment.language = {"code": "en"}
document = experiment.to_record_dict()
assert document["type"] == "Feature"
assert document["properties"]["type"] == "experiment"
assert "stac_version" not in document
restored = OGCRecord.from_dict(document)
assert restored.title == experiment.title
assert restored.get_single_link("related").target == "https://example.org/workflows/ocean-workflow.json"
```

The `workflow` and `experiment` resource type strings describe the application. Constructing these records does not establish full OGC conformance.

## Read, write, and copy

Use `OGCRecord.from_dict(document)` on decoded JSON. Use `json.dumps(record.to_dict())` to serialize it. Pass `href=` to `from_dict()` to set the self link, replacing any incoming self link. Metadata is copied even when `preserve_dict=False`; `migrate` is accepted for PySTAC compatibility but has no effect.

`record.clone()` copies metadata and clones links and assets. `record.record_metadata` is a live view of `record.properties`; its `to_dict()` returns a deep copy. Assigning `None` to a common metadata accessor removes that property.

## Export a STAC Item

```python
from datetime import datetime, timezone

record = OGCRecord(
    id="observation",
    datetime=datetime(2026, 1, 1, tzinfo=timezone.utc),
    properties={"type": "dataset", "title": "Observation"},
)
item = record.to_stac_item()
assert "stac_version" in item.to_dict()
```

STAC export requires `datetime` or both `start_datetime` and `end_datetime`. An OGC `time` member alone does not satisfy this requirement. Export does not validate the resulting Item. See [validation boundaries](../explanation/architecture.md#validation-boundaries).
