# Introduction
This project was the final part of a VLSI Design class I took in the spring of 2024. Each group of 3–4 students was tasked with designing the schematic and physical layout of an 8-bit microprocessor data path, including an Arithmetic Logic Unit (ALU), a barrel shifter, and a register file, using CMOS circuitry and Cadence VLSI design tools. The completed projects were judged based on speed, minimization of area, number of operations and difficulty of the operation set. A panel of Intel representatives and our course professor served as judges, with the top team receiving the Intel Outstanding Project Award. 

I had the privilege of working with two like-minded teammates, and together, we developed a design we were extremely proud of. We secured a close second place, ultimately edged out by a team with a more ambitious operation set. Our final design came out being quite compact, while also beating the timing requirements set out by the instructors. Early on in the project we decided to split the project into the main components, with each of us taking a component and architecting it from start to finish. We were able to work together really well, with lots of communication throughout the entire design process, leading to our components meshing together well when we brought them together for integration. You can see the results of our collaboration in the complete data path layout seen below.

**Complete 8-Bit Data Path:**

![CompleteDataPathsmall](https://github.com/user-attachments/assets/ab02857a-c929-46b8-bbae-d4bb2794ebae)


## Design Workflow
As a little bit of insight into what exactly was done for this project, and how to create this kind of thing I figured I would walk through the steps for creating a basic inverter.

First the workflow as a list:

1. Ideate the basic schematic necessary
2. Create that schematic in CAD
3. Simulate the functionality of the schematic
4. Create a layout for the schematic
5. Simulate the performance of the layout

### 1) Ideation
Step one is to come up with an idea for how to implement what you are trying to make, for an inverter this is pretty simple. But this stage took much more time and effort for the larger more complex components of the project. I have pages and pages worth of notes and drawings that I made when designing the shifter, I'll probably share some of those later.

### 2) Schematic Creation
Next up is taking the design you came up with in step one and implementing it in CAD, we used the Cadence Virtuoso suite for this project.

**Inverter Schematic:**

![image](https://github.com/user-attachments/assets/13a2ebc6-9260-4a29-892c-b18be2c758ce)

### 3) Simulate your schematic
A critical step of this design process is verifying that what you have created actually does what you think it does! It would absolutely suck to spend a few hours making a layout only to have to restart because your initial schematic isn't what you thought it was. To do this we have to create a stimulus file that tells the simulator what to do. We use the pin labels we created in the schematic and define what values we send to the input pins at various points in time. When we simulate the schematic this way we can create a waveform plot that we can then examine to determine if the schemtic functions properly. You can see below that the inverter schematic looks to be doing what it is supposed to:

**Inverter Functional Simulation:**

![image](https://github.com/user-attachments/assets/f8c56e5e-e347-4b6d-bcd0-28e378ccb2bf)

I don't have my inverter stimulus file but here is one for another schematic. You can see how we are defining a waveform behavior for each input pin in order to test each possible input variation.

**Stimulus file for** ![image](https://github.com/user-attachments/assets/bd05b226-6f16-4fb4-a304-ce4275c5866e)**:**

![image](https://github.com/user-attachments/assets/6897c309-d53c-4f90-a888-c092ff91ce5a)

### 4) Create the layout
This is a pretty self explanitory step, but definetly one of the most time consuming and laborious. There are many, many rules that you have to follow when creating your layout and you have to ensure that your layout precisely matches your schematic. Luckily there are a number of tools that can be used to check that they do match and that all rules have been followed, getting those DRC and LVS checks to pass is such an awesome feeling! Here's the layout I came up with for my inverter:

**Inverter Layout:**

![image](https://github.com/user-attachments/assets/0a720330-0365-40e5-98c5-a48bef89a352)

### 5) Simulate the performance of the layout
The final part of the process is another simulation, this time to find out how the layout performs! Strictly speaking this isn't necessary to get a functional layout but it sure is nice to know how well your design does, especially if you are chasing after a performace target. The basic process is very similar to the functional performance from earlier. This time however we dive a little deeper into the function of the circuit, we need to analyze all of the input transitions that yield a change in the output of the circuit. For an inverter this is simple, every time we change the input the output changes. The stimulus file we create has to put the inverter through both a transition from low output to high output and the reverse, high to low. Then we can use some in built tools of the waveform graphs to get the transient timings of the circuit. Doing all that yields the following:

**Inverter Transient Simulation:**

![image](https://github.com/user-attachments/assets/3be778e0-5ac0-4428-b3e7-b827fee4776e)

Here are the results in a table:

![image](https://github.com/user-attachments/assets/d67d2c58-d8a5-4994-b2cd-83ab175bafa8)

Tpdr is the propogation delay for rising edges, going from low to high. Tpdf is propogation delay for falling edges. Rise and fall times are the time it takes for the output to go from one threshold voltage to another.


## Maybe explain shifter?

## Show various component schematics and layouts



























