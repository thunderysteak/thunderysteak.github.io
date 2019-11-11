# Hardware modding Dell Wyse D90D7 Thin Client to add an SSD

Many people are not aware about this, but thin clients are just low power computers that you can usually install Linux or Windows on them and can repurpose for various things such as HTPCs, low power terminals with lightweight web browsing or in situations where you need the low power and form factor of a Raspberry Pi but need x86. I got my refurb unit for 35 Eur shipped from eBay, with the original power brick, DV-I to VGA adapter, paperwork and a mouse. It almost looked like new old stock.


### Things needed
* Phillips #00 PH00 Size Precision Screwdriver
* Zipties
* [Sata Male to Female Extension Cable](https://www.amazon.co.uk/2Pack-iGreely-22-pin-Female-Extension-7-15-Sata-Male/dp/B073GFC98N/)
* (Optional) [Thermal pads](https://www.amazon.co.uk/ARCTIC-Compound-Efficient-Conductivity-Handling/dp/B00UYTTDO6/)
* 2.5" SSD



### About the device

The Wyse D90D7 Thin Client comes with the dual-core AMD G-T48E CPU clocked at 1.4 GHz with 4GB of LDDR3 memory and 16GB of Adaptec flash. The I/O is two USB ports on the back, one DV-I with VGA port, gigabit ethernet and displayport. In front there's a headphone jack and two more USB ports. It also contains an internal speaker, but Linux has issues using it. It can optionally come with Wifi, but my one did not come with it, but it is possible to upgrade it as it has punchout holes in the back for the antennas. The unit is passivelly cooled and gets pretty toasty, which is why I would recommend replacing the thermal pads on the unit as you disassemble it.

### Disassembly and installation

![](/img/DSC_0288.jpg)

On the back of the unit, there are 3 screws that you need to remove. Be aware that they have a little bigger thread than the screws inside. Pull the plastic cover gently to you until you see the metal plate. Undo the metal plate screws and slowly slide it off like the plastic cover before and lift it up. Be aware that there's a speaker connected and you can easily end up ripping it off.

![](/img/DSC_0279.jpg)

As sliding the metal housing off, it can damage the thermal pads. I'd recommend replacing them as the metal cage is used for heat dissipation as well. Undo the screw on the 16GB flash module and gently pull it out. Putting the screw back in is not needed, but I'd recommend it. Because there's a standoff in the way, we need to use an extension cable as a riser. Ziptie it together so it fits into the chassis.

![](/img/DSC_0287.jpg)

Connect one end of the cable into the SATA connector in the PCB and slide the SSD under the sliding flaps. Be careful as you can end up sliding the cable plug under the SATA connector and damage it. 

![](/img/DSC_0294.jpg)

![](/img/DSC_0278.jpg)

This setup will allow you to close up the thin-client completely and the SSD won't rattle around, as both the pressure from the cable and the position the SSD is in will stop it from rattling around.


When you're about to install something onto the SSD, you need to go to the BIOS 
### Performance

Is it worth it? Weeell... Depends. It has similar power to a Raspberry Pi when you compare the Linpack computing scores.

![](/img/ApplicationFrameHost_2019-11-11_03-08-50.png)

The benefits are just that its low power, can have their storage expanded with an SSD, the display I/O and the case. For the price of the thin-client with all of the accessories and 16GB of storage, you'll be able to get only the RPi, but on the other hand, you don't have any community support. If you want something powerful in this form factor, look into the Optiplex Micros.