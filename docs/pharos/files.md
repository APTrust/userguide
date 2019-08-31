# Generic Files

Generic files are the individual files or bitstreams that make up intellectual objects. These may be photos, videos, text documents, XML metadata files exported from your internal repository, etc.

When you send a bag to your receiving bucket, APTrust creates an intellectual object from the bag (or updates an existing object, if the bag name matches the name of an existing object), and APTrust stores all of the bag's payload files and tag files. Any file outside the bag's data directory, except for manifests and tag manifests, is considered a tag file.

## Listing Files

You view a list of your institution's generic files by clicking on the __Files__ tab in Pharos.

![List of Generic Files](/img/pharos/FilesList.png)

Use the filters on the left side of the page to narrow down the list. You can also use the search bar at the top of the page to locate specific files by _File Identifier_. Note that you can search by partial file identifier, so if you're looking for a file called `img_2107.jpg` but can't remember what intellectual object it belongs to, you should still be able to find it using that fragment of the file name.

## File Details

Click on the identifier of any file to view its detail page. From the detail page, you can view events related to the file, or if you have sufficient permissions, you can restore or delete the file.

![Generic File detail page](/img/pharos/FileDetail.png)
