# YDNS Bash Updater Script

This repository contains the bash updater script which can be used on *NIX-like environments to update dynamic hosts. It is recommended to use this script with cronjob to run periodically (preferrably every 15 minutes).

The script uses the YDNS API v1 (dyn-compatible).

ERROR: 

```
[2023/07/18T20:23:15] Current IP: 1111:aaaa:bbbb:abcd:12ab:b6ed:eeb6:9fa9
[2023/07/18T20:23:15] YDNS host update failed:  mihomeserver.ydns.eu (nosuitablerecordfound)
```

This version fixed the following error caused by getting the public IP in v6, but YDNS API requires IP v4. So, it's fixed.

## Installation

First, ensure that your host has [curl](http://curl.haxx.se) installed.

1. Check out the source code (this version of `updater.sh`)
2. Place it into desired place, in `/usr/bin`, for example, and make it executable (`chmod +x updater.sh`).
3. Edit the script and update the user and host information to fit your configuration. The 
4. Use this script for getting the public IP v4, instead IP v6.
5. Test the script as next is indicated.
6. Run the script (either by single call or set up a cronjob to run it periodically)

## Testing

Previously, you should create the directory `/path/to/ydns` and the blank file `update.log`, usually at `/home/<user>/ydns`:

```
$ mkdir /home/<user>/ydns
$ touch /home/<user>/ydns/update.log
```

If the [original YDNS script](https://github.com/ydns/bash-updater/blob/master/updater.sh) is tested use the following command: 

```
$ updater.sh -V -u <user_API> -p <secret_API> -H yourhost.ydns.eu >>/path/to/ydns/updater.log 2>&1
```

But, editing the `updater.log` file: 

```
$ vi /home/<user>/ydns/update.log
```

Your should identify the following error: 

```
[2023/07/18T20:34:34] Current IP: 2107:1067:1610:90aa:95cd:b6ed:eeb6:bbbb
[2023/07/18T20:34:35] YDNS host update failed:  yourhost.ydns.eu (nosuitablerecordfound)
```

because it gets the public IP v6, the public IP v4, instead. This error is fixed in my own version of the script, disabling getting the public IP v6 and enablig the public IP v4 in the script:

```
        # Retrieve current public IP address

        # @jzavalar jzavalar.t.me 07/19/2023
        # Disable public IP v6
        #current_ip=`curl --silent https://ydns.io/api/v1/ip`

        # Enable public IP v4
        current_ip=`curl -4 icanhazip.com`
```

So, the error was fixed when you test this script:

```
$ updater.sh -V -u <user_API> -p <secret_API> -H yourhost.ydns.eu >>/path/to/ydns/updater.log 2>&1
```

You should check it in the log file:

```
[2023/07/18T20:34:34] Current IP: 2107:1067:1610:90aa:95cd:b6ed:eeb6:bbbb
[2023/07/18T20:34:35] YDNS host update failed:  yourhost.ydns.eu (nosuitablerecordfound)
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
^M  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0^M100    15  100    15    0     0    134      0 --:--:-- --:--:-- --:--:--   135
[2023/07/19T10:49:24] Current IP: 123.145.167.189
[2023/07/19T10:49:25] YDNS host updated successfully:  yourhost.ydns.eu (123.145.167.189)
```

## Crontab Setup

To run this script every 15 minutes using `crontab`, add the following line to your crontab list:

```bash
*/15 * * * * updater.sh -V >>/path/to/ydns/updater.log 2>&1
```

Although this works on most all implementations of `crontab`, for more portability use this instead:

```bash
0,15,30,45 * * * * updater.sh -V >>/path/to/ydns/updater.log 2>&1
````

**NOTE:** To gain access to the crontab list to edit and add entries, execute `crontab -e` at the terminal

## Further notes

The code is licensed under the GNU Public License, version 3.

## Contribution

If you like to contribute useful changes to the script, simply fork the project, apply your changes and make a pull request.
