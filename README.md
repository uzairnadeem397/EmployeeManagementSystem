# Employee Management System in C#

A comprehensive Employee Management System (EMS) built with C# and Windows Forms. This desktop application allows for user authentication, managing employee records (CRUD operations), handling employee salaries, and viewing dashboard summaries. Data is persisted using a SQL Server LocalDB (`.mdf` file.

## Features

*   **User Authentication:** Secure Login and Registration system.
*   **Dashboard:** Overview of total, active, and inactive employees.
*   **Employee Management:**
    *   Add, View, Update, and (Soft) Delete employee records.
    *   Import and associate images with employees.
    *   Data displayed in a sortable grid.
*   **Salary Management:** View and update employee salaries for active employees.
*   **Data Persistence:** Uses SQL Server LocalDB for data storage.
*   **Modular Design:** Employs UserControls for different application sections (Dashboard, Add Employee, Salary).
*   **Intuitive Navigation:** Easy-to-use interface for switching between modules.

## Technologies Used

*   **Language:** C#
*   **Framework:** .NET Framework (Windows Forms)
*   **IDE:** Microsoft Visual Studio 2019 (or later)
*   **Database:** SQL Server LocalDB (`.mdf` file)
*   **Data Access:** ADO.NET (`SqlConnection`, `SqlCommand`, etc.)

## Setup and Running the Project

1.  **Prerequisites:**
    *   Microsoft Visual Studio 2019 or later.
    *   .NET Desktop Development workload installed with Visual Studio.
    *   SQL Server Express LocalDB (usually installed with Visual Studio).

2.  **Clone the Repository:**
    ```bash
    git clone https://github.com/marcoman2/Employee-Management-System-in-CSharp.git
    cd Employee-Management-System-in-CSharp
    ```

3.  **Open in Visual Studio:**
    *   Open the `EmployeeManagementSystem.sln` file in Visual Studio.

4.  **Database Configuration:**
    *   The project uses an `.mdf` database file. The connection string in the C# code (`AddEmployee.cs`, `Dashboard.cs`, `EmployeeData.cs`, `Form1.cs`, `RegisterForm.cs`, `Salary.cs`, `SalaryData.cs`) points to an absolute path:
        `@"Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=C:\Users\WINDOWS 10\Documents\employee.mdf;Integrated Security=True;Connect Timeout=30"`
    *   **You WILL LIKELY NEED to modify this path.**
        *   Ensure the `employee.mdf` file (expected to be in a `Documents` folder as per the path) exists or is placed where you want it.
        *   Update the `AttachDbFilename` part of the connection string in all relevant `.cs` files to point to the correct location of your `employee.mdf` file.
        *   Alternatively, you can attach the `.mdf` file to your SQL Server LocalDB instance using SQL Server Management Studio (SSMS) or Visual Studio's SQL Server Object Explorer and then update the connection string to use `Initial Catalog=YourDbName` instead of `AttachDbFilename`.

5.  **Image Directory Configuration:**
    *   The application saves employee images to a hardcoded directory:
        `@"C:\Users\WINDOWS 10\source\repos\EmployeeManagementSystem\EmployeeManagementSystem\Directory\"` (in `AddEmployee.cs`).
    *   Ensure this directory exists, or modify the path in `AddEmployee.cs` to your preferred location. The application will attempt to create it if the base path is accessible.

6.  **Build the Solution:**
    *   In Visual Studio, go to `Build > Build Solution` (or press `Ctrl+Shift+B`).

7.  **Run the Application:**
    *   Press `F5` or click the "Start" button in Visual Studio.
    *   Register a new user first, then log in.

## Key Code Snippet: Adding an Employee

```csharp
// From AddEmployee.cs - (Simplified from addEmployee_addBtn_Click)
// ... (inside button click and after validation & duplicate check)
DateTime today = DateTime.Today;
string insertData = "INSERT INTO employees " +
    "(employee_id, full_name, gender, contact_number, position, image, salary, insert_date, status) " +
    "VALUES(@employeeID, @fullName, @gender, @contactNum, @position, @image, @salary, @insertDate, @status)";

// Image path construction (Note: Hardcoded base path)
string imageFileName = addEmployee_id.Text.Trim() + ".jpg";
string imageDirectory = @"C:\Your\Chosen\Path\To\EmployeeImages\"; // Example, actual is more complex
string path = Path.Combine(imageDirectory, imageFileName);

// Create directory if it doesn't exist
string directoryPath = Path.GetDirectoryName(path);
if (!Directory.Exists(directoryPath))
{
    Directory.CreateDirectory(directoryPath);
}
// Copy selected image to the designated path
File.Copy(addEmployee_picture.ImageLocation, path, true);

using(SqlCommand cmd = new SqlCommand(insertData, connect)) // 'connect' is SqlConnection
{
    cmd.Parameters.AddWithValue("@employeeID", addEmployee_id.Text.Trim());
    cmd.Parameters.AddWithValue("@fullName", addEmployee_fullName.Text.Trim());
    cmd.Parameters.AddWithValue("@gender", addEmployee_gender.Text.Trim());
    cmd.Parameters.AddWithValue("@contactNum", addEmployee_phoneNum.Text.Trim());
    cmd.Parameters.AddWithValue("@position", addEmployee_position.Text.Trim());
    cmd.Parameters.AddWithValue("@image", path); // Store the file path
    cmd.Parameters.AddWithValue("@salary", 0); // Default salary on add
    cmd.Parameters.AddWithValue("@insertDate", today);
    cmd.Parameters.AddWithValue("@status", addEmployee_status.Text.Trim());

    cmd.ExecuteNonQuery();
    // ... (Refresh UI, show success message, clear fields) ...
}
