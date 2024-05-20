# ETL_Data_Pipeline_with_Python
Using Python as a pipe to load data into data storage systems.
<img width="896" alt="etl_pipeline" src="https://github.com/Ackson507/ETL_Data_Pipeline_with_Python/assets/84422970/14db2bc0-291b-4348-9450-98e2ce94df4c">

# Buiding a Data Pipeline from file to Database
In the ETL_Automation-with-Python repository. We were doing two tasks
- Task 1: Data cleaning, trasformation and manipulaton
- Task 2: Wrting a script to automate the Task 1 and defining new funtion with Task 1 steps so that whenever we give this funtion new dataset it automatical cleans it and creates a new dataset appended with last name '_cleand.csv'
- Task 3: This is most intresting one, suppose we we want to be sending the cleaned dataset to another storage not returning as csv file but sending it to a well renouned storgae system such as database, for example like a PostGres.

### Softwares required
- Excel: This will be used to read excel and csv files
- Python V3.10.3: This is main language will use to communicate with excel files and used to connect with Postgres database
- Postgres V15.7 Database type will be using for all projects as Warehouse storage.
### STEP 1
Lets open our IDE or Code Editor, for me its Pycharm and paste the script we used in ETL_Automation-with-Python repository
```python
import pandas as pd
import numpy as np


def clean_data(file):
    #1 Read data from CSV file
    df = pd.read_csv("C:/Users/Ackson/Desktop/ETL_files/Sales_July_2019.csv")
    # Set pandas options to show all columns in a single row
    pd.set_option('display.max_columns', None)
    pd.set_option('display.expand_frame_repr', False)

    # 2 Handling Missing Values - By removing observation with missing values
    df.dropna(inplace=True)  # Dropping missing values
    df.drop_duplicates(inplace=True)  # Removing duplicates

    # 3 Changing data types
    df['Order Date'] = pd.to_datetime(df['Order Date'], errors='coerce')
    df['Quantity Ordered'] = pd.to_numeric(df['Quantity Ordered'], errors='coerce')
    df['Order ID'] = pd.to_numeric(df['Order ID'], errors='coerce')
    df['Price Each'] = pd.to_numeric(df['Price Each'], errors='coerce')

    # 4 Extract two coulums and creating new colums
    df['Date'] = pd.to_datetime(df['Order Date'].dt.date)
    df['Time'] = pd.to_datetime(df['Order Date'].dt.date)
    df['Total Sales'] = df['Quantity Ordered'] * df['Price Each']

    # 5 Droping order date and new null
    df.drop(columns=['Order Date'], inplace=True)
    df.dropna(inplace=True)

    # Export cleaned data to CSV file 
    cleaned_file = file.replace('.csv', '_cleaned.csv') #The new dataset will be exported appended with a last name "_cleaned.csv"
    df.to_csv(cleaned_file, index=False) # This line now exports the trasformed dataset to a new dataset csv
    print(f"Data cleaned and saved to {cleaned_file}")

#Example Use
Clean_data("Path of a file needed to be cleaned")
The cleaned dataset name will be exported as "Sales_July_2019_cleaned.csv"

```
![csv file](https://github.com/Ackson507/ETL_Data_Pipeline_with_Python/assets/84422970/4d1b849f-b384-457b-8233-87c91d6645f0)

### STEP 2
In the above step, we need to take note that the cleaned dataset before we export it as csv its stored in pandas as "df" datafalame. Upon ensuring that postgres is installed and running on outr local machine we import a library that allows python and postgres database to connect called:
- sqlalchemy
- psycopg2
### STEP 3
We need to create a database will be storing the data cleaned in step 1. We can do this by using SQL command in Postgres or use Python execute funtion to run sql command in python. I will can create a database called destination_db and a table with columns headers that exactly matches with the dataset because sql database is case sensitive. Now lets connect to the database and sent the cleaned file to destination_db database.
```python
#Libs
import pandas as pd
from sqlalchemy import create_engine

# Reading the file
df=pd.read_csv("C:/Users/Ackson/Desktop/ETL_files/Cleaned/Sales_August_2019_cleaned.csv")
df.head()

# Creating a connection URL format for SQLAlchemy's create_engine function
# Format: postgresql://<username>
                    :<password>@<host>
                    :<port>/<database>

engine = create_engine('postgresql://postgres:12345@localhost:5432/destination_db')


# Lets send the dataflame to a database and the already existing  table and append data
df.to_sql('august_sales_2019', engine, if_exists='append', index=False)

# Final code for the complete excecution and exporting
print("Data has been successfully loaded into the database.")

```
Done.
APPLICATIONS
- The script can be used to clean data automatically even if more data is added which is a plus for any data working tasks
- ETL purposes for extracting and loading data to a defined destination for storage Extracting or collection can be from files, API or another databases e.t.c utilizing multiple skills using Python
- We can also use workflow managament tools such as Apache airflow to automate all process and use schedulers to run this entire process on intervals. Next projext is data collection from API to same database called destination_db in postgres. Lookout for another data mining from API.

![Postgres](https://github.com/Ackson507/ETL_Data_Pipeline_with_Python/assets/84422970/ed5ef567-39ee-411a-a41d-a1f104970bba)








