# Quick Start

## Logging In

You can log in to Pharos, APTrust's online registry, at the URLs below:

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

See the [User Management](/pharos/user_management) page for info on how to add users to your institution's Pharos account.

## Making Your First Deposit

We suggest you make your first deposits in the [demo repository](https://demo.aptrust.org), so you can test out your workflows and get a feel for how Pharos works.

### Depositing with DART

The easiest way to make your first APTrust deposit is to use DART, which enables drag-and-drop deposits.

DART runs on Windows, Mac, and Linux. To install DART and run your first ingest job, see the [DART user guide](https://aptrust.github.io/dart-docs/users/getting_started/).

DART allows you to test test out different bagging and uploading options through a simple point-and-click interface. Once you find the combination that works for you, you can save it as a [Workflow](https://aptrust.github.io/dart-docs/users/workflows/), and then you can run that workflow from within DART, or from the command line. You can also incorporate DART's command-line workflows into larger scripted workflows using languages like Ruby, Python, PHP, JavaScript or even bash shell scripts.

### Exporting from Fedora

If your local repository uses Fedora, you can [export APTrust-compliant bags directly from Fedora](https://github.com/fcrepo4-labs/fcrepo-import-export/blob/master/README.md#running-the-importexport-utility-with-a-bagit-support) and then upload them to your receiving bucket for ingest.

After Fedora produces the bag, you can use APTrust [apt_upload tool](/partner_tools/) to upload the bag to your receiving bucket. From there, APTrust ingest services will pick it up for processing, and you can track its progress in the [Pharos Work Items](/pharos/work_items/) list.

### Custom Bagging

If you want to bag materials yourself, be sure your bags conform to the [APTrust bagging requirements](/bagging/). After bagging, you can use APTrust's [apt_upload tool](/partner_tools/) or other publicly available tools such as the [MinIO client](https://docs.min.io/docs/minio-client-complete-guide) to upload your bag.

### REST API Integration

If you're interested in automating deposits, restorations and other preservation activities, see our [REST API](../pharos/rest_api) and [Swagger Docs](https://aptrust.github.io/pharos/){target=_blank}
