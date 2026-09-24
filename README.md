# Sleepy1-code
This ESP32-based system monitors gas levels using MQ2 and MQ135 sensors and automatically controls a PWM fan. Under normal conditions, the fan runs at low speed (~1200 RPM). When either sensor exceeds its threshold, the fan increases to ~2100 RPM. Sensor readings are transmitted to a Raspberry Pi via UART every 3 seconds.
