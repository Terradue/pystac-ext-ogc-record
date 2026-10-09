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

# OGC Record reference

[OGC API — Records Part 1: Core 1.0](https://docs.ogc.org/is/20-004r1/20-004r1.html) defines discovery metadata and catalog access. A record describes a resource; the JSON encoding is a GeoJSON Feature. `OGCRecord` implements an in-memory adapter for that document shape, reusing PySTAC facilities. It does not implement service endpoints or search.

## Document structure

The adapter's schema reference is [recordGeoJSON.yaml](https://schemas.opengis.net/ogcapi/records/part1/1.0/openapi/schemas/recordGeoJSON.yaml).

| Member | Adapter behavior |
| --- | --- |
| `id` | String or integer, excluding booleans. `record_id` preserves an input integer; PySTAC `id` is a string. Changing `id` to a different value changes the serialized identifier. |
| `type` | Serialized as `Feature`; `record.type` instead accesses the resource type under `properties`. |
| `geometry`, `bbox` | Copied on construction; geometry may be `None`. No geometric validation is performed. |
| `properties` | Metadata dictionary. Incoming `null` remains `null` when empty; adding metadata produces an object. |
| `links` | PySTAC links; `include_self_link=False` omits self links on serialization. |
| `time` | Optional top-level OGC temporal object, separate from STAC datetime properties. |
| `conformsTo` | `conforms_to` exposes a live list of conformance URIs, separate from STAC extension declarations. |
| `linkTemplates` | `link_templates` exposes a live list of template dictionaries. |
| Foreign members | Preserved in `extra_fields`; managed field collisions in constructor extras raise `ValueError`. Assets, collection ID, and STAC extension declarations are serialized when present. |

`to_dict()` and `to_record_dict()` return copied document metadata. New records do not automatically gain `stac_version`; a supplied foreign member is preserved. Merely creating or reading a record does not establish OGC conformance.

Reading absent `conforms_to` or `link_templates` creates an empty list in `extra_fields`. These getters reject a value of the wrong container/element type with `TypeError`. They do not validate URI syntax or template contents. `time=None` writes a JSON null; remove the `time` key from `extra_fields` to omit it entirely.

## Common metadata accessors

Both the record and `record.record_metadata` expose these live properties:

| Python name | JSON property | Value |
| --- | --- | --- |
| `created`, `updated` | Same name | Timestamp string, with no automatic datetime conversion. |
| `type`, `title`, `description` | Same name | Resource classification and descriptive strings. |
| `keywords` | `keywords` | List of strings. |
| `themes` | `themes` | Theme dictionaries with `scheme` and `concepts`. |
| `language`, `languages` | Same name | Language dictionary, or list of dictionaries. |
| `resource_languages` | `resourceLanguages` | Resource language dictionaries. |
| `external_ids` | `externalIds` | External identifier dictionaries. |
| `formats` | `formats` | Format dictionaries. |
| `contacts` | `contacts` | Contact dictionaries. |
| `license`, `rights` | Same name | License and rights strings. |

Absent accessors return `None`; setting `None` deletes a property. Nested dictionaries remain open and are not schema-validated by the accessors. The existing nested typing helpers are static hints, not runtime validators or generated schema models.

## Compatibility and validation

`matches_object_type()` recognizes a Feature with an identifier, geometry key, and object-or-null properties. It is a structural check and also matches some STAC Items. Use `OGCRecord.from_dict()` explicitly.

`validate()` requires a supplied validator with `validate(document)`, configured for the OGC OpenAPI 3.0 schemas and references. The adapter delegates validation and propagates errors; it returns its schema URI after success. It does not supply a validator or verify service-level conformance. A generic JSON Schema validator must account for OpenAPI schema semantics, including `nullable`.

`to_stac_item()` requires an explicit STAC temporal extent and returns a separate Item. Links and assets are cloned. Validate that Item separately for STAC compliance. Some inherited PySTAC methods assume STAC temporal metadata; test the methods your application uses on timeless records.

See the [workflow/experiment guide](../how-to/ogc-records.md) for creation and serialization examples and the [OGC Records overview](https://ogcapi.ogc.org/records/) for the wider standard.
