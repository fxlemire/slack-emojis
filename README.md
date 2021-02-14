# Emojis for Slack

## How To Use

- Install the [Neutral Face Emoji Tools](https://chrome.google.com/webstore/detail/neutral-face-emoji-tools/anchoacphlfbdomdlomnbbfhcmcdmjej?hl=en) from the Chrome Web Store
- Bulk import all desired emojis from the [emojis](./emojis) folder.

## Emojis Licenses

- [blobs-gg](./emojis/blobs-gg): From the [Blob Emoji](https://blobs.gg/) website. See [LICENSE](./emojis/blobs-gg/LICENSE.txt).
- [cultofthepartyparrot](./emojis/cultofthepartyparrot): From the [Cult of the Party Parrot](https://cultofthepartyparrot.com/) website.
- [slackmojis](./emojis/slackmojis): From the [Slackmojis](https://slackmojis.com/) website.
- [other](./emojis/other): Gathered from quite everywhere on the Web through the years.

## Some useful Fish Shell scripts

For the ones who don't know [Fish Shell](https://fishshell.com/): check it out.

The others: proceed.

### Bulk renaming

Most of the animated gifs were prepended with `a`, and some had `blob` at the end instead of the start. I also wanted to split `blob` with a hyphen. Some useful scripts that helped me:

```fish
# Add "-" after "blob" or "ablob"
ls | sed -r 's/(a?blob)(.+\..{3})/mv \1\2 \1-\2/' | sh

# Rename all "ablob-" to "blob-"
ls | sed -r 's/ablob-(.*)\.gif/mv ablob-\1\.gif blob-\1\.gif/' | sh

# Remove "blob" from end of name, and instead prepend with "blob-"
ls | sed -r 's/(.+)(blob.png)/mv \1\2 blob-\1.png/' | sh
```

### Gif Editing

Slack seems to prefer emojis that are 130 KB (126.95 KiB) and 128x128. Below are some ways to achieve this.

#### CLI

The scripts use [Gifsicle](https://www.lcdf.org/gifsicle/).

```fish
# Install

brew install gifsicle


# Set your filename first. Then you only have to change this if you read to run commands multiple times.

set -x file my-emoji.gif


# Lossy compression
# The higher the number, the higher the compression, the higher the noise, the smaller the file size.

gifsicle --lossy=70 $file > ./compressed/$file # Outputs a new file
gifsicle -b --lossy=100 $file # Overwrites the file


# Finding the number of frames and their delays

gifsicle -I $file


# Removing every 4th frame in a gif with 100 frames
# It's okay to go beyond frames (e.g. 150), gifsicle will complain but not crash. Better more than not enough :)
# To remove every 2nd frame, replace "(math $x%4)" by "(math $x%2)"

gifsicle -U $file (for x in (seq 0 150); if test (math $x%4) != 0; echo "#$x"; end; end;) -O3 -o compressed/$file


# Bringing back speed to normal after cutting frames. This requires the "gifsicle -I" command above.
# If removed 4th frame, multiply delay by 4/3. If removed 3rd frame, by 3/2. If removed 2nd frame, by 2. Etc.
# For example, if delay was 0.05s and we removed every 4th frame, then (math ".05*4/3" = 0.0666), so delay should be 7.
# Note that if your frames have different delays, this method won't be perfect. See the #Website section below.

gifsicle -b --delay=7 $file # Overwrites the file
```

#### Website

For more complex needs, visit [ezgif.com](https://ezgif.com/optimize). It's amazing. You can easily crop, resize, skip frames, etc.
