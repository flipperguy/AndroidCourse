
Use "findViewById" to reference a view

    TextView textView = findViewById(R.id.textView);
    textView.setText("Hello");

Start an Activity
https://developer.android.com/training/basics/firstapp/starting-activity#java

    Intent intent = new Intent(this, DisplayMessageActivity.class);
    startActivity(intent);


