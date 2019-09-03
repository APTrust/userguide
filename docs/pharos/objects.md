# Intellectual Objects

An intellectual object is a logical collection of files. Pharos typically creates one intellectual object for each tarred bag file you upload to your receiving bucket. The only exception to this rule is when your bags following the multi-bag naming convention. For example, the contents of bags `test.edu/photos.bag01.of03.tar`, `test.edu/photos.bag02.of03.tar`, and `test.edu/photos.bag03.of03.tar` would all be collected into a single intellectual object.

## Listing Objects

To view a list of your institution's intellectual objects, click the __Objects__ tab.

![List of intellectual objects](../img/pharos/ObjectList.png)

You can also use the search bar to locate objects by identifier, name, title, bag group identifier, or alternate identifier.

![Pharos search by object identifier](../img/pharos/ObjectSearch.png)

### Alternate Identifiers

Alternate identifiers come from the Internal-Sender-Identifier tag in the bag-info.txt file. This usually identifies an object using your internal identifier scheme. This field is optional and may be blank.

### Bag Group Identifiers

The optional bag group identifier comes from the Bag-Group-Identifier field in the bag-info.txt file. This is used to logically group a number of distinct intellectual objects. For example, some organizations prefer to break large collections into a series of smaller bags, each of which becomes a distinct intellectual object upon ingest.

A group of objects called `test.edu/smith_photos_1946`, `test.edu/smith_photos_1947`, and `test.edu/smith_photos_1948` may be logically grouped with the Bag-Group-Identifier `Smith Photos`. You can then search Pharos for objects belonging to this group to find all files belonging to the `Smith Photos` collection.

## Viewing Object Details

Click any object title in the list of objects to view its details. Note that the object detail page includes buttons to view all of the object's files and events, and if you have sufficient privileges, to restore or delete the object.

![Intellectual object detail page](../img/pharos/ObjectDetail.png)
