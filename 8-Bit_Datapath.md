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

## My Contributions: The Shifter
Great, now that we have gone over the basic process I wanted to show you the part of the project that I was responsible for, the shifter! I'm not going to explain exactly how it works, that's a little beyond the scope of what I want to include here but you can check ot these great explanations: [High level overview](https://amazingalgorithms.com/definitions/barrel-shifter/), [Detailed explanation](https://www.princeton.edu/~rblee/ELE572Papers/Fall04Readings/Shifter_Schulte.pdf).

The shifter I implemented is capable of manipulating the full 8-bit word up to 3 bits either left or right, and can perform both rotations and logical shifts. I initially wanted to do up to 8 bits in either direction but decided that might be too ambitious as I really wanted to get a completed working shifter. In hindsight this was a mistake, adding that extra bit of functionality would not have been a huge amount more work only requiring one more shift stage, and this added complexity might have been enough to push us into first place. I also explored adding arithmetic shifting functionality, I thought I would be able to implement it with just a few additional gates in the masking circuitry and one additional control bit but I wasn't able to get the functional simulations working in time before I had to start on the layouts.

Here is a brief explanation of the shifter components and what each does so that you can better understand the whole thing:

The shifter has two main stages, the rotate stage and the masking stage. The rotate stage is pretty self explanitory, it takes an 8 bit input and can rotate that input up to 3 bits either left or right. The amount and direction rotated is controlled by three control bits, I have named them *Left*, *K0* and *K1*. *Left* controls direction, *K0* and *K1* combined control shift amount. After the rotate stage is a masking stage, which does the masking necessary to turn a rotation into a logical shift using one control bit called *Shift* alongside the control bit *Left*. I broke out the control signal generation for each sub-component into their own circuits, named Rotate Control Gen and Masking control. The control signal generation circuitry is based on decoding the desired mux control signals for each given input, I'll show that decoding along with the drawings from the ideation phase. Breaking these control signal blocks off into seperate components allowed me to break the shifter down into smaller components, which came in handy later during the integration of all three parts. It allowed us to nestle the various shifter parts into convenient gaps that appeared between the ALU and SRAM array. In addition, to make creating the layouts easier I decided to break the rotate and masking components into bitslices. Essentially instead of creating a monolithic 8 bit wide block of muxes I devised a way to break that structure into single bit components which could then be copied as many times as necessary, in this case 8, to create the larger 8-bit array. Doing so with the masking circuitry provided the same ease of implementation. 

With all that out of the way, here's a big dump of images showing what I made:

### Ideation Phase
Please forgive my lack luster drawing skills and handwriting

**Rotate Block Sketch and Decoding:**

![image](https://github.com/user-attachments/assets/6d438b1a-e435-46d8-9e59-ba43e0b2d9b9)

**Rotate Bitslice Sketch:**

![image](https://github.com/user-attachments/assets/8e2964a9-512c-418b-914a-ebb440d1f54e)

**Masking Bitslice Sketch:**

![image](https://github.com/user-attachments/assets/1dd37d40-98eb-49db-b33b-712e02d1aba8)


**Masking Bitslice Decoding:**

![image](https://github.com/user-attachments/assets/dac89963-7125-4fb8-ae1d-e24ed6f37315)

### Schematics

**Rotate Block Schematic:**

![image](https://github.com/user-attachments/assets/8eb8b8b1-07c9-494b-aa8f-ac2c640c2412)

**Rotate Bitslice Schematic:**

![image](https://github.com/user-attachments/assets/0ac7e358-457e-446e-ab5b-97d317ef0259)

**Rotate Control Generation Schematic:**

Again this is from decoding the control signals, you can see the initial sketch of this to the right of the rotate block sketch

![image](https://github.com/user-attachments/assets/ab896b2e-807f-4974-9b2b-5c092632d5b2)

**Masking Block Schematic:**

![image](https://github.com/user-attachments/assets/82a52774-17ee-48cc-b757-50b3f1c85aaa)

**Masking Bitslice Schematic:**

![image](https://github.com/user-attachments/assets/bab93608-3a66-4c49-8b3d-c95986ef5df9)

**Masking Control Schematic:**

Hopefully can see how I stripped this bit of control circuitry off of the bitslice. It is present in the initial sketches I have but at some point I realized that by removing it from each slice I can greatly reduce the number of gates and therefore area used by the masking module. Doing it this way means I only need one set of circuitry for all 8 bit slices.

![image](https://github.com/user-attachments/assets/6dc4353e-f4ab-4baf-9bf7-35def4e808ab)

### Layouts

**Rotate Block Layout:**

![image](https://github.com/user-attachments/assets/4cbd33aa-e48d-4217-8c36-9db412a0ba2a)

**Rotate Bitslice Layout:**

![image](https://github.com/user-attachments/assets/dc238e42-9e5e-4118-8aba-dd8e44c40503)

**Rotate Control Generation Layout:**

![image](https://github.com/user-attachments/assets/2c7b73ca-25ad-4bef-a62b-f48eb9552561)

**Masking Block Layout:**

![image](https://github.com/user-attachments/assets/db20927a-c202-40b7-bf73-23718d437516)

**Masking Bitslice Layout:**

![image](https://github.com/user-attachments/assets/8e895ab6-559d-4b82-92e8-4d416e2d1c7e)

**Masking Control Layout:**

![image](https://github.com/user-attachments/assets/7f6f59ca-75ca-42fe-964f-70ad283abdfd)
