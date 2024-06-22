 

# esp32_mqtt

<a href="https://app.codecov.io/analytics/github/atulkumar6556" target="_blank"><img src="https://codecov.io/gh/rrousselgit/riverpod/branch/master/graph/badge.svg" alt="ATUL KUMAR" style="height: 20px !important;width: 100px !important;" ></a>


** replace your ssid , wifipswd, mqttserver_address & qos 
** 

      
    #include <WiFi.h>           // to enable wifi
    #include <PubSubClient.h>   // mqtt lib
    #include "ArduinoJson.h"    // lib for serialize/deserialize json msg
    //#include "konf.h"
    
    // Replace the next variables with your SSID/Password combination
    const char* ssid = "XYZ_atul";
    const char* password = "atul";
    const char* mqtt_server = "192.168.xx.xxx";
    int qos=0;                  // default on 0 set[0,1,2]
    
    WiFiClient espClient;
    PubSubClient client(espClient);
    long lastMsg = 0;
    char msg[50];
    int value = 0;
    
    
    
    // LED Pin
    const int ledPin = 4;
    
    void setup() {
      Serial.begin(115200);
      setup_wifi();
      client.setServer(mqtt_server, 1883);
      client.setCallback(callback);
      client.subscribe("topic", qos);
    
    
    }
    
    void setup_wifi() {
      delay(10);
      // We start by connecting to a WiFi network
      Serial.println();
      Serial.print("Connecting to ");
      Serial.println(ssid);
    
      WiFi.begin(ssid, password);
    
      while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
      }
    
      Serial.println("");
      Serial.println("WiFi connected");
      Serial.println("IP address: ");
      Serial.println(WiFi.localIP());
    }
    
    void callback(char* topic, byte* message, unsigned int length) {
      Serial.print("Message arrived on topic: ");
      Serial.print(topic);
      Serial.print(". Message: ");
      String recived_subs_MSSG;
    
      for (int i = 0; i < length; i++) {
        Serial.print((char)message[i]);
        recived_subs_MSSG += (char)message[i];
      }
      Serial.println(recived_subs_MSSG);
    
    }
    
    void reconnect() {
      // Loop until we're reconnected
      while (!client.connected()) {
        Serial.print("Attempting MQTT connection...");
        // Attempt to connect
        if (client.connect("ESP8266Client")) {
          Serial.println("connected");
          // Subscribe
          client.subscribe("topic", qos);
        } else {
          Serial.print("failed, rc=");
          Serial.print(client.state());
          Serial.println("try again in 5 seconds");
          // Wait 5 seconds before retrying
          delay(5000);
        }
      }
    }
    void loop() {
      if (!client.connected()) {
        reconnect();
      }
      client.loop();
      
      
    
      client.publish("topic1", "mssg to sent");
      Serial.println("published to mqtt server ");
      delay(8000);
      Serial.println("waiting for msg >>  ");
    }
    
    
