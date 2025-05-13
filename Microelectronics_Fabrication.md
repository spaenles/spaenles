# Introduction
This project involved the fabrication and analysis of microelectronic devices as a hands-on component of a microelectronic fabrication course. The goal was to process a silicon wafer through a series of standard semiconductor manufacturing steps to create a number of functional devices. Following fabrication, the devices were measured and compared to theoretical predictions to evaluate the impact of process variations on device performance. This analysis provided valuable insights into how fabrication conditions influence the electrical properties of microelectronic components.

# Fabrication Steps
I wanted to walk through a number of the steps that our wafers underwent as we processed them. For the analysis stage of the project I chose to measure and do calculations for the mosfets we created so I'll use them as an example to show each step in a little more detail. We used a number of standard processes while creating our devices including photolithography, wet etching, thermal oxidation, metal deposition, and diffusion doping.

Using the mosfet as an example I've drawn up a step by step walkthrough of how we processed the wafers. You can see the lithography masks used for the mosfets, they serve as a stencil allowing us to selectively cut holes through the silicon dioxide layer covering the surface of the wafer. Once we have our holes down to the substrate we can do some process: diffusion doping, metal deposition, or just growing more oxide. You'll be able to see those steps more clearly in the second sketch where I drew out a cross section of the wafer as we go through each step.

**MOSFET fabrication masks:**

![image](https://github.com/user-attachments/assets/020e9d53-dc97-4b46-8eaa-42643ba14d14)


**Fabrication process steps:**

![image](https://github.com/user-attachments/assets/5ad19e2a-caaf-4439-9147-fa9ebe5b2b76)


**Final Results:**

You can see the array of mosfets on the top half of the wafer, hopefully you can also see how the masks and process steps I sketched out factor in to their creation.

![image](https://github.com/user-attachments/assets/0bd65125-b19f-42b2-9739-1383364a9ba0)


# Results/Analysis

As we processed the wafers through each step, we performed calculations related to process parameters, such as expected gate oxide thickness based on oxidation time and temperature, junction depth from drive-in diffusion time and temperature, and gate dimensions determined by the lithography masks. Using these parameters, I was able to calculate the expected electrical characteristics of the MOSFET and generated theoretical I-V curves.

Once the devices were fabricated, we measured their electrical characteristics and compared them with theoretical predictions. The observed discrepancies can be attributed to variations in high-temperature processes, affecting junction depth and gate oxide thickness, as well as batch processing inconsistencies in etching, leading to slight dimensional deviations. These factors resulted in measurable differences between the real and theoretical device performance.

The graphs below illustrate the comparison between the calculated and measured I-V curves.

**Measured I-V Curves:**

![image](https://github.com/user-attachments/assets/ff0bf9f4-a59f-40a5-add1-7f199168f404)

**Calculated I-V Curves:**

![image](https://github.com/user-attachments/assets/8c5d6eac-58d4-4bbc-8d30-77e866bce664)

The second graph shows the idealized I–V curves derived using standard equations for MOSFET operation in the linear and saturation regions. These were calculated under the assumption of nominal parameters—perfect channel geometry, constant mobility, and no second-order effects. In contrast, the first graph displays the measured characteristics from the actual silicon device we fabricated. At first glance, the difference is clear: the measured current levels are generally higher, the transition to saturation occurs at a higher drain voltage, and perhaps most notably, the "flat" saturation region predicted by theory now slopes upward.

So, what's going on here?
### Process Variation:

Even with a tightly controlled fabrication process, no two transistors are identical. Slight variations in photolithography, doping concentrations, oxidation times, and etching can shift the physical dimensions and material properties of the device. For example:

- **Channel length shortening:** A key contributor to the observed differences. A slightly shorter effective channel means lower resistance and higher drive current, pushing the measured I–V curves above the analytical ones.

- **Oxide thickness variation:** A thinner gate oxide increases gate capacitance, thereby enhancing transconductance and current levels.

- **Doping fluctuations:** Can shift the threshold voltage or influence subthreshold behavior.

These process-induced deviations are small in physical terms but have a noticeable impact on electrical behavior. The analytical model, using nominal values, simply can't capture these effects.

### Channel Length Modulation:

One standout feature in the measured curves is the non-flat saturation region. In the ideal MOSFET model, once the transistor enters saturation, the drain current I<sub>D</sub> is supposed to level off. But in our measurements, I<sub>D</sub> keeps rising with V<sub>D</sub>.

This is due to channel length modulation , a second-order effect often omitted in basic models. As V<sub>D</sub>​ increases, the depletion region at the drain end widens, effectively reducing the channel length. A shorter channel means less resistance and, again, more current. The result is a positive slope in the saturation region—subtle, but significant.

Mathematically, this is often captured by modifying the saturation current expression to include a (1 + λV<sub>DS</sub>​) factor, where λ is the channel length modulation parameter. This adjustment helps align theory with real-world data but isn't included in the simpler models used for the analytical curve.

### Other Non-Idealities:

CLM isn't the only second-order effect at play. Real MOSFETs suffer from:

- **Mobility degradation:** Under high electric fields, carrier mobility decreases, which can blunt the current gain expected from increased gate voltage.

- **Parasitic resistances:** Resistance in the source and drain contacts introduces voltage drops not accounted for in ideal models, distorting the effective VGSVGS​ and VDSVDS​ seen by the channel.

These effects combine to make the real transistor behave in a way that diverges from theoretical expectations—sometimes subtly, sometimes dramatically.

# Conclusion

The differences between the analytical and measured I–V curves for our fabricated MOSFET underscore an important lesson in microelectronics: real devices rarely behave like ideal ones. Variations in process parameters, coupled with inherent non-idealities like channel length modulation and mobility degradation, introduce behaviors that are absent from simplified models. Recognizing and accounting for these effects is essential—not just for interpreting experimental data, but for designing robust, high-performance circuits in the real world.


As a quick bonus, during one of labs the mercury vapor bulb used for exposing photoresist in the mask aligner we used exploded! Because this was the only mask aligner in the engineering building we had to move subsequent labs to the engineering research complex where they have a clean room. Normally the clean room is used only for research and graduate level courses so we would not get to work there but because of the mishap we were allowed to use it. It was a super cool experience! I never dreamed I would get to work in an environment like that and as a bonus I got to keep the clean room bunny suit they gave us! Also It was really cool to get to see the machines we had learned about in class and get more detailed explanations of those processes, Chemical vapor deposition and Electron-beam physical vapor deposition are such cool technologies, they are almost like a form of sorcery!

**Me suited up and ready for some clean room action:**

![image](https://github.com/user-attachments/assets/8f669888-613a-46d7-a9b7-795eef99d4ec)
![image](https://github.com/user-attachments/assets/ff5d8bc6-8b58-4868-bef0-4dd6b6403e78)
