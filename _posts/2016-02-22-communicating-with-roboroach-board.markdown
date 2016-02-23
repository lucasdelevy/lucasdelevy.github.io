---
layout: post
title:  "Communicating with RoboRoach board"
date:   2016-02-22 23:16:07 -0300
categories: roboroach tutorial
---

Hello! Thanks for passing by!

I recently started a project for a discipline with the sole purpose of **controlling a cockroach’s movement** using neurostimulation and a motion capture device. I went after **BackyardBrain**’s RoboRoach since it is a reference in terms of hardware **lightness** and project **robustness**. Also they have some really nice video tutorials and resources on their [**website**](https://backyardbrains.com/) (be sure to check them!)

Since I am using [**Polaris Spectra**](http://www.ndigital.com/medical/products/polaris-family/)‘s motion capture setup, I thought it would be easier to implement it all using **Python** (and turns out it was). I tried to use it in Windows or Mac, but ran into several problems since RoboRoach’s communication protocol is based on **GATT** (Generic Attribute Profile) — because of the module used, [**Bluetooth 4.0 (Low Energy)**](http://www.blueradios.com/hardware_LE4.0-S2.htm), which is pretty recent, meaning few people implemented stable libraries on it. I had no choice but to work on **Linux** (Ubuntu 14.0) using a **Bluetooth 4.0 dongle** (something like [**this**](http://www.amazon.com/Kinivo-BTD-400-Bluetooth-4-0-adapter/dp/B007Q45EF4/ref=sr_1_1?ie=UTF8&qid=1433342734&sr=8-1&keywords=bluetooth+4.0+dongle)). Well, let’s cut to the chase and start this tutorial:

---

# We’ll first make sure everything is installed

Python’s `gattlib` is the real star of this tutorial. In order to install it, we will follow [**their own suggested procedure**](https://bitbucket.org/OscarAcena/pygattlib). First type this into the terminal:

```
$ sudo nano /etc/apt/source.list
```

Then add `deb http://babel.esi.uclm.es/arco sid main` to the end of the file. Finally:

```
$ sudo apt-get update
```

<div class = "pinkBorder" markdown="1">
# In case this did not work...

If the error was:

```
...
Fetched 1'931 kB in 4s (448 kB/s)
Reading package lists... Done
W: GPG error: http://babel.esi.uclm.es sid InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY E50C***...
```
Try this:
```
$ wget -qO - http://babel.esi.uclm.es/arco/key.asc | sudo apt-key add -
```

And thus I hope it finally worked.
</div>

Now we can install it:

```
$ sudo apt-get install python3-gattlib python-gattlib
```

Notice I am installing it in `python3` as well as in `python` — just in case.

---

# Let’s try some examples!

You can use the following codes in order to test it:

```
$ sudo python discovery.py
```

```python
from gattlib import DiscoveryService
 
service = DiscoveryService('hci0')
devices = service.discover(2)
 
for address, name in devices.items():
    print('name: {}, address: {}'.format(name, address))
``` 

Or:

```
$ sudo python read_data_assync.py
```

```python
from gattlib import GATTRequester, GATTResponse
import time
 
req = GATTRequester('90:59:AF:14:08:E8')
response = GATTResponse()
 
req.read_by_handle_async(0x15, response)
while not response.received():
    time.sleep(0.1)
 
steps = response.received()[0]
print steps.encode('hex')
```

---

# Exploring RoboRoach!

If you are familiar with the **GATT protocol**, you probably now about **services**, **characteristics**, **attributes** and so on. (If you don’t, it doesn’t hurt to learn a bit about it: [**here**](https://developer.android.com/reference/android/bluetooth/BluetoothGatt.html) and [**here**](https://learn.adafruit.com/introduction-to-bluetooth-low-energy/gatt) will probably be enough.) So what we will do now is to **explore that organization within RoboRoach**. We will use `hcitool`, `hciconfig` and `gatttool`, so we have to first make sure all is installed.

```
$ hcitool -v
```
```
$ hciconfig -v
```
```
$ gattool -v
```

At this point, what we will do is follow something like [**this routine**](http://blog.firszt.eu/index.php?post/2015/02/08/Bluetooth-BLE%2C-gatttool-and-%28almost%29-all-those-numbers-....-explained) with data from [**RoboRoach’s GATT profile**](https://github.com/BackyardBrains/RoboRoach/blob/22bab8ef06a3f0a31e7d5b8e7aa8298ed8b0e90d/Firmware/TI/Source/roboroach_profile.h) designed in their GitHub’s Repository.

After **plugging in your dongle** (which you probably have done already), find out its **name**:

```
$ hciconfig
```

You will see something like that:

```
hci0: Type: BR/EDR Bus: USB
BD Address: 5C:F3:70:68:BF:97 ACL MTU: 1021:8 SCO MTU: 64:1
UP RUNNING PSCAN
RX bytes:6842 acl:142 sco:0 events:380 errors:0
TX bytes:5352 acl:163 sco:0 commands:153 errors:0
```

That is great, seems like my dongle is being called `hci0`. Let’s make sure to remember this. Then we will do:

```
$ sudo hciconfig hci0 up
```
```
$ sudo hcitool -i hci0 lescan
```

And, if we had not found out our RoboRoach’s **MAC address** yet (as we could have, from our `discovery.py`), now it is definitely here:

```
LE Scan ...
90:59:AF:14:08:E8 (unknown)
90:59:AF:14:08:E8 RoboRoach
```

You can ctrl-c as soon as you see this so that the sweeping stops. And there it is — *our board’s MAC address*. Let’s write it down as well.

We will now use `gattool` so we can connect to the board using the terminal.

```
$ sudo gatttool -i hci0 -b 90:59:AF:14:08:E8 -I
```

It is interesting here to check `$ gattool -h` to understand what each flag means.

We are almost inside RoboRoach, so let’s connect!

```
[ ][90:59:AF:14:08:E8][LE]> connect
```

Do not forget to press the board’s button so it is visible!

At last, we are connected! Let’s list all the services available:

```
[CON][90:59:AF:14:08:E8][LE]: primary
```

Something like this will be shown:

<pre>
[CON][90:59:AF:14:08:E8][LE]:
attr handle: 0x0001, end grp handle: 0x000b uuid: 00001800-0000-1000-8000-00805f9b34fb
attr handle: 0x000c, end grp handle: 0x000f uuid: 00001801-0000-1000-8000-00805f9b34fb
attr handle: 0x0010, end grp handle: 0x0022 uuid: 0000180a-0000-1000-8000-00805f9b34fb
attr handle: 0x0023, end grp handle: 0x0027 uuid: 0000180f-0000-1000-8000-00805f9b34fb
attr handle: <b>0x0028</b>, end grp handle: 0xffff uuid: <b>0000b2b0</b>-0000-1000-8000-00805f9b34fb
</pre>

So here we have all **services**, **UUIDs** and the respective **handlers**. From RoboRoach’s repository, we find:

```c
#define ROBOROACH_SERV_UUID 0xB2B0;
```

But, wait, that’s **the last UUID listed**! We will read its characteristics, then.

```
[CON][90:59:AF:14:08:E8][LE]> characteristics 0x0028
```

We do not use the UUID directly, but its handler!

We will read:

```
[CON][90:59:AF:14:08:E8][LE]>
handle: 0x0029, char properties: 0x0a, char value handle: 0x002a, uuid: 0000b2b1-0000-1000-8000-00805f9b34fb
handle: 0x002c, char properties: 0x0a, char value handle: 0x002d, uuid: 0000b2b2-0000-1000-8000-00805f9b34fb
handle: 0x002f, char properties: 0x0a, char value handle: 0x0030, uuid: 0000b2b3-0000-1000-8000-00805f9b34fb
handle: 0x0032, char properties: 0x0a, char value handle: 0x0033, uuid: 0000b2b4-0000-1000-8000-00805f9b34fb
handle: 0x0035, char properties: 0x08, char value handle: 0x0036, uuid: 0000b2b5-0000-1000-8000-00805f9b34fb
handle: 0x0038, char properties: 0x08, char value handle: 0x0039, uuid: 0000b2b6-0000-1000-8000-00805f9b34fb
handle: 0x003b, char properties: 0x0a, char value handle: 0x003c, uuid: 0000b2b7-0000-1000-8000-00805f9b34fb
handle: 0x003e, char properties: 0x0a, char value handle: 0x003f, uuid: 0000b2b8-0000-1000-8000-00805f9b34fb
handle: 0x0041, char properties: 0x0a, char value handle: 0x0042, uuid: 0000b2b9-0000-1000-8000-00805f9b34fb
handle: 0x0044, char properties: 0x0a, char value handle: 0x0045, uuid: 0000b2ba-0000-1000-8000-00805f9b34fb
handle: 0x0047, char properties: 0x0a, char value handle: 0x0048, uuid: 0000b2bb-0000-1000-8000-00805f9b34fb
handle: 0x004a, char properties: 0x0a, char value handle: 0x004b, uuid: 0000b2bc-0000-1000-8000-00805f9b34fb
handle: 0x004d, char properties: 0x0a, char value handle: 0x004e, uuid: 0000b2bd-0000-1000-8000-00805f9b34fb
```

Wow, that is a lot of UUIDs! Well, each one of this represent a **characteristic**, such as *turn right*, *turn left*, *frequency* or *pulse width*. Let’s try to find which is *turn right*, then. From the repository:

<pre>
#define ROBOROACH_CHAR_STIMULATE_LEFT_UUID <b>0xB2B5</b>
</pre>

Remember that, in order to turn right, you must stimulate the left electrode.

There it is, the UUID we were looking for! Now we just have to **write on the characteristic’s attribute** to make some effect on the system. A simple `1` might be enough.

```
[CON][90:59:AF:14:08:E8][LE]> char-write-cmd 0x0036 0x1
```

In other words, write `0x01` onto the characteristic whose handle is `0x0036`.

You will notice **the left electrode just blinked**!
Great, we are finally there, now we can mess with all characteristics whenever we want and need to!

---

# But… how about Python?

Well, once this is all settled, the Python code is pretty simple:

```python
from gattlib import GATTRequester
import time
 
req = GATTRequester("90:59:AF:14:08:E8")
 
req.write_by_handle(0x0036, str(bytearray([1]))) # left
time.sleep(1);
req.write_by_handle(0x0039, str(bytearray([1]))) # right
```

---

Hope that helped! :-)