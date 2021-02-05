# NodeMCU-IoT
Código para la realizar comunicación entre NodeMCU y broker ioticos.org 

#include <ESP8266WiFi.h>
#include <PubSubClient.h>

const char* ssid = "********";
const char* password =  "********";

const char*mqtt_server = "ioticos.org";
//const int mqtt_port = 1883;
const char*user = "*********";
const char*password2 = "*********";
const char *root_topic_publish = "*************";


WiFiClient espClient;
PubSubClient client(espClient); 

void setup (){
Serial.begin (115200);
WiFi.begin(ssid, password);

while (WiFi.status() != WL_CONNECTED){
  delay (500);
  Serial.print(".");
}

Serial.println("Dispositivo conectado");
Serial.println("direccion IP:");
Serial.println(WiFi.localIP());

client.setServer (mqtt_server, 1883);
client.setCallback(callback);
}
void callback (char* topic, byte* payload, unsigned int length){
Serial.print("mensaje cargado [");
Serial.print(topic);
Serial.print("]");

for (int i=0; i<length; i++){
Serial.print((char)payload[i]);
}
Serial.println();
}

void reconnect(){
  while (!client.connected()){

    Serial.println ("Intentando conectar ...");
    String clientId = "IOTICOS_H_W_";
    clientId += String(random(0xffff), HEX);
    
    if (client.connect(clientId.c_str(), user, password2)){
    Serial.println("conectado");
    client.publish (root_topic_publish, "Lanzando primer mensaje");
    client.subscribe(root_topic_publish);
    Serial.println("subscripcion correcta");
  }else{
    Serial.print("fallo subscripcion...");
    Serial.print(client.state());
    Serial.println ("volviendo a conectar ");
    delay (3000);
    }
  }
}
void loop(){
  if (!client.connected()){
    reconnect();
  }
  client.loop();
  
  client.publish(root_topic_publish, "hola mundo");
  
  delay (1000);
  }
