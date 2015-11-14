# ER-2
Arduino Code:

/* Purpose: Record distance and light values, and put it into a recursion program
 *  Date: 14/11/2015
 *  Author: R. Kurdyak
 */

// Define the sensor pins
int ldrMeasure1 = A0;
int ldrMeasure2 = A1;

void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
}

void loop() {
  // put your main code here, to run repeatedly:
int lightVal1 = analogRead(ldrMeasure1);
int lightVal2 = analogRead(ldrMeasure2);
lightVal1 = constrain(lightVal1, 400, 800);
lightVal2 = constrain(lightVal2, 400, 800);
lightVal1 = map(lightVal1, 400, 800, 1, 10);
lightVal2 = map(lightVal2, 400, 800, 0, 255);
Serial.print("Light value 1 = ");
Serial.println(lightVal1);
Serial.print("Light value 2 = ");
Serial.println(lightVal2);
}

Processing Code:

import processing.serial.*;
/* Purpose: To import Arduino information and represent it graphically
   14/11/2015
   Robert Kurdyak
*/ 

Serial port;
Serial port1;
float lightVal1 = 0; // The variable used to represent the LDR reading
float lightVal2 = 0; // The variable used to represent the distance sensor reading
float counterClockwise = random(10, 50); 
float clockwise = random(-40, -80); // Random values for the branch positions


void setup() {
  
  size(700, 700);
  
  port = new Serial(this, Serial.list()[2], 9600); // Sets the reciever to the USB plug
  port.bufferUntil('\n');

  translate(width/2, height); // Moving the origin point to the bottom of the area, halfway across
  scale(1, -1); // 
  rotate(radians(90)); // rotating the graphic 90º counter clockwise, so it faces 

  myLine(0, 0, lightVal1, 0, lightVal2);
}


void myLine(float x1, float y1, float x2, float y2, float exitValue) {
pushMatrix(); // pushMatrix saves the current coordinate system, aka translate and rotate products
  stroke(lightVal1, lightVal1, lightVal1); // coloring the trunk red
  // The trunk
  line(x1, y1, x2, y2);    // Create the trunk, using the myLine variables
  translate(x2, y2);      // Put the origin at the end point of the trunk
  

  rotate(radians(counterClockwise)); // rotate the axis a random value counter-clockwise
  // Draw the left branch
  stroke(lightVal1, lightVal1, lightVal1); // coloring the left branch green
  line(x1, y1, x2 * 0.7, y2); // Draw a branch, using the myCircle values
  translate(x2 * 0.7, y2); // Set the origin point at the the endpoint of the left branch
  // Recurse if x2 is greater than 20
  if (x2 > exitValue) {
    myLine(x1, y1, x2 * 0.7, y2, exitValue);
  }
  translate( - x2 * 0.7, - y2); // move the origin point to the endpoint of myLine
  
  //Draw the right branch
  
  rotate(radians(clockwise)); // Rotate the axis a random value clockwise
  stroke(lightVal1, lightVal1, lightVal1);
  line(x1, y1, x2 * 0.7, y2); //Make the right branch
  translate(x2 * 0.7, y2); // Move the origin point to the end of the right branch
  // Recurse only if x2 is greater than 20
  if (x2 > exitValue) {
    myLine(x1, y1, x2 * 0.7, y2, exitValue);
  }
  popMatrix(); // popMatrix restores the earlier coordinate system
}

void serialEvent1 (Serial port) {
 lightVal1 = float(port.readStringUntil('\n')); // Converts LDR1's value into a float, so we can use it for color
 lightVal2 = float(port.readStringUntil('\n')); // Converts LDR2's value into a float, so it can be used for # of recursion repeats
}

//void serialEvent2 (Serial port) {
  //lightVal2 = float(port.readStringUntil('\n')); // Converts LDR2's value into a float, so it can be used for # of recursion repeats
//}
