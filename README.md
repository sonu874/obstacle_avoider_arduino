# obstacle_avoider_arduino
# An Arduino programming

#include<Servo.h>
Servo myservo;

 /* define L298N motor control pins */
int leftMotorBackward = 1;          
int leftMotorForward = 2;           
int rightMotorForward = 3;          
int rightMotorBackward = 4;         

/*ultrasonic sensor pin connection*/
#define echo 7
#define trig 8

int duration;
int cm;

void setup() {
  myservo.attach(0);                
  pinMode(leftMotorForward,OUTPUT);
  pinMode(leftMotorBackward,OUTPUT);
  pinMode(rightMotorForward,OUTPUT);   
  pinMode(rightMotorBackward,OUTPUT);
  pinMode(echo,INPUT);
  pinMode(trig,OUTPUT);
  Serial.begin(115200);
  myservo.write(90); 
}

//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
/////////////////////////////////  ---------------   MAIN CODE   ------------------ ///////////////////////////////////////////////////
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
void loop() {
  
   check();
   while(cm<=12||cm<0)                      /*If forward distance is less than 10 cm or reading is incorrect then stop for 50 miliseconds and check right distance.*/
   {               
     stops();                                
     delay(100);
     myservo.write(0);                      /*U.S sensor faces right side.*/
     delay(300);
     check();

     myservo.write(90);                     /*U.S sensor is back to position ,i.e faces front.*/
     delay(300);
    
     if(cm>12)                              /*If right distance is greater than 12 cm than turn right.*/
     { 
          sharpright();
          delay(400);
          check();
     }
     else                                   /*If right distance is not greater than 12 cm it turns left.*/
     {
       
          sharpleft();
          delay(400);
          check();
     }
   }
   check();                                 /*If forward distance is greater than 12 cm means bot goes forward.*/
   
   while(cm>12)   
   { 
     myservo.write(90);                     /*U.S sensor is at original position ,i.e faces front.*/
     delay(100);
     forward();
     check();
   }
   
}
void check()                                /*This function measures the distance of obstacle from the U.S sensor*/
{
   digitalWrite(trig,LOW);
   delayMicroseconds(30);
   digitalWrite(trig,LOW);
   delayMicroseconds(2);
   digitalWrite(trig,HIGH);
   duration=pulseIn(echo,HIGH);             /*Calculates after how much time we are getting the response*/
   cm=(duration*0.034)/2;                   /*speed of sound=340m/s i.e,0.034cm/microsec.*/
   Serial.print("Distance=");
   Serial.println(cm);
  }
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
/////////////////////////////////  ---------------   MOTOR FUNCTIONS  ------------------ ///////////////////////////////////////////////////
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
/********************************************* FORWARD *****************************************************/
void forward()   
{
  digitalWrite(leftMotorForward,HIGH);
  digitalWrite(rightMotorForward,HIGH);
  digitalWrite(leftMotorBackward,LOW);
  digitalWrite(rightMotorBackward,LOW);
  Serial.print("Forward//////////");         /*To distinguish between motion of bot and distance in front on Serial Monitor.*/
}

/********************************************* TURN SHARPLEFT *****************************************************/
void sharpleft()   
{
  digitalWrite(rightMotorForward,HIGH);
  digitalWrite(leftMotorBackward,HIGH);
  digitalWrite(rightMotorBackward,LOW);
  digitalWrite(leftMotorForward,LOW);
  Serial.print("Left");
}

/********************************************* TURN SHARPRIGHT *****************************************************/
void sharpright()   
{
  digitalWrite(leftMotorForward,HIGH);
  digitalWrite(rightMotorBackward,HIGH);
  digitalWrite(rightMotorForward,LOW);
  digitalWrite(leftMotorBackward,LOW);
  Serial.print("Right");
}
/********************************************* STOP *****************************************************/
void stops()   
{
  digitalWrite(leftMotorForward,LOW);
  digitalWrite(leftMotorBackward,LOW);
  digitalWrite(rightMotorForward,LOW);
  digitalWrite(rightMotorBackward,LOW);
}
