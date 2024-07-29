# programacion-con-esp32
se presentara un codigo para controlar la temperatura y que lea esta mediante el celuras y se mueva un servomotor 


#include <WiFi.h>
#include <WebServer.h>
#include <DHT.h>

// Configuración del sensor DHT11
#define DHTPIN 18     // Pin digital donde está conectado el DHT11
#define DHTTYPE DHT11 // Definir tipo de sensor DHT

DHT dht(DHTPIN, DHTTYPE);

// Configuración de red Wi-Fi
const char* ssid = "CAMPUS_EPN";
const char* password = "politecnica**";

// Crear instancia del servidor web
WebServer server(80);

void setup() {
  Serial.begin(115200);
  dht.begin();

  // Conectarse a la red Wi-Fi
  WiFi.begin(ssid, password);
  Serial.println("Conectando a WiFi...");
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Intentando conectar...");
  }
  Serial.println("Conectado a WiFi");

  // Imprimir la dirección IP
  Serial.print("Dirección IP: ");
  Serial.println(WiFi.localIP());

  // Iniciar el servidor
  server.on("/", handleRoot);
  server.begin();
  Serial.println("Servidor iniciado");
}

void loop() {
  server.handleClient();
}

void handleRoot() {
  float temperature = dht.readTemperature();

  // Comprobar si la lectura del sensor ha fallado
  if (isnan(temperature)) {
    Serial.println("¡Error al leer el sensor DHT!");
    server.send(500, "text/plain", "Error al leer el sensor DHT");
    return;
  }

  // Enviar datos al cliente
  
  String html = "<html><body><h1>Termometro Digital</h1>";
  html += "<p>Temperatura: " + String(temperature) + " °C</p>";
  html += "</body></html>";

  server.send(200, "text/html", html);
}
