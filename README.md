# Nginx Force Gunzip Module
This is a simple patch modifying the NGINX [gunzip filter module](http://nginx.org/en/docs/http/ngx_http_gunzip_module.html "gunzip filter module") to force inflate compressed responses. This is desirable in the context of an upstream source that sends responses gzipped. Please read the "other comments" section to understand this will decompress *all* content, so you want to specify its use as specific as possible to avoid decompressing content that you otherwise would want left untouched.

This serves multiple purposes:
- It maintains transfering gziped content between upstream server(s) and nginx, thus reducing network bandwidth.
- Some modules require the upstream content to be uncompressed to work properly.
- It allows nginx to recompress the data (i.e. [brotli](https://github.com/google/ngx_brotli "brotli")) before sending to the client.

This has been successfully tested up to version 1.20.0 (the current release as of this writing). I don't think the gunzip module code changes much (if any), so it should patch cleanly against older / future versions.

**NOTE:** The gunzip module is not built by default, you must specify `--with-http_gunzip_module` when compiling nginx.

## Configuration Directives

### `gunzip_force`
- **syntax**: `gunzip_force on|off`
- **default**: `off`
- **context**: `http`, `server`, `location`

Enables or disables forced decompression of upstream content.

## Other Comments
It should also go without saying, you must enable the gunzip module itself `gunzip on;` for the gunzip force option to work.

This is best used used in a `location` context (i.e. where you specify your upstream proxy), especially if you are are also using `gzip_static`. Reason being if there is a static gziped file, instead of passing it directly this patch will decompress the file and recompress it (assuming you have `gzip on`) before sending to the client! 

Make sure you comment / remove `proxy_set_header Accept-Encoding "";` from your nginx configuration, otherwise your upstream server won't compress any content!

I set my upstream server to use gzip level 1 to maximize speed since nginx will recompress the content (typically with brotli).

## Original Author
I claim no rights to this work, I found the patch burried in the [Nginx Mail Archives](http://mailman.nginx.org/pipermail/nginx-devel/2013-January/003276.html "Nginx Mail Archives") from 2013 and simply updated it to patch cleanly on the current mainline release. The original author is Weibin Yao.
