# BeagleY-AI Edge AI Demo Using a USB Webcam

This page describes the first setup of the BeagleY-AI board with a USB webcam. It also documents practical difficulties, possible solutions, and notes for future users.

## Board Overview

BeagleY-AI is an open-source single-board computer developed for edge AI applications. It is based on the Texas Instruments AM67A vision processor and includes hardware for AI and vision processing. This allows the board to run lightweight AI tasks locally.

Figure 1 shows the front and back views of the BeagleY-AI board.

![Front and back view of BeagleY-AI board](images/BYAI_board_front_back.jpeg)

*Figure 1. Front and back views of the BeagleY-AI board.*

The following official BeagleY-AI documentation can be used as the main reference:

* [BeagleY-AI official documentation](https://docs.beagleboard.org/boards/beagley/ai/)
* [BeagleY-AI introduction](https://docs.beagleboard.org/boards/beagley/ai/01-introduction.html)
* [BeagleY-AI quick start](https://docs.beagleboard.org/boards/beagley/ai/02-quick-start.html)
* [Using Edge AI demo](https://docs.beagleboard.org/boards/beagley/ai/demos/using-edge-ai.html)

## Edge AI

Edge AI refers to artificial intelligence models running locally on a device, which reduces the need to send data to a cloud server for processing.

The basic idea is:

USB webcam → BeagleY-AI → AI model → detection result

This approach is useful because the board can analyse camera data directly and produce detection results locally. As a result, the system can respond faster and does not fully depend on cloud processing or a network connection.

## Hardware Used

The following hardware was used:

| Hardware                 | Purpose                            |
| ------------------------ | ---------------------------------- |
| BeagleY-AI board         | Main board for the demo            |
| 64 GB microSD card       | Storage used for flashing OS image |
| USB-C power supply       | Power supply for the board         |
| Logitech C250 USB webcam | Camera input                       |
| HDMI display             | Visual output for the Edge AI GUI  |
| USB mouse                | GUI control                        |
| USB keyboard             | Text input and terminal control    |
| Laptop                   | Setup computer                     |

## Setup Steps

1. Download and flash the correct operating system image to the microSD card.


   Useful links:

   * [Link to recommended OS image](https://www.beagleboard.org/distros/beagley-ai-ti-sdk-edge-ai-11-00-00-08-2025-09-06)
   * [Link to boot tool: bb-imager](https://www.beagleboard.org/bb-imager)
   * [How to flash OS using bb-imager](https://docs.beagleboard.org/boards/beagley/ai/02-quick-start.html#beagley-ai-bb-imager)

   The minimum required selections in bb-imager are the device name, operating system image, and target storage. Other custom options, such as username, password, network settings, and other settings, can be skipped if they are not needed.

   ![bb-imager write image summary](images/bb_imager_summary.jpeg)

   *Figure 2. bb-imager summary with minimum selections.*


2. Insert the microSD card into the BeagleY-AI board.


3. Connect the peripherals: power supply, HDMI display, USB keyboard, USB mouse, and USB webcam.


4. Boot the board and check that the system starts correctly.

   After booting, the board opens a terminal login screen. According to the official Edge AI documentation, the default login user is `root`, and no password is required.

   Basic system verification commands:

   ```bash
   uname -a
   ip a
   ```

5. Check whether the USB webcam is detected.

   Useful commands for checking the webcam:

   ```bash
   lsusb
   ls /dev/video*
   v4l2-ctl --list-devices
   ```

   The `lsusb` command is used to list USB devices connected to the system. Figure 3 shows the detected USB devices, including the Logitech Webcam C250.

   ![USB devices](images/webcam_detection.png)

   *Figure 3. USB devices detected by the system, including Logitech Webcam C250.*


6. Start a video stream using the detected webcam.

   After the webcam is connected, the available video devices are checked with:

   ```bash
   ls /dev/video*
   ```

   In this setup, the Logitech C250 webcam was available as `/dev/video2`. The following command was used to test the live camera stream:

   ```bash
   gst-launch-1.0 -v v4l2src device=/dev/video2 ! videoconvert ! autovideosink
   ```

   The command opens a live video stream on the display. This confirmed that the webcam was detected correctly and could be used as a video input device.

   ![Webcam stream test](images/video_stream.png)

   *Figure 4. Live video stream from the Logitech C250 webcam.*


7. Start the Edge AI graphical interface.

   The Edge AI graphical interface is started with the following command:

   ```bash
   edgeai-gui-app --platform linuxfb
   ```

   After running the command, the Edge AI gallery opens on the display. There are several demo options, including Image Classification, Object Detection, Semantic Segmentation, Multi Channel, and Custom.

   ![Edge AI gallery main screen](images/edge_ai_gallery_main.png)

   *Figure 5. Edge AI gallery main screen with available demo options.*


   The Image Classification demo was tested first. The demo showed example images and recognised classes on the screen.

   ![Edge AI image classification demo](images/image_classification.png)

   *Figure 6. Image Classification demo running in the Edge AI gallery.*


   **Note:** The top-right corner of the interface shows useful runtime information, such as FPS and board temperature. FPS means frames per second and indicates how fast the image stream is being processed. The temperature value can be used to monitor the board during testing, especially when running without active cooling.

## Difficulties and Limitations

During testing, the camera stream worked, but running the camera together with the object detection demo was not stable in this setup. The system froze during the test: the keyboard and mouse became inactive, and the board temperature shown in the Edge AI interface increased to around 80 °C.

Active cooling was not available during this test, so the demo was tested only as far as possible. For future testing, active cooling is recommended, especially when running camera-based AI demos for a longer time.

Useful command to stop the Edge AI GUI:

```bash
killall edgeai-gui-app
```

Useful command to check the board temperature from the terminal:

```bash
cat /sys/class/thermal/thermal_zone0/temp
```

The value is shown in millidegrees Celsius. For example:

```text
61465 = 61 °C
```

If the graphical interface is open but the terminal is needed, try switching to another terminal using:

```text
Ctrl + Alt + F1
Ctrl + Alt + F2
Ctrl + Alt + F3
```

If the system becomes fully unresponsive and keyboard or mouse input does not work, the last option is to power off the board. This should be used only when the system cannot be controlled in any other way.

**Note:** The official documentation also notes that USB on BeagleY-AI can be unreliable and may fail to initialize. This can affect USB devices such as a mouse or camera. If this happens, rebooting the board is recommended.

In this test, the keyboard and mouse became inactive during the attempt to run the webcam live-stream object detection demo. Because of this, the object detection test was not completed. The camera-based object detection demo could be repeated in the future by adding an active cooling system to the setup.