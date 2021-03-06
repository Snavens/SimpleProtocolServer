# Simple Protocol Server

This is a server that sends all the available sound card audio from a Windows PC to the network.  This is meant to be the Windows counterpart to the simple protocol module in PulseAudio.  The simple protocol streams uncompressed 16 bit 2 channel PCM data.

The server is meant to be used with the Simple Protocol Player client on Android.  The play store link is here: https://play.google.com/store/apps/details?id=com.kaytat.simpleprotocolplayer .

The client source is here: https://github.com/kaytat/SimpleProtocolPlayer

## WSAPI Loopback

The server uses the Windows Audio Session API to capture the audio.  And for each audio frame, it sends that data to the connected client.

The code is a simple modification to an existing MSDN example: http://blogs.msdn.com/b/matthew_van_eerde/archive/2014/11/05/draining-the-wasapi-capture-buffer-fully.aspx .  Instead of writing the data to a MMIO file, the data is send over a socket.

## Limitations

### One client only
Although PulseAudio's module will allow multiple connections, this server does not.

### No XP support
I've only tested this with one Win7 machine.  And it doesn't seem to work with XP.

The Win7 machine was recently upgraded to Win10 and that seems to work.

### Sampling rate / format
The Android app supports 4 sampling frequencies - 48, 44.1, 24, and 22.05kHz and both stereo and mono.  The app assumes 16-bit samples.

My sound card doesn't support the lower sampling rates and so the server has a command line option to use an integer divider to downsample.

The sampling rate may need to be manually adjusted by the user since the default format may not be a format supported by the Android app.  To do this, the user will need to manually configure the properties in the "Advanced" tab of the playback device.  Please also see this: http://kaytat.com/blog/?p=332

### Usage
    simpleserver.exe [--device "Device long name"] [--mono] [--div divisor]
    
    -? prints this message.
    --list-devices displays the long names of all active playback devices.
    --device captures from the specified device (default if omitted)
    --mono convert from stereo to mono
    --div divisor reduce sample rate by a factor of divisor
