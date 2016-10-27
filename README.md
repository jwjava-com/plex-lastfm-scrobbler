plex-lastfm-scrobbler
=====================
[![Build Status](https://api.travis-ci.org/jesseward/plex-lastfm-scrobbler.svg?branch=master)](https://api.travis-ci.org/jesseward/plex-lastfm-scrobbler)

plex-lastfm-scrobbler provides a set of scripts that allow you to scrobble played audio items to Last.FM from the Plex Media Server application. plex-lastfm-scrobbler was built to run across platforms, while it has not yet been tested on Windows, it *should* work.

A few points

  - plex-lastfm-scrobbler is an out of process tool. Meaning it is not a Plex Media Server plug-in. This tool runs separately of your Plex Media Server.
  - Must be run on the Plex Media Server
  - Uses python standard library. Python is the only requirement to run this application
  - Parses Plex Media Server logs for the 'got played' string in the log file.
  - Does not differentiate between clients. Meaning all media played, will be scrobbled while the script is running.
  - Your plex-media-server logs must be set at DEBUG level (not VERBOSE)

Installation
----

**Linux, OSX**

It is recommended (but not required) that you install this into a virtualenvironment.

```
virtualenv ~/.virtualenvs/plex-lastfm-scrobber
source ~/.virtualenvs/plex-lastfm-scrobber/bin/activate
```

Fetch and install the source from the github repo.
```
git clone https://github.com/jesseward/plex-lastfm-scrobbler.git
cd plex-lastfm-scrobbler
python setup.py install

```

Alternatively, you can fetch the latest zip from github

```
wget https://github.com/jesseward/plex-lastfm-scrobbler/archive/master.zip
unzip master.zip
cd plex-lastfm-scrobbler-master
python setup.py install
```

You're done.

**Windows**
Note: any feedback regarding MS Windows functionality is appreciated.

*  Download and install Python 2.7.x from https://www.python.org/download/ . Ensure you enable the option to install Python to the system/site path.
*  Download the zip of plex-lastfm-scrobbler from https://github.com/jesseward/plex-lastfm-scrobbler/archive/master.zip .
* unzip archive to a temporary location. for this example we will use c:\temp\plex-lastfm-scrobbler\
* Open a command prompt and change to the \temp folder
```
cd c:\temp\plex-lastfm-scrobbler\
```
* in the c:\temp\plex-lastfm-scrobbler\ directory, run the installer from the dos prompt
```
python setup.py install
```
The above command installs the python scripts to various locations. Directories of interest are :
* c:\Python27\scripts\
* .config  - this directory is created in your Users home directory (http://en.wikipedia.org/wiki/Home_directory#Default_home_directory_per_operating_system). You will need to modify the configuration file from within this directory and point log locations at the appropriate locations for Plex on windows. You can set the "log_file" property to c:\temp or some other location which you wish to write logs to.

To run the application, do the following from a DOS prompt
```
cd c:\Python27\scripts
python plex-scrobble.py
```

Configuration
-----------

The plex-lastfm-scrobbler configuration file (plex_scrobble.conf) is installed to ~/.config/plex-lastfm-scrobbler/ . The following configuration values are available.

If you're running Plex Media Server on a Linux based operating system, things should work out of the box.

```
[plex-scrobble]
# REQUIRED: mediaserver_url is the location of the http service exposed by Plex Media Server
# the default values should be 'ok', assuming you're running the plex scrobble
# script from the same server as your plex media server
mediaserver_url = http://localhost:32400

# REQUIRED: Where do you wish to write the plex-scrobble log file.
log_file = /tmp/plex_scrobble.log

# REQUIRED: a python data struture that stores failed scrobbles. plex-scrobble
# will retry on a 60 minute interval, maximum of 10 attempts if last.fm is
# experiencing issues.
cache_location = /tmp/plex_scrobble.cache

# OPTIONAL: mediaserver_log_location references the log file location of the plex media server
# the default under /var/lib/... is the default install of plex media server on
# a Linux system. You may wish to change this value to reference your OS install.
# https://support.plex.tv/hc/en-us/articles/200250417-Plex-Media-Server-Log-Files
mediaserver_log_location = /var/lib/plexmediaserver/Library/Application Support/Plex Media Server/Logs/Plex Media Server.log

# OPTIONAL: plex_token defines the plex token used to get metadata
# Note: This is required if you use localhost or 127.0.0.1 and Plex Media Server >= 1.1.0
# You will know if you see a line like this your log_file:
# [plex_scrobble.plex_monitor fetch_metadata] [ERROR] urllib2 error reading from http://localhost:32400/library/metadata/48080 'HTTP Error 401: Unauthorized'
# Here is how you can obtain your token https://support.plex.tv/hc/en-us/articles/204059436-Finding-your-account-token-X-Plex-Token
plex_token = YOUR_PLEX_TOKEN
```

Running
--------

If you installed plex-lastfm-scrobble to a virtual environment, enable the virtual env.

```
source ~/.virtualenvs/plex-lastfm-scrobber/bin/activate
```

run the application
```
$ plex-scrobble.py
```
On first run you will be prompted to authenticate and grant access to your Last.fm account. Visit the URL generated by plex-scrobble and follow the prompts to grant access to the application.

Example.


```
$ plex-scrobble.py

== Requesting last.fm auth ==
Please accept authorization at http://www.last.fm/api/auth/?api_key=blunted_dummies-house-for-all-1991

Have you authorized me [y/N] : y
Please relaunch plex-scrobble service.

```

Once this is complete, please re-start the service, listen to audio via Plex and watch as your music is scrobbled. You may wish to leave the service running in the background. On a POSIX system, wrap the script in the no-hangup utility.

```
$ nohup plex-scrobble.py &
```

Troubleshooting & Known Issues
-------------

* If you're experiencing authentication issues (appearing in plex_scrobble.log), remove the ~/.config/plex-lastfm-scrobbler/session file. This stores your Last.FM authentication token. There is no harm in removing/recreating this as many times as needed.
* If your Plex client supports the universal transcoder (see "Old and Universal transcoder @ https://support.plex.tv/hc/en-us/articles/200250377-Transcoding-Media), tracks will be scrobbled at the start of play. This is due to the way that the universal transcoder writes to the Plex log file. See issue 11 (https://github.com/jesseward/plex-lastfm-scrobbler/issues/11) for background discussion.
* We've seen instances when Plex Media Server does not report the length of an audio file. This may occur before a full library analyze has completed. When the track length is not reported by the Plex Media Server, the song will not be scrobble. Try forcing the "Analyze" audio library function. Further discussion found in issue #9 https://github.com/jesseward/plex-lastfm-scrobbler/issues/9

Or browse the github issues list to review old bugs or log a new problem.  See https://github.com/jesseward/plex-lastfm-scrobbler/issues?q=


Contributing
-----------
Any feedback on the performance on a MS Windows installation would be appreciated. I do not have ability to test plex-lastfm-scrobbler on this platform. Please log an issue or a pull request with any fixes.


