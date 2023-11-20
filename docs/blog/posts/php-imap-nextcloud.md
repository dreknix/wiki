---
date: 2023-11-20
categories:
  - UNIX
  - Tools
---

# IMAP-based Authentication in PHP

When using IMAP-based user authentication in applications written in PHP (i.e.
Nextcloud, DokuWiki, etc.), debugging with a test script is essential. The
idea of using `curl` for authentication, is based on the external user
authentication plugin (see: [:fontawesome-brands-github: nextcloud/user_external](
https://github.com/nextcloud/user_external){:target="_blank"}) of Nextcloud.

<!-- more -->

``` php title="imap-test.php"
#!/usr/bin/env php
<?php
$version = phpversion();
print("Current PHP version: {$version}\n");

$url = 'imaps://imap.example.org:993';
$email = 'user@example.org';
$password = 'password'; # password must be in '...'

$ch = curl_init();

# see: https://www.php.net/manual/en/function.curl-setopt.php
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_USERPWD, "{$email}:{$password}");
curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 10);

# equal to: curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'CAPABILITY');
curl_setopt($ch, CURLOPT_CONNECT_ONLY, true);

# get more debug information
curl_setopt($ch, CURLOPT_CERTINFO, true);
curl_setopt($ch, CURLOPT_VERBOSE, true);

print("Connecting to: {$url}\n");
curl_exec($ch);

$errorcode = curl_errno($ch);

if ($errorcode === 0) {
  print("OK\n");
} else if ($errorcode === 67) {
  print("ERROR(" . $errorcode . "): Login denied! Wrong credentials?\n");
} else {
  print("ERROR(" . $errorcode . "): " . curl_error($ch) . "\n");
}
?>
```

!!! tip
    It is important to use the same PHP version for testing, as used for the
    application. To ensure the correct version Docker/Podman can be used.

    ```
    docker run -it --rm \
               --name php-test \
               -v "$PWD:/usr/src/test" \
               -w /usr/src/test php:8.2-cli \
               php imap-test.php
    ```
