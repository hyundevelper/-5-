# 블루투스 통신

# 블루투스(Bluetooth)는 
근거리에서 무선으로 데이터를 주고받을 수 있게 해주는 통신 기술입니다. 주로 휴대폰, 이어폰, 노트북, 스마트워치 등에서 많이 사용된다.

단거리 무선 통신: 일반적으로 10m 내외, 최대 100m까지 통신 가능
저전력 소비: 전력 소모가 적어 웨어러블 기기 등에 적합
면허 불필요한 ISM 대역(2.4GHz) 사용
자동 페어링 및 데이터 전송 가능

# 실습 1: 텍스트 데이터 송수신

코드 작성: 


#include "BluetoothSerial.h"

const char* DEVICE_NAME = "ESP32-BT-Slave";

#if !defined(CONFIG_BT_ENABLED) || !defined(CONFIG_BLUEDROID_ENABLED)
  #error "Bluetooth is not enabled! Please run `make menuconfig` to enable it."
#endif

#if !defined(CONFIG_BT_SPP_ENABLED)
  #error "Serial Port Profile (SPP) is not enabled. It is required for Bluetooth communication on ESP32."
#endif

BluetoothSerial SerialBT;

void setup() {
  Serial.begin(115200);
  SerialBT.begin(DEVICE_NAME);
  //SerialBT.deleteAllBondedDevices();
  Serial.printf("Bluetooth device \"%s\" started.\n", DEVICE_NAME);
  Serial.println("You can now pair it with your Bluetooth device.");
}

void loop() {
  if (Serial.available()) {
    SerialBT.write(Serial.read());
  }

  if (SerialBT.available()) {
    Serial.write(SerialBT.read());
  }

  delay(20);
}



# LED 제어 (블루투스 이용)

코드 작성 :


#include "BluetoothSerial.h"

BluetoothSerial SerialBT;

const int ledPin = 4;

void setup() {
  Serial.begin(115200);
  SerialBT.begin("ESP32-BT-LED");

  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, LOW);

  Serial.println("Bluetooth Started! Pair with ESP32-BT-LED");
}

void loop() {
  if (SerialBT.available()) {
    char incoming = SerialBT.read();
    Serial.write(incoming);

    if (incoming == '1') {
      digitalWrite(ledPin, HIGH);
      Serial.println("LED ON");
    } else if (incoming == '0') {
      digitalWrite(ledPin, LOW);
      Serial.println("LED OFF");
    }
  }
}

