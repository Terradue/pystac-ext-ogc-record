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

# Record fields

`OGCRecord` and `record.record_metadata` expose common discovery metadata under the record's `properties` member.

| Accessor | JSON property | Meaning |
| --- | --- | --- |
| `type` | `type` | Resource classification; separate from the top-level Feature type. |
| `title`, `description` | Same name | Human-readable resource metadata. |
| `created`, `updated` | Same name | Metadata timestamp strings. |
| `keywords` | `keywords` | Discovery terms. |
| `language`, `languages` | Same name | Metadata language dictionaries. |
| `resource_languages` | `resourceLanguages` | Resource language dictionaries. |
| `themes` | `themes` | Theme schemes and concepts. |
| `external_ids` | `externalIds` | External identifiers and optional schemes. |
| `formats` | `formats` | Resource formats. |
| `contacts` | `contacts` | Contact metadata. |
| `license`, `rights` | Same name | License and rights statements. |

Absent common metadata returns `None`. Assigning `None` removes a property. Accessors do not validate nested schema constraints.

Top-level members such as `time`, `conformsTo`, and `linkTemplates` have separate behavior documented in the [OGC Record reference](ogc-records.md).
