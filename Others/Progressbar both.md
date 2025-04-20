
# Circular 

```

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="20dp">

    <ProgressBar
        android:id="@+id/circularProgress"
        style="?android:attr/progressBarStyleLarge"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:indeterminate="true" />

</LinearLayout>

```

```
package com.example.circularprogress;

import android.os.Bundle;
import android.os.Handler;
import android.view.View;
import android.widget.ProgressBar;
import androidx.appcompat.app.AppCompatActivity;

public class CircularProgressActivity extends AppCompatActivity {

    ProgressBar progressBar;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_progress);

        progressBar = findViewById(R.id.circularProgress);

        // Show for 5 seconds, then hide (simulate loading)
        new Handler().postDelayed(() -> {
            progressBar.setVisibility(View.GONE);
        }, 5000);
    }
}

```


# Horizontal 

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="20dp">

    <ProgressBar
        android:id="@+id/horizontalProgressBar"
        android:layout_width="300dp"
        android:layout_height="wrap_content"
        android:progress="0"
        android:max="100"
        android:layout_marginTop="20dp"
        android:indeterminate="false"
        android:progressDrawable="@drawable/progress_bar" />

    <TextView
        android:id="@+id/progressText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Progress: 0%"
        android:textSize="18sp"
        android:layout_marginTop="10dp"/>

</LinearLayout>

```

package com.example.horizontalprogress;

import android.os.Bundle;
import android.os.Handler;
import android.widget.ProgressBar;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class HorizontalProgressActivity extends AppCompatActivity {

    ProgressBar horizontalProgressBar;
    TextView progressText;
    int progressStatus = 0;
    Handler handler = new Handler();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_horizontal_progress);

        horizontalProgressBar = findViewById(R.id.horizontalProgressBar);
        progressText = findViewById(R.id.progressText);

        // Simulate progress
        new Thread(() -> {
            while (progressStatus < 100) {
                progressStatus += 1;  // Increase progress
                // Update progress bar
                handler.post(() -> {
                    horizontalProgressBar.setProgress(progressStatus);
                    progressText.setText("Progress: " + progressStatus + "%");
                });
                try {
                    Thread.sleep(100);  // Sleep for 100ms before updating again
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}
