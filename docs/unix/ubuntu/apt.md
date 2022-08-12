# apt

## Keys

### List Keys

Get a list of all keys configured for `apt` on your system:

```console
$ sudo apt-key list
```

### Refresh Expired Keys

When you get an `EXPKEYSIG` error while `apt update`, you can update the expired
key with the following command:

```console
$ sudo apt-key adv --keyserver keys.gnupg.net --recv-keys AAAABBBBCCCCDDDD
````
