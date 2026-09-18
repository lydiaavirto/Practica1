#include <Arduino.h>
#include <mbed.h>

const int pinADC = A0;

// Configuración del pin D2 (P1.11) como salida PWM nativa
mbed::PwmOut pwmSalida(digitalPinToPinName(D2));

void setup() {
  Serial.begin(9600);

  // 1. Configurar el período para 5 kHz
  // Período = 1 / 5000 Hz = 0.0002 s = 200 microsegundos
  pwmSalida.period_us(200);

  // Inicializar al 0%
  pwmSalida.write(0.0f);
}

void loop() {
  // 1. Lectura del ADC (0 a 1023)
  int valorADC = analogRead(pinADC);

  // 2. Mapeo a flotante para el ciclo de trabajo (0.0 a 1.0)
  float dutyCycle = (float)valorADC / 1023.0f;

  // 3. Aplicar el Duty Cycle al PWM de 5 kHz
  pwmSalida.write(dutyCycle);

  // 4. Mostrar información en el Serial Monitor
  long tension_mV = ((long)valorADC * 3300) / 1023;
  char buffer[64];
  sprintf(buffer, "ADC A0: %4d | Tension: %ld mV | PWM Duty: %d%%", 
          valorADC, tension_mV, (int)(dutyCycle * 100));
  Serial.println(buffer);

  delay(50); // Refresco fluido para el potenciómetro
}