---
layout: post
title:  "Compressing output using GZIP with PHP 5.3"
number: 21
date:   2016-10-25 0:00
categories: development
---
It's pretty hard to compress output with GZIP in PHP 5.3, but in case you ever need to, here's how. To test this out, just dump the output to a file using `file_put_contents()`, and use the command-line `gunzip` utility to decompress your output. Or you can just `vim` the compressed file and see your data.

```php
<?php
$string = 'Ayush Sharma';

# Disable ZLIB output compression first
ini_set('zlib.output_compression','Off');

# Compress data
$gzip_output  = "\x1f\x8b\x08\x00\x00\x00\x00\x00";
$size         = strlen($string);
$crc          = crc32($string);
$compressed   = gzcompress($string, 6);
$compressed   = substr($compressed, 0, strlen($compressed) - 4);
$gzip_output .= $compressed . pack('V', $crc) . pack('V', $size);

# You now have compressed output in $gzip_output. The following headers are needed to send over HTTP.
header('Content-Encoding: gzip');
header('Content-Type: application/x-download');
header('Content-Encoding: gzip');
header('Content-Length: ' . strlen($gzip_output));
header('Content-Disposition: attachment; filename="gzipped.response"');
header('Cache-Control: no-cache, no-store, max-age=0, must-revalidate');
header('Pragma: no-cache');

echo $gzip_output;
```