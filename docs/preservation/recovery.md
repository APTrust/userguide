# Recovery

When a fixity check fails, APTrust sends an alert to Institutional Admin users and APTrust Admins. APTrust does **not** have an automated self-healing process for fixity failures. The recovery process depends on the storage class and the location of the affected object.

## High Assurance

- This class includes two copies:
    - **Hot copy (S3)** – used for regular access and fixity checks.
    - **Cold copy (Glacier)** – used as a second copy.

- Fixity checks are performed on the hot (S3) copy.
- If a fixity check fails:
    1. Contact APTrust support.
    2. We will work with you to restore the Glacier copy.
    3. We'll verify the Glacier copy's fixity.
    4. If valid, we’ll replace the corrupted S3 copy with the Glacier version and record a PREMIS event.

## Basic Archive and Deep Archive

- These classes store **a single copy only**.
- If a fixity check fails, the object cannot be recovered by APTrust.
- To remediate:
    1. Deposit a new version of the object.
    2. Use an [update bag](updates.md) to replace the corrupted file in preservation storage. You only need to include the impacted file (keeping the same file name and path).