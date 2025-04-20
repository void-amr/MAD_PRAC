
package com.example.intents;  
  
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
    private EditText ed;  
  
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
        ed = findViewById(R.id.editTextText);  
        button.setOnClickListener(v-> {  
                String url = ed.getText().toString().trim();  
                Uri webpage = Uri.parse("https://" + url);  
                Intent it = new Intent(Intent.ACTION_VIEW, webpage);  
                startActivity(it);  
        });  
    }  
}