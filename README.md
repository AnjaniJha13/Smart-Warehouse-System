# Smart-Warehouse-System
#include <DHT.h>  // Include DHT library for temperature and humidity sensor

// Pin definitions for ESP8266
#define DHTPIN D4             // Pin connected to DHT22 data pin (GPIO2)
#define DHTTYPE DHT22         // DHT 22 (AM2302) type
#define MOISTURE_PIN A0       // Analog pin connected to the moisture sensor
#define PIR_PIN D3            // Pin connected to PIR motion sensor (GPIO0)
#define VOLTAGE_PIN A0        // Analog pin connected to solar panel voltage sensor (ESP8266 has only one analog pin A0)
#define RAIN_SENSOR D2        // Pin connected to rain sensor (GPIO4)

DHT dht(DHTPIN, DHTTYPE);  // Initialize DHT sensor

void setup() {
  Serial.begin(9600);  // Start serial communication
  dht.begin();         // Initialize DHT sensor
  pinMode(PIR_PIN, INPUT);  // Set PIR sensor pin as input
  pinMode(RAIN_SENSOR, OUTPUT);  // Set alarm pin as output
}

void loop() {
  // Read environmental data
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();
  int moistureValue = analogRead(MOISTURE_PIN);

  // Convert analog moisture value to percentage
  int moisturePercent = map(moistureValue, 1023, 0, 0, 100);

  // Read motion sensor (PIR)
  bool motionDetected = digitalRead(PIR_PIN);

  // Read solar panel voltage (ESP8266 analog input range is 0-1V)
  int voltageValue = analogRead(VOLTAGE_PIN);
  float solarVoltage = voltageValue * (3.3 / 1023.0) * (11);  // Assuming a voltage divider

  // Print data to serial monitor
  Serial.print("Humidity: ");
  Serial.print(humidity);
  Serial.println(" %");

  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" *C");

  Serial.print("Moisture: ");
  Serial.print(moisturePercent);
  Serial.println(" %");

  Serial.print("Solar Voltage: ");
  Serial.print(solarVoltage);
  Serial.println(" V");

  // Motion detection and alarm
  if (motionDetected) {
    Serial.println("Motion Detected!");
    digitalWrite(RAIN_SENSOR, HIGH);  // Activate alarm
  } else {
    digitalWrite(RAIN_SENSOR, LOW);  // Deactivate alarm
  }

  // Delay for a bit before next loop
  delay(2000);  // Adjust as needed
}
