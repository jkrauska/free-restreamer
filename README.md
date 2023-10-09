# free-restreamer
Super Simple RTMP and RTMPS Restreaming Setup

### Method 1: Copying Stream using nginx-rtmp

```Nginx
rtmp {
  server {
    listen 1935;
    chunk_size 4096;
    application restream-ABCABCABC {
      # Enable livestreaming
      live on;

      # Disable recording
      record off;

      # Allow only this machine to play back the stream
      allow play 127.0.0.1;
      deny play all;

      # Push your stream to one or more RTMP destinations
      push rtmp://a.rtmp.youtube.com/live2/AAABBBCCCDDDEEFFF;

      # Push to stunnel to wrap with rtmps
      push rtmp://localhost:19350/app/sk_us-east-AAABBBCCCAAABBBCCC?gc_ext=true;

    }
  }
}
```


### Method 2: Copying Stream using ffmpeg
(work in progress)

```bash
ffmpeg -i input1 -i input2 \
	-acodec … -vcodec … output1 \
	-acodec … -vcodec … output2 \
	-acodec … -vcodec … output3


 ffmpeg -re -nostdin -i "$file" \
    -vcodec libx264 -preset:v ultrafast \
    -acodec aac \
    -f flv rtmp://live.twitch.tv/app/STREAM_KEY

ffmpeg -re -nostdin -i "traffic.mp4" \    \
  -vcodec libx264 -preset:v ultrafast \     
  -acodec aac \     
  -f flv  rtmp://AWSHOST.us-west-1.compute.amazonaws.com:1935/restream-URLKEY
```
    

### Wrapping RTMP in to RTMPS


Stunnel Config (/etc/stunnel.conf)

```Cabal Config
setuid = stunnel4
setgid = stunnel4
pid=/tmp/stunnel.pid
output = /var/log/stunnel4/stunnel.log

socket = r:TCP_NODELAY=1
socket = l:TCP_NODELAY=1

[gamechanger1]
client = yes
accept = 127.0.0.1:19350
connect = 601c62c19c9e.global-contribute.live-video.net:443
verifyChain = no

[gamechanger2]
client = yes
accept = 127.0.0.1:19351
connect = 601c62c19c9e.global-contribute.live-video.net:443
verifyChain = no
```
