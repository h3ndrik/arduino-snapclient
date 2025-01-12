# Snapcast Client for Arduino 

### Snapcast Audio Streaming Client for Arduino

[Snapcast](https://github.com/badaix/snapcast) is a multiroom client-server audio player, where all clients are time synchronized with the server to play perfectly synced audio

### Feature list

- Header only C++ implementation
- PCM, Opus, FLAC and Ogg decoding is supported
- Auto connect to snapcast server on network
- The functionality has been tested on an ESP32
- Memory efficient implementation, so that PSRAM is not needed
- Use any output device or DSP chain suppored by the [Audio Tools](https://github.com/pschatzmann/arduino-audio-tools)

### Description

I have rewritten the [snapclient](https://github.com/jorgenkraghjakobsen/snapclient) from Jørgen Kragh Jakobsen to an Arduino Library and integrated my AudioTools project to be used to define the output devices.

### Example Arduino Sketch

Here is an example Arduino sketch that uses the Wifi as communication API, the WAVs codec and outputs the audio with I2S: 

```C++
#include "AudioTools.h"
#include "SnapClient.h"

WAVDecoder pcm;
WiFiClient wifi;
I2SStream out;
SnapClient client(wifi, out, pcm);

void setup() {
  Serial.begin(115200);
  
  // login to wifi -> Define values in SnapConfig.h or replace them here
  WiFi.begin(CONFIG_WIFI_SSID, CONFIG_WIFI_PASSWORD);
  Serial.print("Connecting to WiFi ..");
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print('.');
    delay(1000);
  }

  // print ip address
  Serial.println();
  Serial.println(WiFi.localIP());

  // setup I2S to define custom pins
  auto cfg = out.defaultConfig();
  config.pin_bck = 14;
  config.pin_ws = 15;
  config.pin_data = 22;
  out.begin(cfg);

  // start snap client
  client.begin();
}

void loop() {
  client.doLoop();
}

```
Change the snapserver.conf to define ```codec = pcm``` and restart the snapserver with ```sudo service snapserver restart```.
You can test now your sketch e.g. with ```ffmpeg -i http://stream.srg-ssr.ch/m/rsj/mp3_128 -f s16le -ar 48000 /tmp/snapfifo```

For further information on codecs please check the information in the Wiki!


### Documentation

For further information 
- consult the [class documentation](https://pschatzmann.github.io/arduino-snapclient/html/annotated.html) or
- the [examples](examples/)
- the Wiki


### Dependencies

- [Audio Tools](https://github.com/pschatzmann/arduino-audio-tools) (mandatory)
- [LibOpus](https://github.com/pschatzmann/arduino-libopus) (optional)
- [LibFLAC.h](https://github.com/pschatzmann/arduino-libflac) (optinal)
- [CodecVorbis](https://github.com/pschatzmann/arduino-libvorbis-idec) (optional)


### Configuration

Configuration settings can be found in [SnapConfig.h](https://github.com/pschatzmann/arduino-snapcast/blob/main/src/SnapConfig.h)


