/*
=========================================================
ESP32
MQ2 + MQ135 + PWM FAN + UART TX ONLY
=========================================================

MQ2 AO       -> GPIO34
MQ135 AO     -> GPIO35

Fan PWM      -> GPIO21

ESP32 TX2    -> GPIO17 -> Raspberry Pi RX
ESP32 GND    -> Raspberry Pi GND

NO RX CONNECTION REQUIRED

=========================================================
FAN CONTROL
=========================================================

Normal condition       -> ~1200 RPM
Gas level increased    -> ~2100 RPM

If MQ2 OR MQ135 exceeds its threshold,
fan runs FAST.

=========================================================
RASPBERRY PI DATA
=========================================================

Data is sent every 3 seconds:

MQ2:875
MQ135:640

If sensor reading is 0:

MQ2:0
MQ135:0

Baud rate: 115200
=========================================================
*/


// =====================================================
// PIN DEFINITIONS
// =====================================================

#define MQ2_PIN    34
#define MQ135_PIN  35

#define FAN_PIN    21

// ESP32 TX2
#define TXD2       17


// =====================================================
// FAN PWM SETTINGS
// =====================================================

const int fanChannel = 0;
const int fanFreq = 25000;
const int fanResolution = 8;


// Approximately 1200 RPM
const int FAN_SLOW = 145;

// Approximately 2100 RPM
const int FAN_FAST = 255;


// =====================================================
// GAS THRESHOLDS
// =====================================================

const int MQ2_THRESHOLD = 1200;
const int MQ135_THRESHOLD = 1200;


// =====================================================
// SEND INTERVAL
// =====================================================

const unsigned long SEND_INTERVAL = 3000;

unsigned long lastSendTime = 0;


// =====================================================
// SETUP
// =====================================================

void setup()
{
  // USB Serial Monitor
  Serial.begin(115200);


  // ===================================================
  // UART2
  // RX disabled
  // TX = GPIO17
  // ===================================================

  Serial2.begin(115200, SERIAL_8N1, -1, TXD2);


  // ===================================================
  // MQ SENSOR PINS
  // ===================================================

  pinMode(MQ2_PIN, INPUT);
  pinMode(MQ135_PIN, INPUT);


  // ===================================================
  // FAN PWM
  // ===================================================

  ledcSetup(fanChannel, fanFreq, fanResolution);
  ledcAttachPin(FAN_PIN, fanChannel);


  // ===================================================
  // START FAN AT NORMAL SPEED
  // ===================================================

  ledcWrite(fanChannel, FAN_SLOW);


  // ===================================================
  // USB SERIAL INFORMATION
  // ===================================================

  Serial.println();
  Serial.println("=================================");
  Serial.println("ESP32 MQ2 + MQ135 SYSTEM");
  Serial.println("=================================");
  Serial.println("MQ2       : GPIO34");
  Serial.println("MQ135     : GPIO35");
  Serial.println("FAN PWM   : GPIO21");
  Serial.println("TX2       : GPIO17");
  Serial.println("Baud      : 115200");
  Serial.println("Send time : 3 seconds");
  Serial.println("=================================");


  // ===================================================
  // INITIAL DATA TO RASPBERRY PI
  // ===================================================

  Serial2.println("MQ2:0");
  Serial2.println("MQ135:0");
}


// =====================================================
// LOOP
// =====================================================

void loop()
{
  // ===================================================
  // READ MQ2
  // ===================================================

  int mq2Value = analogRead(MQ2_PIN);


  // ===================================================
  // READ MQ135
  // ===================================================

  int mq135Value = analogRead(MQ135_PIN);


  // ===================================================
  // ENSURE VALUES ARE NOT NEGATIVE
  // ===================================================

  if (mq2Value < 0)
  {
    mq2Value = 0;
  }

  if (mq135Value < 0)
  {
    mq135Value = 0;
  }


  // ===================================================
  // GAS DETECTION
  // ===================================================

  bool gasDetected = false;


  // MQ2 exceeded threshold
  if (mq2Value > MQ2_THRESHOLD)
  {
    gasDetected = true;
  }


  // MQ135 exceeded threshold
  if (mq135Value > MQ135_THRESHOLD)
  {
    gasDetected = true;
  }


  // ===================================================
  // FAN CONTROL
  // ===================================================

  if (gasDetected)
  {
    // Gas detected
    // Approximately 2100 RPM

    ledcWrite(fanChannel, FAN_FAST);
  }
  else
  {
    // Normal condition
    // Approximately 1200 RPM

    ledcWrite(fanChannel, FAN_SLOW);
  }


  // ===================================================
  // SEND SENSOR VALUES EVERY 3 SECONDS
  // ===================================================

  if (millis() - lastSendTime >= SEND_INTERVAL)
  {
    lastSendTime = millis();


    // -------------------------------------------------
    // LINE 1
    // -------------------------------------------------

    Serial2.print("MQ2:");
    Serial2.println(mq2Value);


    // -------------------------------------------------
    // LINE 2
    // -------------------------------------------------

    Serial2.print("MQ135:");
    Serial2.println(mq135Value);


    // -------------------------------------------------
    // USB SERIAL MONITOR
    // -------------------------------------------------

    Serial.print("MQ2:");
    Serial.println(mq2Value);

    Serial.print("MQ135:");
    Serial.println(mq135Value);
  }


  // NO DELAY HERE
  // ESP32 continuously reads sensors and controls fan.
  // Transmission is independently controlled by millis()
}
