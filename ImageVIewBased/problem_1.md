What ? 
Zoom the image view on click and Zoom out on image view click 

Why ? 
Mentioned in questions 

How ? 

- 1 Create a image view and inflate -> image view la xml file madhun java object madhe convert karaicha-> using r.id.<component_name> because java doesn't understand xml and all the id of components in the xml are stored in the resource files  -> resources.xml 
- Then add a setOnClickListener default shit hyachyane jevha hi imageview warti click karu tevha image view ek operation perform karaichi sandhi deil 
- implement the logic 
Ek variable ghyachya 

1- isZoomedin navacha initially hyala false thevaicha 
ka ? 
Karan apli image initially zoomed in nahich aahe, okay tar jevha me click karel tevha ti zoom hoil ? -> ho kashi ? 

I'm using the **setScaleX** and **setScaleY** methods of ImageView class this will make it more easier for an image to zoom 

**Case 1:** 
let's say app load zhala jevha image click nasel tevha ti normal size madehe asel 
tevha variable -> isZoomedin -> false asel. ka karan clickch nahi kela 
jevha me click karel 

tevha 

first ti check karel 

if(isZoomedin) -> mazhi image zoomedIn aahe ka adhi pasun ? -> nahi 
tar if block execute nahi honar 

Magh ti yenar else block la 

else block madhe jevha image zoom nasel tyacha code lihlay 
image la apan x and y co-ordinate ne zoom karuyat 

later after execution of this block -> apan isZoomedin = !isZoomedin kartoy karan apli image ata click zhali na ? tar tyala true karva lagel 

! -> not operation ahee false cha true and true cha false karto ha operator. 

**case2 :** 

Image hi zoomed in aahe atta zevha click karnar 
tevha 

if(isZoomedin) -> true nighnar 
tar if cha code execute honar 

and later isZoomedin = !isZoomedin -> false honar 
ka ? 
Ata image wapas og shape madhe aali na tar true theun shate upatnar. 

package com.example.myapplication;  
  
import android.os.Bundle;  
import android.widget.ImageView;  
  
import androidx.activity.EdgeToEdge;  
import androidx.appcompat.app.AppCompatActivity;  
import androidx.core.graphics.Insets;  
import androidx.core.view.ViewCompat;  
import androidx.core.view.WindowInsetsCompat;  
  
import java.util.concurrent.atomic.AtomicInteger;  
  
public class MainActivity extends AppCompatActivity {  
    private ImageView iv;  
    private boolean isZoomedin = false;  
  
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
  
        iv = findViewById(R.id.imageView2);  
        iv.setOnClickListener(v->{  
            if(isZoomedin) {  
                iv.setScaleX(1f);  
                iv.setScaleY(1f);  
            }  
            else {  
                iv.setScaleX(1.5f);  
                iv.setScaleY(1.5f);  
            }  
            isZoomedin = !isZoomedin;  
        });  
  
    }  
}