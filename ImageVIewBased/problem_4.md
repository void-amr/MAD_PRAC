
  
dependencies {  
    implementation ("androidx.camera:camera-core:1.3.0")  
    implementation ("androidx.camera:camera-camera2:1.3.0")  
    implementation ("androidx.camera:camera-lifecycle:1.3.0")  
    implementation ("androidx.camera:camera-view:1.3.0")  
    implementation ("androidx.camera:camera-video:1.3.0")  
    implementation(libs.appcompat)  
    implementation(libs.material)  
    implementation(libs.activity)  
    implementation(libs.constraintlayout)  
    implementation(libs.camera.core)  
    testImplementation(libs.junit)  
    androidTestImplementation(libs.ext.junit)  
    androidTestImplementation(libs.espresso.core)  
}

package com.example.myapplication;  
  
import android.Manifest;  
import android.content.pm.PackageManager;  
import android.net.Uri;  
import android.os.Bundle;  
import android.util.Log;  
import android.widget.Button;  
import android.widget.Toast;  
  
import androidx.activity.ComponentActivity;  
import androidx.annotation.NonNull;  
import androidx.camera.core.CameraSelector;  
import androidx.camera.lifecycle.ProcessCameraProvider;  
import androidx.camera.video.FileOutputOptions;  
import androidx.camera.video.PendingRecording;  
import androidx.camera.video.Recorder;  
import androidx.camera.video.Recording;  
import androidx.camera.video.VideoCapture;  
import androidx.camera.video.VideoRecordEvent;  
import androidx.camera.video.MediaStoreOutputOptions;  
import androidx.camera.view.PreviewView;  
import androidx.core.app.ActivityCompat;  
import androidx.core.content.ContextCompat;  
import androidx.lifecycle.LifecycleOwner;  
  
import com.google.common.util.concurrent.ListenableFuture;  
  
import java.io.File;  
import java.text.SimpleDateFormat;  
import java.util.Locale;  
import java.util.concurrent.ExecutionException;  
import java.util.concurrent.ExecutorService;  
import java.util.concurrent.Executors;  
  
public class MainActivity extends ComponentActivity {  
  
    private static final int REQUEST_CODE_PERMISSIONS = 10;  
    private static final String[] REQUIRED_PERMISSIONS = new String[]{  
            Manifest.permission.CAMERA, Manifest.permission.RECORD_AUDIO  
    };  
  
    private PreviewView previewView;  
    private VideoCapture<Recorder> videoCapture;  
    private Recording currentRecording;  
    private ExecutorService cameraExecutor;  
    private Button recordButton;  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
        previewView = findViewById(R.id.preview_view);  
        recordButton = findViewById(R.id.record_button);  
  
        cameraExecutor = Executors.newSingleThreadExecutor();  
  
        if (allPermissionsGranted()) {  
            startCamera();  
        } else {  
            ActivityCompat.requestPermissions(this, REQUIRED_PERMISSIONS, REQUEST_CODE_PERMISSIONS);  
        }  
  
        recordButton.setOnClickListener(v -> {  
            if (currentRecording != null) {  
                currentRecording.stop();  
                currentRecording = null;  
                recordButton.setText("Start Recording");  
            } else {  
                startRecording();  
            }  
        });  
    }  
  
    private void startCamera() {  
        ListenableFuture<ProcessCameraProvider> cameraProviderFuture = ProcessCameraProvider.getInstance(this);  
        cameraProviderFuture.addListener(() -> {  
            try {  
                ProcessCameraProvider cameraProvider = cameraProviderFuture.get();  
  
                CameraSelector cameraSelector = new CameraSelector.Builder()  
                        .requireLensFacing(CameraSelector.LENS_FACING_BACK)  
                        .build();  
  
                Recorder recorder = new Recorder.Builder()  
                        .setExecutor(cameraExecutor)  
                        .build();  
  
                videoCapture = VideoCapture.withOutput(recorder);  
  
                // Initialize Preview and bind it to lifecycle  
                androidx.camera.core.Preview preview = new androidx.camera.core.Preview.Builder().build();  
                preview.setSurfaceProvider(previewView.getSurfaceProvider());  
  
                cameraProvider.unbindAll();  
                cameraProvider.bindToLifecycle(  
                        (LifecycleOwner) this,  
                        cameraSelector,  
                        videoCapture,  
                        preview  
                );  
            } catch (ExecutionException | InterruptedException e) {  
                e.printStackTrace();  
                Toast.makeText(this, "Camera init failed", Toast.LENGTH_SHORT).show();  
            }  
        }, ContextCompat.getMainExecutor(this));  
    }  
  
    private void startRecording() {  
        File outputFile = new File(getExternalFilesDir(null),  
                "VID_" + new SimpleDateFormat("yyyyMMdd_HHmmss", Locale.US).format(System.currentTimeMillis()) + ".mp4");  
  
        FileOutputOptions outputOptions = new FileOutputOptions.Builder(outputFile).build();  
  
        if (ActivityCompat.checkSelfPermission(this, Manifest.permission.RECORD_AUDIO) != PackageManager.PERMISSION_GRANTED) {  
            // TODO: Consider calling  
            //    ActivityCompat#requestPermissions  
            // here to request the missing permissions, and then overriding  
            //   public void onRequestPermissionsResult(int requestCode, String[] permissions,            //                                          int[] grantResults)            // to handle the case where the user grants the permission. See the documentation            // for ActivityCompat#requestPermissions for more details.            return;  
        }  
        PendingRecording pendingRecording = videoCapture.getOutput()  
                .prepareRecording(this, outputOptions)  
                .withAudioEnabled();  
  
        currentRecording = pendingRecording.start(ContextCompat.getMainExecutor(this),  
                videoRecordEvent -> {  
                    if (videoRecordEvent instanceof VideoRecordEvent.Start) {  
                        recordButton.setText("Stop Recording");  
                    } else if (videoRecordEvent instanceof VideoRecordEvent.Finalize) {  
                        Uri savedUri = ((VideoRecordEvent.Finalize) videoRecordEvent).getOutputResults().getOutputUri();  
                        Toast.makeText(this, "Saved: " + savedUri, Toast.LENGTH_SHORT).show();  
                        Log.d("VideoCapture", "Video saved: " + savedUri);  
                    }  
                });  
    }  
  
    private boolean allPermissionsGranted() {  
        for (String permission : REQUIRED_PERMISSIONS) {  
            if (ContextCompat.checkSelfPermission(getBaseContext(), permission) != PackageManager.PERMISSION_GRANTED)  
                return false;  
        }  
        return true;  
    }  
  
    @Override  
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions,  
                                           @NonNull int[] grantResults) {  
        if (requestCode == REQUEST_CODE_PERMISSIONS) {  
            if (allPermissionsGranted()) {  
                startCamera();  
            } else {  
                Toast.makeText(this, "Permissions not granted", Toast.LENGTH_SHORT).show();  
                finish();  
            }  
        }  
    }  
  
    @Override  
    protected void onDestroy() {  
        super.onDestroy();  
        cameraExecutor.shutdown();  
    }  
}



<?xml version="1.0" encoding="utf-8"?>  
<androidx.constraintlayout.widget.ConstraintLayout  
    xmlns:android="http://schemas.android.com/apk/res/android"  
    xmlns:app="http://schemas.android.com/apk/res-auto"  
    android:id="@+id/main"  
    android:layout_width="match_parent"  
    android:layout_height="match_parent">  
  
    <androidx.camera.view.PreviewView        android:id="@+id/preview_view"  
        android:layout_width="0dp"  
        android:layout_height="0dp"  
        app:layout_constraintTop_toTopOf="parent"  
        app:layout_constraintBottom_toTopOf="@+id/record_button"  
        app:layout_constraintStart_toStartOf="parent"  
        app:layout_constraintEnd_toEndOf="parent"/>  
  
    <Button        android:id="@+id/record_button"  
        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:text="Start Recording"  
        app:layout_constraintTop_toBottomOf="@+id/preview_view"  
        app:layout_constraintBottom_toBottomOf="parent"  
        app:layout_constraintStart_toStartOf="parent"  
        app:layout_constraintEnd_toEndOf="parent"  
        android:layout_margin="16dp"/>  
</androidx.constraintlayout.widget.ConstraintLayout>