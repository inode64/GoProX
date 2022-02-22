# GoProX

The missing GoPro workflow and data manager for macOS.
For those with one or more GoPro cameras. When using these  action cameras on
a regular basis, the limitations of the GoPro ecosystem quickly become very
limiting. GoPro is focusing on their mobile app experience and not much development
is directed towards the macOS.
GoProX is geared at focusing on semi professionals or prosumers that simple need
more from their devices.

GoProX is based on a data first approach to importing, processing and maintaining
media generated by various GoPro devices. At this moment the tool is actively
developed and tested with GoPro Hero8, Hero9, Hero10 and GoPro Max.

Once installed and setup GoProX maintains a file system based media library that
is organized by time, camera types and media file types. The main goal is to
maintain all file details from the original to the processed items, even if media
files are further being processed in various apps including
[Apple Photos](https://www.apple.com/ios/photos/) or
[Blackmagic DaVinci Resolve](https://www.blackmagicdesign.com/products/davinciresolve/).

Unfortunately many commonly used apps ignore most of the important metadata,
making it very hard for users to filter, sort or search for common things inside
ever growing libraries of media.

GoProX performs a few simple tasks that will make your life with GoPro
media on Apple platforms a lot easier.

First it renames the files as part of `--import` and `--process` tasks. This is an
often overlooked step that will lead to loss of some of the most basic metadata
over time. Especially when files get exported, copied or moved, things like
original date & time, source of the original image can get lost easily, creating
issue down the line.

It then adds additional tags and keywords into the processed media files to make
searching, filtering and corrections a lot easier.

GoProX supports `--geonames` lookups for gps based timezone information,
`--firmware` checks and upgrades, `--archive` of raw media into compressed
archives before `--import` and `--process` tasks are being applied, a `--clean`
task to remove processed media from storage devices as well as a `--timeshift`
tasks that makes it possible to bulk change the date and time information of
media files. It also makes it easy to apply default `--copyright` tags to all
processed media.
Default options can be set using the `--setup` tasks and settings are being
stored in the users home directory in `~.goprox`.

For developers GoProX comes with a small set of test data from various cameras
that allows for testing and validation of changes with the `--test` option. 

## Disclaimer & Credits

GoProX is not related with GoPro Inc or its products. It is not supported nor
endorsed by GoPro Inc.
GoProX is not related with Apple Inc, Blackmagic Design Pty Ltd or any other
products or platforms referred to as part of the tools description or
documentation unless explicitly stated.   

GoProX leverages the [exiftool](https://exiftool.org) by Phil Harvey to extract
common GoPro metadata in order to name and tag images and videos for further
processing.

GoProX leverages the [GeoNames](https://www.geonames.org) database by Marc Wick
to perform gps based geocode lookups of timezones and related data.

## Filenames

### The Filenames Mess

GoPro uses various different file-naming conventions in its cameras. There is no
clean structure and for most cases you get some sort of prefix followed by a
running number and a file extension.

Here are some examples of GoPro Filenames:

```
GOPR0001.JPG
GH010008.MP4
GX010408.MP4
GS__3305.JPG
GS013331.360
```

If you happen to leverage the GoPro+ Cloud it gets a lot weirder with things
like

```
GPTempDownload.jpg
```

for anything GoPro Quik touches and forwards to eg. Apple Photos.

There are many problems with these filenames - many not unique to GoPro. First
of all they are very non descriptive. You might be able to deduct the camera
model to some extend, but thats about it. No date/time in case that information
gets lost somewhere along the way. But also the fact that these names are
created for a world where users ever only use a single camera at the same time.
As all cameras start with 0001 for the first media and continuously count up,
its only a question of time when you will run into naming conflicts with two or
more cameras. That usually means one file from one camera will overwrite another
file from another camera. And depending on usage each individual camera will
eventually in some cases even regularly restart at 0001.

So instead of very complicated folder structures to keep images from colliding
with each other, GoProX creates more sophisticated filenames that allow you to
sort and act on the filenames themselves.

### A better way to name files

Lets take a look at the files names GoProX creates on `import`:

```
20211010090947_GoPro_Hero10_7678_GOPR0768.JPG
20160130223238_GoPro_Max_6013_GS__1596.JPG
20210615110514_GoPro_Hero9_9650_GOPR1353.JPG
20201231054457_GoPro_Hero8_1659_GOPR0129.JPG
20210731003746_GoPro_Hero9_4139_GH013340.MP4
20211011072108_GoPro_Max_6013_GS012830.360
```

The first 8 digits are the original date when the photo was taken, followed by 6
digits of the time, followed by the model of the camera. The for digits after
that are the last four digits of the cameras serial number - to allow you to
find all the media from a particular camera. Especially important if you find
out after the fact that a setting was wrong in one of your cameras (Like when
you find a 2016 date for GoPro Max media). Finally the original filename and
extension are being added.

Right there is a wealth of information that helps with a lot of issues and even
software bugs in the likes of Apple Photos that for example messes up the date &
time for imported MP4 files. As the filename is kept along with the images and
videos it gives you a very simple way to double check what is going on.

.360 videos from the GoPro Max are listed here as well but cannot be imported
directly into the likes of Apple Photos. You will need GoPro Player on the Mac
or GoPro Quik on iOS to process them for further consumption.

The after initial `import` the `process` option further refines the filenames
and also adds and rewrites the embedded metadata. All file extensions are
normalized to lowercase extensions and the files are sorted by main file type.
As part of that process `.360` files become `.mp4` files that are otherwise
identical to the GoPro `.360` format but can now be handled by most downstream
applications.

Processed files get an additional `P_` prefix to clearly delineate from the
unmodified imported files.

```
P_20210606094917_GoPro_Hero9_4139_GOPR0182.jpg
P_20210806114935_GoPro_Hero9_4139_GOPR3422.jpg
P_20220206144720_GoPro_Hero10_8034_GOPR2313.jpg
P_20220206145556_GoPro_Hero10_8034_GOPR2320.jpg
...
P_20210627102316_GoPro_Hero9_0021_GH013156.mp4
P_20211015084940_GoPro_Max_6013_GS013292.mp4
```    

## Exif data

### Tags

In addition to the files names getting supercharged, there is a ton of
information available inside the Exif metadata that macOS or Apple Photos keep
intact but ignore and not even display, unless you use Preview on a particular
file to inspect the metadata.

Apple Photos really only gives you Title, Caption and Tags to work with to store
additional information aside from the most basic metadata.

This is where GoProX performs a little metadata shuffle to make at least some
of the attributes searchable from within Apple Photos.

Several of the low cardinality Exif fields are getting converted to a list of
tags. That list Apple Photos converts to its own tags when importing the media.

Here is a list of tags that are being created by GoProX:

```CameraModel_4digitSN
Orientation
AutoRotation
SceneCaptureType
ProTune
Sharpness
HDRSetting
ProjectionType
ExposureLockUsed
MeteringMode
GainControl
Contrast
Saturation
WhiteBalance
ImageStabilization
```

These tags can be used inside of Apple Photos for various tasks, most
importantly to create Smart Albums that key off one or more of them.

### Copyright information

As you will capture more and more media with multiple GoPro cameras, it is
always a good idea to set the Artist/Author/Copyright fields in Exif.
That way you keep a record of the files you have created.

## A Simple workflow

All of this needs to be part of a simple workflow that captures and stores the
original media as well as the processed files.

GoProX will most likely only be one step in your process and as been designed
to recursively walk the source tree and process all media files in its
structure. It does not matter if the source images are in a single folder or
organized by date, camera or whatever subject you choose.

On the output side a very simple data based file structure is created with one
folder per original media creation date.

The tool can be run on a single subfolder of your source files or at the root of
it. The output will always be the date folder hierarchy at the destination
folder.

When re-run, existing target files are skipped but logged in the error log. That
way large or incremental processing job can be restarted as often as you
like. This comes with some processing overhead for existing files but is
generally very fast.

If you want to reprocess certain files, simply delete them at the destination
folder and rerun the process.   

## Performance

The [exiftool](https://exiftool.org) has phenomenal capabilities and if you let
it do the vast majority of work by itself, it is really fast.

Testing on 20,000 images on a 2017 iMac with a quad core i5 and one 2TB SSD
(source: original files folder) plus one 14TB HDD (target: processed files
folder) have continuously resulted in 200MB+/sec read and write speeds at less
than 20% CPU utilization.

## Simplicity

I tried to keep the tool as simple as possible. This undoubtedly will come with
limitations for certain use-cases. Feel free to drop me a line or a PR with
enhancements.
