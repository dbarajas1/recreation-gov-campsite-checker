# Campsite Availability Scraping

**This has been updated to work with the new recreation.gov site and API!!!**

This script scrapes the https://recreation.gov website for campsite availabilities.

## Example Usage

```
$ python camping.py --start-date 2018-07-20 --end-date 2018-07-23 232448 232450 232447 232770
❌ TUOLUMNE MEADOWS: 0 site(s) available out of 148 site(s)
🏕 LOWER PINES: 11 site(s) available out of 73 site(s)
❌ UPPER PINES: 0 site(s) available out of 235 site(s)
❌ BASIN MONTANA CAMPGROUND: 0 site(s) available out of 30 site(s)
```

You can also read from stdin. Define a file (e.g. `parks.txt`) with IDs like this:

```
232447
232449
232450
232448
```

and then use it like this:

```
$ python camping.py --start-date 2018-07-20 --end-date 2018-07-23 --stdin < parks.txt
```

You'll want to put this script into a 5 minute crontab. You could also grep the output for the success emoji (🏕) and then do something in response, like notify you that there is a campsite available. See the "Twitter Notification" section below.

## Getting park IDs

What you'll want to do is go to https://recreation.gov and search for the campground you want. Click on it in the search sidebar. This should take you to a page for that campground, the URL will look like `https://www.recreation.gov/camping/campgrounds/<number>`. That number is the park ID.

You can also take [this site for a spin](https://pastudan.github.io/national-parks/). Thanks to [pastudan](https://github.com/pastudan)!

## Installation

I wrote this in Python 3.7 but I've tested it as working with 3.5 and 3.6 also.

```
python3 -m venv myvenv
source myvenv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
# You're good to go!
```

## Development

This code is formatted using black and isort:

```
black -l 80 --py36 camping.py
isort camping.py
```

Note: `black` only really supports 3.6+ so watch out!

Feel free to submit pull requests, or look at the original: https://github.com/bri-bri/yosemite-camping

### Differences from the original

-   Python 3 🐍🐍🐍.
-   Park IDs not hardcoded, passed via the CLI instead.
-   Doesn't give you URLs for campsites with availabilities.
-   Works with any park out of the box, not just those in Yosemite like with the original.
-   **Update 2018-10-21:** Works with the new recreation.gov site.

## Twitter Notification

If you want to be notified about campsite availabilities via Twitter (they're the only API out there that is actually easy to use), you can do this:

1. Make an app via Twitter. It's pretty easy, go to: https://apps.twitter.com/app/new.
2. Change the values in `twitter_credentials.py` to match your key values.
3. Pipe the output of your command into `notifier.py`. See below for an example.

```
python camping.py --start-date 2018-07-20 --end-date 2018-07-23 70926 70928 | python notifier.py @banool1
```

You'll want to make the app on another account (like a bot account), not your own, so you get notified when the tweet goes out.

I left my API keys in here but don't exploit them ty thanks.

**Thanks to https://github.com/bri-bri/yosemite-camping for getting me most of the way there for the old version.**

## Mac OS Notifications

If you want to receive notifications on your Mac when running with Cron, read on.

Install [`terminal-notifier`](https://github.com/julienXX/terminal-notifier) (with Homebrew - `brew install terminal-notifier`).

Then you will want to run the script `cron.sh` to send a notification whenever there are campsites. `cron.sh` takes the same arguments as `camping.py`. If you are using a virtualenv, provide the path to your virtualenv and run the script like so:

```sh
> VIRTUAL_ENV=/path/to/virtualenvdir/.virtualenvs/recreation-gov /path/to/respository/cron.sh --start-date 2019-11-11 --end-date 2019-11-12 232447
```

Then, when the script runs, it'll send notifications.
