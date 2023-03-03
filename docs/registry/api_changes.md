# API Changes from Pharos

The primary API changes from Pharos to Registry include:

* Change in endpoint prefix from **/member-api/v2/** to **/member-api/v3/**
* Use of numeric IDs in URLs instead of object and file identifiers.
* Changes to some filter and sort parameters in endpoints that return lists of records. Registry includes more filter and sort options than Pharos.
* Minor changes to JSON structure of responses. Registry generally returns richer data that is easier to understand.

## Using IDs Instead of Identifiers

The change from identifiers to IDs deserves special mention. In the Pharos REST API, you would request file information with a URL like this:

`GET /member-api/v2/files/test.edu/my-object/data/my-file.pdf`

The corresponding request in the Registry API looks like this:

`GET /member-api/v3/files/21145`

The reason for this change is that file identifiers contain slashes, just like URL path identifiers. They also often contain characters that are illegal in URLs and require special encoding. In addition, Pharos URLs could contain query strings after the embedded file identifer.

In practice, URLs with long, messy file names followed by query strings became nearly impossible to parse. The [pattern](https://github.com/APTrust/pharos/blob/master/config/routes.rb#L53-L54){target=_blank} for extracting embedded file identifiers from URLs became unmaintainable, and still cannot match a number of identifiers in our system.

This change creates a problem. Object and file identifiers are semantic and knowable to depositors. Numeric IDs are opaque and unknowable. So how can you can map the known to the unknown?

Easy. These requests will return object and file records that include IDs:

`GET /member-api/v3/objects?identifier={object-identifier}`

`GET /member-api/v3/files?identifier={file-identifier}`

## List Structure

For endpoints returning lists of records (object, files, events, etc.), Pharos returned this format:

```
{
   count: integer,
   next: string (url),
   previous: string (url),
   results: array of objects
}
```

Registry returns this format. The only difference is that `results` is now `items`:

```
{
   count: integer,
   next: string (url),
   previous: string (url),
   items: array of objects
}
```

Changes in item structure are described below.

## Query Parameter Changes

Endpoints returning lists support more sorting and filtering parameters than their Pharos counterparts. While the exact parameters for each endpoint are listed in the [Registry Swagger docs](https://aptrust.github.io/registry/){_target=blank}, you should note the following patterns.

### Sorting

Sorting parameter names use the pattern {field_name}__{direction}, with two underscores before direction. If no direction is specified, it defaults to `asc`.

Examples:

* `intellectual_object_identifier` - Sort by intellectual object identifier, ascending.
* `intellectual_object_identifier__asc` - Sort by intellectual object identifier, ascending.
* `intellectual_object_identifier__desc` - Sort by intellectual object identifier, descending.

### Filtering

Filtering parameter names generally match JSON field names. For example, to get a list of files or objects stored in Standard storage, add the following to your query string:

`storage_option=Standard`

For datetime and numeric fields, append `__gteq` or `__lteq` to the field name to find items whose field value is "greater than or equal to" (gteq) or "less than or equal to" (lteq) the specified value. Note the **double** unscore before each suffix.

Examples:

* `created_at__gteq=2022-08-01&created_at__lteq=2022-08-31` returns records created between August 1, 2022 and August 31, 2022.
* `size__gteq=1000` returns records where size is at least 1000 bytes.
* `size__lteq=1000` returns records where size is less than or equal to 1000 bytes.
* `size__gteq=1000&size__lteq=2000` returns records where size is between 1000 and 2000 bytes.
* `size=1000` return records where size is exactly 1000 bytes.

## New Endpoints

API endpoints for the following types are new. Please follow the links for details.

* [Alerts](https://aptrust.github.io/registry/#/Alerts){target=_blank}
* [Checksums](https://aptrust.github.io/registry/#/Checksums){target=_blank}
* [Deletion Requests](https://aptrust.github.io/registry/#/Deletion%20Requests){target=_blank}

## Generic Files

### Changed

The following endpoints have changed as noted.

* `/member-api/v2/files/{institution_identifier}` -> `/member-api/v3/files`
* `/member-api/v2/files/{intellectual_object_identifier}` -> `/member-api/v3/files?intellectual_object_id={object_id}`

### Deleted

The following endpoint is no longer implemented:

* `/member-api/v2/files/restore/{generic_file_id}`

### Added

Registry adds the following Generic File endpoint:

`/member-api/v3/files/show/{id}`

This returns a Generic File object with nested Premis Events, Checksums and Storage Records.

### JSON Changes

The GenericFileView object returned by calls to `/member-api/v3/files` is a superset of the File object returned by the Pharos API. In addition to the old Pharos fields, GenericFileView contains the following:

* `size` (integer) - The size of the file, in bytes.
* `object_identifier` (string) - The identifier of the Intellectual Object to which this file belongs.
* `access` (string) - The file's access setting.
* `institution_id` (integer) - The ID of the institution that owns this file.
* `institution_name` (string) - The name of the institution that owns this file.
* `institution_identifier` (string) - The identifier (domain name) of the institution that owns this file.
* `storage_option` (string) - Describes where this file is stored.
* `uuid` (string) - The object's preservation identifier.
* `md5` (string) - The most recently calculated md5 checksum for this file.
* `sha1` (string) - The most recently calculated sha1 checksum for this file.
* `sha256` (string) - The most recently calculated sha256 checksum for this file.
* `sha512` (string) - The most recently calculated sha512 checksum for this file.
* `storage_records` (Array{Storage Record}) - A list of storage records indicating where this file is saved in preservation storage.



## Intellectual Objects

### Changed

The following endpoints have changed as noted.

* `/member-api/v2/objects` -> `/member-api/v3/objects`
* `/member-api/v2/objects/{institution_identifier}` -> `/member-api/v3/objects`

Note that the member API allows you to retrieve information about object belonging to your own institution only.

### Deleted

The following endpoint is no longer implemented:

* `/member-api/v2/objects/restore/{intellectual_object_id}`

### Added

Registry adds the following Intellectual Object endpoint:

`/member-api/v3/objects/show/{id}`

This returns an Intellectual Object summary. The summary does not include files or events because many objects have thousands of files and tens or hundreds of thousands of events. Use the file and event endpoints to retrieve paged lists of files and events.

### JSON Changes

The Intellectual Object view records returned by the Registry API are a superset of the old Pharos object records. In addition to the fields returned by Pharos, Registry adds the following:

* `bag_group_identifier` (string) - Identifies the group of bags to which this bag belongs. This is often empty
* `storage_option` (string) - Describes where this object's files are stored.
* `bagit_profile_identifier` (string) - Describes the BagIt profile used to create and restore this object. This will be either and APTrust or BTR profile identifier.
* `source_organization` (string) - The value of the Source-Organization tag in the object's bag-info.txt file.
* `internal_sender_identifier` (string) - The value of the Internal-Sender-Identifier tag in the object's bag-info.txt file.
* `internal_sender_description` (string) - The value of the Internal-Sender-Description tag in the object's bag-info.txt file.
* `institution_name` (string) - The name of the institution that deposited this object.
* `institution_identifier` (string) - The identifier (domain name) of the institution that deposited this object.
* `institution_type` (string) - Indicates whether the institution that owns this object is a member institution (account) or subscribing institution (sub-account).
* `institution_parent_id` (integer) - ID of this institution's parent. Applicable to subscriber institutions (sub-accounts) only.
* `file_count` (integer) - The total number of files preserved for this object. This includes payload files and preserved manifests. APTrust does not preserve manifests or tag manifests. This number can change over time as additional new files are ingested or existing ones are deleted.
* `size` (integer) - The total number of bytes for all files preserved for this object. This number can change over time as additional new files are ingested or existing ones are deleted.
* `payload_file_count` (integer) - The total number of files in this object's payload. I.e., inside the data directory of the bag that was uploaded for deposit. This number can change over time as additional new files are ingested or existing ones are deleted.
* `payload_size` (integer) - Total size, in bytes, of the files in this object's payload directory (the data directory of the bag that was uploaded for ingest). This number can change over time as additional new files are ingested or existing ones are deleted.


## Premis Events

### Changed

The following endpoints have changed as noted.

* `/member-api/v2/events/{generic_file_identifier}` -> `/member-api/v3/events?generic_file_identifier={identifier}` or `/member-api/v3/events?generic_file_id={id}`
* `/member-api/v2/events/{intellectual_object_identifier}` -> `/member-api/v3/events?intellectual_object_identifier={identifier}` or `/member-api/v3/events?intellectual_object_id={id}`

### Deleted

The following endpoint was deleted because member institutions can retrieve only their own events.

* `/member-api/v2/events/{institution_identifier}`

### Added

The new endpoint `/member-api/v3/events/show/{id}` displays details of a single event.

### JSON Changes

The version 3 JSON contains all of the fields from the version 2 API, plus the following:

* `institution_name` (string) - The name of the institution to which the event pertains.

## Work Items

### Changed

The following endpoints have changed as noted.

* `/member-api/v2/items` - `/member-api/v3/items`

### Deleted

No Work Item endpoints were deleted.

### Added

The following endpoint was added:

* `/member-api/v3/items/show/{id}`

### JSON Changes

* `alt_identifier` (string) - An alternate identifier for this object to which this Work Item pertains, supplied by the depositor.
* `bag_group_identifier` (string) - An optional identifier naming the logical group to which this object belongs. For instance, if ten bags are all part of the same collection, the collection name may be used as the group identifier associating all ten bags.
* `bagit_profile_identifier` (string) - The identifier of the BagIt profile used to create this bag. This can be either the APTrust profile URL or the BTR profile URL. Bags will be restored using the same profile under which they were submitted.
* `date_processed` (datetime as string) - The date and time of last known activity on this work item. This timestamp may change several times during multipart processes such as ingest.
* `institution_identifier` (string) - The identifier (domain name) of the institution that owns the files or objects to which this deletion request pertains.
* `institution_name` (string) - The name of the institution that owns the files or objects to which this work item pertains.
* `internal_sender_identifier` (string) - An optional identifier for internal use by the depositor. The identifier belongs to the object to which this work item pertains.
* `node` (string) - The hostname of the microservice worker currently processing this item. If this is empty, the item is not currently being processed.
* `pid` (integer) - The process ID of the worker that is currently working on this item. If this is empty, the item is not currently being processed.
* `source_organization` (string) - The name of the institution that submitted the object for ingest. This comes from the Source-Organization tag in the original bag.
* `storage_option` (string) - Indicates where the object's files are stored.
