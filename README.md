# Places
30 points
>Why does he have firefox browsing history on the flash drive?
>How do you get browsing history from firefox history?
>It's the same fk1onhl4.default.zip as in Mount Image, but we've reuploaded here as well for your convenience.
## Solving it

To solve this challenge we have to look at Firefox browsing history in the zip file.

I extracted the zip file and saw the following files:

```
# ls
addons.json            favicons.sqlite-wal  prefs.js
addonStartup.json.lz4  features             revocations.txt
AlternateServices.txt  formhistory.sqlite   saved-telemetry-pings
blocklist.xml          gmp                  search.json.mozlz4
bookmarkbackups        gmp-gmpopenh264      secmod.db
cert8.db               gmp-widevinecdm      SecurityPreloadState.txt
compatibility.ini      handlers.json        sessionCheckpoints.json
containers.json        key3.db              sessionstore-backups
content-prefs.sqlite   kinto.sqlite         sessionstore.js
cookies.sqlite         localstore.rdf       shield-preference-experiments.json
crashes                minidumps            SiteSecurityServiceState.txt
datareporting          permissions.sqlite   storage
extensions             places.sqlite        storage.sqlite
extensions.json        places.sqlite-shm    times.json
favicons.sqlite        places.sqlite-wal    webappsstore.sqlite
favicons.sqlite-shm    pluginreg.dat        xulstore.json
````
At first it may look like a bunch of junk, but it's not. The challenge instructions said Firefox browsing history, so I started researching how and where Firefox stores that information.
After some digging through the bowels of Google I was able to determine that the data in the zip file was a Firefox "profile" folder, typically named `$something.default` where `$something` is a random string of letters and numbers like `fk1onhl4`(or at least I think it's random, there might be some pattern I'm not aware of).
On Linux this is stored under `~/.mozilla/firefox`.
One site that I found particularly helpful was the [forensics wiki page on Mozilla Firefox.](http://forensicswiki.org/wiki/Mozilla_Firefox)
According to this page:
>Firefox stores the history of visited sites in a file named places.sqlite. This file uses the SQLite database format.

Based on the information from forensics wiki, I figured out I needed to look at the `places.sqlite`. The name of the challenge "places" also hints that this is the right course.
To do that we need a SQLite database viewer. Thankfully Kali Linux has a graphical tool for that built in. It's called "DB Browser for SQLite."
Once we have the tool open, the first step is to open the database by clicking `Open Database`. Then simply navigate to where the file is stored and click open.
Then you will see this:
![Screenshot1](https://github.com/DigiBrkr/csaw_hsf_qualifier_2017_Places_30/blob/master/Screenshot1.PNG?raw=true)

I know it's a lot but it's not as confusing as it seems. To solve this challenge the only thing we need to do is execute the following (also taken from the forensics wiki):
```
SELECT datetime(moz_historyvisits.visit_date/1000000, 'unixepoch', 'localtime'), moz_places.url FROM moz_places, moz_historyvisits WHERE moz_places.id = moz_historyvisits.place_id;
```
To do this we click the `Execute SQL` button. Then paste in the query and click the triangular play button.
This will then present you with a table of dates and URL's like the following;

![Screenshot2](https://github.com/DigiBrkr/csaw_hsf_qualifier_2017_Places_30/blob/master/Screenshot2.PNG?raw=true)

After looking through it for a little while I came across a particularly interesting Yahoo search: `https://search.yahoo.com/yhs/search?p=flag%7Bshhh_we_n3eded_the_story_to_continue%7D%09&ei=UTF-8&hspart=mozilla&hsimp=yhs-002` going to the URL gives us the flag:
`flag{shhh_we_n3eded_the_story_to_continue}`

