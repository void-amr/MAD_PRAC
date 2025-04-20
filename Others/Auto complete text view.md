
package com.example.searchengine;

import android.os.Bundle;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.AutoCompleteTextView;
import android.widget.Button;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    AutoCompleteTextView autoCompleteTextView;
    Button searchButton;
    TextView resultText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_search_engine);

        autoCompleteTextView = findViewById(R.id.autoCompleteTextView);
        searchButton = findViewById(R.id.searchButton);
        resultText = findViewById(R.id.resultText);

        // Example search suggestions (can be from a database or web service)
        String[] searchSuggestions = {
            "Google",
            "Gmail",
            "GitHub",
            "Googling",
            "Google Translate",
            "Google Maps",
            "Golang",
            "Google Drive"
        };

        // Create an adapter to populate suggestions
        ArrayAdapter<String> adapter = new ArrayAdapter<>(this, android.R.layout.simple_dropdown_item_1line, searchSuggestions);
        autoCompleteTextView.setAdapter(adapter);

        // On click, search the entered term and display a result
        searchButton.setOnClickListener(v -> {
            String query = autoCompleteTextView.getText().toString();
            resultText.setText("You searched for: " + query);
        });
    }
}
