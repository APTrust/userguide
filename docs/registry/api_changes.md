# API Changes

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

* intellectual_object_identifier - Sort by intellectual object identifier, ascending.
* intellectual_object_identifier__asc - Sort by intellectual object identifier, ascending.
* intellectual_object_identifier__desc - Sort by intellectual object identifier, descending.

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

The following paths have changed as noted.

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

* size (integer) - The size of the file, in bytes.
* object_identifier (string) - The identifier of the Intellectual Object to which this file belongs.
* access (string) - The file's access setting.
* institution_id (integer) - The ID of the institution that owns this file.
* institution_name (string) - The name of the institution that owns this file.
* institution_identifier (string) - The identifier (domain name) of the institution that owns this file.
* storage_option (string) - Describes where this file is stored.
* uuid (string) - The object's preservation identifier.
* md5 (string) - The most recently calculated md5 checksum for this file.
* sha1 (string) - The most recently calculated sha1 checksum for this file.
* sha256 (string) - The most recently calculated sha256 checksum for this file.
* sha512 (string) - The most recently calculated sha512 checksum for this file.
* storage_records (Array{Storage Record}) - A list of storage records indicating where this file is saved in preservation storage.



## Intellectual Objects

### Changed

### Deleted

### Added

### JSON Changes



## Premis Events

### Changed

### Deleted

### Added

### JSON Changes



## Work Items

### Changed

### Deleted

### Added

### JSON Changes
