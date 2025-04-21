
# Project Overview

Excess phosphorus in agricultural runoff is a major environmental concern—it feeds harmful algae blooms that deplete oxygen in waterways and threaten aquatic life. For our senior capstone project, we were tasked with the creation of an automated phosphate sensing system that offers an affordable and accessible way to monitor phosphate levels in water sources, especially for agricultural applications.

**Eutrophication as a result of excess phosphate:**

![image](https://github.com/user-attachments/assets/4087380a-c650-4ce4-9e98-d839bb1d4fa7)


The goal was to develop a portable, low-cost system that automates chemical mixing, optical detection, and data analysis—all controlled by an Arduino-based platform. By leveraging a colorimetric reaction and photodetection, the system measures phosphate concentrations with reasonable precision. Designed for use near water sources (rather than submerged in them), this land-based system emphasizes ease of maintenance, reduced environmental interference, and lower production cost than comparable commercial solutions.

Our prototype successfully integrates three core components:
1) An automated dispensing mechanism that combines water samples with chemical reagents for color-based detection.

2) A photodetection system that senses the color intensity of the chemical reaction using a phototransistor.

3) A data processing and calibration routine that analyzes sensor data to estimate phosphate concentration using the Beer-Lambert Law.

## System Design

From the start, one of our biggest constraints was the microcontroller selection. We needed something powerful enough and with enough I/O to handle multiple components — pumps, sensors, data storage — while still being efficient and low-power. We chose an Arduino Uno because it met those requirements while also being low cost and having a wealth of documentation to aid in the software portion of the project.

Any light leakage could throw off our results, as we learned through our various development iterations. So, we needed to make sure that no ambient light could sneak into the chamber and skew the data. We opted for a comercially available container that would completely isolate the system from outside light while also allowing it to be easily portable.

Choosing the chemical reagent we would use also posed an intereseting challenge. Through our research we came across a number of options but eventually settled on vanadate-molybdate as it is already widely used for this purpose and is easy enough for us to source from a supplier. When exposed to phosphate the vanadate-molydate causes a color change, shifting to yellow, with the intensity of the change dependent on phosphate concentration as defined by the Beer-Lambert Law. This decision also drove our choice to go with peristaltic pumps. For one they are commonly used in situations that require accurate dispensation of fluids, but more importantly the pumping action is done without contacting the fluid. The vanadate-molybdate is a corrosive agent so it was important to choose a pump that would not be affected by this.

**Vanadate-Molybdate reacted with various phosphate concentrations:**

![image](https://github.com/user-attachments/assets/40328f46-24a0-445f-9768-05d2d57ecdd6)


### Designing the Reaction and Detection Chambers

At the heart of the system is a 3D-printed reaction chamber. This is where the water sample and reagent are mixed to ensure proper blending. A 1:3 sample-to-reagent ratio was used, as it consistently produced reliable results.

Mounted directly onto the reaction chamber are two key components: a 570 nm phototransistor and a blue LED that emits light in the 430–500 nm range. This specific wavelength pairing allowed us to take advantage of the absorbance spectrum of the yellow phosphate reagent mixture. Light passes through the sample, and the phototransistor detects how much of it is absorbed — a change that's directly related to phosphate concentration.

We also made sure to optimize the light path length, keeping it between 1–10 cm to match the high absorbance of the yellow solution and ensure consistent readings.

The chamber design went through a number of iterations, each one adding more features to ensure smooth operation of the system and to accomodate components that were added throughout development.

**3D model of reaction chamber:**

![image](https://github.com/user-attachments/assets/dfc966b7-3f02-46bf-9a8b-38f308efed46)


### Choosing Phototransistor Over Photodiode

While both photodiodes and phototransistors can detect light, we went with the phototransistor due to its higher sensitivity and built-in amplification. It gave us better resolution in detecting small changes in color — a trade-off we accepted despite its slightly slower response time and higher cost. We mounted the phototransistor and LED on fixed holders inside the chamber to maintain a constant path length for consistent measurements.

### The Final Hardware Setup

The brains of the operation is an Arduino UNO R3, which collects data from the phototransistor, controls three pumps, logs data to an SD card, and interfaces with a level sensor — all powered by a 12V rechargeable battery. Here's how the process flows:

1) Pump 1 pulls in the water sample until the level sensor detects that a 15ml dose of water has been added

3) Pump 2 adds a 5ml dose of the reagent as measured by the level sensor

4) The LED shines through the mixture onto the phototransitor while the Arduino logs the resulting measurements

5) Pump 3 flushes the mixture into a waste container after testing

6) Phosphate concentration is extrapolated using the measured values and a calibration equation then logged to the SD card



**System Diagram:**

![image](https://github.com/user-attachments/assets/de216ef3-937a-435e-a118-1a74e7661c21)


## Software & Data Collection

An Arduino program controlled reagent dispensing and data logging. To manage data collection smoothly, we added an interrupt button that pauses the system so the SD card can be removed for data offload. Once reinserted, the system restarts from the beginning of the program, ensuring nothing is missed.

Here are a couple of code snippets for some of the critical functions:

**Measurment logging code:**
```cpp
void Measure() { 
  digitalWrite(ledPin, HIGH); // Turn LED on 
  delay(50); 
  data = 0;
  // Take num readings of measurements 
  for (int i = 0; i < numReadings; i++) { 
    dataArray[i] = analogRead(pDiodePin);  // Read and store sensor value 
    delay(sampleInterval);              // Wait for next sampling interval  
    data += dataArray[i]; 
    //writeRawToSD(millis()/1000,dataArray[i]); // Write raw data to SD, for testing
    Serial.println(dataArray[i]);
  } 
 
  digitalWrite(ledPin, LOW); // Turn LED off 
 
  //Bellow is for data=average of the readings
  data = data/numReadings;
  // Interpolate concentration from data
  concentration = (data-641.61)/(-15.536);
  //return data; 
} 
```

**Pump control code:**
```cpp
void FillChamber() {
  int pv = 0;
  while(pv != 1){
  
  // Handle pause interrupt
  if (isPaused) {
    pv =1;
      return; // Exit if paused
    }

    // Read the states of sensePin1 and sensePin2 
  int sense1State = digitalRead(sense75); 
  int sense2State = digitalRead(sense100); 
  
  // Control pumps based on the states of sensePin1 and sensePin2 
  if (sense1State == LOW && sense2State == LOW) { 
    // Turn on pump 1 
    analogWrite(pump1Pin, 200); 
    digitalWrite(pump2Pin, LOW); 
    digitalWrite(pump3Pin, LOW);
    
  } 
  else if (sense1State == HIGH && sense2State == LOW) { 
    // Turn on pump 2 
    digitalWrite(pump1Pin, LOW); 
    analogWrite(pump2Pin, 200); 
    digitalWrite(pump3Pin, LOW);
    Serial.println("Sense 1 triggered");
  } 
  else if (sense1State == HIGH && sense2State == HIGH) { 
    // Turn on pump 3  
    digitalWrite(pump1Pin, LOW); 
    digitalWrite(pump2Pin, LOW); 
    digitalWrite(pump3Pin, LOW); 
    Serial.println("Sense 2 triggered");
    pv = 1;
  } 
   
  delay(100);  // Small delay to avoid rapid toggling 
} }
```
## Calibration & Testing

To ensure accurate phosphate concentration readings, we calibrated our system using a series of premade water samples with known phosphate levels. This process was essential for translating the voltage readings from the phototransistor into meaningful concentration values in mg/L.

We began by running the complete sensing cycle on multiple standard samples, each containing a precise amount of phosphate. During each run, the system automatically mixed the sample and reagent, initiated the colorimetric reaction, and recorded the corresponding phototransistor output. These values were then saved to the SD card for analysis.

With this data in hand, we plotted the sensor readings (voltage) against the known phosphate concentrations. The result was a clear, consistent relationship between light absorbance and phosphate level. Using Python and SciPy, we performed a linear regression to model this relationship and generate a calibration curve.

![image](https://github.com/user-attachments/assets/8675e6d8-7be5-43c5-8647-489b5492812f)


The resulting calibration equation allowed us to extrapolate unknown phosphate concentrations from raw sensor data during field tests. This transformation was implemented in post-processing, ensuring that even real-time field measurements could be reliably converted into environmental insights. This calibration step was critical not only for scientific accuracy but also for validating the mechanical and optical design of our sensor chamber.


## Results & Discussion

After calibration, we evaluated the system’s performance using six standard phosphate solutions ranging from 2 ppm to 40 ppm. While we also tested samples up to 80 ppm, the primary focus was on lower concentrations that are more representative of environmental water systems. We found that system was able to fairly accuratley determine phosphate concentrations and based on the data resolution, the system achieved a sensitivity of approximately 0.066 ppm, a promising level of accuracy for field applications. In addition the sensor data revealed a clear inverse relationship between phosphate concentration and light intensity. As phosphate levels increased, the reaction solution became a deeper yellow, absorbing more blue light from the LED. This reduced the amount of light reaching the phototransistor and, consequently, lowered the output current.



## Conclusion and future work

Our system successfully automated the detection of phosphate in water samples by integrating fluid control, optical sensing, and data logging. The Arduino-controlled pumps dispensed precise volumes of sample and reagent, while the level sensor ensured consistent mixture ratios. Encased in a custom 3D-printed reaction chamber and light-blocking enclosure, the optical setup (LED and phototransistor) reliably detected phosphate-induced color changes.

**Completed System:**

![image](https://github.com/user-attachments/assets/363a3abc-0264-4cf5-a922-513afafbaefa)

**Total system cost:**

![image](https://github.com/user-attachments/assets/9da1bc78-a150-41bf-8950-6f9072f79749)

Note: A number of components were provided to us by the university, so they are marked as $0



The calibration tests confirmed that our setup could detect a range of phosphate concentrations with high reliability, making it suitable for monitoring environmental water quality in the field.

Looking ahead, we identified several improvements that could elevate both the accuracy and usability of the device:

- Smaller, more integrated housing for easier deployment in outdoor or remote locations.

- Improved reagents, such as phosphomolybdate blue, for more vivid and consistent color reactions.

- Refined calibration procedures, including better control of light exposure and the use of a broader concentration range.

- Advanced signal processing to compensate for sensor variability and ambient conditions.

By addressing these enhancements, future iterations of this system could serve as powerful tools for real-time, low-cost phosphate monitoring in diverse environments.




## A quick side note
This was a really fun, engaging, and challenging project. The whole class was ran like we were a bunch of small startup companies with each group creating a product for a customer (sponsor) and regularly reporting progress to a direct report (faculty facilitator). We were given a tight budget and a strict timeline and it was really neat to get some exposure to what the real world might be like. I learned a lot about group dynamics, how to manage numerous deadlines, professional writing, and more. For our efforts we were awarded 4th place in the engineering college design day competition. It was really a cool experience and I'm proud of the work that my group and I did. 

**Here we all are:**

![1737648002013](https://github.com/user-attachments/assets/99bcc9bf-397e-4624-806c-90de5d63bf5e)

