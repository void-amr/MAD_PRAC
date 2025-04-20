


package com.example.intents;  
  
import android.Manifest;  
import android.content.pm.PackageManager;  
import android.os.Bundle;  
import android.telephony.SmsManager;  
import android.widget.Button;  
import android.widget.EditText;  
import android.widget.Toast;  
import androidx.appcompat.app.AppCompatActivity;  
import androidx.core.app.ActivityCompat;  
import androidx.core.content.ContextCompat;  
  
import com.example.intents.R;  
  
public class MainActivity extends AppCompatActivity {  
    private static final int SMS_PERMISSION_CODE = 101;  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        EditText numberEditText = findViewById(R.id.phoneNumber);  
        EditText messageEditText = findViewById(R.id.message);  
        Button sendBtn = findViewById(R.id.sendBtn);  
  
        sendBtn.setOnClickListener(v -> {  
            String phone = numberEditText.getText().toString();  
            String message = messageEditText.getText().toString();  
  
            if (ContextCompat.checkSelfPermission(this, Manifest.permission.SEND_SMS) != PackageManager.PERMISSION_GRANTED) {  
                ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.SEND_SMS}, SMS_PERMISSION_CODE);  
            } else {  
                SmsManager smsManager = SmsManager.getDefault();  
                smsManager.sendTextMessage(phone, null, message, null, null);  
                Toast.makeText(this, "SMS Sent", Toast.LENGTH_SHORT).show();  
            }  
        });  
    }  
}

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"  
    android:layout_width="match_parent"  
    android:layout_height="wrap_content"  
    android:orientation="vertical"  
    android:padding="20dp">  
  
    <EditText        android:id="@+id/phoneNumber"  
        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:hint="Phone Number"  
        android:inputType="phone"/>  
  
    <EditText        android:id="@+id/message"  
        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:hint="Enter Message"  
        android:inputType="textMultiLine"/>  
  
    <Button        android:id="@+id/sendBtn"  
        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        android:text="Send SMS"/>  
</LinearLayout>

permissions dya hya

<uses-permission android:name="android.permission.SEND_SMS" />  
<uses-permission android:name="android.permission.RECEIVE_SMS" />  
<uses-permission android:name="android.permission.READ_SMS" />