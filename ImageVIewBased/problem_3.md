
Capture image and store it in ram memory built in memory buffer.  

![[Pasted image 20250420122146.png]]
![[WhatsApp Image 2025-04-20 at 13.34.58_24f71ec6.jpg]]

1) create button 
2) ```ImageCapture imageCapture =
    new ImageCapture.Builder()
        .setTargetRotation(view.getDisplay().getRotation())
        .build();

cameraProvider.bindToLifecycle(lifecycleOwner, cameraSelector, imageCapture, preview);

3) add the android camera core dependency by hovering over the ImageCapture object -> alt+shift+enter. 



package com.example.myapplication;  
  
import android.Manifest;  
import android.content.pm.PackageManager;  
import android.os.Bundle;  
import android.util.Log;  
import android.widget.Button;  
import android.widget.Toast;  
import android.net.Uri;  
import android.widget.ImageView;  
  
  
import androidx.annotation.NonNull;  
import androidx.appcompat.app.AppCompatActivity;  
import androidx.camera.core.CameraSelector;  
import androidx.camera.core.ImageCapture;  
import androidx.camera.core.ImageCaptureException;  
import androidx.camera.core.Preview;  
import androidx.camera.lifecycle.ProcessCameraProvider;  
import androidx.camera.view.PreviewView;  
import androidx.core.app.ActivityCompat;  
import androidx.core.content.ContextCompat;  
  
import com.google.common.util.concurrent.ListenableFuture;  
  
import java.io.File;  
import java.util.concurrent.ExecutionException;  
import java.util.concurrent.ExecutorService;  
import java.util.concurrent.Executors;  
  
public class MainActivity extends AppCompatActivity {  
  
    private static final int REQUEST_CODE_PERMISSIONS = 101;  
    private static final String[] REQUIRED_PERMISSIONS = new String[]{Manifest.permission.CAMERA};  
  
    private PreviewView previewView;  
    private ImageCapture imageCapture;  
    private ExecutorService cameraExecutor;  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
  
        previewView = findViewById(R.id.preview_view);  
        Button capture = findViewById(R.id.capture);  
  
        cameraExecutor = Executors.newSingleThreadExecutor();  
  
        if (allPermissionsGranted()) {  
            startCamera();  
        } else {  
            ActivityCompat.requestPermissions(this, REQUIRED_PERMISSIONS, REQUEST_CODE_PERMISSIONS);  
        }  
  
        ImageView imageView = findViewById(R.id.photo_view);  
        capture.setOnClickListener(v -> {  
            if (imageCapture == null) return;  
  
            File photoFile = new File(getFilesDir(), "photo_" + System.currentTimeMillis() + ".jpg");  
  
            ImageCapture.OutputFileOptions outputOptions =  
                    new ImageCapture.OutputFileOptions.Builder(photoFile).build();  
  
            imageCapture.takePicture(  
                    outputOptions,  
                    ContextCompat.getMainExecutor(this),  
                    new ImageCapture.OnImageSavedCallback() {  
                        @Override  
                        public void onImageSaved(@NonNull ImageCapture.OutputFileResults outputFileResults) {  
                            String msg = "Photo saved to " + photoFile.getAbsolutePath();  
                            Log.d("CameraX", msg);  
                            Toast.makeText(getApplicationContext(), msg, Toast.LENGTH_SHORT).show();  
  
                            // Display the image in the ImageView  
                            Uri uri = Uri.fromFile(photoFile);  
                            imageView.setImageURI(uri);  
                        }  
  
                        @Override  
                        public void onError(@NonNull ImageCaptureException exception) {  
                            Log.e("CameraX", "Photo capture failed: " + exception.getMessage(), exception);  
                            Toast.makeText(getApplicationContext(), "Capture failed", Toast.LENGTH_SHORT).show();  
                        }  
                    }  
            );  
        });  
    }  
  
    private void startCamera() {  
        ListenableFuture<ProcessCameraProvider> cameraProviderFuture = ProcessCameraProvider.getInstance(this);  
  
        cameraProviderFuture.addListener(() -> {  
            try {  
                ProcessCameraProvider cameraProvider = cameraProviderFuture.get();  
  
                Preview preview = new Preview.Builder().build();  
                imageCapture = new ImageCapture.Builder().build();  
  
                CameraSelector cameraSelector = new CameraSelector.Builder()  
                        .requireLensFacing(CameraSelector.LENS_FACING_BACK)  
                        .build();  
  
                preview.setSurfaceProvider(previewView.getSurfaceProvider());  
  
                cameraProvider.unbindAll();  
                cameraProvider.bindToLifecycle(  
                        this, cameraSelector, preview, imageCapture  
                );  
  
                Log.d("CameraX", "Camera started");  
  
            } catch (ExecutionException | InterruptedException e) {  
                e.printStackTrace();  
                Toast.makeText(this, "Error starting camera", Toast.LENGTH_SHORT).show();  
            }  
        }, ContextCompat.getMainExecutor(this));  
    }  
  
    private boolean allPermissionsGranted() {  
        for (String permission : REQUIRED_PERMISSIONS) {  
            if (ContextCompat.checkSelfPermission(this, permission)  
                    != PackageManager.PERMISSION_GRANTED) {  
                return false;  
            }  
        }  
        return true;  
    }  
  
    @Override  
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions,  
                                           @NonNull int[] grantResults) {  
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);  
        if (requestCode == REQUEST_CODE_PERMISSIONS) {  
            if (allPermissionsGranted()) {  
                startCamera();  
            } else {  
                Toast.makeText(this, "Camera permission denied", Toast.LENGTH_SHORT).show();  
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
        app:layout_constraintStart_toStartOf="parent"  
        app:layout_constraintEnd_toEndOf="parent"  
        app:layout_constraintBottom_toTopOf="@+id/capture"  
        android:layout_margin="16dp"/>  
  
    <Button        android:id="@+id/capture"  
        android:layout_width="wrap_content"  
        android:layout_height="wrap_content"  
        android:text="Capture"  
        app:layout_constraintBottom_toBottomOf="parent"  
        app:layout_constraintStart_toStartOf="parent"  
        app:layout_constraintEnd_toEndOf="parent"  
        android:layout_marginBottom="100dp"/>  
  
    <ImageView        android:id="@+id/photo_view"  
        android:layout_width="120dp"  
        android:layout_height="120dp"  
        android:scaleType="centerCrop"  
        android:layout_margin="16dp"  
        app:layout_constraintBottom_toTopOf="@+id/capture"  
        app:layout_constraintEnd_toEndOf="parent"/>  
</androidx.constraintlayout.widget.ConstraintLayout>

copy paste this code 
