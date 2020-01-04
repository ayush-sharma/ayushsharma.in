---
layout: post
title:  "Decompressing request using GZIP with Nginx"
number: 22
date:   2016-10-25 1:00
categories: development
---
If you're getting compressed data in the request, and need to pass on the decompressed data, you can use the following code snippet to decompress the request in flight.

```lua
-- Handle request
local zlib = require 'zlib'

myStr = nil
if ngx.req.get_headers()['Content-Encoding'] == 'gzip' then

    ngx.req.read_body()
    local myStr = zlib.inflate()(ngx.var.request_body, 'finish')

    ngx.req.clear_header('Content-Encoding')
    ngx.req.clear_header('Content-Length')
    ngx.req.set_body_data(myStr)
end
```

To use the above, you will need the `lua-zlib` library which you can install using `luarocks install lua-zlib`.