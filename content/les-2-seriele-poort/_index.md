---
title: "Les 2: Seriële poort"
date: 2018-05-11
weight: 2
---

Dit hoofdstuk beschrijft het gebruik van de seriële verbinding tussen het Arduino bord en de computer. Met de seriële verbinding kan een Arduino berichten naar de aangesloten computer sturen en vice-versa. De meeste Arduinos hebben een USB poort om met de computer te verbinden. Over deze USB verbinding wordt een seriële poort gesimuleerd. Een seriële poort is simpel te gebruiken in eigen applicaties, met behulp van een "terminal" programma of vanuit de Arduino IDE.

## Benodigde onderdelen

|                                       | Item                                | Aantal |
|---------------------------------------|-------------------------------------|--------|
| ![uno](/images/arduino-uno-2.jpg)     | Arduino Uno                         |      1 |
| ![breadboard](/images/breadboard.jpg) | Breadboard                          |      1 |
| ![draden](/images/draden.jpg)         | Breadboard draden                   |     10 |
| ![potmeter](/images/potmeter.jpg)     | Potentiometer (variabele weerstand) |      1 |

# Berichten sturen vanaf de Arduino

Om verbinding te maken met de computer is het nodig de seriële poort te starten. Dit kan met behulp van de functie `Serial.begin(<snelheid>);`. De poort hoeft maar één keer geopend te worden. Je kunt de `Serial.begin();` functie daarom het beste in de `setup()` neerzetten.

Standaard gebruikt de Arduino IDE een snelheid ([baudrate](https://nl.wikipedia.org/wiki/Baud_(telecommunicatie))) van `9600` baud. Hogere snelheden zijn ook mogelijk. Wij raden u aan een snelheid van `115200` baud te gebruiken. Het is belangrijk dat u op zowel de Arduino als op de computer de zelfde snelheid kiest. Anders komen de berichten niet leesbaar over.

### Baudrates

| Baudrate | Opmerking                                                               |
|----------|-------------------------------------------------------------------------|
| 300      |                                                                         |
| 1200     |                                                                         |
| 2400     |                                                                         |
| 4800     |                                                                         |
| 9600     | Standaard baudrate voor de seriële monitor in de Arduino IDE            |
| 19200    |                                                                         |
| 31250    | Geen "standaard" baudrate, maar wordt gebruikt voor het MIDI protocol   |
| 38400    |                                                                         |
| 57600    |                                                                         |
| 74880    |                                                                         |
| 115200   | Door ons aangeraden snelheid. (snel, maar werkt vrijwel altijd stabiel) |
| 230400   |                                                                         |
| 250000   | Standaard snelheid voor veel 3D printers                                |
| 500000   |                                                                         |
| 1000000  |                                                                         |
| 2000000  |                                                                         |

Nadat de seriële poort geopend is kan je berichten naar de computer sturen met behulp van de functie `Serial.print(...);`. Aan deze functie kan je veel verschillende soorten variabelen meegeven, waaronder getallen en Strings. Naast de `print` functie is er ook een `Serial.println(...);` functie, welke na de tekst naar de volgende regel zal gaan. Tekst verzonden met de `print` functie wordt namelijk achter elkaar geplaatst.

### Voorbeeld 1

(Voor dit voorbeeld is het niet nodig iets aan te sluiten op het Arduino bord.)


```cpp
void setup() {
  Serial.begin(9600);
}

void loop() {
  Serial.print("Hello ");
  Serial.println("world.");
}
```

Nadat je dit programma geupload hebt naar het Arduino bord kan je de "seriële monitor" openen door op het het knopje met het vergrootglas rechts bovenin de menubalk te klikken.

![ide](/images/arduino-ide-serial.png)

Controleer of de baudrate onder in het scherm het zelfde staat ingesteld als de snelheid in het Arduino programma. Je zou nu het volgende moeten zien:

![monitor](/images/monitor.png)


### Voorbeeld 2

In dit voorbeeld lezen we de spanning uit die op een analoge ingang binnenkomt.

De ADC (Analog to Digital Converter) in de Arduino geeft een 10 bits waarde terug. Een 10 bits breed getal kan een waarde bevatten van 0 tot en met 1023. De ADC op een Arduino geeft een waarde van 0 terug wanneer de spanning op de ingang 0 volt is en 1023 wanneer de spanning op de ingang 5 volt is.

Het uitlezen van de ADC doe je met de `analogRead(<poort>);` functie. Deze functie geeft een getal (integer) terug. De teruggegeven waarde van het lezen van poort `A0` kan je als volgt opslaan in een variabele: `int value = analogRead(A0);`.

De volledige programmacode voor het lezen van poort `A0` en het naar de computer sturen van de gelezen waarde komt er zo uit te zien: 

```
void setup() {
  pinMode(A0, INPUT);
  Serial.begin(9600);
}

void loop() {
  int value = analogRead(A0);
  Serial.println(value);
}
```

Upload dit programma naar de Arduino.

Wanneer de analoge ingang nergens op aangesloten is zal de Arduino omgevingsruis opvangen en willekeurig lijkende waarden teruggeven. Het is nodig om een circuit aan de analoge ingang te verbinden om hem te kunnen testen. Voor dit voorbeeld gebruiken we een variabele weerstand (potmeter).

De potmeter kan je als volgt aansluiten op de Arduino:

![schema](/images/potmeter_bb.svg)


Als je nu aan de potmeter draait zul je zien dat de waarde in de seriële monitor veranderd. Dit zal er als volgt uitzien, bij jouw zullen de gemeten waarden anders zijn:

```
10
23
56
109
63
42
```

Het is ook mogelijk deze getallen te plotten in een grafiek. U kunt de seriële plotter openen door de seriële monitor te sluiten en in het menu `Tools` op de keuze `Serial Plotter` te klikken.

![plotter](/images/plotter.png)


# Berichten ontvangen op de Arduino

Naast het sturen van berichten van de Arduino naar de computer toe is het ook mogelijk om berichten te sturen vanaf de computer naar de Arduino.

Berichten vanaf de computer kan je per karakter lezen met het `Serial.read();` commando. Dit commando geeft een variabele terug van het type `char`.

Tekst die vanaf de computer naar de Arduino verzonden wordt komt in een buffer terecht. Controleren of er tekst in de buffer staat kan je doen met de `Serial.available();` functie. Die functie geeft als getal (integer) terug hoeveel karakters er in de buffer staan.

Het volgende programma stuurt alle ontvangen karakters meteen terug naar de computer. Je kunt een bericht naar de Arduino sturen door te typen in het tekstvak bovenin de seriële monitor. Zodra je op enter drukt zal het bericht verzonden worden.


```
void setup() {
  Serial.begin(9600);
}

void loop() {
  if (Serial.available() > 0) {
    char c = Serial.read();
    Serial.print(c);
  }
}
```

### Voorbeeld 1

Het volgende programma controleert of er een `1` of een `0` ontvangen wordt en schakelt daarmee een uitgang.

```
void setup() {
  Serial.begin(9600);
  pinMode(13, OUTPUT);
}

void loop() {
  if (Serial.available() > 0) {
    char c = Serial.read();
    if (c == '1') {
      digitalWrite(13, HIGH);
    } else if (c == '0') {
      digitalWrite(13, LOW);
    } else {
      //Als het karakter niet 1 of 0 is dan doen we niets...
    }
  }
}
```

Waar een `String` in Arduino wordt omsloten met dubbele aanhalingstekens `"tekst"` wordt een enkel karakter (char) omsloten met enkele aanhalingstekens `'A'`.

### Voorbeeld 2

Het volgende programma verzamelt alle karakters die ontvangen worden in een `String`. Zodra er een regeleinde (newline of `\n`) ontvangen wordt roept het programma een door ons gedefinieerde functie aan die de ontvangen tekst terug stuurt naar de computer.

Het is belangrijk dat u in de seriële monitor het regeleinde instelt op "Newline".

```
String input = "";

void setup() {
  Serial.begin(9600);
}

void handleSerialInput() {
    Serial.print("Ontvangen tekst: '");
    Serial.print(input);
    Serial.println("'");
    input = ""; //Wis het ontvangen bericht
}

void loop() {
  while (Serial.available() > 0) {
    char c = Serial.read();
    if (c == '\n') {
      handleSerialInput();
    } else if (c != '\r') {
      input += c;
    }
  }
}
```

Zoals je kunt zien wordt het `\r` karakter genegeerd. Het `\r` karakter representeert de opdracht om terug te keren naar het begin van de regel ([carriage return](https://en.wikipedia.org/wiki/Carriage_return)). Sommige terminalprogrammas sturen dit onzichtbare karakter mee wanneer u op enter drukt in de terminal. Om te voorkomen dat we dit onzichtbare en ongebruikte karakter opnemen in de `String` negeren we het wanneer het ontvangen wordt.
