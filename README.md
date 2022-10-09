# libevdev - wrapper library for evdev input devices

libevdev is a wrapper library for evdev devices. it moves the common tasks when dealing with evdev devices into a library and provides a library interface to the callers, thus avoiding erroneous ioctls, etc.

The eventual goal is that libevdev wraps all ioctls available to evdev devices, thus making direct access unnecessary.


## Directory structure

```
README.md                   English description
README_zh.md                Chinese description
export_include/libevdev/    API Definition
include/                    Reference header file
libevdev/                   Encapsulation layer implementation
README.OpenSource           Open Source Description
```

## How OpenHarmony integrates libevdev
### 1.Header file import
```c
#define EVDEV_H
#include <libevdev/libevdev.h>
```
### 2.BUILD.gn add Reference
```c
public_deps += ["//third_party/libevdev:libevdev"]
```
### 3.Example of calling libevdev function
```c
// Below is a simple example that shows how libevdev could be used. This example opens a device, checks for relative axes and a left mouse button and if it finds them monitors the device to print the event.

// open a device
struct libevdev *dev = NULL;
int fd;
int rc = 1;
 
fd = open("/dev/input/event0", O_RDONLY|O_NONBLOCK);
rc = libevdev_new_from_fd(fd, &dev);
if (rc < 0) {
        fprintf(stderr, "Failed to init libevdev (%s)\n", strerror(-rc));
        exit(1);
}
// print the device info
printf("Input device name: \"%s\"\n", libevdev_get_name(dev));
printf("Input device ID: bus %#x vendor %#x product %#x\n",
       libevdev_get_id_bustype(dev),
       libevdev_get_id_vendor(dev),
       libevdev_get_id_product(dev));
// hecks for relative axes and a left mouse button
if (!libevdev_has_event_type(dev, EV_REL) ||
    !libevdev_has_event_code(dev, EV_KEY, BTN_LEFT)) {
        printf("This device does not look like a mouse\n");
        exit(1);
}
 
 // monitors the device to print the event
do {
        struct input_event ev;
        rc = libevdev_next_event(dev, LIBEVDEV_READ_FLAG_NORMAL, &ev);
        if (rc == 0)
                printf("Event: %s %s %d\n",
                       libevdev_event_type_get_name(ev.type),
                       libevdev_event_code_get_name(ev.type, ev.code),
                       ev.value);
} while (rc == 1 || rc == 0 || rc == -EAGAIN);
```

## libevdev Working with documents

Code gitlab https://gitlab.freedesktop.org/libevdev/libevdev.git

Official API Document  http://www.freedesktop.org/software/libevdev/doc/latest/

Detailed API definition https://www.freedesktop.org/software/libevdev/doc/latest/libevdev_8h.html

Patches https://gitlab.freedesktop.org/libevdev/libevdev/merge_requests/

Bugs https://gitlab.freedesktop.org/libevdev/libevdev/issues/

Questions http://lists.freedesktop.org/mailman/listinfo/input-tools


## License

See [LICENSE](MITLicense).

---

