# Nginx Force Gunzip Module
This is a simple patch modifying the gunzip filter module to force inflate compressed responses from an upstream server.

This serves multiple purposes:
- It greatly reduces network bandwidth between nginx and upstream server(s).
- Some modules require upstream content to be uncompressed to work properly.
- It allows nginx to recompress the data (i.e. [brotli](https://github.com/google/ngx_brotli "brotli")) before sending to the client.

## Configuration Directives

### `gunzip_force`
- **syntax**: `gunzip_force on|off`
- **default**: `off`
- **context**: `http`, `server`, `location`

Enables or disables forced decompression of upstream content.

## Comments
Make sure you comment / remove `proxy_set_header   Accept-Encoding "";` from your nginx configuration, otherwise your upstream server won't compress any content!

I set my upstream server to use gzip level 1 to maximize speed since nginx will recompress the content.

## Original Author
I claim no rights to this work, I found the patch burried in the [Nginx Mail Archives](http://mailman.nginx.org/pipermail/nginx-devel/2013-January/003276.html "Nginx Mail Archives") from 2013 and simply updated it to patch cleanly on the current mainline release. The original author is Weibin Yao.
