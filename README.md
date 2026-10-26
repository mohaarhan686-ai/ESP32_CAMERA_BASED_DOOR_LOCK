 Smart Door Lock
A Wi-Fi-controlled smart door-lock prototype built around the AI Thinker ESP32-CAM and an SG90 servo motor. It creates its own Wi-Fi access point, provides a mobile-friendly browser dashboard, streams live camera video, and offers remote lock/unlock and light controls.
Project status: Prototype / educational project. Review the security and hardware notes below before connecting it to a real door.

Features
- Standalone Wi-Fi access point: No router or internet connection is required.
- Live camera feed: MJPEG stream served separately on port 81.
- Browser dashboard: Open Gate, Keep Locked, Light, Snapshot, gate state, uptime, connected-client count, and activity log.
- Servo control: Moves the servo between configured locked and unlocked angles.
- Automatic re-lock: Relocks after the configured delay (5 seconds by default).
- Status indicators: Uses the configured status LED and camera flash/light output.
- Responsive interface: Designed for phone and desktop browsers.
Hardware Required
- AI Thinker ESP32-CAM
- SG90 servo motor (or a compatible servo after checking current and signal requirements)
- Stable regulated power supply suitable for the ESP32-CAM and servo
- USB-to-TTL programmer for uploading
- Jumper wires and a suitable mechanical lock linkage
- Optional external light, if using the flash output for that purpose
Pin Configuration
Function	GPIO	Notes
Servo signal	GPIO 2	Defined by SERVO_PIN; check boot behaviour and your board wiring
Flash / light output	GPIO 4	Also associated with the ESP32-CAM's onboard flash LED
Status LED	GPIO 33	Defined by STATUS_LED_PIN; verify availability on your exact board
Camera pins	See source	Camera pin mapping is configured for the AI Thinker ESP32-CAM


The camera pin mapping is defined in the sketch under CAMERA PINS. Do not assign those camera pins to other peripherals.
Default Configuration
Setting	Default
Wi-Fi network (SSID)	SmartDoorLock
Wi-Fi password	12345678
Access point IP	Usually 192.168.4.1
Dashboard port	80
MJPEG stream port	81
Servo locked angle	0°
Servo unlocked angle	90°
Automatic re-lock delay	5000 ms
Camera frame size	VGA (640 × 480) when PSRAM is available
Serial monitor baud rate	115200


You can adjust these values in the CONFIGURATION section of Arhan_Smart_Door_Lock.ino.
How It Works
1. The ESP32-CAM initializes the servo PWM output and camera.
2. It starts a Wi-Fi access point using the configured SSID and password.
3. The dashboard and control endpoints run on HTTP port 80.
4. The live MJPEG stream runs separately on port 81 so video streaming is less likely to block dashboard controls.
5. When Open Gate is pressed, the servo moves to OPEN_ANGLE and the auto re-lock timer starts.
6. When Keep Locked is pressed—or the timer expires—the servo moves to LOCK_ANGLE.
7. The dashboard periodically requests status information and displays the current gate state and recent events.
Upload Instructions
1. Install the ESP32 board package in Arduino IDE.
2. Select AI Thinker ESP32-CAM as the board.
3. Set the upload speed to 115200.
4. Connect the USB-to-TTL programmer correctly and provide stable power.
5. For upload mode, connect GPIO0 to GND, reset the board, and upload the sketch.
6. After upload, disconnect GPIO0 from GND and reset the board to boot normally.
7. Open Serial Monitor at 115200 baud to check startup messages and the access point IP.
Follow the pinout and power requirements for your exact ESP32-CAM module and programmer. Avoid powering the servo from a weak 3.3 V pin; use a suitable regulated supply and connect grounds together.
How to Use
1. Power on the ESP32-CAM.
2. On your phone or computer, connect to Wi-Fi SmartDoorLock.
3. Enter the configured password 12345678.
4. Open http://192.168.4.1 in a browser.
5. Use the dashboard controls:
   - Open Gate — moves the servo to the unlocked angle.
   - Keep Locked — moves the servo to the locked angle.
   - Light — toggles the configured flash/light output.
   - Snapshot — opens a still image from the camera.
6. The live video appears in the dashboard. The interface also shows gate state, uptime, connected viewers, and activity events.
If the access point uses a different IP address, use the address printed in Serial Monitor.
HTTP Endpoints
Endpoint	Purpose
/	Dashboard
/status	Current state and event log as JSON
/open	Unlock command
/lock	Lock command
/flash	Toggle light output
/snapshot	Capture a still JPEG image
http://192.168.4.1:81/stream	MJPEG live stream


Customization
Edit the configuration constants near the top of the sketch:
- AP_SSID and AP_PASSWORD — access point credentials.
- SERVO_PIN — servo signal pin.
- OPEN_ANGLE and LOCK_ANGLE — servo positions; calibrate carefully for your mechanism.
- SERVO_MIN_US and SERVO_MAX_US — servo pulse-width range.
- AUTO_RELOCK_MS — automatic re-lock delay; set to 0 to disable the timer.
- CAM_QUALITY and CAM_FRAMESIZE — image quality and resolution.
- CAM_VFLIP and CAM_HMIRROR — correct the camera orientation.
Test servo angles without connecting the mechanism to a real lock, and make sure the linkage cannot jam or damage the servo.
Safety and Security Notes
- Change the default Wi-Fi password before any real-world use.
- The current sketch does not implement user authentication, HTTPS, or authorization for the control endpoints. Anyone who can join the access point may be able to operate the lock.
- Do not expose the dashboard or endpoints to the public internet.
- The automatic re-lock is software-controlled; it is not a substitute for a mechanically secure lock or a fail-safe design.
- Test the mechanism with the door open and keep a manual way to unlock it.
- GPIO assignments and onboard LED behaviour can vary by ESP32-CAM board revision.
- The sketch disables the brownout detector. This can hide power-supply problems; use a stable supply and consider restoring brownout protection during development.
- The event log is held in RAM and is not persistent across resets.
- The source uses ESP32 camera and LEDC APIs; API compatibility can vary between ESP32 Arduino core versions. Compile and test with your installed board package.
Troubleshooting
- Camera initialization fails: Confirm the board selection, camera module, power supply, and camera pin mapping.
- Dashboard does not load: Ensure your device is connected to the ESP32-CAM access point and open http://192.168.4.1.
- Video does not appear: Try reducing CAM_FRAMESIZE or adjusting CAM_QUALITY; check port 81 and power stability.
- Servo jitters or resets the board: Use a suitable regulated servo supply, connect grounds together, and check for mechanical binding.
- Servo moves in the wrong direction: Calibrate OPEN_ANGLE and LOCK_ANGLE for your mechanism.
- Upload fails: Verify the USB-to-TTL wiring, select the correct board, enter upload mode using GPIO0, and reset after uploading.
Project Structure
Arhan-Smart-Door-Lock/
├── Arhan_Smart_Door_Lock.ino
└── README.md
Author
Arhan

Built as an embedded systems and IoT prototype combining ESP32-CAM, web-based control, live video streaming, and servo actuation.
