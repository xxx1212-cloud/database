package com.example.myapplication;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

public class DatabaseHelper extends SQLiteOpenHelper {

    public DatabaseHelper(Context context) {
        super(context, "StudentDB", null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL("CREATE TABLE students (id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, number TEXT, address TEXT, class TEXT)");
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS students");
        onCreate(db);
    }

    public boolean insertData(String name, String number, String address, String studentClass) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put("name", name);
        values.put("number", number);
        values.put("address", address);
        values.put("class", studentClass);
        return db.insert("students", null, values) != -1;
    }

    public Cursor getAllData() {
        SQLiteDatabase db = this.getReadableDatabase();
        return db.rawQuery("SELECT * FROM students", null);
    }
}
package com.example.myapplication;

import android.database.Cursor;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    DatabaseHelper db;
    EditText nameInput, numberInput, addressInput, classInput;
    Button addButton, viewButton;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        db = new DatabaseHelper(this);
        nameInput = findViewById(R.id.editTextName);
        numberInput = findViewById(R.id.editTextNumber);
        addressInput = findViewById(R.id.editTextAddress);
        classInput = findViewById(R.id.editTextClass);
        addButton = findViewById(R.id.buttonAdd);
        viewButton = findViewById(R.id.buttonView);

        addButton.setOnClickListener(v -> {
            boolean inserted = db.insertData(
                    nameInput.getText().toString(),
                    numberInput.getText().toString(),
                    addressInput.getText().toString(),
                    classInput.getText().toString()
            );
            Toast.makeText(this, inserted ? "Inserted" : "Failed", Toast.LENGTH_SHORT).show();
        });

        viewButton.setOnClickListener(v -> {
            Cursor data = db.getAllData();
            if (data.getCount() == 0) {
                Toast.makeText(this, "No Data", Toast.LENGTH_SHORT).show();
            } else {
                StringBuilder result = new StringBuilder();
                while (data.moveToNext()) {
                    result.append("ID: ").append(data.getString(0)).append("\n")
                            .append("Name: ").append(data.getString(1)).append("\n")
                            .append("Number: ").append(data.getString(2)).append("\n")
                            .append("Address: ").append(data.getString(3)).append("\n")
                            .append("Class: ").append(data.getString(4)).append("\n\n");
                }
                Toast.makeText(this, result.toString(), Toast.LENGTH_LONG).show();
            }
        });
    }
}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp">

    <EditText
        android:id="@+id/editTextName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Name" />

    <EditText
        android:id="@+id/editTextNumber"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Number" />

    <EditText
        android:id="@+id/editTextAddress"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Address" />

    <EditText
        android:id="@+id/editTextClass"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="Class" />

    <Button
        android:id="@+id/buttonAdd"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Add" />

    <Button
        android:id="@+id/buttonView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="View" />
</LinearLayout>
