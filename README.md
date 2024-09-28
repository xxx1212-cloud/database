package com.example.db4;
import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;
import androidx.annotation.Nullable;
public class mydbhelper extends SQLiteOpenHelper {
 private static final String DATABASE_NAME = "userdb.db";
 private static final int DATABASE_VERSION = 1;
 private static final String TABLE_NAME = "usertable";
 private static final String COLUMN_NAME = "name";
 private static final String COLUMN_NO = "mobile";
 private static final String COLUMN_DOB = "dob";
 private static final String COLUMN_CITY = "address";
 public mydbhelper(@Nullable Context context) {
 super(context, DATABASE_NAME, null, DATABASE_VERSION);
 }
 @Override
 public void onCreate(SQLiteDatabase db) {
 // Corrected CREATE TABLE query
 String CREATE_TABLE = "CREATE TABLE " + TABLE_NAME + " (" +
 COLUMN_NAME + " TEXT, " +
 COLUMN_NO + " TEXT, " +
 COLUMN_DOB + " TEXT, " +
 COLUMN_CITY + " TEXT)";
 db.execSQL(CREATE_TABLE);
 }
 @Override
 public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
 // Drop the table if it exists and recreate it
 db.execSQL("DROP TABLE IF EXISTS " + TABLE_NAME);
 onCreate(db);
 }
 // Method to insert data
 public boolean insertdata(String name, String mobile, String address, String dob) {
 SQLiteDatabase db = this.getWritableDatabase();
MAD LAB
 ContentValues values = new ContentValues();
 values.put(COLUMN_NAME, name);
 values.put(COLUMN_NO, mobile);
 values.put(COLUMN_CITY, address);
 values.put(COLUMN_DOB, dob);
 long result = db.insert(TABLE_NAME, null, values);
 return result != -1; // return true if insert is successful, false otherwise
 }
 // Method to retrieve data
 public Cursor getdata() {
 SQLiteDatabase db = this.getWritableDatabase();
 return db.rawQuery("SELECT * FROM " + TABLE_NAME, null);
 }
}
MainActivity.java
package com.example.db4;
import android.annotation.SuppressLint;
import android.database.Cursor;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
public class MainActivity extends AppCompatActivity {
 EditText name, mobile, dob, address;
 Button insert, view;
 @SuppressLint("MissingInflatedId")
 @Override
 protected void onCreate(Bundle savedInstanceState) {
 super.onCreate(savedInstanceState);
 setContentView(R.layout.activity_main);
 // Initialize the views
 name = findViewById(R.id.name);
 mobile = findViewById(R.id.mobile);
 dob = findViewById(R.id.dob);
MAD LAB
 address = findViewById(R.id.address);
 insert = findViewById(R.id.insert);
 view = findViewById(R.id.view);
 mydbhelper db = new mydbhelper(this);
 // Insert button onClick listener
 insert.setOnClickListener(new View.OnClickListener() {
 @Override
 public void onClick(View view) {
 String Username = name.getText().toString().trim();
 String Userno = mobile.getText().toString().trim();
 String Userdob = dob.getText().toString().trim();
 String Useradd = address.getText().toString().trim();
 // Check for empty fields
 if (Username.isEmpty() || Userno.isEmpty() || Userdob.isEmpty() || Useradd.isEmpty()) {
 Toast.makeText(MainActivity.this, "Please fill all fields", Toast.LENGTH_SHORT).show();
 return;
 }
 boolean checkinsert = db.insertdata(Username, Userno, Userdob, Useradd);
 if (checkinsert) {
 Toast.makeText(MainActivity.this, "User data inserted", Toast.LENGTH_SHORT).show();
 } else {
 Toast.makeText(MainActivity.this, "User data not inserted", Toast.LENGTH_SHORT).show();
 }
 }
 });
 // View button onClick listener
 view.setOnClickListener(new View.OnClickListener() {
 @Override
 public void onClick(View v) {
 Cursor res = db.getdata();
 if (res.getCount() == 0) {
 Toast.makeText(MainActivity.this, "No entry exists", Toast.LENGTH_SHORT).show();
 return;
 }
 StringBuffer buffer = new StringBuffer();
 while (res.moveToNext()) {
 buffer.append("Name: " + res.getString(0) + "\n");
 buffer.append("Mobile: " + res.getString(1) + "\n");
 buffer.append("DOB: " + res.getString(2) + "\n");
 buffer.append("Address: " + res.getString(3) + "\n\n");
 }
 AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
 builder.setCancelable(true);
 builder.setTitle("User Entries");
MAD LAB
 builder.setMessage(buffer.toString());
 builder.show();
 }
 });
 }
