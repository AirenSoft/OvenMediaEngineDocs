# Transcoding

OvenMediaEngine has embedded live transcoder. The live transcoder can decode the incoming live source and encode it to the appropriate codec to be published or can encode it in multi bitrates by adjusting the quality.

## Supported Video and Audio Codecs

OvenMediaEngine currently supports the following codecs:

| Video Decoding | Audio Decoding | Video Encoding | Audio Encoding |
| :--- | :--- | :--- | :--- |
| H.264 \(Baseline\) | AAC | H.264 \(Baseline\) | AAC |
|  |  | VP8 | Opus |

## Encodes and Streams

You can re-encode the incoming video source through the `<Encode>` setting and make the encoded video and audio into a new stream. The newly created stream can be used according to the streaming URL format.

```markup
<Application>
   <Encodes>
      <Encode>
         <Name>HD</Name>
         <Video> ... </Video>
         <Audio> ... </Audio>
      </Encode>
      <Encode>
         <Name>FHD</Name>
         ...
      </Encode>
   </Encodes>
   <Streams>
      <Stream>
         <Name>${OriginStreamName}_o</Name>
         <Profiles>
            <Profile>HD</Profile>
         </Profiles>
      </Stream>
   </Streams>
</Application>
```

By the above setting, Video and Audio encoded with `<Encode><Name>` of FHD and HD are newly created and included in `<Stream><Name>` named `${OriginStreamName}_o`. In other words, if a stream inputted as `app/stream` is transcoded, `<Stream><Name>` named `stream_o` is created.

Also, the newly created stream is used in the following URL format:

* **`WebRTC`**    ws://192.168.0.1:3333/app/`stream_o`
* **`HLS`**       http://192.168.0.1:8080/app/`stream_o`/playlist.m3u8
* **`MPEG-DASH`** http://192.168.0.1:8080/app/`stream_o`/manifest.mpd
* **`Low-Latency MPEG-DASH`** http://192.168.0.1:8080/app/`stream_o`/manifest\_ll.mpd

## Profile

### Video

You can set the video profile as below:

```markup
<Video>
    <Codec>vp8</Codec>
    <Width>1280</Width>
    <Height>720</Height>
    <Bitrate>2000000</Bitrate>
    <Framerate>30.0</Framerate>
</Video>
```

The meaning of each property is as follows:

| Property | Description |
| :--- | :--- |
| Codec | Specifies the `vp8` or `h.264` codec to use |
| Width | Width of resolution |
| Height | Height of resolution |
| Bitrate | Bit per second |
| Framerate | Frames per second |

### Audio

You can set the audio profile as below:

```markup
<Audio>
    <Codec>opus</Codec>
    <Bitrate>128000</Bitrate>
    <Samplerate>48000</Samplerate>
    <Channel>2</Channel>
</Audio>
```

The meaning of each property is as follows:

| Property | Description |
| :--- | :--- |
| Codec | Specifies the `opus` or `aac` codec to use |
| Bitrates | Bits per second |
| Samplerate | Samples per second |
| Channel | The number of audio channels |

### Bypass transcoding

You can configure Video and Audio to bypass transcoding as follows:

```markup
<Video>
    <Bypass>true</Bypass>
</Video>
<Audio>
    <Bypass>true</Bypass>
</Audio>
```

{% hint style="warning" %}
You need to consider codec compatibility with some browsers. For example, chrome only supports OPUS codec for audio to play WebRTC stream. If you set to bypass incoming audio, audio will not play on chrome.
{% endhint %}

WebRTC does not support AAC, so if video bypasses transcoding, audio must be encoded in opus.

```markup
<Video>
    <Bypass>true</Bypass>
</Video>
<Audio>
    <Codec>opus</Codec>
    <Bitrate>128000</Bitrate>
    <Samplerate>48000</Samplerate>
    <Channel>2</Channel>
</Audio>
```

## Supported codecs by streaming protocol 

Even if you set up multiple codecs, there is a codec that matches each streaming protocol supported by OME, so it can automatically select and stream codecs that match the protocol. However, if you do not set a codec that matches the streaming protocol you want to use, it will not be streamed.

The following is a list of codecs that match each streaming protocol:

| Protocol | Supported Codec |
| :--- | :--- |
| WebRTC | VP8, H.264, Opus |
| HLS | H.264, AAC |
| Low-Latency MPEG-Dash | H.264, AAC |

Therefore, you set it up as shown in the table. If you want to stream using HLS or MPEG-DASH, you need to set up H.264 and AAC, and if you want to stream using WebRTC, you need to set up Opus.

Also, if you are going to use WebRTC on all platforms, you need to configure both VP8 and H.264. This is because different codecs are supported for each browser, for example, VP8 only, H264 only, or both.

However, you do not worry. If you set the codecs correctly, OME automatically sends the stream of codecs requested by the browser.

{% hint style="info" %}
Currently, OME does not support adaptive streaming on HLS, MPEG-DASH. However, it will be updated soon.
{% endhint %}


