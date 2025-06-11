# skel

![Project Image](skel-banner.png)

## Install
1. clone the bluez repo
```
git clone https://github.com/pauloborges/bluez/tree/master
```
   
2. install prerequisites for bluez
```
sudo apt-get update && sudo apt-get install gcc libglib2.0-dev dbus dbus-x11 udev libdbus-1-dev libdbus-glib-1-dev libudev-dev libical-dev libreadline-dev libjson-c-dev
```
   
3. install latest version of `readline`, you should be able to find with `apt-cache search readline | grep readline`, example:
```
sudo apt-get install libreadline8 libreadline8-dev
```

4. apply patches to bluez:
5. patch1 (reference: https://github.com/LibtraceTeam/libtrace/issues/117#issuecomment-1024508895):
```
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
```

6. patch2 (reference: https://patches.linaro.org/project/linux-bluetooth/patch/20210912204839.3018089-1-fontaine.fabrice@gmail.com/):
```
diff --git a/tools/l2test.c b/tools/l2test.c
index 125532b83..b0dd969c5 100644
--- a/tools/l2test.c
+++ b/tools/l2test.c
@@ -26,7 +26,7 @@
 #ifdef HAVE_CONFIG_H
 #include <config.h>
 #endif
-
+#include <linux/sockios.h>
 #include <stdio.h>
 #include <errno.h>
 #include <ctype.h>

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

7. patch 3:
```
diff --git a/obexd/client/sync.c b/obexd/client/sync.c
index 1a4b482cf..a0855065a 100644
--- a/obexd/client/sync.c
+++ b/obexd/client/sync.c
@@ -221,7 +221,7 @@ static void sync_remove(struct obc_session *session)
        g_dbus_unregister_interface(conn, path, SYNC_INTERFACE);
 }

-static struct obc_driver sync = {
+static struct obc_driver sync2 = {
        .service = "SYNC",
        .uuid = SYNC_UUID,
        .target = OBEX_SYNC_UUID,
@@ -240,7 +240,7 @@ int sync_init(void)
        if (!conn)
                return -EIO;

-       err = obc_driver_register(&sync);
+       err = obc_driver_register(&sync2);
        if (err < 0) {
                dbus_connection_unref(conn);
                conn = NULL;
@@ -257,5 +257,5 @@ void sync_exit(void)
        dbus_connection_unref(conn);
        conn = NULL;

-       obc_driver_unregister(&sync);
+       obc_driver_unregister(&sync2);
 }
```
8. configure bluez and then install
```
cd bluez && \
  ./configure --prefix=/usr --mandir=/usr/share/man --sysconfdir=/etc --localstatedir=/var && \
  make && sudo make install # sudo because /usr/bin/bluetoothctl
```
