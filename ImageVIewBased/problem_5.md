
package com.example.rotation;  
  
import android.os.Bundle;  
import android.widget.Button;  
import android.widget.ImageView;  
  
import androidx.activity.EdgeToEdge;  
import androidx.appcompat.app.AppCompatActivity;  
import androidx.core.graphics.Insets;  
import androidx.core.view.ViewCompat;  
import androidx.core.view.WindowInsetsCompat;  
  
import com.google.android.material.badge.BadgeUtils;  
  
public class MainActivity extends AppCompatActivity {  
  
    private ImageView maam;  
    int le = 50;  
  
    private Button clock,anticlock;  
    private boolean isClicked = false;  
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
  
        maam = findViewById(R.id.maam);  
        clock = findViewById(R.id.clock);  
        anticlock = findViewById(R.id.anticlock);  
  
        clock.setOnClickListener(v -> {  
                maam.setRotation(le);  
                le = le + 50;  
        });  
        anticlock.setOnClickListener(v -> {  
            maam.setRotation(le-50);  
            le = le - 50;  
        });  
    }  
}

SO here we change the rotation of the image by 50 we can even initialize the le to 0 such that it will start rotation from 0 -> 50 but due to my laziness I've kept it 50. 