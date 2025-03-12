# Design Youtube

## Features

1. Uploading vides
2. Viewing videos
3. Searching for videos
4. Youtube Live
5. Youtube Music
6. Shorts

## Requirements

### Functional
There are 2 personas we need to deal with - 
1. Content creator: who creates and uploads content
2. Viewer - Who views the content on platfrom

### Non Functional
1. System should be scalable as we expect more and more users to upload and view.
2. Fault tolerant
3. Highly Available
4. Usability

## Capacity Estimation
Total Num of Users : 500 mil
DAU - 100 mil

### Storage Estimation
Num of uploads per day per user (1 Mil) = 1 Video (1GB) = 10^9
1 Mil uploads/day = 10^*109 = 10^15 = 1PB/day object store

### Compute Estimation
Compute fleet of encoding and decoding
12 req/sec at 1mil req/day
Each req approx: 1200 secs to process/video
12 req/sec = 1200 secs/upload , approx 100 nodes FFMPEG Cluster for encoding and decoding


## Design
### Services :
1. Upload Service - User uploads video into system
2. Recommendation Service - Upon login, load the user relavant videos
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
