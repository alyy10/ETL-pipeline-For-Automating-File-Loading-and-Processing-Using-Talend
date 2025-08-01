# ETL Pipeline for Automating File Loading and Processing Using Talend

This project builds an automated ETL (Extract, Transform, Load) pipeline using Talend Open Studio to process multiple sales order files daily. It eliminates manual data handling by extracting data from CSV files, transforming it with MySQL reference data, loading it into a database, and archiving processed files. The pipeline is scheduled to run automatically via Windows Task Scheduler, ensuring efficiency, accuracy, and scalability.

## Table of Contents

- [Project Overview](#project-overview)
- [Prerequisites](#prerequisites)
- [Project Setup](#project-setup)
- [ETL Pipeline Workflow](#etl-pipeline-workflow)
- [Talend Components Used](#talend-components-used)
- [Running the Pipeline](#running-the-pipeline)
- [Scheduling the Job](#scheduling-the-job)
- [Benefits](#benefits)
- [Contributing](#contributing)
- [License](#license)

## Project Overview

The project automates the processing of sales order data files (CSVs) that arrive daily in a folder. Manual processing is time-consuming and error-prone, so this ETL pipeline:

- Extracts data from CSV files and a MySQL database.
- Transforms it by joining with reference data, applying calculations, and adding metadata.
- Loads the transformed data into a MySQL database.
- Archives processed files to prevent reprocessing.
- Automates the process using Windows Task Scheduler.

This solution improves data accuracy, reduces processing time, and scales to handle increasing file volumes.

## Prerequisites

Before setting up the project, ensure you have:

- Talend Open Studio for Data Integration (version 7.3.1 or later) installed. Download [here](https://www.talend.com/products/talend-open-studio/).
- Java 8 or higher (Talend is Java-based).
- MySQL Database with:
  - A `state_map` table containing state names and abbreviations.
  - A target table for loading transformed data.
  - JDBC driver for MySQL connectivity.
- Operating System: Windows (for Task Scheduler integration).
- Hardware: Minimum 2 GB RAM, 2 GB disk space.
- Input Folder: A directory for incoming CSV files (e.g., `C:/InputFiles/`).
- Archive Folder: A directory for processed files (e.g., `C:/ArchiveFiles/`).
- Sample CSV files with sales data (columns: `Order_Date`, `Buyer_Region`, `ProfitPercent`, etc.).

## Project Setup

### Clone the Repository:

```bash
git clone https://github.com/alyy10/ETL-pipeline-For-Automating-File-Loading-and-Processing-Using-Talend.git
cd ETL-pipeline-For-Automating-File-Loading-and-Processing-Using-Talend
```

### Install Talend Open Studio:

- Follow the installation instructions for your OS.
- Ensure Java 8 is configured in the system's PATH.

### Set Up MySQL:

- Create a database (e.g., `sales_db`).
- Create a `state_map` table:

```sql
CREATE TABLE state_map (
  state_name VARCHAR(50),
  state_abbreviation VARCHAR(2),
  PRIMARY KEY (state_name)
);
```

- Insert sample data (e.g., `INSERT INTO state_map VALUES ('California', 'CA');`).
- Create a target table for output data (schema to match transformed data).
- Install the MySQL JDBC driver in Talend (add to `lib/java` folder).

### Prepare Input and Archive Folders:

- Create directories (e.g., `C:/InputFiles/` and `C:/ArchiveFiles/`).
- Place sample CSV files in the input folder.

## ETL Pipeline Workflow

The pipeline processes sales order CSV files daily, following these steps:

1. **Scan Input Folder**: Identifies all CSV files in the input directory.
2. **Extract Data**: Reads each CSV file and pulls reference data from MySQL.
3. **Transform Data**: Joins CSV data with MySQL data, applies calculations, and adds metadata.
4. **Load Data**: Writes transformed data to a MySQL table.
5. **Archive Files**: Moves processed files to an archive folder.
6. **Automate**: Runs daily via Windows Task Scheduler.

## Talend Components Used

The pipeline uses the following Talend components:

- **`tFileList`**: Scans the input folder and iterates over CSV files.
- **`tFileInputDelimited`**: Reads CSV files into structured data (e.g., `Order_Date`, `Buyer_Region`, `ProfitPercent`).
- **`tDBInput`**: Extracts reference data (state abbreviations) from MySQL's `state_map` table.
- **`tMap`**: Joins CSV and MySQL data using `Buyer_Region`, rounds `ProfitPercent` (e.g., `Math.round(Double.parseDouble(row1.ProfitPercent) * 1.90.0) / 100.0`), and adds `filename` and `load_date`.
- **`tDBOutput`**: Loads transformed data into a MySQL target table in batch mode.
- **`tFileCopy`**: Moves processed files to the archive folder.

## Running the Pipeline

### Open Talend Open Studio:

Launch Talend and import the project from the cloned repository (File > Import > Existing Project).

### Configure Components:

- Set the input folder path in `tFileList` (e.g., `C:/InputFiles/`).
- Define the CSV schema in `tFileInputDelimited` (match your file structure).
- Configure MySQL connections in `tDBInput` and `tDBOutput` (host, port, username, password).
- Set the archive folder path in `tFileCopy` (e.g., `C:/ArchiveFiles/`).

### Run the Job:

- Open the job in Talend's Job Designer.
- Click "Run" to execute the pipeline.
- Verify output in the MySQL target table and check the archive folder for moved files.

## Scheduling the Job

To automate the pipeline:

### Build the Job:

- In Talend, right-click the job and select "Build Job."
- Export as a standalone ZIP file containing a Windows batch file (e.g., `Multfile_Loading_run.bat`).

### Schedule with Windows Task Scheduler:

- Open Task Scheduler (`taskschd.msc`).
- Create a Basic Task:
  - **Name**: "ETL Pipeline"
  - **Trigger**: Daily (e.g., 8 AM)
  - **Action**: Start a Program
  - **Program**: Path to the batch file (e.g., `C:/Project/Multfile_Loading_run.bat`)
- Save and test the schedule to ensure it runs automatically.

## Benefits

- **Time Savings**: Automates file processing, reducing manual effort.
- **Error Reduction**: Minimizes human errors in data handling.
- **Scalability**: Handles increasing file volumes efficiently.
- **Reliability**: Scheduled execution ensures consistent processing.
- **Data Quality**: Transformations ensure clean, enriched data.

## Contributing

Contributions are welcome! To contribute:

1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/your-feature`).
3. Commit changes (`git commit -m "Add your feature"`).
4. Push to the branch (`git push origin feature/your-feature`).
5. Open a Pull Request.

Please ensure your code follows Talend best practices and includes clear documentation.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
