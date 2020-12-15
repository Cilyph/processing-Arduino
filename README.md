/*# processing-Arduino
导论大作业
processing里的代码：*/


import processing.serial.*;
Serial myport;
import java.util.Iterator;

snowsystem snow;
PImage img;
PImage img2;
PImage img3;
float vol;
float speed_=vol/20;

void setup(){
size(1280,680);
snow =new snowsystem(0.2); 
img=loadImage("the cat.jpg");
img2=loadImage("dongman.jpg");
img3=img2;
myport=new Serial(this,"com4",9600);
}
void draw(){
  if(myport.available()>0){
    vol=myport.read();
    println(vol);
  }
 background(40);
//img3=img;
  image(img3,0,0,width,height);
   snow.run(); 
if(vol>0){
    int r=int(random(255));
    int g=int(random(255));
    int b=int(random(255));
    stroke(r,g,b);
  }
    translate(width /9, height / 5);
    float bins=1024;
  for(int i=0;i<bins -1;i++) {
    strokeWeight(2);
    float r1 = 100+ 150 *vol/200;
    float r2 = 100 +150 * vol/200;
    float R1 = 100 + 150 *vol/200;
    float R2 = 100 + 150 * vol/200;
    float rad1 = 2 * PI * (i / bins);
    float rad2 = 2 * PI * ((i + 1) / bins);
    float Rad1 = 2 * PI * (i / bins);
    float Rad2 = 2 * PI * ((i + 1) / bins);
    line(r1 * cos(rad1)+500, r1 * sin(rad1)+500, r2 * cos(rad2)+500, r2 * sin(rad2)+500);
    line(R1 * cos(Rad1)+500, R1 * sin(Rad1)+500, R2 * cos(Rad2)+500, R2 * sin(Rad2)+500); 
  }
    noFill();
     arc(500, 500, vol/5, vol/5, -2*PIE, 2*PIE);
    arc(500, 500,vol/5, vol/5, -2*PIE, 2*PIE);
    
  translate(-width / 9, -height / 5);
  
  stroke(225);
 // snow.run();
}
class Branch  
{
 float radius,ratio,rotation;
 Branch(float radius_,float ratio_,float rot){
  radius=radius_;
  ratio=ratio_;
  rotation=rot;
}
 void generate()
{
   pushMatrix();
   rotate(radians(rotation)); 
   float len=getStartLength(radius,ratio);
   branch(len);
   popMatrix();
 }
 void branch(float len)
{
     strokeWeight(len*0.5); 
     strokeCap(ROUND);      
     line(0,0,0,-len);              
     translate(0,-len);           
     
     if(len>2) 
    {
       pushMatrix();
       branch(len*ratio);
       popMatrix();
       
       pushMatrix();
       rotate(radians(-60)); 
       branch(getStartLength(len,ratio));
       popMatrix();
       
       pushMatrix();
       rotate(radians(60)); 
       branch(getStartLength(len,ratio));
       popMatrix();
     }
 }
 
 float getStartLength(float length,float  ratio)
 {
    float len=(1-ratio)*length;
    return len;
 }
}

class snowflake 
{
   PVector position ,velocity;
   float rotation ,aVelocity,radius,ratio;
   Branch[] branches=new Branch[6];
   
    snowflake(PVector pos,PVector vel,float rot,float aVel,float r,float rat)
    {
     position =pos;
     velocity=vel;
     rotation=rot;
     aVelocity=aVel;
     radius=r;
     ratio=rat;
     
     for(int i=0;i<6;i++){ branches[i]=new Branch(radius,ratio,i*60);}
    }
   
    void update()
    {
    position.add(velocity);
    rotation+=aVelocity;  
    }
    void show()  
    {
    pushMatrix();
    translate(position.x,position.y);
    rotate(radians(rotation));
    for(Branch b:branches)
    {b.generate();}
    popMatrix();  
    }
}

class snowsystem   
{
  float speed ;
  ArrayList<snowflake> snowflakes;
  
  snowsystem (float speed_){
  speed=speed_;
  snowflakes=new ArrayList<snowflake>();
 }

void  generate() 
{
 PVector position =new PVector(random(0,width),0);
 //PVector velocity =new PVector(0,random(5,10));
 PVector velocity =new PVector(0,vol/5);
 float rotation =random(0,360);
 //float rotation =vol;
 float aVel=random(-2,5);
    float radius =random(10,vol/3);
   //float radius =vol/5;
   if(radius>10)radius=vol/3;
 float ratio=0.618;
 
 snowflake s=new snowflake(position,velocity,rotation,aVel,radius,ratio);
 snowflakes.add(s);
}

void  emit() 
{
   if(speed >=1)
   {
   for(int i=0;i<speed;i++){ generate();}
   }
   else if(speed>0){if(random(1)<speed){generate();}}
}

void update()  
{
Iterator<snowflake> ite=snowflakes.iterator(); 
while(ite.hasNext()){snowflake s=ite.next();s.update();s.show(); 
if(s.position.y>(height+s.radius)){ite.remove();}                        
} 
}
void run () 
{ emit();update();}
}
void mouseClicked(){
  if(mouseX<640)
  img3=img;
  else 
  img3=img2;
}


