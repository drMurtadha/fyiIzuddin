To program an Arduino-based people counting system for your **IoT-enabled Visitor Management System**, we’ll use sensors such as **infrared (IR) break beam sensors** or **PIR motion sensors** to detect the entry and exit of visitors. Below is a step-by-step guide on how to program an Arduino for people counting using these sensors.

### **1. Hardware Setup:**
For this example, let’s assume you’re using **IR break beam sensors** or **PIR motion sensors** to detect when people pass through an entry or exit point.

- **IR Break Beam Sensor:** It has two parts: an emitter and a receiver. When someone passes through the beam, it breaks the line of sight, which can be used to trigger a count.
- **PIR Sensor:** It detects movement and can also be used for this purpose but with less precision than IR break beam sensors for counting.

#### **Hardware Components:**
- **Arduino Board** (e.g., Arduino Uno)
- **IR Break Beam Sensor** (or PIR Sensor) for entry detection
- **IR Break Beam Sensor** (or PIR Sensor) for exit detection
- **Resistors** (if required for specific sensors)
- **Jumper wires**

### **2. Wiring the Sensors to Arduino:**
- **Break Beam Sensors:**
  - Connect the power pin (VCC) to the **5V** pin on the Arduino.
  - Connect the ground pin (GND) to the **GND** pin on the Arduino.
  - Connect the signal (output) pin of the **entry sensor** to **digital pin 2** on the Arduino.
  - Connect the signal (output) pin of the **exit sensor** to **digital pin 3** on the Arduino.
  
- **PIR Sensors (alternative option):**
  - Similar wiring but using PIR sensors connected to digital pins 2 (for entry) and 3 (for exit).

### **3. Arduino Code:**
Here’s a simple Arduino sketch to count people entering and exiting through the sensors.

```cpp
// Pin Definitions
const int entrySensorPin = 2;  // Pin for Entry Sensor
const int exitSensorPin = 3;   // Pin for Exit Sensor

// Variables to keep track of visitors
int peopleCount = 0;

void setup() {
  // Initialize serial communication
  Serial.begin(9600);

  // Set sensor pins as inputs
  pinMode(entrySensorPin, INPUT);
  pinMode(exitSensorPin, INPUT);

  // Initialize the people count display
  Serial.println("Visitor Counter Initialized");
}

void loop() {
  // Read the state of the entry and exit sensors
  int entryState = digitalRead(entrySensorPin);
  int exitState = digitalRead(exitSensorPin);

  // If the entry sensor is triggered, increment the people count
  if (entryState == HIGH) {
    peopleCount++;
    Serial.println("Visitor entered");
    Serial.print("People Count: ");
    Serial.println(peopleCount);
    delay(1000); // Small delay to avoid double counting
  }

  // If the exit sensor is triggered, decrement the people count
  if (exitState == HIGH) {
    if (peopleCount > 0) {
      peopleCount--;
      Serial.println("Visitor exited");
      Serial.print("People Count: ");
      Serial.println(peopleCount);
    }
    delay(1000); // Small delay to avoid double counting
  }

  // Optionally, send data to a server or cloud via serial, Wi-Fi, or another protocol
}
```

### **4. Code Explanation:**
- **Sensor Inputs:** The program reads the state of the sensors (`entrySensorPin` and `exitSensorPin`) using the `digitalRead()` function.
- **People Count:** If the entry sensor is triggered (person breaks the beam or PIR detects motion), the `peopleCount` is incremented. If the exit sensor is triggered, the `peopleCount` is decremented, but it ensures the count never goes below zero.
- **Delay:** A delay of 1 second (`delay(1000)`) is added to avoid multiple counts for the same person.
- **Serial Monitor:** The visitor count is printed to the **Serial Monitor**, which can later be sent to a web server or cloud service for further processing.

### **5. Next Steps:**
1. **Test the System:** Connect the Arduino to your computer and upload the code. Open the **Serial Monitor** in the Arduino IDE to see the people count in real time.
2. **Real-Time Data Transmission:** You can modify the code to send this data to a server or cloud via an internet module (e.g., **ESP8266 Wi-Fi Module**) or an Ethernet shield, allowing the data to be displayed on a web dashboard.

---

### **If Using ESP8266 for Wi-Fi (Optional):**
If you want the Arduino to send data to a web server in real-time, you could integrate a **Wi-Fi module** like **ESP8266** and modify the sketch as follows:

1. **Add the ESP8266 Library** to your Arduino IDE.
2. Modify the code to connect to a Wi-Fi network and send `peopleCount` to a server via HTTP.

Example Code Snippet:
```cpp
#include <ESP8266WiFi.h>

// Wi-Fi credentials
const char* ssid = "Your_SSID";
const char* password = "Your_PASSWORD";

void setup() {
  // Initialize Serial
  Serial.begin(9600);

  // Connect to Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to Wi-Fi...");
  }
  Serial.println("Connected to Wi-Fi");

  // Your existing code for people counting...
}

void loop() {
  // When a visitor is counted, send the data to a server
  if (peopleCountChanged) {
    sendDataToServer(peopleCount);
  }

  // Rest of the loop...
}

// Function to send data to server
void sendDataToServer(int count) {
  WiFiClient client;
  const char* host = "your-server.com";
  if (client.connect(host, 80)) {
    client.print(String("GET /updateCount?count=") + count + " HTTP/1.1\r\n" +
                 "Host: " + host + "\r\n" +
                 "Connection: close\r\n\r\n");
    delay(10);
  }
  client.stop();
}
```

With this setup, you’ll have a fully functioning **IoT-based people counting system** that can send data to a web application and provide real-time visitor information for your heritage site’s management dashboard.
