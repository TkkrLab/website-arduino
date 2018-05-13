---
title: "Les 4: Relais"
date: 2018-05-11
weight: 4
---

In dit hoofdstuk leer je hoe je een relais kunt aansturen met een Arduino. Softwarematig werkt het aansturen van een relais net zo als een led, maar op hardwaregebied zijn er een paar grote verschillen.

## Benodigde onderdelen

|                                       | Item                                | Aantal |
|---------------------------------------|-------------------------------------|--------|
| ![uno](/images/arduino-uno-2.jpg)     | Arduino Uno                         |      1 |
| ![breadboard](/images/breadboard.jpg) | Breadboard                          |      1 |
| ![draden](/images/draden.jpg)         | Breadboard draden                   |     10 |
| ![relay](/images/relay.jpg)           | Relais                              |      1 |
| ![diode](/images/diode2.jpg)          | Diode                               |      1 |


# Het aansturen van een relais

Een relais is een spoel die als elektromagneet werkt, gekoppeld met een mechanische schakelaar. Wanneer de elektromagneet bekrachtigd wordt schakelt de schakelaar naar de aan positie. Zodra de elektromagneet losgekoppeld wordt zal de schakelaar terugvallen naar de uit positie. De pinnen op het relais hebben de volgende functies:

Het bijzondere van een relais is dat de schakelaar volledig elektrisch gescheiden is van de elektromagneet en dus de Arduino. Dit betekent dat je zonder problemen grotere vermogens op hogere spanningen kan schakelen, denk aan een lamp op 230v, met een Arduino.

| Pin     | Functie                                         |
|---------|-------------------------------------------------|
| Common  | Ingang van de schakelaar                        |
| NC      | Normally closed (uit) contact van de schakelaar |
| NO      | Normally open (aan) contact van de schakelaar   |
| S1      | Aansluiting 1 van de elektromagneet spoel       |
| S2      | Aansluiting 2 van de elektromagneet spoel       |

Omdat de elektromagneet in het relais te veel vermogen trekt om direct met de digitale uitgang van een Arduino aangestuurd te worden moet het signaal van de Arduino eerst worden versterkt. Dit kan door een NPN transistor te gebruiken. In ons voorbeeld wordt een BC547 NPN transistor gebruikt.

Een transistor heeft drie pinnen: de base, de collector en de emitter. Een NPN transistor werkt als een schakelaar die stroom van de collector naar de emitter laat lopen wanneer een (veel kleinere) stroom wordt aangeboden op de base (ten opzichte van de emitter).

Door de base pin via een weerstand aan te sluiten op een digitale uitgang van de Arduino en de emitter te verbinden met de ground kunnen we de stroom van de base naar de emitter aanbieden. Daardoor kan er stroom lopen van de collector naar de emitter. De collector sluiten we aan op de spoel in het relais. De andere kant van de spoel sluiten we aan op de voedingsspanning (5v) van de Arduino.

Het laatste onderdeel dat wordt gebruikt in het schema is een diode. Een diode is een component dat in één richting stroom doorlaat en in de andere richting niet. De diode is in sper over de elektromagneet geplaatst om de spanning die ontstaat tijdens het uitschakelen van de spoel weg te nemen. Zonder de diode zal de vrijkomende energie uit de spoel de transistor beschadigen.

![schema](/images/relais_schema.png)

![diode](/images/diode.jpg)

![pinout](/images/relay_pinout.png)

# Software

Het onderstaande knipperlicht programma zal het relais iedere twee seconden een keer aan en uit zetten.

```
void setup() { 
  pinMode(2, OUTPUT);
}

void loop() {
  digitalWrite(2, HIGH);
  delay(1000);
  digitalWrite(2, LOW);
  delay(1000);
}  
```
