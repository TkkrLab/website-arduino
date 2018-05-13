---
title: "Looplicht zonder delay"
date: 2018-05-11
weight: 30
--- 

In de vorige les hebben we de functie delay gebruikt om de processor te laten wachten.

Deze functie is in sommige gevallen erg handig maar delay kan ook erg lastig zijn als je bijvoorbeeld 2 dingen "tegelijk" wilt doen. Stel je voor dat je 1 led wilt laten knipperen met een tussenpauze van 100ms en de andere met een tussenpauze van een seconde.

Wanneer je een delay gebruikt zal de arduino elke keer een seconde staan wachten en krijg je de 2de led nooit op een snelheid van 100ms.
De Arduino beschikt ook over een interne klok, hiermee kunnen we dus vergelijken en bepalen wanneer er een seconde voorbij en is wanneer er 100ms voorbij zijn.



Zorg er voor dat je alle voedingsspanningen los koppelt (usb niet aangesloten op de pc en geen power adapter aangesloten op de voedingsplug).
sluit de Arduino aan volgens onderstaand schema:


Start het Arduino programma en sluit de USB kabel aan.
Typ de volgende code over of kopieer deze naar de Arduino IDE:

```
const int ledPin =  12;
int ledState = LOW;

unsigned long previousMillis = 0;

const long interval = 1000;

void setup() {
  pinMode(ledPin, OUTPUT);
}

void loop() {
  unsigned long currentMillis = millis();

  if (currentMillis - previousMillis >= interval) {
    previousMillis = currentMillis;
    
    if (ledState == LOW) {
      ledState = HIGH;
    } else {
      ledState = LOW;
    }
    
    digitalWrite(ledPin, ledState);
  }
}
```

Klik op de upload button.
We zien nu 1 led knipperen.

Opdracht: pas de code aan zodat je 2 leds met een verschillend interval kunt laten knipperen.
