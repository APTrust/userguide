# APTrust Bagging Requirements

APTrust currently accepts bags for ingest in the APTrust BagIt format. In 2020, APTrust will begin accepting bags in Beyond the Repository (BTR) format as well, but we suggest APTrust members stick to the APTrust format.

## APTrust BagIt Specification

In addition to conforming to the BagIt specification [version 0.97](https://tools.ietf.org/html/draft-kunze-bagit-14) or [version 1.0](https://tools.ietf.org/html/rfc8493), valid APTrust bags must include the tag files and tags listed below, and must meet the following criteria:

* Bags must be serialized in tar format.

* Tarred bags must untar to a single directory whose name matches the name of the tar file. For example, `my_bag.tar` must untar to a directory called `my_bag`.

* The name of the tarred bag file must not include directories. `my.edu.my_bag.tar` is valid. `C:\path\to\my.edu.my_bag.tar` is not.

* Bags must contain either an md5 or sha256 manifest, or both

* Bags must be 5 terabytes or less in size.

* Bags may not contain a fetch.txt file.

* Bags may contain tag manifests.

* Bags may contain files outside of the data directory other than manifests and tag manifests. APTrust will consider these to be tag files, and will not try to parse them.

* When uploading multipart bags, use the [multipart bag naming format](#multipart-bags) described below.

A valid untarred APTrust bag has the following structure:

```
           <base directory>/
           |   aptrust-info.txt
           |   bag-info.txt
           |   bagit.txt
           |   manifest-&lt;algorithm&gt;.txt (md5 AND/OR sha256)
           |   [optional tag manifests]
           |   [optional additional tag files]
           \--- data/
                 |   [payload files]
           \--- [optional tag directories]/
                 |   [optional tag files]

```

### File and Directory Names

* File and Folder names must follow POSIX conventions:
* Contain upper or lower case letters, numbers, dots, underscores, percent signs, or dashes. (A–Z a–z 0–9 . _% -)
* May contain virtually any printable character, except newlines, carriage returns, tabs, vertical tabs and ASCII bells. (As of 1/30/2017)
* Are considered case sensitive.
* MUST not begin with a dash. (-)
* MUST not contain whitespaces
* May contain whitespaces. (As of 1/30/2017)
* Restricted to 255 characters in length including extension.
* MUST be at least 1 character in length.


### bagit.txt file

The bag must have a bagit.txt file, with the following tags.

Tag | Allowed values
----|----
BagIt-Version | 0.97 or 1.0
BagIt-Version | UTF-8

### bag-info.txt file

The bag-info.txt file should contain the following tags:

Tag  | Description | Example
---- | ---- | ----
Source-Organization | This should be the human readable name of the APTrust partner organization. For example, "University of Virginia." You may be more specific, if you wish, specifying a specific college or library within the university, such as "Georgetown University Law Library." However, when APTrust restores bags, the source organization in the bag-info.txt file will be set to the name of the partner institution. | University of Virginia
Bagging-Date | The date the content was bagged. Use ISO 8601 UTC format (YYYY-MM-DD). | 2019-08-19
Bag-Count | Two numbers separated by "of", in particular, "N of T", where T is the total number of bags in a group of bags and N is the ordinal number within the group; if T is not known, specify it as "?" (question mark). | Examples: 1 of 2, 4 of 4, 3 of ?, 89 of 145.
Internal-Sender-Description | A sender-local explanation of the contents and provenance. |
Internal-Sender-Identifier | An alternate sender-specific identifier for the content and/or bag. |
Bag-Group-Identifier | A sender-supplied identifier for the set, if any, of bags to which it logically belongs. | Greeling Photo Collection

For more on bag group identifiers, see the [Bag Group Identifiers](../pharos/objects#bag-group-identifiers) section of the Objects page.

For a list of other commonly-used tags in the bag-info.txt file, see the official BagIt specification for [version 0.97](https://tools.ietf.org/html/draft-kunze-bagit-14) or [version 1.0](https://tools.ietf.org/html/rfc8493).

APTrust supports all of the tags mentioned in the 0.97 and 1.0 specifications. You may also add your own custom tags to this file.

### aptrust-info.txt file

This file must contain the following tags:

Tag  | Description
---- | ----
Title | A human readable title for searching and listing in APTrust. This cannot be empty.
Description | A human-readable description of the bag. This will appear in Pharos. |
Access | One of three access options listed below. The access option describes who can see an object's metadata, including its name and description, a list of its generic files and events. APTrust does not currently provide access to the objects themselves, except when you restore one of your bags. No matter which access option you choose, no other institution can access your intellectual object.
Storage-Option | This indicates how and where you want APTrust to store your bag. If omitted, Storage-Option defaults to "Standard". See the section on Storage Options below for more information.

#### Allowed Access Values

* __Restricted__: Metadata about this object is accessible to the institutional administrator (at the depositing institution) and to the APTrust admin. No one else can even see that this object exists in the repository.

* __Institution__: All users at the depositing institution can see metadata about this object.

* __Consortia__: All APTrust members can see this object's metadata.

#### Allowed Storage-Option Values

* __Standard__: The bag's contents will be store in S3 in Northern Virginia and Glacier in Oregon. APTrust will perform fixity checks on the S3 files every 90 days.

* __Glacier-OH__: Files will be stored ONLY in Glacier, in AWS's Ohio region, and will be encrypted during storage. APTrust will not perform any fixity checks on these files.

* __Glacier-OR__: Files will be stored ONLY in Glacier, in AWS's Oregon region, and will be encrypted during storage. APTrust will not perform any fixity checks on these files.

* __Glacier-VA__: Files will be stored ONLY in Glacier, in AWS's Northern Virginia region, and will be encrypted during storage. APTrust will not perform any fixity checks on these files.

* __Glacier-Deep-OH__: Files will be stored ONLY in Glacier Deep Archive, in AWS's Ohio region, and will be encrypted during storage. APTrust will not perform any fixity checks on these files.

* __Glacier-Deep-OR__: Files will be stored ONLY in Glacier Deep Archive, in AWS's Oregon region, and will be encrypted during storage. APTrust will not perform any fixity checks on these files.

* __Glacier-Deep-VA__: Files will be stored ONLY in Glacier Deep Archive, in AWS's Northern Virginia region, and will be encrypted during storage. APTrust will not perform any fixity checks on these files.

!!! warning "A note on storage options"
    When you update an existing bag, APTrust will apply Storage-Option of the original version to the new version, even if the new version's Storage-Option tag explicitly specifies something different. This is to prevent the proliferation of multiple different versions of an object across multiple storage areas. If you want to change the Storage-Option of an existing object, you must delete it and then re-ingest it with the new option.

## Multipart bags

You may split a single large bag into a number of smaller bags by using the naming convention `institution_identifier.bag_identifier.b###.of###`. That is, you append `.bag01.of16`, `.bag02.of16`, etc. to the end of the bag name of each bag in the group.

Upon ingest, APTrust will treat all of the contents of all of the bags as part of a single intellectual object. Thus, the contents of `test.edu/photos.bag01.of03.tar`, `test.edu/photos.bag02.of03.tar`, and `test.edu/photos.bag03.of03.tar` would all be collected into a single intellectual object called `test.edu/photos`.

Because APTrust re-bags files for restoration, the bags returned by a restoration request will not match the bags you originally submitted, but all of the payload files will be present.

Generic Files in APTrust are referenced by their URI, which is the original filepath relative to the bag. File and folder names should be unique across multi-part bags to make sure all items are processed and not treated as a file update.

## Versioning

APTrust does not currently support versioning. When you upload new versions of existing files, APTrust overwrites the old version with the new. See [Updates](../preservation/updates.md) for details.

If you want to keep multiple versions of a bag or file, append a version number or timestamp to the end of the bag or file name. For example, `test.edu/bag_of_photos` and `test.edu/bag_of_photos.v2` will be stored as separate objects, and files from the second version will not overwrite files from the first.

## BTR BagIt Profile

APTrust plans to support version 1.0 of the [BTR bagit profile](https://github.com/dpscollaborative/btr_bagit_profile/blob/1.0/btr-bagit-profile.json){target=_blank} later in 2021. Support will include both the ingest and restoration of BTR bags.
