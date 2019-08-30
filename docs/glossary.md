# Core Concepts

Before proceeding with this documentation, we'd like to clarify a few core concepts and essential terms.

## Generic File

A single file or bitstream that makes up part of an intellectual object. For example, in a collection of jpeg photos that includes an XML metadata file, each jpeg and the XML file is a generic file.

## Intellectual Object

A collection of generic files logically grouped into a single unit. An intellectual object typically consists of the payload files and tag files submitted in a bag.

## Pharos

Pharos is the searchable online registry that keeps track of everything you've deposited in APTrust. It includes a web UI and a REST API that enable you to do the following:

* See what items you've deposited
* Search for items by object name, file name, and a number of other attributes
* View audit data (PREMIS events) for items you've deposited
* Request that objects and/or files be restored
* Request that objects and/or files be deleted (Does the REST API support this?)
* Query the status of ingest and restoration requests in progress
* Add and remove user accounts for your institution

Pharos includes both a demo and production system. The demo system is for depositors to test new workflows and to get familiar with the system's general features. The production system is for long-term preservation.

**Pharos Demo URL**: https://demo.aptrust.org

**Pharos Production URL**: https://repo.aptrust.org


!!! info "API Keys and Separate Systems"
    Once you have a valid Pharos login, you can generate your own API key to use the REST API. [Link when available...] Keep in mind that while your login email may be the same for both the demo and production Pharos systems, your API keys will be different.


## Receiving Bucket

APTrust provides two receiving buckets for each depositor: one for the demo environment and one for the production environment. A receiving bucket is an Amazon AWS S3 bucket to which you upload materials for ingest into APTrust. Upload bags to your demo receiving bucket to ingest them into the demo system, and to the production bucket to ingest them into the production system.

Receiving bucket names follow this pattern:

**Demo System**: aptrust.receiving.test.&lt;your-domain.xyx&gt;

**Production System**: aptrust.receiving.&lt;your-domain.xyz&gt;

For example, if your domain name is example.org, your receiving buckets will be aptrust.receiving.test.example.or for demo and aptrust.receiving.example.org for production.

## Restoration Bucket

When you ask for APTrust to restore an object, our system reassembles all of the object's files into a BagIt bag and copies it to your receiving bucket. (Individual files can be restored the same way, though they are not bagged.) The restoration process may take anywhere from a few seconds to several hours, depending on the amount of data restored and where it is stored. Once the restoration is complete, you can download the restore object or files from your restoration bucket.

!!! info "You Need AWS Access Keys to Use Your Buckets!"
    You will need to get AWS credentials from APTrust to access your receiving and restoration buckets. APTrust sends them as part of the onboarding process when you first set up your account. If you need credentials, contact help@aptrust.org.

## DART

DART is the Digital Archivist's Resource Tool. The initial release of DART 2.0 provides a drag-and-drop interface for bagging files and sending them to APTrust. DART is the easiest way to complete your first APTrust Deposit.

To get started with DART, visit https://aptrust.github.io/dart-docs/users/getting_started/.
