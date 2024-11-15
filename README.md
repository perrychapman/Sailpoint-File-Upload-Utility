# Directory Creation and SailPoint File Upload Utility

This document provides detailed instructions for setting up and running the `DirectoryCreateScript.ps1` to configure required directories and settings, followed by using the `FileUploadScript.ps1` to process and upload data files to SailPoint. It includes steps for scheduling the upload script as a Windows Task, system requirements, operational details, and common troubleshooting steps.

---

## Table of Contents

1. [System Requirements](#system-requirements)
2. [Setup Instructions](#setup-instructions)
3. [Running the File Upload Script](#running-the-file-upload-script)
4. [Task Scheduler Setup](#task-scheduler-setup)
5. [Config.json Structure](#configjson-structure)
6. [Operation Details](#operation-details)
7. [Troubleshooting](#troubleshooting)

---

## System Requirements

### Hardware

- Windows Operating System (Windows 10 or higher recommended)
- At least 4GB RAM and 1GB free disk space

### Software

- PowerShell 7+ (Required for `FileUploadScript.ps1`)
- Administrator permissions to run PowerShell scripts
- Internet connection for API calls to SailPoint
- Java Runtime Environment (JRE) version 11 or later
- [ImportExcel PowerShell Module](https://github.com/dfinke/ImportExcel) (Automatically installed by the script if not already installed)

### Files Required

- `DirectoryCreateScript.ps1` (for initial setup)
- `settings.json` (created during setup by the Directory Creation script)
- `FileUploadScript.ps1` (for data processing and SailPoint uploads)
- `config.json` (required for each app folder)

---

## Setup Instructions

### Step 1: Run the Directory Creation Script

1. Open PowerShell as Administrator.
2. Navigate to the directory where `DirectoryCreateScript.ps1` is saved.
3. Execute the script:

   ```powershell
   .\DirectoryCreateScript.ps1
   ```

4. Follow the on-screen prompts to configure settings.

### Step 2: Verify Settings

- After the script runs, a `settings.json` file is created in the script's directory.
- Open `settings.json` to confirm the details are correct. Example `settings.json`:

   ```json
   {
       "ParentDirectory": "C:\Powershell\FileUploadUtility",
       "AppFolder": "C:\Powershell\FileUploadUtility\Import",
       "FileUploadUtility": "C:\Powershell\sailpoint-file-upload-utility-4.1.0.jar",
       "ClientSecret": "YourSecretHere",
       "ClientID": "YourClientIDHere",
       "tenant": "YourTenantHere",
       "isDebug": false
   }
   ```

---

## Running the File Upload Script

### Manual Execution

1. Open PowerShell and navigate to the directory where `FileUploadScript.ps1` is located.
2. Run the script:

   ```powershell
   .\FileUploadScript.ps1
   ```

---

## Task Scheduler Setup

### Step 1: Open Task Scheduler

1. Press `Win + R`, type `taskschd.msc`, and press Enter.
2. Create a new task and configure it as follows:

### Step 2: Configure General Settings

- **Name**: SailPoint File Upload
- **Run whether user is logged on or not**
- **Run with highest privileges**

### Step 3: Configure Triggers

- Set the frequency (e.g., Daily, Weekly) and start time.

### Step 4: Configure Actions

- **Program/script**: `powershell.exe`
- **Arguments**: `-File "C:\Path\To\FileUploadScript.ps1"`

### Step 5: Configure Conditions

- Uncheck `Start the task only if the computer is on AC power` to allow the task to run on battery power.

### Step 6: Save and Test

1. Save the task.
2. Test the task by right-clicking it in Task Scheduler and selecting **Run**.

---

## Config.json Structure

Each app folder must contain a `config.json` file to define the file processing and upload behavior.

### Example Config.json

```json
{
    "sourceID": "12345",
    "isMonarch": false,
    "disableField": "Status",
    "disableValue": ["Inactive", "Disabled"],
    "groupTypes": "Group,Department",
    "groupDelimiter": ",",
    "isUpload": true,
    "headerRow": 1,
    "trimTopRows": 2,
    "trimBottomRows": 0,
    "trimLeftColumns": 1,
    "trimRightColumns": 0,
    "dropColumns": "Column1,Column2",
    "columnsToMerge": "FirstName,LastName",
    "mergedColumnName": "FullName",
    "adminColumnName": "Role",
    "adminColumnValue": "Admin"
}
```

### Explanation of Config.json Fields

| Field                          | Description                                                |
|--------------------------------|------------------------------------------------------------|
| `sourceID`                     | Unique identifier for the app in SailPoint.                |
| `isMonarch`                    | Set to `true` if the app uses Monarch for file processing. |
| `disableField`                 | Name of the column to identify inactive users.             |
| `disableValue`                 | Array of values in `disableField` that signify inactivity. |
| `groupTypes`                   | Columns containing group/entitlement information.          |
| `groupDelimiter`               | Delimiter to split group values.                          |
| `isUpload`                     | Upload the processed file if `true`.                      |
| `headerRow`                    | Row number where the file header begins.                  |
| `trimTopRows`, `trimBottomRows`| Rows to remove from the file.                              |
| `trimLeftColumns`, `trimRightColumns` | Columns to remove from the file.                    |
| `dropColumns`                  | Columns to exclude from processing.                       |
| `columnsToMerge`               | Columns to merge into a single column.                    |
| `mergedColumnName`             | Name of the merged column.                                |
| `adminColumnName`, `adminColumnValue` | Define role assignment logic.                      |

---

## Operation Details

### Key Features

- **Data Trimming**: Supports trimming rows and columns from imported files.
- **Role Assignment**: Assign roles (`Admin` or `User`) based on specified column values.
- **File Processing**: Processes CSV, Excel, and TXT files.
- **SailPoint Upload**: Uses APIs to upload processed files.
- **File Archival**: Automatically archives original and processed files.

### Logs

- **Execution Logs**: Stored in the `ExecutionLogDir` specified in `settings.json`.
- **App Logs**: Specific to each app directory, stored in the `Log` subdirectory.

---

## Troubleshooting

### Settings Not Saved

- Ensure the `DirectoryCreateScript.ps1` was run as Administrator.

### Missing ImportExcel Module

- Ensure you have an internet connection and permission to install PowerShell modules.

### API Authentication Fails

- Double-check the **Client ID**, **Client Secret**, and **Tenant** values in `settings.json`.

### File Not Uploaded

- Check the app’s log file for errors.

### Task Scheduler Task Does Not Run

- Ensure the task is set to run with the highest privileges.
- Check the Task Scheduler history for error messages.

---
