# Azure-DataFactory-SCD-Type1
This project demonstrates how to implement Slowly Changing Dimension (SCD) Type 1 using Azure Data Factory (ADF) Data Flows.

🚀 Project Overview
This project demonstrates how to implement Slowly Changing Dimension (SCD) Type 1 using Azure Data Factory (ADF) Data Flows. The project involves:<br>
Setting up an Azure Blob Storage container to store employee datasets in CSV format.<br>
Creating an Azure SQL Database to store employee records.<br>
Building an ADF pipeline to initially load employee data into SQL.<br>
Implementing SCD Type 1 using ADF Data Flows, which:<br>
Overwrites the salary of employees with updated salaries.<br>
Inserts new employees into the table.


📌 1. Setting Up Azure Resources<br>
1️⃣ Creating Azure Blob Storage<br>
Created a Storage Account in Azure.<br>
Created a Blob Container named employee-data.<br>
Uploaded the following files:<br>
Employees_Initial.csv (Initial employee data)<br>
Employees_Updated.csv (Updated employee data)


2️⃣ Creating Azure SQL Database<br>
Created an Azure SQL Database named EmployeeDB.<br>
Created a table to store employee records using the following query:<br>
CREATE TABLE Employees (<br>
    EmployeeID INT PRIMARY KEY,<br>
    Name VARCHAR(100),<br>
    Salary DECIMAL(10,2),<br>
    Location VARCHAR(100)<br>
);


📌 2. Setting Up Azure Data Factory (ADF)
1️⃣ Creating Linked Services
In Azure Data Factory, I created two linked services:

Azure Blob Storage Linked Service – Connects to my storage account.
Azure SQL Database Linked Service – Connects to my SQL database.
2️⃣ Creating Datasets
Datasets help ADF read/write data from different sources. I created:

🔹 Datasets for Azure Blob Storage
Employees_Initial_DS → Points to Employees_Initial.csv in Blob Storage.
Employees_Updated_DS → Points to Employees_Updated.csv in Blob Storage.
🔹 Dataset for Azure SQL Database
Employees_SQL_DS → Points to the Employees table in SQL Database.
📌 3. Loading Initial Employee Data into SQL
Pipeline: LoadInitialEmployees
Source → Employees_Initial_DS (from Blob Storage).
Sink → Employees_SQL_DS (to SQL Database).
Mapping → Mapped columns:
EmployeeID → EmployeeID
Name → Name
Salary → Salary
Location → Location
Executed Pipeline → Successfully inserted records into the SQL database.
📌 4. Implementing SCD Type 1 using ADF Data Flow
🔹 Step-by-Step Breakdown of the Data Flow
I created a Data Flow named SCDType1DataFlow and added the following transformations:

1️⃣ Source: Employees_Updated
Reads the updated employee dataset (Employees_Updated.csv) from Blob Storage.
2️⃣ Source: Employees_DB
Reads the existing employee records from the SQL Database.
3️⃣ Join Transformation (Left Outer Join)
Primary Stream: Employees_Updated (Latest data).
Lookup Stream: Employees_DB (Existing data).
Join Condition: Employees_Updated.EmployeeID == Employees_DB.EmployeeID.
This allows us to compare new data with existing records.
4️⃣ Derived Column Transformation
Created two new columns:
IsUpdated → if(Employees_DB.Salary != Employees_Updated.Salary, 1, 0) (Checks if salary has changed).
IsNew → if(isNull(Employees_DB.EmployeeID), 1, 0) (Checks if the employee is new).
5️⃣ Filter Transformation
Filter 1 (Updated Employees) → IsUpdated == 1 (Extracts employees with salary updates).
Filter 2 (New Employees) → IsNew == 1 (Extracts new employees).
6️⃣ Alter Row Transformation
Condition: Update if IsUpdated == 1.
Condition: Insert if IsNew == 1.
7️⃣ Sink Transformation
Sink 1 → Updates employees in SQL Database.
Sink 2 → Inserts new employees into SQL Database.
📌 5. Running & Validating the Pipeline
Ran the Pipeline → Successfully processed the updated employee data.

