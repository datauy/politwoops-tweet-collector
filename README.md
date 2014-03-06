## Install Beanstalkd

http://kr.github.com/beanstalkd/download.html

Requires installing the libevent-dev package on apt-based systems.


## Install Python dependencies

Install pip if you don't already have it then run:

```bash
pip install -r requirements.txt
```


## Edit config file

First:

```
cp conf/tweets-client.ini.example conf/tweets-client.ini
```

In the [tweets-client] section, add your Twitter account's username and password. This account will be authenticated against to make all API requests.

In the [beanstalk] section, change "tweets_tube" and "screenshot_tube". The values don't matter much, they just need to be unique.

In the [database] section, update the "host", "port", "username", "password", and "database" sections with your own details, if the defaults are not appropriate.

In the [aws] section, add your access key, secret access key, bucket name, and any path prefix inside the bucket you want to use. This is for archiving images and screenshots of tweeted links.


## Running

Run tweets-client.py to start streaming items from Twitter into the beanstalk queue. Append the lib directory to the PYTHONPATH, either persistently or as part of the command:

```bash
PYTHONPATH=$PYTHONPATH:`pwd`/lib ./bin/tweets-client.py
```

Then run politwoops-worker.py to start pulling the tweets out of beanstalk and loading them into MySQL:

```bash
PYTHONPATH=$PYTHONPATH:`pwd`/lib ./bin/politwoops-worker.py --images
```

Finally, if you ran politwoops-worker.py with the images option turned on, run screenshot-worker.py to grab screenshots of webpages and mirror images linked in tweets.

```bash
PYTHONPATH=$PYTHONPATH:`pwd`/lib ./bin/screenshot-worker.py
```

These three scripts all accept the following options:

* `--loglevel` - Sets the verbosity of logging.
* `--output` - Destination for log files. 
* `--restart` - Restart if the script encounters an error that cannot be handled.

# Run As a Service in Linux
```
cp bin/start.sample bin/sample
cp bin/stop.sample bin/stop
cp bin/start-worker.sample bin/start-worker
cp bin/stop-worker.sample bin/stop-worker
```

for each one of these files:
- edit the project path
- change mod to executable ```sudo chmod a+x```

copy the files inside service_scripts directory to /etc/init.d
```
cp service_scripts/politwoops_tweet_collector.sample /etc/init.d/politwoops_tweet_collector
cp service_scripts/politwoops_tweet_worker.sample /etc/init.d/politwoops_tweet_worker
```

for each one of these files:
- edit the project path
- change mod to executable ```sudo chmod a+x```
