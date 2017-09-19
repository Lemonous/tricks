# Transferring a playlist from Google Play to Spotify

I used Chrome for this - the steps may differ slightly in Firefox.

## Preparing to get data from Gplay

Firstly, open the Google Play playlist webpage. Once you have done that, put a breakpoint on attribute changes of the playlist table tbody (hopefully of class ".song-table"). Notice that tbody has several attributes with the word index in them.

Simply scroll up or down. The execution will stop, and it will point you to the line where execution stopped. Pretty print the file; this will make reading it a lot easier.

Find the line containing something like this:

```javascript
var e = b.bl.G5().height
```

The important part is .height - it's the only line containing that property. Slightly further down in the file should be a line something like this:

```javascript
e = new qZ(f-10,f+Math.ceil(h/e)+10)
```

You want to change it to this:

```javascript
e = new qZ(0, *_amount of songs in playlist_*)
```

To do so, you have to find the same code in the non-prettyprinted file. This is easily done: type in the variable names and remove unneeded spaces, like this: `e=new qZ`, substituting for your own variable names.

You can then right click the tab at the top of the dev window (something.js:formatted) and select "Reveal in Navigator" to be sent to the original file. Find the correct place by searching for the string you just made, and change it like in the above example.

Save the edited file with Ctrl+S; it will say that it couldn't save to disk, but that doesn't matter.

Let the current function call finish, then scroll the page again. All the songs in the playlist should be loaded - you can verify this by using this code in the console:

```javascript
document.querySelectorAll('.song-table tr.song-row').length;
```

Now you can finally copy the names and artists with the following code snippet in console:

```javascript
var playlistString = "", playlist = document.querySelectorAll('.song-table tr.song-row');
for (let track of playlist) {
        var title, artist = "";
        for (let info of track.childNodes) {
                if (info.getAttribute('data-col') == 'title') {
                        title = info.querySelector('.column-content').textContent;
                } else if (info.getAttribute('data-col') == 'artist') {
                        artist = info.querySelector('.column-content a').textContent;
                }
        }
        playlistString += ('"' + artist + '", "' + title + '"\n');
}
console.log(playlistString);
```

Copy the resulting text into a text file - we'll have to do a few changes to it before it can be imported, because the formats are different in Spotify and Google Play.

## Changing the data

If you have a really long playlist, you might want to use some kind of automata here. My playlist was short enough that I didn't need it, so I didn't make such a script. If you do use make one, please contact me so I can add it here :)

These are the differences I have found so far between Gplay and Spotify:

 - Spotify almost never has "(feat. otherPerson)" in the title. More songs are successfully converted if you delete this part
 - If several artists are strung together with ampersands ("&"), keep only the most well-known of the artists, or change ampersands to commas
 - Some inconsistency between square brackets and parentheses. Spotify seems to use parentheses almost everywhere.
 
## Converting the data

Paste your modified data into [this excellent website](http://www.playlist-converter.net/#/), and click "Convert to Spotify". Hopefully it should find most of the songs. Connect it to your Spotify account to automatically make a new playlist. Remember to open the playlist in a new tab if you do it in the browser.

The few songs that weren't found can now be added manually to the mostly complete playlist. Enjoy!

Also: consider donating to the folks behind Playlist Converter. Without them, this would have been a lot harder :) There's a Paypal button if you follow the link above!
