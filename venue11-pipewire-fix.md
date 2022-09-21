# Pipewire Audio fix for Dell Venue 11 Pro

During the time where Fedora Linux switched from PulseAudio to Pipewire for audio backend between versions 34 and 35, a hardware compatibility was broken for the headphone jack for the Venue 11 Pro tablets.

## Symptoms

This fix will fix these symptoms:  

* Distorded or glitchy audio when audio channels are balanced
* Mono playback on both channels when audio channel set to either left or right  

This is caused by the ALSA backend of Pipewire not recognising the proper combo jack wiring.

## The fix

We need to set the SND HDA Intel module to use the Dell headset model

Execute this command:

```
sudo cat options snd-hda-intel model=dell-headset-multi > /etc/modprobe.d/audio-jack.conf
```

Then reboot. This should fix the audio output issue. Audio input not tested.

For list of HD Audio Codec models to use, refer to the [Linux Sound Subsystem Documentation - HD-Audio Codec-Specific Models](https://www.kernel.org/doc/html/latest/sound/hd-audio/models.html#alc22x-23x-25x-269-27x-28x-29x-and-vendor-specific-alc3xxx-models) documentation.


