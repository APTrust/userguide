# Moving

**It is not possible to change an object's (or file's) storage class or location after it has been successfully ingested.** Do not try to change the storage class or location by sending an [update](updates.md) with a new value in the storage-option field. When this happens, Registry enforces the original storage class and location. The correct procedure to move an object to a new storage class or location is described below.

1. If necessary, [restore](restoration.md) the relevant objects. If you have local copies, this may not be needed.
1. [Delete](deletion.md) the objects in Registry. Wait until the deletion is approved and fully performed before proceeding.
1. Modify the restored objects to have a new value in the [`Storage-Option` field](../depositing/index.md#allowed-storage-option-values) in the `aptrust-info.txt` tag file. If you have a tag manifest in your bag, it will either need to be manually updated or you can safely delete it. (Tag manifest are validated if included, but including them is optional.) Alternatively if working with local copies, simply rebag them using DART or your preferred tool and use the new `Storage-Option` value.
1. Upload the new version of the object to your receiving bucket. Once ingested, it will be in the new location. 

If it's helpful, send a request with a list of object identifiers to help@aptrust.org for any batch restorations or deletions.