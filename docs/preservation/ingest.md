# Ingest

!!! notice AWS Credentials required
	Before sending materials to APTrust for ingest, you'll need to get AWS keys that allow you to upload materials to your receiving bucket. If you don't already have these, contact help@aptrust.org to get them. Also keep in mind that you'll have separate AWS keys for the demo and production environments.

You'll also need to know how to produce a valid APTrust bag. If you don't know how to do that yet, see the [bagging page](../bagging/index.md) for details, or use [DART](https://aptrust.github.io/dart-docs/users/getting_started/) to get going quickly.

## Uploading for Ingest

Assuming you have a valid bag, you can send it to our production system by uploading it to `aptrust.receiving.test.<institution.domain>` for the demo system or `aptrust.receiving.<institution.domain>` for the production system. In each case, replace `<institution.domain>` with your institution's domain name.

The following tools can upload files to your receiving bucket:

* [DART](https://aptrust.github.io/dart-docs/users/getting_started/)
* [APTrust Partner Tools](../partner_tools.md)
* [Minio Client](https://docs.min.io/docs/minio-client-complete-guide)
* [Amazon's AWS Command Line Tools](https://aws.amazon.com/cli/)

If you plan on interacting frequently with S3, the [Minio Client](https://docs.min.io/docs/minio-client-complete-guide) provides the best combination of rich features, ease of installation, and ease of use.

For most of what you'll be doing with APTrust, [DART](https://aptrust.github.io/dart-docs/users/getting_started/) and the [APTrust Partner Tools](../partner_tools.md) should be sufficient.

__Note:__ Some members have issues using DART to upload large bags (400GB or larger) because of local networking infrastructure. If you encounter this, we recommend using DART to create bags and using a third-party S3 client (e.g., [CyberDuck](https://cyberduck.io/){:target="_blank"}, [S3 Browser](https://s3browser.com/){:target="_blank"}) to upload them. 

## The Ingest Process

!!! warning Delay in ingest
	There can be a delay of up to 15 minutes before the tarred bag shows up in the work item list.

After you upload tarred bag to your receiving bucket, APTrust's ingest process will add it to a list of items waiting to be processed.  You can check the status of your bag in the list of Registry [Work Items](../registry/work_items.md), using the REST API, or using the apt_check_ingest program from the [partner tools](../partner_tools.md). Once your bag is successfully ingested it is automatically deleted from your receiving bucket. If the ingest fails you can see details in Registry.

!!! notice
	Failed bags stay in your receiving bucket for 30 or 60 days (demo or production) for your review. After that period the bag is automatically deleted.

![Ingest process on the backend](../img/aptrust_ingest_process.png# boxshadow)

[DART's dashboard](https://aptrust.github.io/dart-docs/users/dashboard/) also shows the status of items recently ingested and pending ingest.

Smaller bags (those under about 5GB) tend to ingest quickly. Larger bags can take longer, with multi-terabyte bags sometimes taking a few days. This is because the ingest process calculates checksums on every byte of data in the bag and then typically copies each of the bag's files to two distinct regions of the country.

## Ingest Restrictions

* Materials must be sent in tarred bags.
* Bag names, and the names of files within bags, may not include control characters (such as backspace, delete, etc.)
* Maximum bag size on our demo system is 5 GB
* Maximum bag size on our production system is 5 TB

You can get around the 5TB bag size limit by using bag groups and the Bag-Group-Identifier tag. See the [bagging page](../bagging/index.md) for more info.

## Reingesting Existing Bags

You can re-upload a bag any time you like to your receiving bucket, but be sure to read the page on [updates](updates.md) so you understand how APTrust processes bag updates.
