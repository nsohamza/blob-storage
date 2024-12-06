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
