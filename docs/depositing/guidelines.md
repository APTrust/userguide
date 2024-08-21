# Deposit Guidelines

This document provides guidelines for bagging and depositing content to APTrust. Many deposited bags fall outside these characteristics; members have a lot of flexibility in how and what they bag and how they deposit. As our services mature, members have been sending more, differing bags at a higher frequency. The document describes typical bag characteristics so members can more easily identify unusual deposits and notify APTrust staff for manual scaling or additional monitoring if required. **This document does not limit APTrust’s [SIP specification](index.md); members can deposit bags outside these characteristics, but we request advance notice when possible.**

* Bags are 2TB or smaller.  
* Bags contain less than 10,000 files.  
* Bags are validated before deposit.  
* The full path, including filenames, of each file within a bag is less than 1,000 characters.  
* Filenames within bags do not include non-printing characters in their path and are POSIX compliant.  
* Deposits are organized into batches containing 1,000 or fewer bags.   
* Previous deposit batches are fully ingested before sending the next batch.  
* Deposits include only new or updated content.

APTrust has designed its systems to be very efficient, using minimum resources, and able to scale rapidly on the preservation platform. For most use cases, the resources will scale automatically to the limit of the constraints placed to prevent runaway costs. However, in some use cases, bags with specific characteristics can rapidly exhaust those resources, and the resources must be manually scaled. This guidance provides use cases with context to help members scope their deposits, enabling us to mitigate risks and be good stewards of our natural resources. Over time, APTrust staff will seek to find better and more efficient methods to improve the ingest process, scale the system resources, and update this documentation accordingly. 

## Computing Resources

Preservation Services require a combination of CPU, networking, and memory across multiple steps to successfully ingest bags. Each step may need more or less of a particular resource based on the ingest stage and how the bag is packaged.

### CPU

CPU provides processing capability across all resources and is most impacted by large files or bags with many small files. This is because of the amount of data to be processed and because CPU size drives container networking capabilities. It can also impact the database by pushing thousands of small bags simultaneously.

### Networking

Networking provides communication and data transfer between resources. Performance is most noticeable when copying files to S3 at different stages during ingest. Large bags with few or large files will be most impacted, as networking is constrained by container CPU size, and the time to copy can be extended due to intermittent throttling. 

### Memory

Memory supports processing for all resources. Its most important role is as a high-performance database (Redis) handling record keeping at all processing stages through ingest. This is most impacted by bags with high numbers of files (10k+). 

## Bag Characteristics

### Bags Under 2TB

Bags no greater than 2TB are a good rule of thumb due to the CPU and network demands. The choices made by APTrust in resourcing CPU, networking, and preservation workers allow bags of many types to work steadily and in parallel through horizontal scaling and elasticity. By this, we mean that container numbers at a stage grow and shrink based on the demand.  A bag up to 2TB can take days to get through a single stage, and by scaling horizontally, other bags can be processed and ‘pass by’ the big bag.

However, these large bags take longer to process and are more likely to be exposed to different types of temporary disruption.  Containers may die, networking may eventually be throttled by AWS—slowing even more, and there may be enough network errors causing a bag to fail and require APTrust to restart them. Bags over 1TB are typical; such bags may take up to a week to ingest. Bags above 2TBs could take much longer without significant additional CPU resourcing.

Sometimes, smaller bags are assigned the same container as a big bag; when these smaller bags ride along with bigger bags, they also take much longer to ingest because they can only go as fast as the big bag can be processed. In other words, the large bag of one member may slow the ingestion of another member's small bag.

If several multi-TB bags need to be deposited, we can scale temporarily. Note that a bag has a hard limit of 5TB. 

### Bags Should Contain No More Than 10,000 Files

Each ingested file will create a record in the Redis database (Elasticache on AWS). These records each use \~2KB of data, which will expand to \~10KB each during the record stage of ingest. Thus, bags with 10k files will start at about 20MB and grow to 100MB. Traditionally, the memory size for the Redis database was 3Gbs, meaning a bag with 10k files will use \~3%  memory but should progress steadily through the ingest process without any impact. Bags with 100k files would increase to 1GB at their peak, using 33% of all the memory available to ingest. The restoration of such bags has no significant impact on Redis and Elasticache.

**If you need to deposit several bags with high file counts, please contact APTrust staff to plan, as the Elasticache cluster can be scaled temporarily.** 

### Valid Bags

APTrust provides tools ([DART](https://aptrust.org/documentation-page/dart-digital-archivists-resource-tool/), [Partner Tools](https://aptrust.org/documentation-page/partner-tools/)) so members can pre-validate bags against the [APTrust SIP specifications](index.md) before depositing. A valid BagIt bag may not necessarily be a valid APTrust BagIt bag. Pre-validating bags saves members time and saves APTrust from consuming unnecessary resources.

### Files whose Path and Name are over 1,000 Characters

Each file managed by APTrust’s preservation repository is tracked in multiple ways. When a file’s path and name are over 1,000 characters, our ability to record information may be limited.

### Filenames that include Non-Printing Characters

Bags and the names of files they contain must be POSIX compliant. Filenames containing [non-printing characters](https://en.wikipedia.org/wiki/Non-printing\_character\_in\_word\_processors) (e.g., nonbreaking space, tab, newline feed, carriage return) will fail to ingest and require manual intervention by the Depositor.

Filenames and paths can include:

* the uppercase letters (A-Z)  
* the lowercase letters (a-z)  
* the decimal digits (0-9)  
* the dot (.), the hyphen (-) and the underscore (\_)

Filenames should not begin with a hyphen, although it may appear in any other position. Specifically, the following characters should not be used in file or directory names: <pre>\! " \` ' ; / $ \< \> ( ) | { } \[ \] \~</pre>

## Deposit Characteristics

### Batches with 1,000 or Fewer Bags

Batching deposits into approximately 1,000 bags maximizes system efficiency and supports APTrust’s commitment to green sustainability. This approach requires fewer computing resources, ensuring we are good stewards of our environment. When developing systems integrations, please avoid sending all objects at once.

### Concurrent Batches

Because batches and their bags can vary, please wait until one batch is fully ingested before sending another. Oversized bags (bags with a small amount of large files and bags with a large amount of small files) take longer to ingest, sometimes up to a week or longer. 

### New or Updated Content Only

Sending duplicate bags to APTrust consumes unnecessary resources. This incurs higher operating costs, causes other deposits to take longer to ingest, and creates a higher environmental impact (more power and water consumption). In the early days of APTrust, ingest volumes were low, and it was inconsequential to send an entire repository regularly, letting APTrust sort out what was new and what wasn’t. However, we no longer operate in this context.