# customer_database
Links the SQL to python code to automatically store data of the customer in the SQL.

import pyodbc
 
# Database connection setup
# Change SERVER and DATABASE to match your SQL Server setup
connection = pyodbc.connect(
    'DRIVER={ODBC Driver 17 for SQL Server};'
    'SERVER=localhost;'       # or use 'SERVER=YourServerName'
    'DATABASE=CustomerDB;'    # make sure you create this DB in SQL Server
    'Trusted_Connection=yes;' # if using Windows Authentication
)
 
cursor = connection.cursor()
 
# Create table if it doesn't exist
cursor.execute("""
IF NOT EXISTS (SELECT * FROM sysobjects WHERE name='Customers' AND xtype='U')
CREATE TABLE Customers (
    CustomerID INT IDENTITY(1,1) PRIMARY KEY,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    Email NVARCHAR(100),
    Phone NVARCHAR(20)
)
""")
connection.commit()
 
print("Customers table is ready.")
 
# Function to insert customer data
def add_customer(first_name, last_name, email, phone):
    cursor.execute("""
    INSERT INTO Customers (FirstName, LastName, Email, Phone)
    VALUES (?, ?, ?, ?)
    """, (first_name, last_name, email, phone))
    connection.commit()
    print(f"Saved: {first_name} {last_name}")
 
# Example usage
if __name__ == "__main__":
    while True:
        print("\n--- Add New Customer ---")
        fname = input("Enter First Name: ")
        lname = input("Enter Last Name: ")
        email = input("Enter Email: ")
        phone = input("Enter Phone: ")
 
        add_customer(fname, lname, email, phone)
 
        more = input("Add another customer? (y/n): ")
        if more.lower() != "y":
            break
 
# Close connection at the end
connection.close()
print("Database connection closed.")
