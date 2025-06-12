# YouTube-For-Xbmc4Xbox
YouTube For Xbmc4Xbox 3.6
🎮 XBMC4Xbox YouTube Plugin Guide

Stream YouTube videos on your original Xbox using yt-dlp

 Tools You'll Need
Tool	Purpose	Download/Setup
PC (Windows/Linux/macOS)	Runs the helper server	Any modern system on same network as Xbox
 Python 3.9+	Required to run yt-dlp & Flask	python.org
 yt-dlp	Extracts YouTube video & stream info	pip install yt-dlp
 Flask	Lightweight Python server framework	pip install flask
 FTP client	Transfer plugin to Xbox (e.g. FileZilla)	filezilla-project.org
 How It Works
XBMC plugin sends requests to your helper server
Server uses yt-dlp to fetch:
 Search results
▶ Stream URLs
Plugin then plays the MP4 stream on the Xbox
 Step 1: Set Up Helper Server on Your PC
Create a file named server.py with this code:

from flask import Flask, request, jsonify
import yt_dlp

app = Flask(__name__)

@app.route('/search')
def search():
    q = request.args.get('q')
    ydl_opts = {'quiet': True, 'extract_flat': True, 'force_generic_extractor': True}
    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        result = ydl.extract_info(f"ytsearch20:{q}", download=False)
    return jsonify(result['entries'])

@app.route('/stream')
def stream():
    video_id = request.args.get('id')
    url = f"https://www.youtube.com/watch?v={video_id}"
    ydl_opts = {
        'quiet': True,
        'format': 'bestvideo[ext=mp4][vcodec^=avc1]+bestaudio[ext=m4a]/best[ext=mp4][vcodec^=avc1]',
        'merge_output_format': 'mp4'
    }
    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        info = ydl.extract_info(url, download=False)
    return jsonify({'url': info['url'], 'title': info['title']})

app.run(host='0.0.0.0', port=5000)
Then run it:

python server.py
You should see:

Running on http://0.0.0.0:5000/
Test in browser:

http://<your-pc-ip>:5000/search?q=test
 Step 2: Install the Plugin on Xbox
Download the plugin:
 plugin.video.youtube4xbox_safe.zip
Transfer it to your Xbox using FTP:
Put it in:
Q:\plugins\video\
In XBMC4Xbox:
Go to Videos → Add-ons → Install from ZIP
Select the ZIP file
Done! Open it from:
Videos → Add-ons → YouTube for Xbox
 Step 3: Update the Server IP (If Needed)
If your server IP changes (e.g., you reboot your PC):

Open the file:
Q:\plugins\video\YouTube\default.py
Edit the line:
BASE_URL = "http://192.168.x.x:5000"
Replace 192.168.x.x with your PC's IP (find it by typing ipconfig or ifconfig on your PC).
Save and reboot XBMC.
 Optional: Test Stream Compatibility
To test stream format support:

Go to:
http://<your-ip>:5000/stream?id=dQw4w9WgXcQ
Copy the resulting URL and try it in your Xbox using:
Videos → Play using URL
 You're All Set!
You now have:

A working YouTube plugin for Xbox
No API key limits
Full local control over quality and functionality

Make sure you are using xbmc4xbox from rocky5 you can get it from his site downloader or xbmc4gamer downloader
