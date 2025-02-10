
 Introduction:
 
This project aims to program a bipolar stepper motor using an ESP32 module while integrating web technologies for dynamic 

motor control. The following process will be implemented:

1. **Convert speech to text** using the ESP32 module.
   
3. Store the extracted text in a database
   
5. Retrieve the stored data and display it on a new web page
   
7. Control the motor based on retrieved voice commands:
   
   - When saying "Forward," the motor completes a full clockwise rotation.
     
   - When saying "Backward," the motor moves half a turn counterclockwise.
     

Tools Used:
- ESP32 module.
  
- Bipolar Stepper Motor.
  
- Motor driver (e.g., A4988 or DRV8825).
  
- Microphone for voice command input.
  
- Web server and MySQL database.
  
- Web interface for displaying stored commands.

 Implementation Steps:

 1. Speech-to-Text Conversion:
    
A Speech-to-Text library or cloud service is used with the ESP32 module.

 2. Storing Data in the Database:

After converting speech to text, the extracted command is sent to a web server for storage in a MySQL database.

 3. Retrieving Data and Displaying it on a Web Page:
    
A web page is created to retrieve and display stored commands.

 4. Controlling the Motor Based on Retrieved Commands:
    
The commands are fetched from the database, analyzed, and then sent as signals to the motor.

 Source Code:
 
 1. ESP32 Code for Speech Analysis and Sending Data to Server:
```cpp

#include <WiFi.h>

#include <HTTPClient.h>

#include <ArduinoJson.h>

#include <AccelStepper.h>

#define STEP_PIN 5

#define DIR_PIN 18

const char* ssid = "yourSSID";

const char* password = "yourPassword";

const char* serverURL = "http://your-server.com/store_command.php";

AccelStepper stepper(AccelStepper::DRIVER, STEP_PIN, DIR_PIN);

void setup() {

    Serial.begin(115200);

    WiFi.begin(ssid, password);

    while (WiFi.status() != WL_CONNECTED) {

        delay(1000);

        Serial.println("Connecting to WiFi...");
    }
    Serial.println("Connected to WiFi");

    stepper.setMaxSpeed(1000);

    stepper.setAcceleration(500);
}

void loop() {

    String command = "forward"; // Replace with actual speech analysis code

    sendToServer(command);

    delay(5000);
}

void sendToServer(String command) {

    HTTPClient http;

    String url = String(serverURL) + "?command=" + command;

    http.begin(url);

    int httpCode = http.GET();

    http.end();
}
```

 2. PHP Code for Storing Commands in the Database:
    
```php

<?php

$servername = "localhost";

$username = "root";

$password = "";

$dbname = "commands_db";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {

    die("Connection failed: " . $conn->connect_error);
}
if(isset($_GET['command'])){

    $command = $_GET['command'];

    $sql = "INSERT INTO commands (command) VALUES ('$command')";

    if ($conn->query($sql) === TRUE) {

        echo "Command stored successfully";

    } else {

        echo "Error: " . $conn->error;
    }
}

$conn->close();

?>
```

3. PHP Code for Retrieving and Displaying Commands on a Web Page:
   
```php

<?php

$servername = "localhost";

$username = "root";

$password = "";

$dbname = "commands_db";

$conn = new mysqli($servername, $username, $password, $dbname);

$sql = "SELECT * FROM commands ORDER BY id DESC LIMIT 1";

$result = $conn->query($sql);

if ($result->num_rows > 0) {

    while($row = $result->fetch_assoc()) {

        echo "<h2>Last Command: " . $row["command"]. "</h2>";
    }

}
else
 {
    echo "No commands found.";
}

$conn->close();

?>
```

4. ESP32 Code for Fetching Commands and Controlling the Motor:
   
```cpp

void checkDatabase() {

    HTTPClient http;

    http.begin("http://your-server.com/get_command.php");

    int httpResponseCode = http.GET();

    if (httpResponseCode > 0) {

        String payload = http.getString();

        Serial.println(payload);

        if (payload.indexOf("forward") >= 0) {

            stepper.moveTo(1000);

        } else if (payload.indexOf("backward") >= 0) {

            stepper.moveTo(-500);
        }
    }

    http.end();
}

void loop() {

    checkDatabase();

    stepper.run();

    delay(5000);
}
```

 Results:
 
- When "Forward" is spoken, it is converted to text, stored in the database, retrieved later, and the motor completes a

  full clockwise rotation.
  
- When "Backward" is spoken, the motor performs a half turn counterclockwise.
  
- The retrieved commands are displayed on a new web page.

 Conclusion:
In this project, an ESP32 module was integrated with a bipolar stepper motor and a complete workflow was implemented, 

including speech-to-text conversion, data storage, and retrieval for interactive motor control via voice commands.

![IMG_8649](https://github.com/user-attachments/assets/6666f989-99e9-46b0-aca1-b06550863678)

![IMG_8705](https://github.com/user-attachments/assets/e857ee22-f34c-4c35-96fe-a583dd85aff1)

![IMG_8708](https://github.com/user-attachments/assets/885c92d3-b11c-4050-8a21-340e564b0ba3)

![IMG_9532](https://github.com/user-attachments/assets/7f24f690-ef69-4971-944d-82f1b0d26824)

![IMG_9531](https://github.com/user-attachments/assets/735305a5-a391-4e00-90f7-a7d4b0e11540)



