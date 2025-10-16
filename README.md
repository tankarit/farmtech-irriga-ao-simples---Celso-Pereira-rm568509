 FarmTech Solutions – Sistema de Irrigação Inteligente (V

Este projeto faz parte da **Fase 2** e representa um **protótipo de irrigação inteligente** desenvolvido pela startup fictícia **Tankar FarmTech Solutions**.  
O sistema foi criado no **Wokwi.com**, utilizando um **ESP32**, **DHT22**, **LDR (módulo de 4 pinos)**, **3 botões (NPK)** e **1 LED** para simular a bomba de irrigação.

---

 Objetivo do Projeto
Criar um sistema **simples e funcional** capaz de:
- Medir a **umidade do solo** com o **DHT22**.  
- Simular o **pH do solo** com um **sensor LDR (módulo)**.  
- Usar **botões** para representar os níveis dos nutrientes **Nitrogênio (N)**, **Fósforo (P)** e **Potássio (K)**.  
- **Acionar automaticamente a irrigação** (LED) quando o solo estiver “seco”.

---

## 1. O **DHT22** mede a umidade.  
2. Se a **umidade estiver abaixo de 40%**, o LED acende, simulando a **bomba ligada**.  
3. O **LDR** representa o pH — quanto mais luz, maior o valor lido (apenas ilustrativo).  
4. Os **botões N, P e K** simulam o estado dos nutrientes (apenas para visualização no monitor serial).  

---

##  Componentes Utilizados
| Componente | Função | Pinos usados |
|-------------|---------|---------------|
| ESP32 DevKit V1 | Microcontrolador principal | — |
| DHT22 | Sensor de umidade | Data → GPIO 15 |
| Módulo LDR (4 pinos) | Simula o pH da terra | AO → GPIO 34 |
| LED | Simula a bomba de irrigação | Anodo → GPIO 5 |
| Resistor 220Ω | Limita a corrente do LED | Em série com o LED |
| 3 Botões | Simulam N, P e K | GPIO 25 / 26 / 27 |

---

##  Ligações Detalhadas
| Componente | Pino ESP32 | Ligação |
|-------------|-------------|----------|
| **DHT22 (DATA)** | GPIO 15 | VCC→3.3V / GND→GND |
| **LDR (AO)** | GPIO 34 | VCC→3.3V / GND→GND / DO (não usado) |
| **Botão N** | GPIO 25 | Um lado no pino, outro no GND |
| **Botão P** | GPIO 26 | Um lado no pino, outro no GND |
| **Botão K** | GPIO 27 | Um lado no pino, outro no GND |
| **LED (bomba)** | GPIO 5 | GPIO5 → resistor 220Ω → LED → GND |

---

##  Código-Fonte
Arquivo: `code/irrigacao_simples.ino`

```cpp
#include <DHTesp.h>

#define DHT_PIN 15
#define LDR_PIN 34
#define LED_PIN 5
#define BTN_N 25
#define BTN_P 26
#define BTN_K 27

DHTesp dht;

void setup() {
  Serial.begin(115200);
  dht.setup(DHT_PIN, DHTesp::DHT22);

  pinMode(LED_PIN, OUTPUT);
  pinMode(BTN_N, INPUT_PULLUP);
  pinMode(BTN_P, INPUT_PULLUP);
  pinMode(BTN_K, INPUT_PULLUP);

  Serial.println("=== Sistema de Irrigacao Simples - FarmTech ===");
}

void loop() {
  TempAndHumidity data = dht.getTempAndHumidity();
  float umidade = data.humidity;
  int ldr = analogRead(LDR_PIN);

  bool n = digitalRead(BTN_N) == LOW;
  bool p = digitalRead(BTN_P) == LOW;
  bool k = digitalRead(BTN_K) == LOW;

  Serial.println("---------------------------");
  Serial.print("Umidade: "); Serial.print(umidade); Serial.println(" %");
  Serial.print("LDR (simula pH): "); Serial.println(ldr);
  Serial.print("N: "); Serial.print(n ? "pressionado" : "solto");
  Serial.print(" | P: "); Serial.print(p ? "pressionado" : "solto");
  Serial.print(" | K: "); Serial.println(k ? "pressionado" : "solto");

  if (umidade < 40) {
    digitalWrite(LED_PIN, HIGH);
    Serial.println("Solo seco! Bomba LIGADA!");
  } else {
    digitalWrite(LED_PIN, LOW);
    Serial.println("Solo úmido. Bomba desligada.");
  }

  delay(2000);
}
