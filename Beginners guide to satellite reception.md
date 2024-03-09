[//]: # (NOTE: I have explicitly requested permission from lego11 to use his guides as a refernece, he said he's cool with it. I owe that man half the shit I know)

# Preamble

Before anything, I have to give credit and extreme kudos to the **SDR++ and SigIdWiki Discords**. Everyone there has been incredible and has helped me learn basically everything that you'll be able to read here. 

**A lot of the information you will be able to find here is from [lego11s articles](https://a-centauri.com/articoli/), check them out if you want more awesome and detailed guides.**

The purpose of this page is to guide complete SDR beginners as well as more experienced folk looking to learn something through getting pretty images from weather satellites. A lot of guides out there are severaly outdated, focus on specific things while omitting important details or at times even nonexistent!


# Glossary
## Hardware terms
- **SDR** - Software Defined Radio -> A device used to translate radio waves into digital data
- **LNA** - Low Noise Amplifier -> A tool used to amplify radio signals
- **Bias-t**/**Bias tee** -> A device used to inject DC power into the RF line (To power devices such as LNAs). **DO NOT PLUG IT IN AIMING AT YOUR SDR, IT WILL KILL IT!**

<break>

- **Sma** -> Type of connector used by most SDRs
- **Balun** -> Converts an **Un**balanced signal to a **Bal**anced one and vice versa

<break>

- **Pass** -> Refers to the time when you can see a satellite passing overhead, used with orbiting satellites
- **Elevation** -> Height of a satellite above the horizon


## Software terms
- **AGC** - Automatic Gain Control -> Automatically sets the gain based on the signal strength
- **SNR** - Signal to Noise Ratio -> The difference in dB between the noise floor and the signal peak, ergo how strong the signal is
- **FFT Spectrum** - Fast Fourier Transform Spectrum -> The slice of the radio spectrum being sampled by your SDR
- **FFT Waterfall** - Fast Fourier Transform Waterfall -> A visual representation of the spectrum throughout time, almost always found right below the FFT Spectrum
- **Interference** -> Commonly referred to as RFI (Radio Frequency Interference), is an umbrella term for unwanted signals produced by erroneous sources such as cheap power supplies, HDMI cables and devices such as laptops (USB RFI @ 480 MHz)
- **Overloading** -> Occurs when your gain is set too high andor you are near a very strong broadcast. Presents as your noise floor jumping/being unstable or spurs of interference throughout your spectrum.
- **TLE** - Two Line Element set -> A format used to list the location of objects orbitting the earth

## Data transmission formats
Don't worry if you don't understand these yet, they will be explained in more detail later and are here just so you have an idea of what is meant if they are mentioned prior to their full explanation.

- **APT** - Automatic Picture Transmission -> VHF Image broadcast currently used on NOAA satellites
- **LRPT** - Low Rate Picture Transmission -> VHF Image broadcast currently used on Meteor-M satellites
- **HRPT** - High Rate Picture Transmission -> L-band high quality image and telemetry broadcast format
- **LRIT** - Low Rate Information Transmission -> L-band information and telemetry broadcast format



## Examples of interference and overloading:

### Interference

![An example of interference caused by a cell tower](./Assets/Radio/Interference-example.png)

*Pictured is an SDR being overloaded with interference caused by a very strong cell tower broadcast. On the left is an FM station, the rest of the lines are erroneous.*

### Overloading

![An image of a relatively normal FFT](./Assets/Radio/Normal-fft.png)
*Pictured is a relatively normal FFT, excluding the lines*

![An image of a FFT suffering from severe overloading](./Assets/Radio/Overloaded-fft.png)
*After upping the gain, you can see the significant amount of FM overloading present throughout the whole FFT.*


# Mistakes and pitfalls
This is a list of mistakes I made that ended up in wasted time, avoid them for the sake of saving you a headache or two

- **Using old, outdated guides and software** -> Radio is very niche, almost all guides you can find online are heavily outdated giving you bad advice and suggesting deprecated software, leading to confusion and subpar results. Please make sure that any sources you use are up to date.

- **Compass tracking with directional antennas** -> Go by signal strength, NOT by elevation and azimuth. Use apps to find the time and general direction of the satellite, don't go measuring exact compass readings. Find the signal when it is weak, move slow and with purpose to get it to be as strong as possible.

- **Doppler tracking when it is not needed** -> Signals covered here such as APT and HRPT were designed to be thin enough to not need doppler tracking, **DON'T BOTHER DOING IT, IT IS NOT NEEDED.**

- **Blindly maxxing the gain setting** -> Upping the gain only makes the signal louder up to a certain point, after which it starts amplifying the noise floor much more than actual signals. This leads to them being drowned out. Turn it up only until you see, that the signal isn't getting any stronger (Use SNR, NOT its position on the fft).

- **Turning on automatic gain control (AGC)** -> AGC was designed for much wider broadcasts such as DVB-T (Terrestrial TV) than the signals described in this page. It *won't* recognize the signals and end up cranking the gain up way higher than needed which ends up drowning the signal out.

- **Using an LNA with direct sampling** -> Direct sampling is just piping the whole target frequency range to the SDR at once, meaning that weaker signals will get drowned out by stronger ones incredibly easily. Using an LNA makes strong signals stronger, drowning weak ones out completely.

- **Not using actual connectors and cables** -> In my first few days with an SDR, lacking an actual sma cable, I went by the definition of an antenna - "An antenna is just a piece of wire" - and stuck a wire straight into the sma port. While yes, it works, it is extremely dangerous since it can damage your sma port, electrocute you if you have a bias-t, introduce major signal loss, or just end up not working correctly. Just get proper cables, man

- **Using a cheap LNA with higher frequencies (L-band and above)** -> Cheap LNAs do work, but very poorly and are very often not worth the money spent. Check out the HRPT section for more info.


# RTL-SDR specific things
These apply to all SDRs using RTL chipsets.

- The maximum stable sampling rate is **2.56 Msps!** Using anything higher can lead to sample drops if you don't have one of the few incredibly specific usb controllers with which the RTL chipset can pull 3.2 Msps without dropping samples. 2.88 Msps has worked for me before, but is still iffy - you can test if works on your setup by running `rtl_test -s 2.88e6` and seeing if any data is lost
- When direct sampling for HF, use the Q branch
- RTL-SDR Blog V4 needs specific drivers to work with most software, this is usually described on their download ṕage.


# Preferred software
Arguably the by far best program for **pulling data** off of satellites is [SatDump](https://github.com/SatDump/SatDump/releases)

You can also use SDR++ for recording and then process the recordings using SatDump, but that is often just unnecessary extra effort.

> Always download the nightly builds of SDR** and SatDump, update them frequently. The tools are relatively new and are still being actively developed and have new additions on a daily basis. There are other programs you can use but I won't focus on them for the sake of keeping this guide concise.
---
 To **track satellites** and figure out their future passes (Most are orbiting the Earth after all), you can use these:

Cross-Platform:
- [SatDump](https://github.com/SatDump/SatDump/releases) - Windows, Linux, MacOS, Android - SatDump has an inbuilt module you can use for tracking. **It can only track one satellite at a time, doesn't do predictions.**
- [N2YO](https://n2yo.com/) - Web - Does the job, however lacks the polish of other apps

PC:
- [Gpredict](https://oz9aec.dk/gpredict/) - Windows, Linux, MacOS - A relatively young tool, arguably the best choice for tracking on your computer
- [Orbitron](http://www.stoff.pl/) - Windows, Linux (wine) - Quite dated but functionally sound

Mobile:
- [Look4Sat](https://play.google.com/store/apps/details?id=com.rtbishop.look4sat&hl=en&gl=US) - Android - Provides everything essential in a simple UI.


There are a few apps for IOS but they have severe limitations, using any of the above is heavily encouraged.

> **Make sure you update your TLEs**, not doing so might make the satellite locations be outdated or just outright incorrect.

I personally use Gpredict for long term and Look4Sat for short term predictions, the SatDump tracking module during passes.


# VHF APT/LRPT reception guide (137MHz)
- Receiving VHF broadcasts is **incredibly easy** -> all that you need is just some wire, an SDR and some patience
- As of writing this article there are currently **5** weather satellites that broadcast in this band
- While easy to receive, they have a **relatively low quality** (4 km/px on APT and 1 km/px with jpeg compression on LRPT) and transmit only 2-3 channels (Images) while broadcasts in higher frequencies which usually transmit 5+ raw, 1 km/px channels 

## The four weather satellites still broadcasting images on VHF

This is a brief historical overview in case you want to know a bit about the satellites you can receive. Feel free to skip this heading if you are just here to receive stuff.

**NOAA**

- These are the last **3** remaining members of the **POES** (Polar Orbiting Environmental Satellites) constellation, consisting of **NOAA 15, 18 and 19** being launched in 1998, 2005 and 2009 respectively.
- These satellites broadcast an *analogue*  **[APT (Automatic picture transmission)](https://www.sigidwiki.com/wiki/Automatic_Picture_Transmission_(APT))** signal that has two channels and a 4km/px quality. Its analogue nature means, that if the signal has even just a bit of noise, you will get static grain on output images.

<break>

- New satellite launches are a part of the JPSS constellation, which only includes a much harder to receive X-band signal that requires much more expensive hardware. No future satellite launches from NOAA are planned to include a VHF antenna.

---
**METEOR-M**

- As for their Russian counterpart, **2** satellites are currently broadcasting in VHF: **Meteor-M N°2-3 and Meteor-M N°2-4** (Meteor M2-x for short), a part of the **Meteor-M** constellation. They were launched very recently - in June of 2023 and February of 2024 respectively. 
- Meteor-M satellites broadcast a *digital* **[LRPT (Low rate picture transmission)](https://www.sigidwiki.com/wiki/Low_Rate_Picture_Transmission_(LRPT))** signal that includes 3 channels at a JPEG-compressed 1 km/px quality. It includes **FEC** to make sure the picture doesn't come out grainy as well as allowing you to decode the signal properly even if the signal is fairly weak.

<break>

- This satellite series has been plagued with accidents, faults, and delays. Meteor M1 and M2 lost altitude control, M2-1 exploded on launch and M2-2 got hit by a micrometeor making it imposible to broadcast LRPT. M2-3 is sadly no exception: its LRPT antenna didn't fully extend, leaving it in a tilted angle making the signal improperly polarized, experience random drops as well as making it generally weaker than it is suppoed to be. M2-4 has succesfully launched on leap day in 2024, is broadcasting LRPT at a full strength. No issues have been detected with the satellite so far.


## Broadcast issues

- NOAA 15 has had several major hiccups with its scan motor current spiking due to it grinding through debris, causing a loss of synchronization between the scan motor and the processor presenting as major glitches appearing in place of imagery. A major spike could lead to a complete motor stall, from which recovery is highly unlikely. As of writing this article (03/2024) the satellite has mostly recovered and is broadcasting fine.
- NOAA 18 has had a configuration error present ever since management was transferred to Parons tech, making it broadcast a visible channel during the night instead of an infrared one. This presents itself as half of the image being black.
- Meteor-M N°2-3 has an incorrectly deployed VHF antenna, meaning the signal is weaker than intended and might unexpectedly drop from time to time.


## Example processed APT and LRPT images
> Note: The images don't have maps on them, they were added in post processing.


![A processed APT image](./Assets/Radio/APT-Sample-image.png)
*NOAA 18 APT received on 02/01/2024 using a 5 element yagi-uda antenna. Processed using SatDump with the `HVC` RGB composite. Equalized, median blur applied.*


![A processed LRPT image](./Assets/Radio/LRPT-Sample-image.png)
*Meteor M2-3 LRPT received on 02-01-2024 using a 5 element yagi-uda antenna. Processed using SatDump with the `221` RGB composite. Equalized.*


## Hardware needed to receive these satellites

You will need an SDR and an antenna, **no other special equipment is required for VHF**.

The SDR should be a **reputable brand** if you want optimal performance (E.g. AirSpy, RTLSDR Blog, Nooelec...). 
> In simpler terms; avoid aliexpress chinesium sdrs. They CAN work, but expect worse results.

As for the **antenna**, you have the choice between:
- Directional antennas
- Omnidirectional antennas

The difference between these is, that omnidirectional antennas don't need any tracking to be done (remains stationary throughout the pass) while directional antennas require hand tracking and **can only do one satellite at a time**


*Popular antenna types for receiving in this frequency include:*
### 1. V dipole antenna
- Omnidirectional
- Very easy to make, very portable
- Fair results, has some nulls due to its inconsistent polarization
- Arguably the best for beginners
- When using, point directly north/south & move about 50 cm from the ground (Personal tip: Play around with it and figure out when the signal is the strongest, stick with what works!)
  
To build it: 
1. Get a chock block (electrical terminal), a coaxial cable and two preferably copper, unshielded wires that are ~54.5 cm long (This is because the v dipole elements have to be a fourth the wavelength, which in this case is `299,792,458 ms⁻¹/137,500,000 hz = ~2.18 m; 2,18/4 = ~0.545 m = ~54.5 cm`. Why? [It's how a v dipole works.](https://upload.wikimedia.org/wikipedia/commons/d/d8/Dipole_antenna_standing_waves_animation_6_-_10fps.gif))
2. Stick the shielding of the coaxial cable in one hole and the copper core into the other (keep it as short as possible)
3. Put the two wires into the holes and spread them 120° apart making a V shape.
That's it. Really.

![A visual guide on how the antenna should look](./Assets/Radio/V-Dipole-guide.png)

*This image suggests a wire length of 53.4 cm, while that would work the actual length should be approximately 54.5cm. It also suggests using aluminum rods, while that'd work, copper is about twice as conductive (will lead to better results).*

### 2. Quadrifilar helical antenna (QFH)
- Omnidirectional
- Fairly difficult to build
- Very good results thanks to its true circular polarization
- Best choice for permanent fixtures
  
I will not be describing how to build it, since the building process is quite involved. There are plenty of guides out there, if I ever get around to building one I will link it here.

![Sample QFH antenna](./Assets/Radio/QFH-guide.png)
*[Source](https://okelectronic.wordpress.com/2014/08/20/rtl-sdr-second-attempt/)*

### 3. Yagi antenna
- Directional
- Fairly easy to make
- Very good results thanks to its high gain
- Requires manual tracking
  
![An image describing the parts of a yagi antenna](./Assets/Radio/Yagi-example.png)
*[Source](https://www.everythingrf.com/community/what-is-a-yagi-antenna)*

I personally use this type of antenna for VHF passes, since it constantly gets a great SNR (~45-50 with APT) and is quite easy to build albeit requiring a bit more wire. 

To make it:
1. Get a Boom (Anything long and nonconductive, just consider, that you will HAVE to manhandle it later while tracking sats - don't get a tungsten rod or something) and find out how many elements fit on it
> More elements = More directional (Harder to track), higher gain (Better signal strength), longer boom length (Bulkier)
2. Shove appropriate numbers into [this calculator](https://www.steeman.org/Antenna/Yagi-Antenna-Calculator) (For frequency choose 137.5 MHz)
3. Cut copper wires to length, place them onto the boom according to to the values from the calculator
4. For the driven element (dipole), cut it in half and put one side in a terminal with the shielding of a coaxial wire and the other with the core of the same coaxial wire\
**Make sure the cables don't touch or are short together in any way, this will make the antenna not work**
5. Coil the coaxial cable up a few times right after the feed point in order to convert the **unbalanced** signal to a **balanced** one (In essence creating a HF choke)

![My yagi setup showcasing the balun and feed](./Assets/Radio/My-yagi-dipole-feed.png)
*The choked balun and dipole feed I use on my yagi*


## Frequency reference

As of 24.2.2024, the frequencies these satellites broadcast in are as follows:

|Satellite|Frequency|
|---|---|
|NOAA 15|137.62 MHz|
|NOAA 18|137.9125 MHz|
|NOAA 19|137.1 MHz|
|Meteor M2-3|137.9 MHz|
|Meteor M2-4|137.1 MHz|

## Actually receiving the satellites!
1. Get to a place with a good view of the sky - The more you can see, the longer you can receive the satellite for and the longer the resulting image will be
2. Open SatDump and navigate to the `Recorder` tab, select your SDR, set the sampling rate to `2.4 Msps` (Or whatever your SDR supports) and hit `Start`

- Rise the gain until your noise floor starts to rise more than the target signal, or until before your SDR overloads, whichever comes first. Try not to move it around during the pass, it will lead to the image having sections with a different brightness. In my experience it is stable at around 30 dB gain, it depends on receiving conditions though.

3. In the side panel, open the `Processing` menu and do the following:

### FOR NOAA APT
- Select the `NOAA APT` pipeline
- Enable `DC Blocking` and `SDR++ Noise Reduction`
- Select the proper NOAA satellite
- Open the frequency menu, select the correct satellite

![A screenshot of the settings mentioned above](./Assets/Radio/APT-SatDump-Settings.png)

### FOR METEOR-M LRPT
- Select the `METEOR M2-x LRPT 72k` pipeline
- Enable `DC Blocking`
- For the frequency choose `Primary` for 137.9 MHz and `Backup` for 137.1 MHz
> The frequency can change when the satellite conflict switches (Tries to avoid using the same frequency as other satellites in the same band)

![A screenshot of the settings mentioned above](./Assets/Radio/LRPT-SatDump-Settings.png)

4. When the satellite comes overhead, press `Start` on the processing window

> When using a directional antenna, move slowly and try keeping the signal as strong as possible using the SNR with LRPT and the waterfall with APT. You might not get the hang of it on your first try, tracking is a skill you have to learn!

If everything is right, you are now receiving a beeping APT signal or you see four dots on the demodulator if it is LRPT!

5. Once you see the signal has completely disappeared and isn't coming back, press `Stop`

6. SatDump will now begin autoprocessing the results, you can see the progress on the bottom (You can disable the autoprocessing in the settings if you want to tinker with the images yourself and don't want SatDup creating automatic images)

7. Once it finishes processing, head to the `Viewer` tab and select the pass you decoded on the top left.

You are done! Feel free to play around with the image settings and enhancements, you can figure it out :)

## Common issues
- The image is solid black!\
If the image is from LRPT on an evening pass, you need to select Channel 4 (IR) to see anything - Channels 1 and 2 are visible, during night it is solid black.

- There is grain all over the image!\
Some grain is expected on APT images, you can get rid of it by ticking `Median blur`. If it is present after, you either didn't enable the noise reduction when recording or the signal was just too weak. The latter is most common, grain appears whenever there is crackling during recording.


# L-band HRPT/HRIT/LRIT reception guide (1.7 GHz)
> This section will be fairly limited in terms of HRIT/LRIT reception, given that I only have LOS with three geostationary satellites.
- L-band reception is the next logical step after VHF, it is **harder to receive** requiring more **expensive equipment** and more effort making the antenna as well as requiring a **dish** and some half decent tracking skills.
- While requiring more dedication, it offers much more interesting things than VHF: for example being able to receive 5+ channels of pure and uncompressed 1km/px images as well as full disc Earth images using geostationary satellites broadcasting HRIT/LRIT (or other alternatives)

<break>

- The are far more satellites you can receive, they divide into:
    - Geostationary satellites (10+):
        - GOES in the US (One additional limited GOES in Europe and Asia)
        - Elektro-L in Europe, Asia and Oceania
        - Fengyun in Asia and Oceania
        - Himawari in Asia and Oceania
        - Geo-Kompsat in Asia and Oceania
        - Insat in India

    - Orbitting satellites (8):
        - 3x NOAA POES
        - 2x Meteor-M
        - 2x MetOp
        - 1x FengYun (Only broadcasts in sight China)

## Exemplary processed HRPT and xRIT images

![Best HRPT to date](./Assets/Sat-reception-journey/Best-HRPT-yet.png)
*NOAA 19 received on 14/1/2024 using a 90 cm dish and a SawBird GOES+. Processed using SatDump with the `NOAA Natural Color` RGB composite. Median blur applied, equalized. My single best HRPT image to date*

![Latest full disc Earth image I have](./Assets/Sat-reception-journey/Best-Earth-full-disc.png)
*Elektro-L N3 LRIT received on 11/2/2024 using a 125 cm dish and a SawBird GOES+. Decoded using SatDump. Pictured is the autogenerated `NC` (Natural Color) composite.*

## The satellites you can receive

Just like VHF, I will talk a bit about the background of the satellites you can receive. Feel free to skip this heading if you are just here to receive stuff.

### Orbitting
---
**NOAA POES**

- These are the same as VHF: NOAA 15, 18 and 19.
- Have a [POES HRPT](https://www.sigidwiki.com/wiki/NOAA_POES_High_Resolution_Picture_Transmission_(HRPT)) (High Rate Picture Transmission) broadcast which transmits 5 AVHRR channels as well as some more data (Refer to wiki)
- The broadcast features a very strong carrier wave making it quite easy to track.

<break>

> Fun fact: As of 02/2024, NOAA 2 (ITOS-D) - A 50 year old satellite! - has recently gone back to life broadcasting a legacy [ITOS HRPT](https://www.sigidwiki.com/wiki/NOAA_ITOS_High_Resolution_Picture_Transmission_(HRPT)) signal. **It includes no actual data** since the VHRR sensor has died ages ago, however it still matches the modulation and spec - if decoded properly you can still see the familliar sync lines from APT broadcasts.

---
**METEOR-M**

- Two Meteor-M satellites broadcast in L-band: Meteor M2-2 and Meteor M2-3
- Both of these have a [**Meteor HRPT**](https://www.sigidwiki.com/wiki/METEOR-M_High_Resolution_Picture_Transmission_(HRPT)) broadcast containing 6 MSU-MR channels in addition to 30 MTVZA channels.
- The broadcast, much like POES HRPT, has a very strong carrier wave making it very easy to track.

<break>

> You might notice that Meteor M2-2 is here even though it doesn't broadcast LRPT in the VHF band. This is because of a micrometeor strike causing a leak of thermal transfer gas, leaviing LRPT unpoperable due to inadequate cooling ([Source](https://www.rtl-sdr.com/meteor-m-n2-2-has-failed-but-recovery-may-be-possible/)). HRPT has recovered, and has been working without any issues since. The satellite orbits a bit later than the rest, making you able to receive some HRPT at noon, unlike APT & LRPT which only have early morning and late evening passes.

---
**MetOp**
- There are two functional satellites: MetOp-B and MetOp-C operated by EumetSat, launched in 2013 and 2019 respectively.
- They have a [MetOp AHRPT](https://www.sigidwiki.com/wiki/METOP_Advanced_High_Resolution_Picture_Transmission_(AHRPT)) (Advanced High Rate Picture Transmission) broadcast which - unlike NOAA POES and METEOR-M HRPT - includes Reed-Solomon FEC to make sure your picture doesn't come out with grain. The broadcast also contains much more information and instrument data, including 5 AVHRR channels.
- The signal does not have a carrier wave or easily decernible bumps making it a bit harder to track, you will have to go by the SNR meter.

---
**FengYun**

- The only satellite broadcasting HRPT is **Fengyun 3C**, it is a fairly special case. It is the last surviving member of the FengYun 3 constellation still broadcasting in the L-band, due to a severe power supply failure **it only broadcasts when it sees China**. 
- It broadcasts a FengYun AHRPT signal, which - much like MetOp AHRPT - has Reed-Solomon FEC, but unlike any other satellite in L-band **it broadcasts a true color channel** (The rest can only do RGB composites) - exactly what you would see with your eyes if you stood right next to the satellite. 
- Another simmilarity with MetOp is its lacking carrier wave, meaningyou have to go solely by the SNR as well.
- The signal is quite wide and has a much higher symbol rate in comparison to the rest of the satellites mentioned here, meaning you can not use a standard RTL-SDR dongle to receive it.


---

### Geostationary
I will only mention the few relevant to me right now, will add the rest once possible.

---
**Elektro-L**
- These are Elektro-L N3 and Elektro-L N4 (Elektro-L# for short). Due to a fairly recent power supply failure, Elektro-L2 only broadcasts a beamed X-band transmission to Moscow.
- They broadcast a Low Rate Information Transmission (LRIT) in addition to a High Rate Information Transmission (HRIT) signal containing full disc images of the earth. It includes Reed-Solomon FEC, meaning you can get just a few dBs of the signal and still get a proper decode without any grain.

---

**GOES**

> I will only cover the European EWS-G2 (GOES 15), given that I only have LOS with this one. The US has several other GOES satellites, their reception is simmilar to Elektro-L LRIT/HRIT.
- EWS-G2, a retired GOES satellite moved to Europe to replace EWS-G1 (GOES 13) broadcasts a **linearly polarized** GVAR signal. It lacks FEC, meaning you have to get it at a fairly decent strength for a decode witout any grain.

---

**FengYun**
> I don't have information about 4A and 4B, **I only have LOS and verified information about FengYun 2H**, will not include anything else to avoid giving false information.

- The Fengyun 2 and 4 series are geostaionary, FengYun 2G, 2H, 4A and 4B currently transmit an L-band signal with imagery.

- You can use both a prime focus or an offset dish, the former just requiring less turns on the helix.

### Feed

- The dish is only a half of the job though, you will need to DIY the feed yourself - it sounds much harder than it actually is though.
- The preferred and most forgiving feed you can make is a **Helical antenna**, which will be placed on the mounting arm of the dish


## Build and reception
- For these, there is no point in me writing it out: Lego has these covered in his incredible [HRPT guide](https://www.a-centauri.com/articoli/easy-hrpt-guide), a much more solid and complete source for this stuff. Anything further up will just be expanding upon the article.

## Signal information

|Signal|Minimum viable dish size|FEC|Notes|
|---|---|---|---|
|NOAA POES HRPT|60|No|
|Meteor HRPT|60|No|
|MetOp AHRPT|XX|Yes|Just barely receivable with an RTLSDR, might cause issues
|FengYun AHRPT|XX|Yes|Not receivable by an RTLSDR, needs at least 4 Msps


## Receiving geostationary satellites
> I will cover the satellites relevant to me, the rest of the world is pretty much the same thing wth just a different pipeline and schedule though.
- Extremely simple
- Doesn't require tracking (It's on the tin -geo**stationary**
- Provides full disc images of the earth

### Transmission types
Unlike orbitting satellites which use (A)HRPT, geostationary ones use different formats able to carry more than just images - the most common type that we are interested in broadcasted this band being **LRIT** (Low Rate Information Transmission). You can also find other types such as:

- HRIT - Hgh Rate Information Transmission -> A higher quality broadcast of data, often harder to receive
- GOES GVAR - Goes Variable - Only broadcasts a set amount of data
- GGAK/CDA -> Broadcasts space weather information, these have been decoded but the information they hold is pretty much useless.


### Signal information
> Only the ones relevant to Europe are listed! There are others not mentioned here.

|Satellite series|Signal type|Frequency|Polarization|Minimum dish size|FCC|Transmits...
|---|---|---|---|---|---|---|
|Elektro-L|LRIT|1691.5 MHz|RHCP|90 cm|Yes|Every 3 hours from midnight UTC at XX:42 ecluding 06:42
|Elektro-L|HRIT|1691.5 MHz|RHCP|150 cm|Yes|Every 3 hours from midnight UTC at XX:12 excluding 06:12
|Elektro-L|GGAK|1693 MHz|RHCP|80 cm|N/A|Constantly, can be used to verify your setup.
|Fengyun 2|S-VISSR|1687.5 MHz|Linear|125 cm|No|XX:00 - XX:28 and XX:30-XX:48\*
|GOES|GVAR|1685.7 MHz|Linear|150 cm|No|Constantly

\* During XX:28 - XX:30 the sensor rolls back, his presents itself as a very strong carrier wave in place of S-VISSR. During XX:48-XX:00 the satellite broadcasts dead (filler) LRIT on 1690.5 MHz, causes the second image to be cut in half at about 57%.

You mighthave noticed, that some signals are linearly polarized instead of our familliar RHCP (Right Hand Circular Polarization). You **can** receive these signals with a differently polarized feed **at the cost of 3 dB**. 


### Actually receiving the satellites!

1. Aim your dish using whatever broadcast the satellite has, or using a dish tracking app (Less accurate).
2. Open SatDump, move to the `Recording tab` following the same setup as for HRPT
3. Start the appropriate pipeline:
- Elektro LRIT: `Elektro-L LRIT` 
- Elektro HRIT: `Elektro-L HRIT`
- S-VISSR: `FengYun 2 S-VISSR` 
- GOES GVAR: `GOES GVAR`

4. The broadcast will show up as a bump that occasionally jumps up and down, SatDump should be locked onto it.

![A screenshot of SatDump taken while receiving Elektro-L LRIT](./Assets/Radio/Lrit-SatDump.png)

5. After the transmission stops or you are satisfied with the results, hit `Stop` on the pipeline

6. As of writing this article, SatDump does **NOT** support processing of images received from geostationary satellites, so **they will not show up in the `Viewer` tab.** To access them, navigate to your live output directory, open the folder for the latest live recording. The images will be in the `IMAGES` folder.

7. You are now done! Feel free to play aroud with the results using 3rd party tools.

### Common issues

#### MetOp donut shaped constellation
When decoding MetOp AHRPT on RTL-SDRs, you might notice that even while threre is a decent signal (Several dB), you still have `NOSYNC` indicated on the Vitterbi and have a donut shaped constellation. This happens, when the MetOp pipeline doens't see enough of the broadcast for a decode. To fix this, you have a few options:
- Make sure you are using 2.56 Msps (2.88 if it's stable)
- Move the frequency around by a few kHz
- Lower the MetOp pipeline bandwidth:
1. Open the SatDump folder (On android you need to download a debuggable APK, then run `adb run-as org.SatDump.SatDump`)
2. Open the pipeline for MetOp
3. Locate the `Pipeline bandwidth` option and set it to .05

If you continue to get a donut shaped constellation even after making the adjustments, you'll likely just need a bettter SDR or a machine that supports the 2.88/3.2 Msps sampling rate

## Reception tips and  notes

### Pass rating scale
This scale is not official or mentioned anywhere, I just created it to be able to gauge how good each HRPT satellite pass was. Some people use SNR to gauge the pass quality, but I personally prefer using AVHRR frames. These dictate how long the AVHRR image is, is the only keasurement unit consistent across most weather satellites.

You can see the frame count in SatDump after hitting `Stop` on the pipeline and going to the `Offline processing` tab, or by reprocessing the cadu at a later date in the same tab.
|AVHRR frame count|Pass quality|
|---|---|
|<250|Very poor|
|250-1000|Poor|
|1000-2000|Okay|
|2000-3000|Good|
|3000-4000|Very good|
|4000-5000|Excellent|
|5000-5500|Almost perfect|
|>5500|Perfect|

The theoretical limit for these is about 6000, I have managed to get a 5650 frames from a 0° - 2.5° pass.

### MetOp
