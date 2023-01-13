---
title: "Les 7: Arduino datashield"
date: 2022-12-28
weight: 7
---

#### Deze documentatie is nog 'Work in progres', zie je fouten laat mij (Dave) dit weten.

![Arduino Data Logger Shield](/images/arduino-data-logger.jpg)

In deze workshop gaan we de temperatuur met datumtijd loggen naar een SD kaart. Presentatie van deze workshop kun je [hier vinden](https://docs.google.com/presentation/d/1XHDRSEYP50TZxLUKir2bVfbAtpUMhwvs52Qhers0uQ8/edit?usp=sharing).

Op de data logger shield zit een aantal componenten die het makkelijk maken om een data logger van je Arduino te maken. Deze zouden dan weggeschreven kunnen worden op de SD kaart. Met behulp van de realtime klok (met batterij) kun je ook de datum/tijd bijhouden.

#### Arduino shields
Met de (data)shield kun je de functionaliteit van de Arduino op een eenvoudige manier uitbreiden. Let hierbij wel op welke pinnen door de shield gebruikt word en wat dan nog vrij is voor je eigen toepassingen. Voor de datalog shield worden pinnen A4,A5 (voor RTC klok) en 10 - 13 (voor SD card reader) gebruikt.

Mits pinnen niet voor verschillende doeleinden gebruikt word zou je zelfs meerdere shields op elkaar kunnen stapelen.

### Benodigde onderdelen voor deze workshop

* Arduino Uno met USB kabel voor programmeren.
* breadboard met 3 kabeltjes
* Data logger shield met DS1307 RTC klok en SD Card reader.
* SD card (FAT16 of FAT32 geformarteerd).
* CR1220 batterij voor realtime klok
* MCP9701E temperatuur sensor


## MCP9701E Temperatuur sensor
Grote voordeel van deze sensor dat deze annoloog temperatuur terug geeft. Dit maakt het eenvoudig in gebruik en de benodigde software eenvoudig.

### Hardware

![MCP9701E temperatuur sensor](/images/lm-35.jpg)

* Pin1/ Vcc pin / Input pin, tussen 4V-30V
* Pin2/ Out/ Analog pin - gemeten waarde proportioneel aan temperatuur.
* Pin3/ GND or ground pin.

[Datasheet MCP9701E sensor](MCP970X.pdf)

## MCP9701E Arduino
Je sluit de sensor als volgt aan op de Arduino. Gebruik hiervoor de meegeleverder breadboard met kabeltjes.

![MCP9701E Arduino](/images/lm35_arduino.png)

### Code

We gaan eerst de temperatuur sensor de waarde naar seriele console loggen :

```cpp
void setup() {
  pinMode(A1, INPUT);
  Serial.begin(115200);
}

void loop() {
  // put your main code here, to run repeatedly:
  int analogCounts = analogRead(A1);
  float analogVoltage = (analogCounts * 5.0) / 1024.0;
  float analogTemperature = analogVoltage / 0.01;

  Serial.println("Analog voltage: " + String(analogVoltage) + " volt, temperature: " + String(analogTemperature) + " *c");

  delay(200);
}
```


## DS1307 RTC Klok
Voor de realtime klok (RTC) word de DS1307 gebruikt, een populaire chip voor deze toepassingen. Met behulp van een batterij word ook de tijd bijgehouden als er geen stroom op staat. 

[Datasheet DS1307](DS1307.pdf)

### Hardware
In deze workshop gebruiken we de RTC klok die op de data logger shield zit. Hiermee word de klok aangesloten.

Intern gebruikt de datashield pinnen A4 (SLA) en A5 (SCL). Deze pinnen kunnen dus niet meer voor andere functies gebruikt worden. Wel zou je andere devices/chips op kunnen aansluiten die ook de [I2C communicatie](https://docs.arduino.cc/learn/communication/wire) ondersteunen.

### Code
Voor het programma hebben we ook een extra library nodig. We gebruiken hier de 'RTCLib by Adafruit', zoek deze in de Library manager en installeer deze.

```cpp
#include "RTClib.h"

RTC_DS1307 rtc;

char daysOfTheWeek[7][12] = {"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"};

void setup () {
  Serial.begin(115200);

  if (! rtc.begin()) {
    Serial.println("Couldn't find RTC");
    Serial.flush();
    while (1) delay(10);
  }

  if (! rtc.isrunning()) {
    Serial.println("RTC is NOT running, let's set the time!");
    // When time needs to be set on a new device, or after a power loss, the
    // following line sets the RTC to the date & time this sketch was compiled
    rtc.adjust(DateTime(F(__DATE__), F(__TIME__)));
    // This line sets the RTC with an explicit date & time, for example to set
    // January 21, 2014 at 3am you would call:
    // rtc.adjust(DateTime(2014, 1, 21, 3, 0, 0));
  }
}

void loop () {
    DateTime now = rtc.now();

    Serial.print(now.year(), DEC);
    Serial.print('/');
    Serial.print(now.month(), DEC);
    Serial.print('/');
    Serial.print(now.day(), DEC);
    Serial.print(" (");
    Serial.print(daysOfTheWeek[now.dayOfTheWeek()]);
    Serial.print(") ");
    Serial.print(now.hour(), DEC);
    Serial.print(':');
    Serial.print(now.minute(), DEC);
    Serial.print(':');
    Serial.print(now.second(), DEC);
    Serial.print("  unix timestamp ");
    Serial.print(" (seconds since midnight 1/1/1970) ");
    Serial.print(now.unixtime());
    Serial.println();
    delay(3000);
}
```

## SD Card logger

### Hardware
Voor de SD card reader zijn 4 pinnen nodig voor de communicatie , voor onze datashield word hiervoor pin 10 t/m 13 gebruikt.

* pin 10 (CS) 
* pin 11 (DI) 
* pin 12 (DO)
* pin 13 (CLK)

Je hebt natuurlijk ook pinnen nodig voor de 5V en GND

### Code
Programma om informatie te loggen naar SD card. In dit voorbeeld is dit niet een echte meting maar een test waarde om de werking te testen.

```cpp
#include <SPI.h>
#include <SD.h>

const int pinChipSelect = 10;

void setup() {
  pinMode(pinChipSelect, OUTPUT);
  digitalWrite(pinChipSelect, HIGH);

  Serial.begin(115200);

  while (!SD.begin(pinChipSelect)) {
    Serial.println("Failed to init SD card, trying again in 500ms...");
    delay(500);
  }
  Serial.println("SD card initialized!");
}

void log(unsigned long time, float temperature) {
  String logLine = String(time) + "," + String(temperature);
  Serial.println(logLine);
  File dataFile = SD.open("data.txt", FILE_WRITE);
  if (!dataFile) {
    Serial.println("Failed to write to the SD card");
    return;
  }
  dataFile.println(logLine);
  dataFile.close();
}

void loop() {
  unsigned long time = millis();
  float temperature = 13.37;
  log(time, temperature);
  delay(1000);
}
```

Zie ook [Adafruit data logger shield](https://learn.adafruit.com/adafruit-data-logger-shield)

## Oefening

Combineer bovenstaande oderdelen tot 1 geheel. Gebruik de waarden van de temperatuur sensor om deze naar de SD kaart te schrijven.