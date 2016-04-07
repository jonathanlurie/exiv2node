#Exiv2

Exiv2 is a native c++ extension for [io.js](https://iojs.org/en/index.html) and
[node.js](https://nodejs.org/) that provides support for reading and writing
image metadata via the [Exiv2 library](http://www.exiv2.org).

***

## A word about this fork

This fork was made to use exiv2node without having dylib dependencies (other than system libs like libstdc++ or libSystem). The point is to make it embedable anywhere as soon as it is compiled for the target plateform. In my personal case, I made it to use within Electron so that I don't have to worry about distribution.  

### What you need to activate static linking

in the file `binding.gyp`, edit the following lines with the real path on your machine:

```json
'include_dirs' : [
        '/path/exiv2/include',
        '/path/to/expat/include',
         ...
      ],
```

and

```json
'libraries': [
        '/path/to/libexiv2.a',
        '/path/to/libexpat.a'
      ],
```

I strongly advise you do not use port or brew to get exiv2 because it will give you only dylib, which won't help you. (reminder: you cannot static link a dylib). So download the [sources of exif2](http://www.exiv2.org/download.html) and compile them using a regular:

```shell
$ ./configure --prefix=/some/local/folder/of/yours/
$ make; make install
```

**What about libExpat?**

You might already have it on your system. To check, type:

```shell
$ pkg-config --libs --static expat
```

(you don't have pkg-config? install it with `brew install pkg-config` or `sudo port install pkgconfig`)

If it gives you a result, it means it is certainly at `/opt/local/lib/libexpat.a` (or whatever folder it gave you with the **-L**).

When you have edited to `biding.gyp`, you can run:

```shell
$ cd exiv2node
$ npm install
```

The result, in addition to download npm dependencies, should be a binary file at `exiv2node/build/Release/exiv2.node`, weighting approximatelly 2.7MB.  
Be sure this `exiv2.node` file does not have any dependencies with:

```shell
# on mac osx
$ otool -L exiv2.node

# on linux
$ ldd exiv2.node
```

Now, you can follow to rest of the original **readme**...
***

## Installation Instructions

Once the dependencies are in place, you can build and install the module using
npm:

    npm install exiv2

You can verify that everything is installed and operating correctly by running
the tests:

    npm test

## Sample Usage

### Read tags:

    var ex = require('exiv2');

    ex.getImageTags('./photo.jpg', function(err, tags) {
      console.log("DateTime: " + tags["Exif.Image.DateTime"]);
      console.log("DateTimeOriginal: " + tags["Exif.Photo.DateTimeOriginal"]);
    });

### Load preview images:

    var ex = require('exiv2')
      , fs = require('fs');

    ex.getImagePreviews('./photo.jpg', function(err, previews) {
      // Display information about the previews.
      console.log(previews);

      // Or you can save them--though you'll probably want to check the MIME
      // type before picking an extension.
      fs.writeFile('preview.jpg', previews[0].data);
    });

### Write tags:

    var ex = require('exiv2')

    var newTags = {
      "Exif.Photo.UserComment" : "Some Comment..",
      "Exif.Canon.OwnerName" : "My Camera"
    };
    ex.setImageTags('./photo.jpg', newTags, function(err){
      if (err) {
        console.error(err);
      } else {
        console.log("setImageTags complete..");
      }
    });

### Delete tags:

    var ex = require('exiv2')

    var tagsToDelete = ["Exif.Photo.UserComment", "Exif.Canon.OwnerName"];
    ex.deleteImageTags('./photo.jpg', tagsToDelete, function(err){
      if (err) {
        console.error(err);
      } else {
        console.log("deleteImageTags complete..");
      }
    });

Take a look at the `examples/` and `test/` directories for more.

email: dberesford at gmail
twitter: @dberesford
