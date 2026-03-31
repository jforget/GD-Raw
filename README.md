[![Actions Status](https://github.com/raku-community-modules/GD-Raw/actions/workflows/linux.yml/badge.svg)](https://github.com/raku-community-modules/GD-Raw/actions) [![Actions Status](https://github.com/raku-community-modules/GD-Raw/actions/workflows/macos.yml/badge.svg)](https://github.com/raku-community-modules/GD-Raw/actions)

NAME
====

GD::Raw - Low level language bindings to GD Graphics Library

SYNOPSIS
========

```raku
use GD::Raw;

my $fh = fopen("my-image.png", "rb");
my $img = gdImageCreateFromPng($fh);
LEAVE gdImageDestroy($_) with $img;

say "Image resolution is ", gdImageSX($img), "x", gdImageSX($img);
```

DESCRIPTION
===========

`GD::Raw` is a low level language bindings to LibGD. It does not attempt to provide you with an rakuish interface, but tries to stay as close to its `C` origin as possible.

LibGD is large and this module far from covers it all. Feel free to add anything your missing and submit a pull request!

MEMORY MANAGEMENT
=================

When creating an in-memory image, some memory is allocated in GD. This memory is not automatically deallocated when the variable which refers to the image goes out of scope. To counter this possible memory leak, the simplest way is to use the `LEAVE` phaser and call function `gdImageDestroy` like this

```raku
my $img = gdImageCreateFromPng($fh);
LEAVE gdImageDestroy($_) with $img;
```

If a program creates several images, there will be a problem if the program reuses the `$img` variable. In this case, you cannot use the `LEAVE` phaser, you must call `gdImageDestroy` before creating the second image (and the third, and...)

```raku
my $img = gdImageCreateFromPng($fh1);
[...]
gdImageDestroy($img);
$img = gdImageCreateFromPng($fh2);
[...]
gdImageDestroy($img);
$img = gdImageCreateFromPng($fh3);
[...]
gdImageDestroy($img);
```

Or a simpler solution is to use different variables `$img1`, `$img2`, `$img3` and so on, and calling `gdImageDestroy` each time with the `LEAVE` phaser.

```raku
my $img1 = gdImageCreateFromPng($fh1);
LEAVE gdImageDestroy($_) with $img1;
[...]
my $img2 = gdImageCreateFromPng($fh2);
LEAVE gdImageDestroy($_) with $img2;
[...]
my $img3 = gdImageCreateFromPng($fh3);
LEAVE gdImageDestroy($_) with $img3;
[...]
```

SEE ALSO
========

Raku Module `GD`: [https://github.com/raku-community-modules/GD](https://github.com/raku-community-modules/GD)

C library: [https://libgd.github.io/](https://libgd.github.io/)

AUTHORS
=======

  * Dagur Valberg Johannsson

  * Raku Community

COPYRIGHT AND LICENSE
=====================

Copyright 2013 - 2018 Dagur Valberg Johannsson

Copyright 2024, 2026 Raku Community

This library is free software; you can redistribute it and/or modify it under the Artistic License 2.0.

