# APTrust Partner Tools

The aptrust command-line utility (apt-cmd, aka Partner Tools) enables you to create and validate bags, manage S3 files, and query data in the APTrust Registry. This tool replaces the older version 2.x partner tools which did not have bag creation features and worked only with Pharos.

You can create workflows with apt-cmd that include:

* creating a bag
* validating the bag
* uploading the bag to an S3 bucket
* checking the APTrust registry to see when the bag was ingested
* retrieving object and file details, including checksums and PREMIS events,
from the APTrust registry

For bag creating and validation, the current release supports tarred bags
only. Though access to the APTrust registry is limited to APTrust depositors,
anyone can use apt-cmd's bagging and S3 features.

## Downloads

| Platform | Architecture | Version | SHA-256 |
| -------- | ------------ | ------- | ------- |
| [Windows](https://s3.amazonaws.com/aptrust.public.download/apt-cmd/v3.0.0-beta/windows/apt-cmd.exe) | Intel 64-bit | v3.0.0-beta | b12d7daf68ca2a2ea99ea208143e4571cf49fd8221ea1998a9b4f5db9774b631 |
| [Mac Intel](https://s3.amazonaws.com/aptrust.public.download/apt-cmd/v3.0.0-beta/mac-intel/apt-cmd)  | Intel 64-bit | v3.0.0-beta | 1b5ceb015744e9ca818e5526f0940988fd4dad7f56c1bde105762bd89522265b |
| [Mac ARM](https://s3.amazonaws.com/aptrust.public.download/apt-cmd/v3.0.0-beta/mac-arm/apt-cmd) | Apple Silicon (M series) | v3.0.0-beta | 0327e04b44137ce856b342542563133b9f8184364513394013bf200939dd6c8e |
| [Linux](https://s3.amazonaws.com/aptrust.public.download/apt-cmd/v3.0.0-beta/linux/apt-cmd) | Intel 64-bit | v3.0.0-beta | 88d13960e50478066a6426ffd49e4763feece4642b7420e0d2e4f8d9412e9c88 |

APTrust partner tools are open source, distributed under the BSD 2-clause license. The source code is available on github at [https://github.com/APTrust/partner-tools](https://github.com/APTrust/partner-tools){target=_blank}.


## Help and Troubleshooting

You can get help for any apt-cmd command using the `--help` flag.
For example, `apt-cmd --help` shows the list of available commands. To get
help with a specific command, try `apt-cmd [command] --help`. For example,
`apt-cmd bag create --help`.

All operations support the `--debug` flag. When supplied, apt-cmd will print
out additional detailed information about what it's doing.

## Exit Codes

The apt-cmd tool returns consistent exit codes for all operations. If you're
using apt-cmd in scripts, check the exit code of each call to ensure it
succeeded.

An exit code of 0 (zero) indicates the call succeeded. A non-zero exit code
means the call failed.

| Code | Meaning |
| ---- | ------- |
| 0 | The operation completed without errors. |
| 1 | The operation failed due to a runtime error. Causes may include a network error, full disk, lack of file permissions, etc. |
| 2 | The bag you validated turned out to be invalid. Check the error output for details. |
| 3 | The operation failed due to a user error. This is almost always caused by missing or invalid command-line parameters, or missing/invalid configuration settings. |
| 4 | The operation did not complete because an external service responded with an error. Common causes including passing bad authentication values to the APTrust registry or the remote S3 service, requesting an object that doesn't exist, and requesting an item you're not authorized to access. |

## Creating a Bag

You can create bags that conform to the APTrust, BTR or empty BagIt profile.
If you specify the empty profile, apt-cmd will create a valid bag with the
tags and manifests you specify.

The example below demonstrates how to speficy flags and tag values.

For tag values, use the format "filename.txt/Tag-Name=tag value". If you
omit the file name, it defaults to bag-info.txt. For example, the following
two tags will both be written into the Source-Organization tag in
bag-info.txt:

```
  --tags="bag-info.txt/Source-Organization=Faber College"
  --tags="Source-Organization=Faber College"
```

Note that tag values are quoted in their entirety, both the name and
the value.

Apply double quotes to values containing special characters such as
spaces and symbols and to values containing environment variables that
you want to expand, such as "$HOME".

Apply single quotes to values containing symbols that you don't want
the shell to expand, such as curly braces, ampersands, and random dollar
signs.

You can specify any tag files and tag names you want.

The following example packages the directory /home/josie/photos according
to the APTrust BagIt profile and writes the tarred bag into
/home/josie/bags/photos.tar.

This bag will include md5 and sha256 manifests and tag manifests. It will
also include the specified tags in the bag-info.txt and aptrust-info.txt
tag files.

```bash
  apt-cmd bag create \
    --profile=aptrust \
    --manifest-algs='md5,sha256' \
    --output-file='/home/josie/bags/photos.tar' \
    --bag-dir='/home/josie/photos' \
    --tags='aptrust-info.txt/Title=My Bag of Photos' \
    --tags='aptrust-info.txt/Access=Institution' \
    --tags='aptrust-info.txt/Storage-Option=Standard' \
    --tags='bag-info.txt/Source-Organization=Faber College' \
    --tags='Custom-Tag=Single quoted because it {contains} $weird &characters'
```

### Troubleshooting

1. Use the --debug flag to get the program to tell what it thinks it's
   supposed to be doing.
2. If you use backslashes, as in the example able, be sure there are no
   trailing spaces or any characters other than a newline following the
   backslash.

### Limitations

1. This tool currently supports only APTrust, BTR, and empty/generic
   BagIt profiles.
2. For now, all bags will be output as tar files.
3. This tool currently supports only the md5, sha1, sha256, and sha512
   algorithms for manifests and tag manifests.
4. This tool currently will not generate a fetch.txt file.

## Validating a Bag

Currently, apt-cmd validates only tarred bags. The following commands
validate a bag according to the APTrust BagIt profile:

```bash
  apt-cmd bag validate my_bag.tar
  apt-cmd bag validate -p aptrust my_bag.tar
```

To validate a bag using the Beyond the Repository (BTR) profile:

```bash
  apt-cmd bag validate -p btr my_bag.tar
```

To validate a bag using the empty profile:

```bash
  apt-cmd bag validate -p empty my_bag.tar
```

The empty profile simply ensures the bag is valid according to the general
BagIt specification.

### Limitations

The validator only works with tarred bags and will not validate fetch.txt files.

## S3 Commands

apt-cmd can upload to and download from any service with an S3-compliant
API. It can also list bucket contents in JSON or text formats, and delete
items from S3.

To access S3 services, you'll need to S3 credentials, as specified in the
[Configuration Settings](#configuration-settings) section below.

## Uploading a File to an S3 Bucket

apt-cmd can upload a file to any S3-compatible service. For this to work,
you will need to have APTRUST_AWS_KEY and APTRUST_AWS_SECRET set in your
environment, or in a config file specified with the --config flag.

### Examples

Upload file photo.jpg to Amazon's S3 service:

```bash
    apt-cmd s3 upload --host=s3.amazonaws.com --bucket="my-bucket" photo.jpg
```

Upload the same file, but call it renamed.jpg in S3:

```bash
    apt-cmd s3 upload --host=s3.amazonaws.com  \
             --bucket="my-bucket" \
             --key='renamed.jpg' \
             photo.jpg
```

## Listing the Contents of an S3 Bucket

You can list files from any S3-compatible service. For this to work,
you will need to have APTRUST_AWS_KEY and APTRUST_AWS_SECRET set in your
environment, or in a config file specified with the --config flag.

List output is in JSON format, unless you specify `--format=text`.

### Examples

List items in my_bucket with prefix "photo":

```bash
    apt-cmd s3 list --host=s3.amazonaws.com --bucket=my_bucket --prefix=photo
```

List 10 items in my_bucket with prefix "photo", using plain text output:

```bash
    apt-cmd s3 list --host=s3.amazonaws.com \
                    --bucket=my_bucket \
                    --prefix=photo \
                    --maxitems=10 \
                    --format=text
```

## Downloading an S3 File

You can download files from any S3-compatible service. For this to work,
you will need to have APTRUST_AWS_KEY and APTRUST_AWS_SECRET set in your
environment, or in a config file specified with the --config flag.

### Examples

Download a file from Amazon's S3 service into the current directory:

```bash
    apt-cmd s3 download --host=s3.amazonaws.com --bucket="my-bucket" --key='photo_001.jpg'
```

Download the same file and save it with a custom name on your desktop:

```bash
    apt-cmd s3 download --host=s3.amazonaws.com  \
               --bucket="my-bucket" \
               --key='photo_001.jpg' \
               --save-as="$HOME/Desktop/vacation.jpg"
```

## Deleting an S3 File

You can delete objects from any S3-compatible service. For this to work,
you will need to have APTRUST_AWS_KEY and APTRUST_AWS_SECRET set in your
environment, or in a config file specified with the --config flag.

### Example

Delete object photo.jpg from my-bucket on AWS S3:

```bash
    apt-cmd s3 delete --host=s3.amazonaws.com --bucket="my-bucket" --key='photo.jpg'
```

Note: This returns exit status zero and `'{ "result": "OK" }'` if the key is
successfully deleted or if the key wasn't in the bucket to begin with.

## Registry Commands

Registry commands retrieve information from the APTrust registry. You'll
need APTrust credentials to query the Registry. See
[Configuration Settings](#configuration-settings) below for info on how
to pass these credentials to apt-cmd.

## Listing Registry Work Items

List work items from the APTrust registry, or run a special "quick report."

You'll typically want to list work items so you can track the progress of
an ingest or restoration.

Note that when filtering Work Items, Objects or Files using any of the list
commands, you can supply filters as name-value pairs on the command line.
These pairs **do not use the double-dash (--) prefix**.

### Examples

List recent ingests:

```bash
  apt-cmd registry list workitems action='Ingest' sort='date_processed__desc'
```

List all work items since April 6, 2023:

```bash
  apt-cmd registry list workitems date_processed__gteq='2023-04-06' sort='date_processed__desc'
```

List failed work items:

```bash
  apt-cmd registry list workitems status='Failed' sort='date_processed__desc'
```

List work items pertaining to a tar file you uploaded:

```bash
  apt-cmd registry list workitems name='bag-of-photos.tar'
```

List work items pertaining to a bag with a specific etag:

```bash
  apt-cmd registry list workitems etag='987654321-100'
```

List work items pertaining to a specific intellectual object:

```bash
  apt-cmd registry list workitems object_identifier='test.edu/TestBag'
```

List restorations or deletions of a specific file:

```bash
  apt-cmd registry list workitems generic_file_identifier='test.edu/TestBag/data/photo1.jpg'
```

### Quick Reports

List all items from the past 30 days that are still in process:

```bash
  apt-cmd registry list workitems --report=inprocess
```

List all items from the past 30 days that failed or were cancelled:

```bash
  apt-cmd registry list workitems --report=problems
```

List all restorations from the past 30 days:

```bash
  apt-cmd registry list workitems --report=restorations
```

When running quick reports, this tool ignores all other query params.

### WorkItem List Filter Options

When querying for Work Items, apt-cmd supports the following filter
options:

| Name | DataType | Description |
| ---- | -------- | ----------- |
| action | string | The action, one of: Delete, Restore Object, Restore File, Glacier Restore, Ingest |
| action__in | string | You can specify this multiple times to get results that include any of the named actions. For example, to get work items pertaining to any type of restoration, specify `action_in="Restore Object" action_in="Restore File" action_in="Glacier Restore"` |
| alt_identifier | string | Return work items pertaining to objects with this alternate identifier. |
| bag_date__gteq | date string 'yyyy-mm-dd' | Return work items pertaining to bags created on or after this date. |
| bag_date__lteq | date string 'yyyy-mm-dd' | Return work items pertaining to bags created on or before this date. |
| bag_group_identifier | string | Return work items pertaining to bags having this bag group identifier. (Exact match only. Group identifier prefixes won't work here.) |
| bagit_profile_identifier | string | Return work items pertaining to bags having this BagIt profile identifier. Allowed values are https://github.com/dpscollaborative/btr_bagit_profile/releases/download/1.0/btr-bagit-profile.json (for the BTR profile), and https://raw.githubusercontent.com/APTrust/preservation-services/master/profiles/aptrust-v2.2.json (for the APTrust profile). |
| date_processed__gteq | date string 'yyyy-mm-dd' | Return items last touched by a worker process on or after this date. |
| date_processed__lteq | date string 'yyyy-mm-dd' | Return items last touched by a worker process on or before this date. |
| etag | string | Return work items pertaining to an uploaded bag with this etag. |
| generic_file_id | integer | Return work items pertaining to this generic file. |
| generic_file_id__is_null | string | Set this to "true" to retrieve work items with an empty generic file identifier. Results will include only object-level work items, such as ingests, object deletions and object restorations.  |
| generic_file_identifier | string | Return work items pertaining to the generic file with this identifier. |
| intellectual_object_id | integer | Return work items pertaining to this intellectual object. Note that for ingests, the object id will be empty until the ingest completes. |
| intellectual_object_id__is_null | string | Set this to "true" to include work items with an empty intellectual object id. This will return ingests that have not yet completed. All other work items are associated with an object. |
| name | string | The name of the tar file you want to check on. This is the name of the file you uploaded to your receiving bucket for ingest. For example, "virginia.edu/bag-of-photos.tar". |
| needs_admin_review | string | Set this to "true" if you want to retrieve work items that have been flagged as problematic, requiring review from an APTrust administrator. |
| node__is_null | string | Set this to "true" to retrieve work items with a null worker node. These items are currently not being worked on by any process. Items are in this state when awaiting processing and when processing has completed. |
| node__not_null | string | Set this to "true" to retrieve items that are undergoing active processing by an ingest, restoration or deletion worker. |
| object_identifier | string | Return items associated with this intellectual object identifier. |
| queued_at__is_null | date string 'yyyy-mm-dd' | Return items queued for work on or after this date. |
| queued_at__not_null | date string 'yyyy-mm-dd' | Return items queued for work on or before this date. |
| retry | string | Set this to "true" to retrieve items that are eligible for processing. Set to "false" to retrieve problematic items that either will not be retried or require manually requeueing. |
| size__gteq | integer | Return work items associated with files or objects containing at least this number of bytes. |
| size__lteq | integer | Return work items associated with files or objects containing no more than this number of bytes. |
| stage | string | Return items in this stage of processing. Allowed values: Available in S3, Cleanup, Copy To Staging, Fetch, Format Identification, Package, Receive, Record, Reingest Check, Requested, Resolve, Restoring, Storage Validation, Store, Unpack, Validate. |
| stage__in | string | Specify this filter multiple times to retrieve items in any one of a number of stages. For example, to retrieve items in the Store, Unpack, or Validate stages, use `stage__in=Store stage__in=Unpack stage_in=Validate` |
| status | string | Retrieve items having this status. Values include Cancelled, Failed, Pending, Started, Success, Suspended. |
| status__in | string | Specify this filter multiple times to retrieve items having any of a number of statuses. For example, to retrieve items with Pending and Started status, use `status__in=Pending status__in=Started`. |
| storage_option | string | Retrieve items pertaining to objects or files having the specified storage option. Available values : Glacier-Deep-OH, Glacier-Deep-OR, Glacier-Deep-VA, Glacier-OH, Glacier-OR, Glacier-VA, Standard, Wasabi-OR, Wasabi-VA |
| user | string (email address) | Return work items initiated by the user with this email address. |

### WorkItem List Paging and Sort Options

By default, the `list workitems` command returns the first 25 results,
sorting items by id. You can override these defaults with the following
settings.

| Name | DataType | Description |
| ---- | -------- | ----------- |
| per_page | integer | The number of results to return per request. Default is 25. Max is 1000. |
| page | integer | The page of results you want to retrieve. Default is 1. |
| sort | string | Sort results using the specified column. You can sort on any field listed in the JSON sample below. Append "__desc" to the field name to do a descending (reverse) sort. For example, `sort=name` sorts by the name of the tar file, while `sort=name__desc` does a reverse sort by name. |

### WorkItem List Response Format

The `workitem list` command returns JSON in the following format.
Note that `count` indicates the total number of results, while
`next` and `previous` are URLs pointing to the next and previous
pages of results.

```json
{
  "count": 0,
  "next": "string",
  "previous": "string",
  "items": [
    {
      "id": 0,
      "action": "Delete",
      "alt_identifier": "string",
      "aptrust_approver": "user@example.com",
      "bagit_profile_identifier": "https://github.com/dpscollaborative/btr_bagit_profile/releases/download/1.0/btr-bagit-profile.json",
      "bag_date": "2023-04-20T17:34:07.911Z",
      "bag_group_identifier": "string",
      "bucket": "string",
      "created_at": "2023-04-20T17:34:07.911Z",
      "date_processed": "2023-04-20T17:34:07.911Z",
      "etag": "string",
      "generic_file_id": 0,
      "generic_file_identifier": "string",
      "institution_id": 0,
      "institution_name": "string",
      "institution_identifier": "string",
      "inst_approver": "user@example.com",
      "intellectual_object_id": 0,
      "internal_sender_identifier": "string",
      "name": "string",
      "needs_admin_review": true,
      "node": "string",
      "note": "string",
      "object_identifier": "string",
      "outcome": "Failure",
      "pid": 0,
      "queued_at": "2023-04-20T17:34:07.911Z",
      "retry": true,
      "size": 0,
      "source_organization": "string",
      "stage": "Available in S3",
      "stage_started_at": "2023-04-20T17:34:07.912Z",
      "status": "Cancelled",
      "storage_option": "Glacier-Deep-OH",
      "updated_at": "2023-04-20T17:34:07.912Z",
      "user": "user@example.com"
    }
  ]
}
```

## Retrieving a Single Registry Work Item

To retrieve a single WorkItem record from the APTrust Registry, use the
command below. Note that id is an integer.

```bash
  apt-cmd registry get workitem <id>
```

### WorkItem Get Response Format

The command `aptrust registry get workitem` returns JSON in the following
format:

```json
{
  "id": 0,
  "action": "Restore Object",
  "alt_identifier": "string",
  "aptrust_approver": "user@example.com",
  "bagit_profile_identifier": "https://github.com/dpscollaborative/btr_bagit_profile/releases/download/1.0/btr-bagit-profile.json",
  "bag_date": "2023-04-20T17:45:43.969Z",
  "bag_group_identifier": "string",
  "bucket": "string",
  "created_at": "2023-04-20T17:45:43.970Z",
  "date_processed": "2023-04-20T17:45:43.970Z",
  "etag": "string",
  "generic_file_id": 0,
  "generic_file_identifier": "string",
  "institution_id": 0,
  "institution_name": "string",
  "institution_identifier": "string",
  "inst_approver": "user@example.com",
  "intellectual_object_id": 0,
  "internal_sender_identifier": "string",
  "name": "string",
  "needs_admin_review": true,
  "node": "string",
  "note": "string",
  "object_identifier": "string",
  "outcome": "Success",
  "pid": 0,
  "queued_at": "2023-04-20T17:45:43.970Z",
  "retry": true,
  "size": 0,
  "source_organization": "string",
  "stage": "Available in S3",
  "stage_started_at": "2023-04-20T17:45:43.970Z",
  "status": "Cancelled",
  "storage_option": "Glacier-Deep-OH",
  "updated_at": "2023-04-20T17:45:43.970Z",
  "user": "user@example.com"
}
```

## Listing Registry Objects

You can list objects from the APTrust Registry, with filters.

### Examples

List 20 objects ordered by identifer:

```bash
  apt-cmd registry list objects sort='identifier' per_page='20'
```

List 20 objects reverse ordered by identifer:

```bash
  apt-cmd registry list objects sort='identifier__desc' per_page='20'
```

List objects created after April 6, 2023

```bash
  apt-cmd registry list files created_at__gteq='2023-04-06'
```

### Object List Filter Options

| Name | DataType | Description |
| ---- | -------- | ----------- |
| access | string | Return objects with this access setting. Available values : consortia, institution, restricted |
| alt_identifier | string | Return objects with this alternate identifier. |
| alt_identifier__starts_with | string | Return objects whose alternate identifier starts with the speficied string. |
| bag_group_identifier | string | Return objects with this bag group identifier. |
| bag_group_identifier__starts_with | string | Return objects whose bag group identifier starts with the specified string. |
| bagit_profile_identifier | string | Return objects with this BagIt profile identifier. Available values : https://github.com/dpscollaborative/btr_bagit_profile/releases/download/1.0/btr-bagit-profile.json (BTR profile) and https://raw.githubusercontent.com/APTrust/preservation-services/master/profiles/aptrust-v2.2.json (APTrust profile). |
| created_at__gteq | date string 'yyyy-mm-dd' | Return objects created on or after the given date. |
| created_at__lteq | date string 'yyyy-mm-dd' | Return objects created on or before the given date. |
| etag | string | Return the object with this etag. |
| file_count__gteq | integer | Return objects containing at least this number of files. |
| file_count__lteq | integer | Return objects containing no more than this number of files. |
| identifier | string | Return the object with this identifier. |
| internal_sender_identifier | string | Return the object with this internal sender identifier. |
| size__gteq | integer | Return objects whose size is at least this number of bytes. |
| size__lteq | integer | Return objects whose size is no more than this number of bytes. |
| state | string | Return objects with this state. A = Active, D = Deleted. Available values: A, D. |
| storage_option | string | Return objects with the specified storage option. Available values: Glacier-Deep-OH, Glacier-Deep-OR, Glacier-Deep-VA, Glacier-OH, Glacier-OR, Glacier-VA, Standard, Wasabi-OR, Wasabi-VA. |
| updated_at__gteq | date string 'yyyy-mm-dd' | Return objects updated on or after the given timestamp. |
| updated_at__lteq | date string 'yyyy-mm-dd' | Return objects updated on or before the given timestamp. |


### Object List Paging and Sort Options

By default, the `list objects` command returns the first 25 results,
sorting objects by id. You can override these defaults with the following
settings.

| Name | DataType | Description |
| ---- | -------- | ----------- |
| per_page | integer | The number of results to return per request. Default is 25. Max is 1000. |
| page | integer | The page of results you want to retrieve. Default is 1. |
| sort | string | Sort results using the specified column. You can sort on any field listed in the JSON sample below. Append "__desc" to the field name to do a descending (reverse) sort. For example, `sort=identifier` sorts by the object identifier, while `sort=identifier__desc` does a reverse sort by identifier. |

### Object List Response Format

The `list objects` command returns JSON with the following format. Note that
`count` is the total number of resuls matching your query, while `next` and
`previous` are URLs pointing to the next and previous pages of results.

```json
{
  "count": 0,
  "next": "string",
  "previous": "string",
  "items": [
    {
      "id": 0,
      "title": "string",
      "description": "string",
      "identifier": "string",
      "alt_identifier": "string",
      "access": "consortia",
      "bag_name": "string",
      "institution_id": 0,
      "created_at": "2023-04-20T18:25:28.513Z",
      "updated_at": "2023-04-20T18:25:28.513Z",
      "state": "A",
      "etag": "string",
      "bag_group_identifier": "string",
      "storage_option": "Glacier-Deep-OH",
      "bagit_profile_identifier": "https://github.com/dpscollaborative/btr_bagit_profile/releases/download/1.0/btr-bagit-profile.json",
      "source_organization": "string",
      "internal_sender_identifier": "string",
      "internal_sender_description": "string",
      "institution_name": "string",
      "institution_identifier": "string",
      "institution_type": "MemberInstitution",
      "institution_parent_id": 0,
      "file_count": 0,
      "size": 0,
      "payload_file_count": 0,
      "payload_size": 0
    }
  ]
}
```

## Retrieving a Single Registry Object

You can retrieve objects from the Registry by identifier or
by id. Object identifiers are strings, such as 'example.edu/photos'.
Ids are numeric.

### Examples

```bash
apt-cmd registry get object identifier=<object_identifier>
apt-cmd registry get object id=<object_id>
```

## Get Object Response Format

The `get object` command returns JSON in the following format:

```json
{
  "id": 0,
  "title": "string",
  "description": "string",
  "identifier": "string",
  "alt_identifier": "string",
  "access": "consortia",
  "bag_name": "string",
  "institution_id": 0,
  "created_at": "2023-04-20T18:52:35.412Z",
  "updated_at": "2023-04-20T18:52:35.412Z",
  "state": "A",
  "etag": "string",
  "bag_group_identifier": "string",
  "storage_option": "Glacier-Deep-OH",
  "bagit_profile_identifier": "https://github.com/dpscollaborative/btr_bagit_profile/releases/download/1.0/btr-bagit-profile.json",
  "source_organization": "string",
  "internal_sender_identifier": "string",
  "internal_sender_description": "string",
  "institution_name": "string",
  "institution_identifier": "string",
  "institution_type": "MemberInstitution",
  "institution_parent_id": 0,
  "file_count": 0,
  "size": 0,
  "payload_file_count": 0,
  "payload_size": 0
}
```

## Listing Registry Files

The `list files` command lists files from the APTrust Registry,
applying whatever filters you specify. Note that the file JSON returned
by this call includes only summary information for each file, while
the `get file` call returns more detailed info.

### Examples

List files belonging to object test.edu/my_bag, ordered by identifer:

```bash
  apt-cmd registry list files intellectual_object_identifier='test.edu/my_bag' sort='identifier'
```

List only the first 10 files from that same bag:

```bash
  apt-cmd registry list files intellectual_object_identifier='test.edu/my_bag' sort='identifier' per_page=10
```

List files created after April 6, 2023

```bash
  apt-cmd registry list files created_at__gteq='2023-04-06'
```

### File List Filter Options

| Name | DataType | Description |
| ---- | -------- | ----------- |
| created_at__gteq | date string 'yyyy-mm-dd' | Return files created on or after the given timestamp. |
| created_at__lteq | date string 'yyyy-mm-dd' | Return files created on or before the given timestamp. |
| identifier | string | Return the file with this identifier. |
| intellectual_object_id | integer | Return files belonging to the specified intellectual object. |
| last_fixity_check__gteq | date string 'yyyy-mm-dd' | Return files whose last fixity check was on or after the given timestamp. |
| last_fixity_check__lteq | date string 'yyyy-mm-dd' | Return files whose last fixity check was on or before the given timestamp. |
| size__gteq | integer | Return files whose size is at least this number of bytes. |
| size__lteq | integer | Return files whose size is no more than this number of bytes. |
| state | string | Return files with this state. A = Active, D = Deleted. Available values: A, D. |
| storage_option | string | Return files with the specified storage option. Available values: Glacier-Deep-OH, Glacier-Deep-OR, Glacier-Deep-VA, Glacier-OH, Glacier-OR, Glacier-VA, Standard, Wasabi-OR, Wasabi-VA. |
| updated_at__gteq | date string 'yyyy-mm-dd' | Return files updated on or after the given timestamp. |
| updated_at__lteq | date string 'yyyy-mm-dd' | Return files updated on or before the given timestamp. |


### File List Paging and Sort Options

By default, the `list files` command returns the first 25 results,
sorting files by id. You can override these defaults with the following
settings.

| Name | DataType | Description |
| ---- | -------- | ----------- |
| per_page | integer | The number of results to return per request. Default is 25. Max is 1000. |
| page | integer | The page of results you want to retrieve. Default is 1. |
| sort | string | Sort results using the specified column. You can sort on any field listed in the JSON sample below. Append "__desc" to the field name to do a descending (reverse) sort. For example, `sort=identifier` sorts by the file identifier, while `sort=identifier__desc` does a reverse sort by identifier. |

### File List Response Format

The `list files` command returns JSON with the following format. Note that
`count` is the total number of resuls matching your query, while `next` and
`previous` are URLs pointing to the next and previous pages of results.

```json
{
  "count": 0,
  "next": "string",
  "previous": "string",
  "items": [
    {
      "id": 0,
      "file_format": "string",
      "size": 0,
      "identifier": "string",
      "intellectual_object_id": 0,
      "object_identifier": "string",
      "access": "consortia",
      "state": "A",
      "last_fixity_check": "2023-04-20T18:55:14.164Z",
      "institution_id": 0,
      "institution_name": "string",
      "institution_identifier": "string",
      "storage_option": "Glacier-Deep-OH",
      "uuid": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "md5": "string",
      "sha1": "string",
      "sha256": "string",
      "sha512": "string",
      "created_at": "2023-04-20T18:55:14.165Z",
      "updated_at": "2023-04-20T18:55:14.165Z"
    }
  ]
}
```

## Retrieving a Single Registry File

You can retrieve a JSON record from the APTrust registry describing a
generic file with a specified identifier or id. File identifiers are strings,
such as 'example.edu/photos/data/image1.jpg'. Ids are numeric.

Note that this call returns not only the generic file info, but also all
of the checksums and PREMIS events associated with the file.

### Examples

```bash
apt-cmd registry get file <file_identifier>
apt-cmd registry get file <file_id>
```

## Get File Response Format

The `get file` command returns JSON in the following format:

```json
{
  "id": 0,
  "file_format": "string",
  "size": 0,
  "identifier": "string",
  "intellectual_object_id": 0,
  "state": "A",
  "last_fixity_check": "2023-04-20T19:09:44.674Z",
  "institution_id": 0,
  "storage_option": "Glacier-Deep-OH",
  "uuid": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "premis_events": [
    {
      "id": 0,
      "agent": "string",
      "date_time": "2023-04-20T19:09:44.674Z",
      "detail": "string",
      "event_type": "access assignment",
      "generic_file_id": 0,
      "identifier": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "institution_id": 0,
      "intellectual_object_id": 0,
      "object": "string",
      "old_uuid": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "outcome": "Failure",
      "outcome_detail": "string",
      "outcome_information": "string",
      "created_at": "2023-04-20T19:09:44.674Z",
      "updated_at": "2023-04-20T19:09:44.674Z"
    }
  ],
  "checksums": [
    {
      "id": 0,
      "algorithm": "md5",
      "datetime": "2023-04-20T19:09:44.674Z",
      "digest": "string",
      "generic_file_id": 0
    }
  ],
  "storage_records": [
    {
      "id": 0,
      "generic_file_id": 0,
      "url": "string"
    }
  ],
  "created_at": "2023-04-20T19:09:44.674Z",
  "updated_at": "2023-04-20T19:09:44.674Z"
}
```

## Configuration Settings

apt-cmd will read configuration values from a config file if you specify one
with the --config flag. Otherwise, it will read config values from the
environment. The [testconfig.env](https://github.com/APTrust/partner-tools/blob/master/testconfig.env){target=_blank} file shows a sample
configuration used for testing.

Config files can use .env, JSON, or YAML format. Just be sure to include the
correct file extension (.env, .yml, .yaml, or .json) so apt-cmd knows how to
parse the file.

You may find it useful to maintain separate config file for separate profiles.
For example, you may want to store S3 credentials for Amazon in aws.env,
credentials for Wasabi in wasabi.env, and for Minio in minio.env.

Or, you can skip config files altogether and use environment variables
like so:

```bash
  APTRUST_AWS_KEY=my_key APTRUST_AWS_SECRET=my_secrete apt-cmd s3 upload --host=s3.amazonaws.com --bucket="my-bucket" photo.jpg
```

apt-cmd uses the following settings.

| Name | Description |
| ---- | ----------- |
| APTRUST_AWS_KEY | Access Key ID to access S3. Required only for S3 operations. Works with any S3-compatible service. |
| APTRUST_AWS_SECRET | Secret access key to access S3. Required only for S3 operations. Works with any S3-compatible service. |
| APTRUST_REGISTRY_URL | URL of the APTrust registry you want to access. Production is https://repo.aptrust.org. Demo is https://demo.aptrust.org. Required only for registry operations. |
| APTRUST_REGISTRY_API_VERSION | Version of the current registry API. For now, this should be `v3`. Required only for registry operations. |
| APTRUST_REGISTRY_EMAIL | The email address associated with your APTrust registry account. Required only for registry operations. |
| APTRUST_REGISTRY_API_KEY | The API key associated with your APTrust registry account. Required only for registry operations. |
