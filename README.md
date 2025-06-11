# skel

![Project Image](skel-banner.png)

## Install
0. clone the bluez repo
```
git clone https://github.com/pauloborges/bluez/tree/master
```
0. install prerequisites for bluez
```
sudo apt-get update && sudo apt-get install gcc libglib2.0-dev dbus dbus-x11 udev libdbus-1-dev libdbus-glib-1-dev libudev-dev libical-dev libreadline-dev libjson-c-dev
```
0. install latest version of `readline`, you should be able to find with `apt-cache search readline | grep readline`, example:
```
sudo apt-get install libreadline8 libreadline8-dev
```
0. apply patches to bluez
```
patch1:
diff --git a/profiles/audio/media.c b/profiles/audio/media.c
index 9c72b8dc4..ee11da8ab 100644
--- a/profiles/audio/media.c
+++ b/profiles/audio/media.c
@@ -1215,7 +1215,7 @@ static bool stop(void *user_data)
        return media_player_send(mp, "Stop");
 }

-static bool pause(void *user_data)
+static bool media_player_pause(void *user_data)
 {
        struct media_player *mp = user_data;

@@ -1264,7 +1264,7 @@ static struct avrcp_player_cb player_cb = {
        .set_volume = set_volume,
        .play = play,
        .stop = stop,
-       .pause = pause,
+       .pause = media_player_pause,
        .next = next,
        .previous = previous,
 };

patch2:
diff --git a/tools/rctest.c b/tools/rctest.c
index 77fa03c74..4dbfc79df 100644
--- a/tools/rctest.c
+++ b/tools/rctest.c
@@ -46,6 +46,7 @@
 #include <bluetooth/rfcomm.h>
 #include <bluetooth/sdp.h>
 #include <bluetooth/sdp_lib.h>
+#include <linux/sockios.h>

 /* Test modes */
 enum {
```
0. configure bluez and then install
```
cd bluez && \
  ./configure --prefix=/usr --mandir=/usr/share/man --sysconfdir=/etc --localstatedir=/var && \
  make && make install
```
