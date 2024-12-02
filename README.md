# RoomSpy Hardware

This folder contains the firmware code for **RoomSpy**, a room occupancy monitoring system using the **HC-SR501 Motion Sensor** and **ESP32** microcontroller. The system detects motion, determines room occupancy status, and communicates with a backend API over WiFi.

---

## **Hardware Requirements**
1. **ESP32** microcontroller
2. **HC-SR501 Motion Sensor**
3. LEDs for status indication:
   - Built-in LED (WiFi connection indicator)
   - External LED for error indication
4. Power supply and wires for connections

---

## **How It Works**
1. **Motion Detection**: The HC-SR501 sensor detects motion and triggers an interrupt.
2. **Room Occupancy Logic**: 
   - Tracks motion events in a rolling time window (default: 10 entries).
   - Considers the room "occupied" if at least one motion event occurs within the last minute.
3. **WiFi Communication**:
   - Connects to a specified WiFi network.
   - Sends room occupancy status updates to a backend API in JSON format.
4. **Status Indication**:
   - Built-in LED blinks during WiFi connection and stays solid when connected.
   - Error LED blinks if a failure occurs while sending data or reconnecting WiFi.

---

## **Setup Instructions**

### **Hardware Setup**
1. Connect the **HC-SR501** sensor to the ESP32:
   - VCC: 5V
   - GND: GND
   - OUT: GPIO 34
2. Connect an external error LED to GPIO 12.
3. Power the ESP32.

### **Firmware Configuration**
1. Update the WiFi credentials in the code:
   ```cpp
   const char* ssid = "[WIFI SSID]";         // Your WiFi SSID
   const char* password = "[WIFI PASSWORD]"; // Your WiFi password
   ```
2. Replace the backend API URL:
   ```cpp
   const char* serverUrl = "https://[BACKEND API URL]";
   ```
3. Customize the room name if needed:
   ```cpp
   const char* RoomName = "Damietta";
   ```

### **Uploading Code**
1. Open the code in the Arduino IDE or your preferred development environment.
2. Select the correct **board** (ESP32) and **port**.
3. Compile and upload the code to the ESP32.

---

## **Operation**
- The system automatically connects to WiFi on boot.
- When motion is detected, the system:
  - Updates the rolling motion timestamps.
  - Checks and updates the room's occupancy status.
  - Sends the status to the backend API if there's a change or periodically (every minute).
- The built-in LED indicates WiFi connection status.
- The error LED blinks if communication with the backend API fails.

---

## **Code Highlights**

### Motion Detection
```cpp
void IRAM_ATTR handleMotion() {
  motionDetected = true;
}
```

### Room Status Update
```cpp
void updateRoomStatus() {
  bool occupied = motionCount >= 1;  // Detects occupancy based on motion within the window
  if (occupied != roomOccupied) {
    roomOccupied = occupied;
    sendStatusData(roomOccupied);
  }
}
```

### Sending Data to API
```cpp
void sendStatusData(bool occupied) {
  String payload = "{\"RoomName\": \"" + String(RoomName) + "\", \"occupied\": " + String(occupied ? "true" : "false") + ", \"timestamp\": " + String(millis()) + "}";
  // Sends the data using HTTP POST
}
```

---

## **Customization**
- **Rolling Window Size**: Adjust `motionWindowSize` to modify the number of motion events tracked.
- **Occupancy Threshold**: Change the logic in `updateRoomStatus` to define custom thresholds for occupancy.

---

## **Troubleshooting**
1. **No WiFi Connection**:
   - Verify the SSID and password.
   - Ensure the WiFi network is active.
2. **No API Updates**:
   - Check the `serverUrl` value.
   - Ensure the backend API is reachable.
3. **Unexpected Behavior**:
   - Check the serial monitor for debugging messages.
   - Verify the motion sensor's wiring and orientation.

---

## **License**
This project is open-source. Feel free to modify and redistribute as needed. 

--- 

Enjoy monitoring your room with **RoomSpy**! 🚀
