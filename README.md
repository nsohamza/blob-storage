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


# **Exercise - Move Azure Storage blobs using AzCopy**

You decided to evaluate the AzCopy command as an alternative to using the Azure CLI. 

As before, you want to move blobs that contain the details for product specifications, and that are more than six months old, to an archive store in a separate Blob Storage account.

Create environment variables for your storage account name and region. Replace `<location>` with a region from the previous list

`HOT_STORAGE_NAME=hotstorage$RANDOM
COOL_STORAGE_NAME=coolstorage$RANDOM
LOCATION=<location>`

**Specific variables being created:**
HOT_STORAGE_NAME and COOL_STORAGE_NAME:

- These variables will store unique names for two different storage accounts.
- The `$RANDOM` part generates a random number, ensuring the names are unique each time the script is run.
- This is important because storage account names must be globally unique in Azure.

**Purpose of environment variables:**
Environment variables are used to store configuration settings and other values that can be easily accessed by scripts and applications. They're particularly useful for:
a) Keeping sensitive information out of your code
b) Making scripts more flexible and portable
c) Simplifying configuration management

- Hot Storage:
    - Optimized for frequently accessed data
    - Higher storage costs, but lower access costs
    - Ideal for data that is accessed or modified regularly
    - Examples: Active customer data, current projects, frequently accessed media files
- Cool Storage:
    - Designed for infrequently accessed data that needs to be stored for at least 30 days
    - Lower storage costs, but higher access costs compared to hot storage
    - Suitable for short-term backup and older data not used actively but might be needed occasionally
    - Examples: Short-term backups, older media content, archived documents still needed for reference

Next, run the following command to create a storage account to hold blobs.

`az storage account create --location $LOCATION --name $HOT_STORAGE_NAME --resource-group "learn-bcac2534-15b7-4042-a53a-54bf6fa6cf7f" --sku Standard_RAGRS --kind BlobStorage --access-tier Hot`

Create a storage account for holding the archived blobs. Use the **Cool** access tier. As before, specify an appropriate region, and choose a unique name for your storage account.

`az storage account create --location $LOCATION --name $COOL_STORAGE_NAME --resource-group "learn-bcac2534-15b7-4042-a53a-54bf6fa6cf7f" --sku Standard_RAGRS --kind BlobStorage --access-tier Cool`

## **Setup**

Let's start by downloading the latest version of AzCopy

Let's also generate SAS tokens for our storage accounts and then upload some sample data.

In the Cloud Shell window, run the following commands to download and extract the most recent version of *AzCopy* for Linux.

`wget -O azcopy.gz https://aka.ms/downloadazcopy-v10-linux
gunzip azcopy.gz
tar xvf azcopy`

1. Select [this link](https://portal.azure.com/learn.docs.microsoft.com) to sign in to the Azure portal. This link signs you in to the Azure portal by using the same account with which you activated the sandbox.
2. Select **All resources**, and select your destination (cool) storage account.
3. On the storage account page, under **Security + networking**, select **Shared access signature**.
4. On the shared access signature page, check **Container** and **Object** under the **Allowed resource types** section.
5. With the allowed resource types checked, select **Generate SAS and connection string** at the bottom of the shared access signature page.
6. In the Cloud Shell, create an environment variable and assign the generated **SAS token** value from the portal.
- `COOL_SAS_TOKEN="<token from portal>”`
1. Repeat these steps, and assign a generated SAS token for the hot storage account to a variable named **HOT_SAS_TOKEN**.
- `HOT_SAS_TOKEN="<token from portal>”`

### **Create a container for holding archived blobs**

Here’s a step-by-step breakdown of what’s happening in the commands you provided:

1. **Navigate to AzCopy Directory:**
- Navigate to the directory that contains *AzCopy* utility,
    - `cd azcopy_linux_amd64_<version>`: Move to the folder where the AzCopy utility is located (replace `<version>` with the actual version).
1. **Create Cool Storage Container:**
    - `./azcopy make https://$COOL_STORAGE_NAME.blob.core.windows.net/azcopy-archive$COOL_SAS_TOKEN`: Create a container named "azcopy-archive" in the **cool** storage tier, which is cheaper but slower to access.
2. **Create Hot Storage Container:**
    - `./azcopy make https://$HOT_STORAGE_NAME.blob.core.windows.net/specifications$HOT_SAS_TOKEN`: Create another container called "specifications" in **hot** storage, meant for frequently accessed files.
3. **Upload Files to Hot Storage:**
    - `./azcopy copy "../sample/specifications/*" "https://$HOT_STORAGE_NAME.blob.core.windows.net/specifications$HOT_SAS_TOKEN"`: Upload multiple specification files to the "specifications" container in hot storage.
4. **List Blobs in Hot Storage:**
    - `az storage blob list`: Display a list of blobs and their last modified times from the "specifications" container in the hot storage account.
5. **Copy Old Blob to Cool Storage:**
    - `./azcopy copy https://$HOT_STORAGE_NAME.blob.core.windows.net/specifications/specification01.md$HOT_SAS_TOKEN https://$COOL_STORAGE_NAME.blob.core.windows.net/azcopy-archive$COOL_SAS_TOKEN`: Transfer a specific file (e.g., `specification01.md`) from hot storage to cool storage.
6. **View AzCopy Job Status:**
    - `./azcopy jobs list`: Show the list of jobs (file transfers) that have run or are running.
    - `./azcopy jobs show <jobid>`: Check the status of a specific job by entering its job ID.
7. **Verify Blob in Cool Storage:**
    - `./azcopy list https://$COOL_STORAGE_NAME.blob.core.windows.net/azcopy-archive$COOL_SAS_TOKEN`: List the blobs stored in the "azcopy-archive" container to verify the transfer.
8. **Delete Blob from Hot Storage:**
    - `./azcopy remove https://$HOT_STORAGE_NAME.blob.core.windows.net/specifications/specification01.md$HOT_SAS_TOKEN`: Delete the original file (`specification01.md`) from the hot storage to free up space.

### SUMMARY:

Create environment variables for my storage account and region. one location set up for both. 

- Now $random gives us unique name for the storage accounts and they are stored in the environmental variables hot and cold.
- We stored them in these environmental variables so they can be easily accessed everywhere

Create storage account that holds blobs. Hot tier access. 

Create storage account that holds archived blobs. Cool tier access

Dowload latest version of Azcopy

Generate SAS tokens by:

- Go to azure portal.
- Select cool storage account destination, security and networking, under shared access signature.
- Select container and object under allowed resource types
- Do the same for hot storage account, repeat same steps.
- place sas tokens in variables.

Navigate to azcopy/azcopy utility directory 

use the latest version. 

Create storage container for cold storage

Create storage container for hot storage 

Uploads files to hot storage

check list of files in hot storage

Transfer file from hot storage to cold storage.

veiw azcopy job status 

verify blob in cool storage

Delete the original file in hot storage to free up space, now you have copied it to the cool storage and archived. Its in there.


