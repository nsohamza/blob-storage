BLOB-STORAGE
# COPY AND MOVE BLOBS FROM ONE CONTAINER OR STORAGE ACCOUNT TO ANOTHER USING THE AzCopy COMMAND

# INTRODUCTION
Azure Blob Storage provides a convenient space for storing large quantities of data in a safe, reliable manner.

You can use blobs to hold all types of data, 
- ranging from individual files and graphics to an entire database.
Blob storage is useful as a store for active data, but is also ideally suited for acting as low-cost archive storage.

Your company decides to move any inactive documents older than six months to a separate storage location.
You want to use a new Azure Storage account for archiving.
`#The AzCopy utility was written specifically for transferring data into, out of, and between Azure Storage accounts.`

A key strength of AzCopy over the Azure CLI is that all operations run asynchronously, and they're recoverable.
AzCopy tracks the progress of copy operations, and if an operation fails, it can be restarted close to the point of failure.

As with the Azure CLI, AzCopy makes use of the Azure Storage service to transfer blobs between storage accounts.

# Moving azure storage blobs using 

# Az-Copy

The **AzCopy** command is a command-line utility specially created and optimized for moving data in and out of Azure Storage. 
`Az-copy is a command line tool used to upload, download and manage data in azure storage.` 

Running AzCopy, you can bulk-transfer from the local file system of an on-premises computer to the cloud.

AzCopy performs its operations asynchronously, and can use multiple concurrent threads to read and write data. Additionally, it's fault-tolerant. If a transfer is interrupted for some reason and fails, AzCopy can resume the operation from where it left off after the situation is resolved.

The latest AzCopy version offers two key features for managing Azure Blob Storage:

1. Incremental backups: This allows you to back up only the changes made since the last backup, rather than copying all data every time. It saves time and resources by focusing on what's new or modified.
2. Synchronization between accounts: You can keep two Blob Storage accounts in sync, ensuring they contain the same version of data. This is useful for maintaining consistency across different storage locations.

**Basic Usage:**

- You use AzCopy by typing commands in the command line (terminal).
- The basic command structure is: `azcopy copy [source] [destination]`