
package com.example.datepickerexample;

import android.app.DatePickerDialog;
import android.os.Bundle;
import android.widget.Button;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;
import java.util.Calendar;

public class DatePickerActivity extends AppCompatActivity {

    TextView selectedDateText;
    Button pickDateButton;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_date_picker);

        selectedDateText = findViewById(R.id.selectedDate);
        pickDateButton = findViewById(R.id.pickDateButton);

        pickDateButton.setOnClickListener(v -> {
            // Get current date
            Calendar calendar = Calendar.getInstance();
            int year = calendar.get(Calendar.YEAR);
            int month = calendar.get(Calendar.MONTH);
            int day = calendar.get(Calendar.DAY_OF_MONTH);

            // Open date picker dialog
            DatePickerDialog datePickerDialog = new DatePickerDialog(
                DatePickerActivity.this,
                (view, year1, month1, dayOfMonth) -> {
                    // Month is 0-indexed
                    String date = dayOfMonth + "/" + (month1 + 1) + "/" + year1;
                    selectedDateText.setText("Selected Date: " + date);
                },
                year, month, day
            );
            datePickerDialog.show();
        });
    }
}


```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="30dp"
    android:gravity="center">

    <Button
        android:id="@+id/pickDateButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Pick a Date" />

    <TextView
        android:id="@+id/selectedDate"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Selected Date: "
        android:textSize="18sp"
        android:layout_marginTop="20dp"/>
</LinearLayout>

