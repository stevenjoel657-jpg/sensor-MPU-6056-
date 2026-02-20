# sensor-MPU-6056-
EL SENSOR MPU 6050 QUE NOS AYUDA A DETECTAR EL MOVIMIENTO DE CABEZA 
CODIGO ARDUINO 
![Uploading WhatsApp Image 2026-02-20 at 11.40.14 AM.jpeg…]()

#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <MPU6050.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET    -1
#define SCREEN_ADDRESS 0x3C  // Dirección típica del OLED

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);
MPU6050 mpu;

const int led = 2;
float angulo = -20.0;

void setup() {
  Serial.begin(115200);

  // Iniciar I2C para ESP32 (SDA 21, SCL 22)
  Wire.begin(21, 22);

  // Inicializar OLED
  if(!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
    Serial.println("Error OLED");
    while(true);
  }

  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(WHITE);
  display.setCursor(0, 0);
  display.println("Detector Sueno");
  display.display();

  // LED
  pinMode(led, OUTPUT);
  digitalWrite(led, LOW);

  // Inicializar MPU6050
  mpu.initialize();

  if(!mpu.testConnection()){
    Serial.println("MPU6050 ERROR!");
    display.clearDisplay();
    display.setCursor(0, 0);
    display.println("MPU6050 ERROR!");
    display.display();
    while(1);
  } else {
    Serial.println("MPU6050 OK");
  }
}

void loop() {
  int16_t ax, ay, az;
  mpu.getAcceleration(&ax, &ay, &az);

  float fx = ax / 16384.0;
  float fy = ay / 16384.0;
  float fz = az / 16384.0;

  float inclinacion = atan2(fx, sqrt(fy*fy + fz*fz)) * 180.0 / PI;

  display.clearDisplay();
  display.setCursor(0, 0);

  if(inclinacion < angulo){
    digitalWrite(led, HIGH);
    display.setTextSize(2);
    display.println("ALERTA!");
    display.setTextSize(1);
    display.println("NO TE DUERMAS");
  } else {
    digitalWrite(led, LOW);
    display.setTextSize(1);
    display.println("Detector Sueno");
    display.print("Inclinacion: ");
    display.print(inclinacion, 1);
    display.println(" deg");
  }

  display.display();
  delay(200);
}


ENLACE DEL VIDEO
https://www.youtube.com/watch?v=InEQ3uZh_EM
