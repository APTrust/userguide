# Quick Start

## Logging In

You can log in to Registry, APTrust's online registry, at the URLs below:

* __Staging Repository__ - https://staging.aptrust.org
    - Staging is for very new, potentially unstable features.
    - Staging is currently the only system that accepts Beyond the Repository (BTR) formatted bags.
    - Staging does not preserve anything for more than 7 days.
* __Test/Demo Repository__ - https://demo.aptrust.org
    - Demo is for you to test your bagging process and for APTrust to test new features that are considered stable.
    - Demo does not guarantee preservation of anything beyond a few days.
* __Production Repository__ - https://repo.aptrust.org
    - Production is for materials you want to preserve for the long term.

## Adding Users

See the [User Management](registry/user_management.md) page for info on how to add users to your institution's Registry account.

## Making Your First Deposit

We suggest you make your first deposits in the [demo repository](https://demo.aptrust.org), so you can test out your workflows and get a feel for how Registry works.

### Depositing with DART

The easiest way to make your first APTrust deposit is to use DART, which enables drag-and-drop deposits.

DART runs on Windows, Mac, and Linux. To install DART and run your first ingest job, see the [DART user guide](https://aptrust.github.io/dart-docs/users/getting_started/).

DART allows you to test test out different bagging and uploading options through a simple point-and-click interface. Once you find the combination that works for you, you can save it as a [Workflow](https://aptrust.github.io/dart-docs/users/workflows/), and then you can run that workflow from within DART, or from the command line. You can also incorporate DART's command-line workflows into larger scripted workflows using languages like Ruby, Python, PHP, JavaScript or even bash shell scripts.

__Note:__ Some members have issues using DART to upload large bags (400GB or larger) because of local networking infrastructure. If you encounter this, we recommend using DART to create bags and using a third-party S3 client (e.g., [CyberDuck](https://cyberduck.io/){:target="_blank"}, [S3 Browser](https://s3browser.com/){:target="_blank"}) to upload them. 

### Exporting from Fedora

If your local repository uses Fedora, you can [export APTrust-compliant bags directly from Fedora](https://github.com/fcrepo4-labs/fcrepo-import-export/blob/master/README.md#running-the-importexport-utility-with-a-bagit-support) and then upload them to your receiving bucket for ingest.

After Fedora produces the bag, you can use APTrust [apt_upload tool](partner_tools.md) to upload the bag to your receiving bucket. From there, APTrust ingest services will pick it up for processing, and you can track its progress in the [Registry Work Items](registry/work_items.md) list.

### Custom Bagging

If you want to bag materials yourself, be sure your bags conform to the [APTrust bagging requirements](bagging/index.md). After bagging, you can use APTrust's [apt_upload tool](partner_tools.md) or other publicly available tools such as the [MinIO client](https://docs.min.io/docs/minio-client-complete-guide) to upload your bag.

### REST API Integration

If you're interested in automating deposits, restorations and other preservation activities, see our [REST API](registry/rest_api.md) and [Swagger Docs](https://aptrust.github.io/registry/){target=_blank}
