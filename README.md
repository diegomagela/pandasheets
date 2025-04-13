# pandasheets

<div align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/diegomagela/pandasheets/refs/heads/main/img/logo_dark.png">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/diegomagela/pandasheets/refs/heads/main/img/logo_light.png">
    <img src="https://raw.githubusercontent.com/diegomagela/pandasheets/refs/heads/main/img/logo_light.png" alt="Logo" width="250">
  </picture>
</div>

**pandasheets** is a convenient wrapper around [gspread](https://github.com/burnash/gspread) that simplifies common operations (such as downloading, uploading, and appending) when working with Google Sheets using Pandas. 

## Getting Started

To use **pandasheets**, you need to set up a Google Cloud service account credential that grants your code access to manage spreadsheets on your behalf. Below are the steps to create and configure these credentials.

### 1. Enable API access for your project
1. Go to the [Google Cloud Console](https://console.cloud.google.com).
2. From the left-hand navigation menu, select **APIs & Services**.
3. Click **Enable APIs & Services**, then **Create project** if you don’t already have one.
4. Provide a **Project name** and **Location**, then click **Create**.

### 2. Create a Service Account Credential
1. Under **APIs & Services**, click **Credentials**.
2. Select **Create credentials** and choose **Service account**.
3. Fill out the required fields (e.g., name, description).
4. Copy the generated **email address** (it ends with `iam.gserviceaccount.com`, e.g., `myserviceaccount@my-awesome-project.iam.gserviceaccount.com`).
5. Click **Create and continue**.
6. Assign a suitable **Role** for the service account.
7. Finalize the creation process.

### 3. Generate the JSON key
1. On the **Service Accounts** page, locate the service account you just created.
2. Click on it to view the details.
3. In the top menu, select **Keys**.
4. Click **Add key** > **Create new key** > **JSON** > **Create**.
5. A JSON file will be downloaded. Store it in a secure location.

### 4. Enable the Google Sheets API
1. Return to **APIs & Services** in the [Google Cloud Console](https://console.cloud.google.com).
2. Click **Enable APIs and services**.
3. Search for and select **Google Sheets API**.
4. Click **Enable**.

### 5. Enable the Google Drive API
Repeat the process from **Step 4** to enable the **Google Drive API**.

### 6. Share your spreadsheet
Finally, open the Google Sheet you want to access and click **Share**. Enter the **service account email** you copied in **Step 2**, and grant appropriate permissions (e.g., Editor). Click **Done**.

> **Important**: Make sure the service account email has permission to access the spreadsheet and that both the **Google Sheets API** and the **Google Drive API** are enabled in your project.

## Installation

```sh
pip install pandasheets
```

## Usage

### Downloading a sheet from Google Sheets into a DataFrame

```python
import pandasheets as ps

# Define the sheet name, spreadsheet name, and path to the JSON credential file

sheet = "mySheet" # sheet to be downloaded and loaded as a DataFrame
spreadsheet = "mySpreadsheet" # Name of the spreadsheet containing the sheet
credential = "myCredential.json" # Path to the JSON credential file

# Download the sheet and load it as a DataFrame
df = ps.get_sheet_to_dataframe(
    sheet=sheet,
    spreadsheet=spreadsheet,
    credential=credential
)
```

### Uploading a DataFrame to a new sheet

```python
import pandasheets as ps

# Define your DataFrame, target sheet, spreadsheet name, and credential file

df = myDf # DataFrame to be uploaded
sheet = "mySheet" # Target sheet name for the upload
spreadsheet = "mySpreadsheet" # Name of the spreadsheet where the sheet will be created
credential = "myCredential.json" # Path to the JSON credential file

ps.upload_dataframe_to_spreadsheet(
    df=df,
    sheet=sheet,
    spreadsheet=spreadsheet,
    credential=credential
)
```
By default, the DataFrame will be uploaded formatted as:
- Bold header
- All cells in columns A to Z are set to clip text wrapping
- The first row is frozen to keep the headers visible during scrolling.

If you wish to avoid the default formatting, call the function with the `formatting` parameter set to `False`:

```python
ps.get_sheet_to_dataframe(
    df=df,
    sheet=sheet,
    spreadsheet=spreadsheet,
    credential=credential,
    formatting=False
)
```

> **Important**: If a sheet with the same name already exists in the specified spreadsheet, a `ValueError` will be raised. The target spreadsheet must exist prior to uploading the sheet, otherwise an exception will be raised. No new spreadsheet will be created automatically.

### Appending a DataFrame to an existing sheet

```python
import pandasheets as ps

# Create a sample DataFrame to be appended
df_to_append = pd.DataFram({
    "column_A": ["A1", "A2", "A3"],
    "column_B": ["B1", "B2", "B3"],
})

sheet = "mySheet" # Sheet name to which the data will be appended
spreadsheet = "mySpreadsheet" # Spreadsheet name where the sheet exists
credential = "myCredential.json" # Path to the JSON credential file

ps.append_dataframe_to_sheet(
    df=df_to_append,
    sheet=sheet,
    spreadsheet=spreadsheet,
    credential=credential
)
```

> **Important**: Only values for existing columns in the sheet are appended. Missing values in the DataFrame (present in the sheet) are set as empty strings. Duplicate values are not appended by default.

If duplicate values are acceptable and you would like to append them, set the `duplicates` parameter to `True`:

```python
ps.append_dataframe_to_sheet(
    df=df,
    sheet=sheet,
    spreadsheet=spreadsheet,
    credential=credential,
    duplicates=True
)
```



