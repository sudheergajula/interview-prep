## Facebook livestream
FB heavily relies on RTMP for serving content to user on live stream.
Note -
Celebrity problem -
when a public figure having millions of followers starts a livesteam this can cause servers to crash with too many requesting hitting origin servers. This is typically called Thundering Herd problem where there is stampede of requests onto servers that can degradation of services.

To avoid this situation, content has to served from edge caches. These live video streams are converted to 3secs segemnts and stored at CDN. If there is cache miss, requestes are queued so that cache write happens, else many requests can query origin servers due to cache miss.

RTMP in Action :
1. Capture and Encoding: The live video and audio content are captured using cameras, microphones, or professional broadcasting equipment. These signals are then encoded into a digital format suitable for transmission.

2. RTMP Server Configuration: The broadcaster configures an RTMP server, specifying the necessary settings such as stream keys, access control, and streaming quality parameters.

3. Streaming Software Setup: Broadcasting software, such as OBS (Open Broadcaster Software) or Wirecast, is configured to establish a connection with the RTMP server. The software handles the encoding, packaging, and transmission of the live content.

4. Initiating the Live Stream: With the streaming software configured, the broadcaster initiates the live stream, which establishes a connection with the RTMP server. The server receives the encoded data and prepares it for distribution.

5. Viewer Engagement: Viewers access the live stream through various platforms, including websites, social media platforms, or dedicated streaming applications. The RTMP client on their end receives the video and audio streams from the server, allowing them to watch the live content in real time.

6. Real-Time Interactivity: During the live stream, viewers can actively engage with the content and other participants through features such as chat, comments, likes, and interactive overlays. These interactive elements foster a dynamic and immersive viewing experience.

7. Archiving and Storage: After the live stream concludes, broadcasters often have the option to archive the recorded stream for later playback. This feature enables on-demand access to previously streamed content, extending its reach beyond the live event.
