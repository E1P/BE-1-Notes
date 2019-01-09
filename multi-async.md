## Multiple asynchronous requests

In practice, we may not be dealing with a single async process: in fact, it may be the case that we have to fire off multiple requests and wait for them to all come back with something before we can do stuff.

```js
const getMP3 = (title, handleMP3) => {
  setTimeout(() => {
    handleMP3(null, `${title}.MP3`);
  }, Math.random() * 5000);
};
```

We may want to use above function to fire off multiple requests. e.g. say if we wanted to get an album of MP3 from a list of song titles.

```js
const getAlbum = (songTitles, handleAlbum) => {
  // handleAlbum will get invoked with an array of MP3s.
};
```

Normally,if we were writing synchronous code, we could appeal to `map` to do something like this: however, this no longer works because `map` uses the return value of the iteratee.

We will also need to consider two important things:

* Which is the best array method for this problem?
* When we will invoke the finall callback `handleAlbum` ?
* How can we guarantee that the MP3s will come back in the right order ?

```js
const getAlbum = (songs, handleAlbum) => {
  const album = [];
  songs.forEach(songTitle => {
    getMP3(songTitle, (err, MP3) => {
      album.push(MP3);
      if (songTitles.length === album.length){ handleAlbum(null, album);
}
    });
  });
};
```

In this situation our album array will get built for each callback that is invoked once the MP3 has come back from the request.
However, now we have lost the original order in which the callbacks came back. In order to get around this, we can pass the index to forEach's iteratee and by closure when the callbacks are called they will have a reference to this index.

```js
const getAlbum = (songs, handleAlbum) => {
  const album = [];
  songs.forEach((songTitle, index) => {
    getMP3(songTitle, (err, MP3) => {
      album[index] = MP3;
      if (songTitles.length === album.length){
	handleAlbum(null, album)
	};
    });
  });
};
```

The final problem is that the final callback `handleAlbum` might get called too early if the last title times out first :

```js
[undefined, undefined, 'Wanna Be.mp3'];
```

Now we need a counter to trace the number of times each callback has been fired:

```js
const getAlbum = (songs, handleAlbum) => {
  const album = [];
  let count = 0;
  songs.forEach((songTitle, index) => {
    getMP3(songTitle, (err, MP3) => {
      album[index] = MP3;
      if (++count === songs.length) {
		handleAlbum(null, album);
		}
    });
  });
};
```

## fs

`fs` is a module in node standing for file system. It is part of node so we don't need to have it installed in the node_modules. We can use it to asynchronously create and read files and directories etc.

```js
const fs = require('fs');
fs.readFile('./data.js', 'utf8', (err, fileData) => {
  console.log(fileData);
});