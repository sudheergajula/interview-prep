# Design Youtube

## Features

1. Uploading vides
2. Viweing videos
3. Searching for videos
4. Youtube Live
5. Youtube Music
6. Shorts

## Requirements

### Functional
There are 2 personas we need to deal with - 
1. Content creator: who creates and uploads content
2. Viewer - Who views the content on platfrom

### Non Fucntional
1. System should be scalable as we expect more and more users to upload and view.
2. Fault tolerant
3. Highly Available
4. Usability

## Design
### Services :
1. Upload Service - User uploads video into system
2. Recemmondation Service - Upon login, load the user relavant videos
3. Ads Service - Load ads
4. Broadcast Service - Upon upload completion, notify subscribers


![screenshot](images/youtube.png)

Table Design

```sql
create table video(
    video_id uuid,
    user_id string,
    status string,
    title string
)
create index video_user_idx on video(video_id, user_id);

create index video_user_idx on video(video_id, status);

create index videobloom on video using bloom (user_id, title);
```
Blooom filter will help us to quickly check if there is another instance of upload initatited from user for same video.

Definitely NO if its doesn't or else go through.

### Upload Service
1. User initiates upload of raw uncompressed video.
2. Once upload is initiated, it can take upto few mins/hours.
3. System shall respond with 202 status code with upload_id where status can be tracked.
4. All the videos are uploaded into object store, like s3/gcs.
5. Once the upload is completed, table is updated with status.
6. An event is trigerred onCompletion and EncoderService shall listen to these events.

### Encoder Service
Encoder service has compute fleet installed with FFMEPG that is heavily used to encode videos, and also has auto scalars enabled to upscale/downscale. For parallel processing of 4K videos, this video can be split up since they do fit into instance disk, they can be encoded in parallel.
1. Once event is consumed, raw uncompressed video is compressed by Lossy/Lossless compression.
2. When video is compression is complete, they are encoded into different video resolution formats 4k, 1080, 720p, 480p, 320p and they are converted to various video file formats.
3. Post this process, all the videos are segemented into smaller segments varying from 3s to 10secs and then these segemnts are assembeled into containers.
4. All the formats of these videos are then uploaded to CDN to avoid outburst of traffic to origin nodes and they can be served from edge servers.

### Broadcast Service
This service will be notified when the video is available for viewing, and all the subscribers should be notified about availability of new video.

### Viewing
Once the user is landed onto youtube.com, it has to start loading relevant content for user. (New videos, Interests, etc)
### Recemmondation Service
This service will load the homepage for user based on the user profile, subscriptions, interesets, past watch time etc.
### Ads Service
Load clickable ads and skippable and non-skippable ads for user.

### View 
Once the video is clicked for viewing, based on video_id request is propogated CDN as well as to servers to load page. HTTP live streaming shall be used to stream the segments to users device. 

Types of streaming protocols:
1. HLS (Apple)
2. DASH 
3. RTMP (Facebook live stream)
4. WebRTC (WhatsApp)

Websockets are ideal to push data from servers to clients or when client needs to make request. This can make it ideal to capture analytics from user to capture their watch time etc.
For this, we will need an WebScoketRegistry to handle stateful sticky session between client and server. Netflix has Zuul to handle this at scale, it works on event loop and enhanced Netty.

All the events can be streamed into streams and further used to analytics, can be persisted in Cassandra/DynamoDB/ScyllaDB

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
