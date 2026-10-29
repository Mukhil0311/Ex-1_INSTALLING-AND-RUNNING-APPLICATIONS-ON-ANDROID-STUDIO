# Ex-1_INSTALLING-AND-RUNNING-APPLICATIONS-ON-ANDROID-STUDIO

**Name:** MUKHIL S

**Register Number:** 212225040263

## Software Requirements
- Android Studio
- Android SDK
- Android Virtual Device (AVD)
- Java
- XML

---

## Step 1 – System Requirements

The required software tools are used to develop, design, and run Android applications. Android Studio provides the development environment, Java is used to implement application functionality, and XML is used to design the user interface.

### Required Software
- Android Studio
- Java Development Kit (JDK)
- Android SDK
- Android Emulator

---

## Step 2 – Setup Android Studio

Android Studio is the official Integrated Development Environment (IDE) used for Android application development.

### Installation Procedure
1. Download Android Studio from the official website.
2. Run the installer and follow the installation instructions.
3. Install the required Android SDK components.
4. Configure the Android SDK location.
5. Set up the Android Virtual Device if required.
6. Launch Android Studio.
7. Create a new Android project using the Empty Views Activity template.
8. Select Java as the programming language.

---

## Creating the Student Details Application

1. Open the project in Android Studio.
2. Design the user interface using the `activity_main.xml` file.
3. Add a heading named **STUDENT DETAILS**.
4. Add a button to reveal the student information.
5. Add a TextView to display the student name and register number.
6. Open the `MainActivity.java` file.
7. Initialize the Button and TextView using `findViewById()`.
8. Implement the button click event using `setOnClickListener()`.
9. Display the student information when the button is clicked.
10. Change the button text to **Details Revealed** after displaying the information.

---

## Step 3 – Create Android Virtual Device (AVD)

An Android Virtual Device is used to test and run the application without requiring a physical Android phone.

### Procedure
1. Open Device Manager in Android Studio.
2. Click **Create Virtual Device**.
3. Select a suitable device configuration.
4. Select and download an Android system image if required.
5. Finish the configuration and start the emulator.
6. Select the emulator from the device dropdown menu.

---

## Step 4 – Run the Application

1. Open the Student Details project.
2. Select the configured Android Emulator.
3. Click the **Run ▶** button in Android Studio.
4. Wait for the application to build and launch.
5. Verify that the Student Details screen appears.
6. Click the button to display the student information.


## PROGRAM

# ACTIVITY_MAIN.XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="20dp">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@string/app_title"
            android:textSize="24sp"
            android:textStyle="bold"
            android:gravity="center"
            android:padding="15dp" />

        <EditText
            android:id="@+id/etId"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="@string/employee_id"
            android:inputType="text"
            android:autofillHints="username" />

        <EditText
            android:id="@+id/etName"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="@string/employee_name"
            android:inputType="textPersonName"
            android:autofillHints="name" />

        <EditText
            android:id="@+id/etSalary"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="@string/employee_salary"
            android:inputType="numberDecimal"
            android:autofillHints="none" />

        <Button
            android:id="@+id/btnAdd"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@string/add_employee" />

        <Button
            android:id="@+id/btnUpdate"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@string/update_employee" />

        <Button
            android:id="@+id/btnDelete"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@string/delete_employee" />

    </LinearLayout>
</ScrollView>
```

# MAIN_ACTIVITY.JAVA

```java

package com.example.pmd1;

import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

import com.google.firebase.database.DatabaseReference;
import com.google.firebase.database.FirebaseDatabase;

public class MainActivity extends AppCompatActivity {

    EditText etId, etName, etSalary;
    Button btnAdd, btnUpdate, btnDelete;
    DatabaseReference database;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);

        ViewCompat.setOnApplyWindowInsetsListener(
                findViewById(R.id.main), (v, insets) -> {
                    Insets systemBars = insets.getInsets(
                            WindowInsetsCompat.Type.systemBars());
                    v.setPadding(systemBars.left, systemBars.top,
                            systemBars.right, systemBars.bottom);
                    return insets;
                });

        etId = findViewById(R.id.etId);
        etName = findViewById(R.id.etName);
        etSalary = findViewById(R.id.etSalary);

        btnAdd = findViewById(R.id.btnAdd);
        btnUpdate = findViewById(R.id.btnUpdate);
        btnDelete = findViewById(R.id.btnDelete);

        database = FirebaseDatabase.getInstance()
                .getReference("Employees");

        btnAdd.setOnClickListener(v -> addEmployee());
        btnUpdate.setOnClickListener(v -> updateEmployee());
        btnDelete.setOnClickListener(v -> deleteEmployee());
    }

    private void addEmployee() {
        String id = etId.getText().toString().trim();
        String name = etName.getText().toString().trim();
        String salaryText = etSalary.getText().toString().trim();

        if (id.isEmpty() || name.isEmpty() || salaryText.isEmpty()) {
            Toast.makeText(this, "Enter all details",
                    Toast.LENGTH_SHORT).show();
            return;
        }

        try {
            double salary = Double.parseDouble(salaryText);
            emp employee = new emp(id, name, salary);

            database.child(id).setValue(employee)
                    .addOnSuccessListener(unused ->
                            Toast.makeText(this, "Employee Added",
                                    Toast.LENGTH_SHORT).show())
                    .addOnFailureListener(e ->
                            Toast.makeText(this, e.getMessage(),
                                    Toast.LENGTH_LONG).show());

        } catch (NumberFormatException e) {
            Toast.makeText(this, "Enter a valid salary",
                    Toast.LENGTH_SHORT).show();
        }
    }

    private void updateEmployee() {
        String id = etId.getText().toString().trim();
        String name = etName.getText().toString().trim();
        String salaryText = etSalary.getText().toString().trim();

        if (id.isEmpty() || name.isEmpty() || salaryText.isEmpty()) {
            Toast.makeText(this, "Enter all details",
                    Toast.LENGTH_SHORT).show();
            return;
        }

        try {
            double salary = Double.parseDouble(salaryText);

            java.util.Map<String, Object> updates =
                    new java.util.HashMap<>();
            updates.put("id", id);
            updates.put("name", name);
            updates.put("salary", salary);

            database.child(id).updateChildren(updates)
                    .addOnSuccessListener(unused ->
                            Toast.makeText(this, "Employee Updated",
                                    Toast.LENGTH_SHORT).show())
                    .addOnFailureListener(e ->
                            Toast.makeText(this, e.getMessage(),
                                    Toast.LENGTH_LONG).show());

        } catch (NumberFormatException e) {
            Toast.makeText(this, "Enter a valid salary",
                    Toast.LENGTH_SHORT).show();
        }
    }

    private void deleteEmployee() {
        String id = etId.getText().toString().trim();

        if (id.isEmpty()) {
            Toast.makeText(this, "Enter Employee ID",
                    Toast.LENGTH_SHORT).show();
            return;
        }

        database.child(id).removeValue()
                .addOnSuccessListener(unused ->
                        Toast.makeText(this, "Employee Deleted",
                                Toast.LENGTH_SHORT).show())
                .addOnFailureListener(e ->
                        Toast.makeText(this, e.getMessage(),
                                Toast.LENGTH_LONG).show());
    }
}
```

# EMP.JAVA

```java

package com.example.pmd1;

public class emp {

    public String id;
    public String name;
    public double salary;

    public emp() {
    }

    public emp(String id, String name, double salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
    }
}
```

## OUTPUT

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/356e024e-96e0-4741-aaf1-41f2a56bbdc2" />

## RESULT

Thus, the Student Details application was successfully created and executed using Android Studio, and the student information was displayed by clicking the button.

