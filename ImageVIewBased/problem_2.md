
  
iv = findViewById(R.id.imageView2);  
    iv.setOnClickListener(v->{  
            Thread t = new Thread(() -> {  
                try {  
                    iv.setImageAlpha(1);  
                    Thread.sleep(3000);  
                    iv.setImageAlpha(255);  
                } catch (InterruptedException e) {  
                    throw new RuntimeException(e);  
                }  
            });  
            t.start();  
    });

Ithe -> Apan background madhe new thread create karto 

Ani thread varti multiple operations perform kartoy 

1) iv.setImageAlpha(1) => Image opacity is reduced and taken to 1 
2) Thread.sleep(3000miliseconds) Causes the currently executing thread to sleep (temporarily cease execution) for the specified number of milliseconds plus the specified number of nanoseconds, subject to the precision and accuracy of system timers and schedulers. The thread does not lose ownership of any monitors.
3) iv.setImageAlpha(255) -> max opacity 

Try catch classic stuff 