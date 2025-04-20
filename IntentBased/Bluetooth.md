package com.example.intents;  
import static android.widget.Toast.LENGTH_LONG;  
import android.Manifest;  
import android.bluetooth.BluetoothAdapter;  
import android.bluetooth.BluetoothManager;  
import android.content.BroadcastReceiver;  
import android.content.Context;  
import android.content.Intent;  
import android.content.IntentFilter;  
import android.content.pm.PackageManager;  
import android.os.Build;  
import android.os.Bundle;  
import android.widget.Toast;  
import androidx.activity.EdgeToEdge;  
import androidx.appcompat.app.AppCompatActivity;  
import androidx.core.app.ActivityCompat;  
import androidx.core.content.ContextCompat;  
import androidx.core.graphics.Insets;  
import androidx.core.view.ViewCompat;  
import androidx.core.view.WindowInsetsCompat;  
  
public class MainActivity extends AppCompatActivity {  
    private static final int REQUEST_ENABLE_BT = 1;  
    private BluetoothManager bluetoothManager;  
    private BluetoothAdapter bluetoothAdapter;  
  
    private final BroadcastReceiver btStateReceiver = new BroadcastReceiver() {  
        @Override  
        public void onReceive(Context context, Intent intent) {  
            String action = intent.getAction();  
            if (BluetoothAdapter.ACTION_STATE_CHANGED.equals(action)) {  
                int state = intent.getIntExtra(BluetoothAdapter.EXTRA_STATE, BluetoothAdapter.ERROR);  
                switch (state) {  
                    case BluetoothAdapter.STATE_ON:  
                        Toast.makeText(context, "Bluetooth is ON", LENGTH_LONG).show();  
                        break;  
                    case BluetoothAdapter.STATE_OFF:  
                        Toast.makeText(context, "Bluetooth is OFF", LENGTH_LONG).show();  
                        break;  
                }  
            }  
        }  
    };  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        EdgeToEdge.enable(this);  
        setContentView(R.layout.activity_main);  
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {  
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());  
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);  
            return insets;  
        });  
  
        // Initialize Bluetooth manager and adapter  
        bluetoothManager = getSystemService(BluetoothManager.class);  
        bluetoothAdapter = bluetoothManager.getAdapter();  
  
        // Register BroadcastReceiver for Bluetooth state change  
        registerReceiver(btStateReceiver, new IntentFilter(BluetoothAdapter.ACTION_STATE_CHANGED));  
  
        // Check for permissions  
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {  
            if (ContextCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH_SCAN)  
                    != PackageManager.PERMISSION_GRANTED ||  
                    ContextCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH_CONNECT)  
                            != PackageManager.PERMISSION_GRANTED) {  
  
                ActivityCompat.requestPermissions(this,  
                        new String[]{Manifest.permission.BLUETOOTH_SCAN, Manifest.permission.BLUETOOTH_CONNECT},  
                        REQUEST_ENABLE_BT);  
            } else {  
                checkAndEnableBluetooth();  
            }  
        } else {  
            // For Android < 12, no need for runtime permissions  
            checkAndEnableBluetooth();  
        }  
    }  
  
    private void checkAndEnableBluetooth() {  
        if (!bluetoothAdapter.isEnabled()) {  
            Intent enableBtIntent = new Intent(BluetoothAdapter.ACTION_REQUEST_ENABLE);  
            if (ActivityCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH_CONNECT) != PackageManager.PERMISSION_GRANTED) {  
                // TODO: Consider calling  
                //    ActivityCompat#requestPermissions  
                // here to request the missing permissions, and then overriding  
                //   public void onRequestPermissionsResult(int requestCode, String[] permissions,                //                                          int[] grantResults)                // to handle the case where the user grants the permission. See the documentation                // for ActivityCompat#requestPermissions for more details.                return;  
            }  
            startActivityForResult(enableBtIntent, REQUEST_ENABLE_BT);  
        } else {  
            Toast.makeText(this, "Bluetooth is already ON", LENGTH_LONG).show();  
        }  
    }  
  
    @Override  
    public void onRequestPermissionsResult(int requestCode, String[] permissions,  
                                           int[] grantResults) {  
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);  
  
        if (requestCode == REQUEST_ENABLE_BT) {  
            boolean granted = true;  
            for (int result : grantResults) {  
                if (result != PackageManager.PERMISSION_GRANTED) {  
                    granted = false;  
                    break;  
                }  
            }  
  
            if (granted) {  
                checkAndEnableBluetooth();  
            } else {  
                Toast.makeText(this, "Bluetooth permissions denied", LENGTH_LONG).show();  
            }  
        }  
    }  
  
    @Override  
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {  
        super.onActivityResult(requestCode, resultCode, data);  
  
        if (requestCode == REQUEST_ENABLE_BT) {  
            if (resultCode == RESULT_OK) {  
                Toast.makeText(this, "User enabled Bluetooth", LENGTH_LONG).show();  
            } else {  
                Toast.makeText(this, "User declined to enable Bluetooth", LENGTH_LONG).show();  
            }  
        }  
    }  
  
    @Override  
    protected void onDestroy() {  
        super.onDestroy();  
        unregisterReceiver(btStateReceiver);  
    }  
}


permissions 


<uses-permission android:name="android.permission.BLUETOOTH" />  
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />  
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />  
<uses-permission android:name="android.permission.BLUETOOTH_SCAN" />