# Updates

APTrust does not version bags. If you want to keep multiple versions of a bag, use a naming convention. For example: `virginia.edu.bag_of_photos`, `virginia.edu.bag_of_photos_V2`, `virginia.edu.bag_of_photos_V3`.

When you upload a bag that has the same name as an existing bag, this is what happens:

1. If a file in the new bag has the same name as a file in the old bag and the size or the md5 checksum or the sha256 checksum has changed, we overwrite the old file with the new one. You cannot recover the old file.

1. If a file in the new bag has the same name as a file in the old bag and the size and checksums have not changed, we do nothing.

1. If a file in the new bag did not exist in the old bag, we save it, but if a file in the old bag is not present in the new bag, we do not delete it.

The table below shows what happens when you upload a new version of a previously ingested bag.

Old Bag | New Bag | What we preserve | Why
---- | ---- | ---- | ----
bag-info.txt | bag-info.txt (changed) | the new version | Contents in new version have changed
data/document.pdf | data/document.pdf (unchanged) | old version | The document did not change
(file not present) | data/new_image.jpg | new version | File did not exist in old bag, but it's here now
data/old_image.jpg | (file not present) | old version | Although this file has been deleted from the new bag, we will not assume you want to delete it from storage. File deletion must be a deliberate act of the depositor.

**WARNING:** In general, depositors should not upload update bag until they see that the initial ingest has completed successfully. Preservation Services will ingest only one version of a bag at a time, and it will pick the latest version whenever possible, cancelling ingest of the earlier version. Files from the cancelled version will not be ingested. To ensure ingest of all files, wait until the first ingest completes successfully before uploading new versions of the bag.

This update policy has three important implications:

1. If you want to delete files from an ingested bag / intellectual object, you must do that deliberately. Currently, you can delete only through our Web UI.

1. When you restore the bag described in the table above, you'll get back both old_image.jpg and new_image.jpg (unless you manually delete one of them before you restore).

1. You can update metadata in a bag by uploading only the metadata, as long as there's at least one file in the data directory and the bag is otherwise valid. This may be useful for bags that contain 100GB of data and 100KB of frequently-updated metadata.

In Registry, updating (overwriting) an existing file in a bag causes the following to happen:

1. A new PREMIS ingestion event appears with the date of the new ingest.

1. Two new PREMIS fixity generation events appear, one with the md5 checksum of the new file, and one with the sha256 checksum.

The Registry file page for the updated file will show the new checksums for the file at the bottom of the page, along with the date on which the new checksums were calculated. Below those will be the older checksums, and the dates they were calculated.

Future fixity checks on the updated files will test against _the latest fixity value_ of the updated files.
