# ID3v2

## IMHO
I think, **ID3** is a very overwhelmed standard: it does **more than it really should do**. There are a lot of aspects, which developer should take into consideration. And that's why it's pretty complicated to write a **good library**. So if you have some thoughts about writing a **new simply and elegant standard** for providing information for digital music tracks, just write me. I think, it's a good time to write an appropriate standard for it 😉

## Information
**Stable and fast ID3v2 Library written in Go**

This library can only set and write tags, but can't read them. So if you only want to set tags, it fits you. And if there is a tag of version 3 or 4, this library will just delete it. If version of the tag is small than 3, this library will return an error.

What it **can** do:
* Set artist, album, year, genre, unsynchronised lyrics/text (USLT), comments and **attached pictures** (e.g. album covers) and write all to file
* Set several USLT, comments and attached pictures

What it **can't** do:
* Parse tags
* Work with extended header, flags, padding
* Work with encodings, except UTF-8

**If you want some functionality, that library can't do, just write an issue. I will implement it as fast as I can**

**And of course, pull requests are welcome!**

## Benchmarks

All benchmarks run on **MacBook Air 13" (early 2013, 1,4GHz Intel Core i5, 4GB 1600MHz DDR3)**

#### Set title, artist, year and 50KB picture to 4,6 MP3:
```
BenchmarkSetCommonCase-4	     100	  15881298 ns/op	   13526 B/op	      34 allocs/op
```

#### Set title, artist, album, year, genre, unsynchronised lyrics, comment and 50KB picture to 4,6MB MP3:
```
BenchmarkSetManyTags-4  	     100	  15251942 ns/op	   15081 B/op	      50 allocs/op
```

## Installation
  	$ go get -u github.com/bogem/id3v2

## Usage
### Example:
```go
package main

import (
  "github.com/bogem/id3v2"
  "log"
)

func main() {
  tag, err := id3v2.Open("file.mp3")
  if err != nil {
   log.Fatal("Error while opening mp3 file: ", err)
  }

  tag.SetArtist("Artist")
  tag.SetTitle("Title")
  tag.SetYear("2016")
  ...


  if err = tag.Flush(); err != nil {
    log.Fatal("Error while closing a tag: ", err)
  }
}

```

### Available functions for setting text frames:
```go
tag.SetTitle(title string)
tag.SetArtist(artist string)
tag.SetAlbum(album string)
tag.SetYear(year string)
tag.SetGenre(genre string)
```

___

### Setting a picture:
```go
package main

import (
  "github.com/bogem/id3v2"
  "log"
)

func main() {
  tag, err := id3v2.Open("file.mp3")
  if err != nil {
    log.Fatal("Error while opening mp3 file: ", err)
  }

  pic := id3v2.NewAttachedPicture()
  pic.SetMimeType("image/jpeg")
  pic.SetDescription("Cover")
  pic.SetPictureType(id3v2.PTFrontCover)
  if err = pic.SetPictureFromFile("artwork.jpg"); err != nil {
    log.Fatal("Error while setting a picture from file: ", err)
  }
  tag.AddAttachedPicture(pic)

  if err = tag.Flush(); err != nil {
    log.Fatal("Error while closing a tag: ", err)
  }
}
```

**Available picture types:**
* `PTOther`
* `PTFileIcon`
* `PTOtherFileIcon`
* `PTFrontCover`
* `PTBackCover`
* `PTLeafletPage`
* `PTMedia`
* `PTLeadArtistSoloist`
* `PTArtistPerformer`
* `PTConductor`
* `PTBandOrchestra`
* `PTComposer`
* `PTLyricistTextWriter`
* `PTRecordingLocation`
* `PTDuringRecording`
* `PTDuringPerformance`
* `PTMovieScreenCapture`
* `PTBrightColouredFish`
* `PTIllustration`
* `PTBandArtistLogotype`
* `PTPublisherStudioLogotype`

___

### Setting an unsynchronised lyrics/text:
```go
  ...
  uslt := id3v2.NewUnsynchronisedLyricsFrame()
  uslt.SetLanguage("eng")
  uslt.SetContentDescriptor("Content descriptor")
  uslt.SetLyrics("Lyrics")
  tag.AddUnsynchronisedLyricsFrame(uslt)
  ...
```

**You should choose a language code from [ISO 639-2 code list](https://www.loc.gov/standards/iso639-2/php/code_list.php)**

___

### Setting a comment:
```go
  ...
  comment := id3v2.NewCommentFrame()
  comment.SetLanguage("eng")
  comment.SetDescription("Short description")
  comment.SetText("The actual text")
  tag.AddCommentFrame(comment)
  ...
```

**You should choose a language code from [ISO 639-2 code list](https://www.loc.gov/standards/iso639-2/php/code_list.php)**

___

## TODO

- [ ] Documentation
- [ ] Parse tags
- [ ] Work with other encodings
- [ ] Work with extended header, flags, padding ***(Does somebody really use it?)***

## License
MIT
