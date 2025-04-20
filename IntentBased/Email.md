
package com.example.intents;  
  
import static java.net.Proxy.Type.HTTP;  
  
import android.content.Intent;  
import android.net.Uri;  
import android.os.Bundle;  
import android.widget.Button;  
import android.widget.EditText;  
  
import androidx.activity.EdgeToEdge;  
import androidx.appcompat.app.AppCompatActivity;  
import androidx.core.graphics.Insets;  
import androidx.core.view.ViewCompat;  
import androidx.core.view.WindowInsetsCompat;  
  
public class MainActivity extends AppCompatActivity {  
    private Button button;  
    private EditText ed, ed1,ed2;  
  
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
        button = findViewById(R.id.button);  
        button.setOnClickListener(v-> {  
            Intent emailIntent = new Intent(Intent.ACTION_SEND);  
            emailIntent.setType("message/rfc822"); // Set MIME type for email apps  
            emailIntent.putExtra(Intent.EXTRA_EMAIL, new String[] {"jan@example.com"}); // recipients  
            emailIntent.putExtra(Intent.EXTRA_SUBJECT, "Email subject");  
            emailIntent.putExtra(Intent.EXTRA_TEXT, "Email message text");  
// You can also attach multiple items by passing an ArrayList of Uris  
                startActivity(emailIntent);  
        });  
    }  
}