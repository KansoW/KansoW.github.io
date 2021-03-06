---
layout: project
title: Two-wheel Self-balancing Robot
date: March 26, 2018
image: https://raw.githubusercontent.com/KansoW/KansoW.github.io/master/public/images/former_front.jpg
---

## Project description

# **Two-wheel Self_balance Robot with Arduino** 

______
### **General Idea**
##### This is a project that combines classical ideas from other projects. 
 1. The inverse pendulum problem that is often seen in the dynamics and control peojects or courses.
 2. The Arduino car driving project that is often used as a startup proejcts for learning Arduino.
 3. The IMU (_Inertial Measurement Unit_) and I2C communication that is widely used for balancing projects.
##### This project does a simple mission: **Balancing by itself on two wheels**. 
##### The idea is that this robot will use **IMU sensor** to detect leaning angel and **hall-effect sensor** to detect linear movement. Then the data will be used by the **microcontroller (Arduino Uno)** to put in a **LQR (linear-quadratic regulation)** controller to find the proper motor PWM. Then the motor driver unit will apply PWM to drive the motors accordingly. The whole sensing and responding system will run at **400 Hz**. 

_____
### **Materials** 
#### **Electrical**
##### 1. Arduino UNO V3 
##### <img src="https://store-cdn.arduino.cc/usa/catalog/product/cache/1/image/520x330/604a3538c15e081937dbfbd20aa60aad/a/0/a000066_featured.jpg" width="400" height="350" />
##### 2. DF Robot L298N Motor Driver
##### <img src="https://www.dfrobot.com/wiki/images/thumb/c/c5/IGP59861_new.jpg/600px-IGP59861_new.jpg" width="400" height="350" />
##### 3. Pololu 20.4:1 Metal Gearmotor 25Dx50L mm LP 12V with 48 CPR Encoder
##### <img src="https://a.pololu-files.com/picture/0J3799.600x480.jpg?03a71976857bd4e6d447a7cd8817f31c" width="400" height="350" />
##### 4. MPU6050 6-axis IMU (accelerometer + gyro)
##### <img src="https://www.makerlab-electronics.com/my_uploads/2015/05/mpu-6050-1.jpg" width="400" height="350" />
##### 5. 12V 1600 mAh NiMh Battery
##### 6. Mini breadborad
##### 7. Jumper wires
##### 8. Power cable toggle switch

______
#### **Mechanical**
##### 1. Pololu Acrylic Chassis Borads
#####   <img src="https://a.pololu-files.com/picture/0J1573.600x480.jpg?83308509dcd3b4cfda6e51d1ab730d27" width="400" height="350" />
##### 2. DF Robot Metal Alluminum Standoffs
#####   <img src="http://image.dfrobot.com/image/cache/data/FIT0064/IMG_1595-450x300.jpg" width="400" height="350" />
##### 3. Pololu 80mm dia Wheels
#####   <img src="https://a.pololu-files.com/picture/0J2583.600x480.jpg?074bb6080dfb9b4b2349997803f1aae0" width="400" height="350" />
##### 4. Pololu 25mm Metal Gear Brackets
##### 5. Polou 4mm Motor Shaft Adapters

______
### Balancing Control Theory
#### PID:
##### PID control is the most common and eaisest method to control the motion of the __*Inverse Penculum*__ or the __*Self-balance*__ questions.
##### I used the **leaing angle** as the controllable variable and **the linear pushing or pulling force** as the input. The IMU will sense gyro reading in the **x direction** and acceleromter reading in the **y and z direction**, of which the code snip is shown below:
```cpp
  // read acceleration and gyroscope values
  accY = mpu.getAccelerationY();
  accZ = mpu.getAccelerationZ();  
  gyroX = mpu.getRotationX();
```
##### Below is the calculatoin of leaning angel and PID algorithm for motor PWM:
```cpp
  // calculate the angle of inclination
  accAngle = atan2(accY, accZ)*RAD_TO_DEG;
  gyroRate = map(gyroX, -32768, 32767, -250, 250);
  gyroAngle = (float)gyroRate*incTime;  
  // with complementary filter filter
  currAngle = 0.999*(prevAngle + gyroAngle) + 0.001*(accAngle);
  // claculate angle error 
  err = currAngle - targetAngle;
  errSum += err;  
  errSum = constrain(errSum, -600, 600);
  // calculate PWM output with PID 
  motorPWM = Kp*err + Ki*errSum*incTime + Kd*(currAngle-prevAngle)/incTime;
```

____
#### LQR

###### I used **Wolfram Mathematica** to build the model of this project. Because I have done the simulation of the **Inverse Pendulum** problem before, it's easier for me to build up from there. I also calculated the **LQR control gains (leaning angel, leaning angel changing rate, linear movement, linear speed)** in Mathematica.
###### Then I set the gains in my Arduino code as defined global variables so that the motorPWM will be calculated accordingly.
###### Here I have a snippet of the LQR calculation in my code:
```cpp
  y = (float)(encode/CPR)*0.08*3.1415926;
  dy = (y-prevy)/incTime;
  dtheta = (float)(currAngle-prevAngle)*DEG_TO_RAD/incTime;
  // calculate PWM output with LQR
  motorPWM = constrain(1*(-Ky*y - Kdy*dy - Kth*err*DEG_TO_RAD - Kdth*dtheta), -255, 255);
```
###### And here below is where I set the control gains at the front of the code:
```cpp
// LQR gains
#define Ky 2236
#define Kdy 967
#define Kth -2051
#define Kdth -166
```

______
#### Turning
###### Later on I added turning algorithms so I can make it turn left or right with bluetooth control:
```cpp
  switch (turnStat) {
    case 1 :
      if (abs(motorPWM) >= 200) {
        pitchDif = 120;
      }
      else if ((motorPWM < 200 && motorPWM >= 100) || (motorPWM <= -100 && motorPWM > -200)) {
        pitchDif = 80;
      }
      else if ((motorPWM < 100 && motorPWM > 50) || (motorPWM < -50 && motorPWM > -100)) {
        pitchDif = 40;
      }
      else {
        pitchDif = 20;
      }
      break;
    case -1 :
      if (abs(motorPWM) >= 200) {
        pitchDif = -150;
      }
      else if ((motorPWM < 200 && motorPWM > 100) || (motorPWM < -100 && motorPWM > -200)) {
        pitchDif = -100;
      }
      else if ((motorPWM < 100 && motorPWM > 50) || (motorPWM < -50 && motorPWM > -100)) {
        pitchDif = -50;
      }
      else {
        pitchDif = -20;
      }
      break;
    case 0 :
      pitchDif = 0;
      break;
  }
  //motorPWM = 10*(-Kth*err*DEG_TO_RAD - Kdth*gyroAngle);
  // constrain motor output range
  leftmotorPWM = constrain(motorPWM+pitchDif, -255, 255);
  rightmotorPWM = constrain(motorPWM-pitchDif, -255, 255);
  prevAngle = currAngle;
  prevy = y;
  
```

_______
#### Bluetooth Connection and Control
###### I put on a bluetooth module on the Arduino in order to communicate with my laptop and preferrably, my cell-phone. The module I used is a **Bluetooth HC-05** which I bought from Amazon.com:
###### <img src="https://www.robomart.com/image/cache/catalog/RM0847/hc-05-bluetooth-module-with-base-bluetooth-modules-rm0847-by-robomart-917-500x500.jpg" width="400" height="350" />
#### 
#### Videos:
##### __Click the link below to be directed to the videos__
###### [Simple PID_1](https://youtu.be/JYqBO8-WjIE)
###### [Simple PID_2](https://youtu.be/JYqBO8-WjIE)


_____
### Refrence and Sources
##### 1. SUHARDI , et al. “[TWO-WHEELED BALANCING ROBOT CONTROLLER DESIGNED USING PID.](https://pdfs.semanticscholar.org/5a5b/3b44c6456ae231162ce013a8e493dc1bc6db.pdf)” Faculty of Electrical and Electronic Engineering Universiti Tun Hussein Onn Malaysia, Jan. 2015, pp. 1–39.
##### 2. Stanford University EE363. “[Linear Quadratic Regulator: Discrete-Time Finite Horizon.](https://stanford.edu/class/ee363/lectures/dlqr.pdf)” Linear Quadratic Regulator: Discrete-Time Finite Horizon, Jan. 2009, pp. 1–32.()
##### 3. CDS 110b. “[Lecture 2 – LQR Control](https://www.cds.caltech.edu/~murray/courses/cds110/wi06/lqr.pdf).” CALIFORNIA INSTITUTE OF TECHNOLOGY Control and Dynamical Systems, 11 Jan. 2006, pp. 1–14.
##### 4. [DF Robot MD1.3 2A Dual Motor Controller SKU DRI0002](https://www.dfrobot.com/wiki/index.php/MD1.3_2A_Dual_Motor_Controller_SKU_DRI0002)
##### 5. [Pololu 20.4:1 Metal Gearmotor 25Dx50L mm LP 12V with 48 CPR Encoder](https://www.pololu.com/product/3263)
##### 6. [Instructables ARDUINO SELF-BALANCING ROBOT](https://www.instructables.com/id/Arduino-Self-Balancing-Robot-1/)
##### 7. [MPU-6050 auto-calibration](http://42bots.com/tutorials/arduino-script-for-mpu-6050-auto-calibration/)
##### 8. [HC-06 Bluetooth module datasheet and configuration with Arduino
](http://42bots.com/tutorials/hc-06-bluetooth-module-datasheet-and-configuration-with-arduino/)
##### 9. [WOLFRAM BLOG Stabilized Inverted Pendulum](http://blog.wolfram.com/2011/01/19/stabilized-inverted-pendulum/)
##### 10. [Stages of development of the robot-balancer](http://spin7ion.ru/ru/blog/balancerBuildSteps)





