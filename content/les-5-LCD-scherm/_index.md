---
title: "Les 5: LCD scherm"
date: 2018-05-11
weight: 5
---

In dit hoofdstuk leer je hoe je een HD44780 karakterdisplay aan kunt sluiten op een Arduino. Eerst leer je hoe je het LCD aansluit, waarna de programmacode wordt uitgelegd.

## Benodigde onderdelen

|                                       | Item              | Aantal |
|---------------------------------------|-------------------|--------|
| ![lcd](/images/lcd-small.jpg)         | HD44780 LCD       |      1 |
| ![weerstand](/images/weerstand.jpg)   | Potmeter          |      1 |
| ![breadboard](/images/breadboard.jpg) | Breadboard        |      1 |
| ![draden](/images/draden.jpg)         | Breadboard draden |     10 |
| ![uno](/images/arduino-uno-2.jpg)     | Arduino Uno       |      1 |

# De hardware

Zorg er altijd voor dat je alle voedingsspanningen los koppelt voordat je het aan het circuit werkt (USB niet aangesloten op de pc en geen power adapter aangesloten op de voedingsplug).

![breadboard](/images/lcd_bb.svg)

![schema](/images/lcd_schematic.png)

# De software

Start het Arduino programma en sluit de USB kabel aan.

```cpp
#include <LiquidCrystal.h>
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

int teller = 0;

void setup() {
  lcd.begin(16, 2); //Vertel de arduino hoe groot het scherm is
  lcd.clear(); //Maak het scherm leeg
  lcd.print("Arduino workshop"); //Stuur tekst naar het scherm
  lcd.setCursor(0, 1); //Zet de cursor op de eerste letter van de tweede regel
  lcd.print("Tkkrlab"); //Stuur tekst naar het scherm
  delay(1000);
  lcd.setCursor(0, 1); //Zet de cursor op de eerste letter van de tweede regel
  lcd.print("                "); //Wis de tweede regel
}

void loop() {
  delay(1000); //Wacht 1 seconde
  lcd.setCursor(0, 1); //Zet de cursor op de eerste letter van de tweede regel
  lcd.print(teller);
  teller = teller + 1;
}
```

Dit programma laad eerst de `LiquidCrystal` library in, met het include commando. Daarna wordt een object gemaakt voor het aangesloten LCD scherm. Tijdens het maken van het object worden de gebruikte pinnummers opgegeven (`LiquidCrystal lcd(RS, Enable, D4, D5, D6, D7);`).

Met het `lcd.begin(16, 2);` commando vertellen we het object dat ons LCD scherm 16 karakters per regel heeft en dat het scherm twee regels hoog is.

Daarna maken we het scherm leeg met `lcd.clear();` en printen we de tekst "Arduino workshop" naar het LCD met de `lcd.print(<teskt>);` functie.

Met `lcd.setCursor(<col>, <row>);` zetten we de cursor van het LCD scherm aan het begin van de tweede regel. Daarna gebruiken we opnieuw `lcd.print` om de tekst "Tkkrlab" op de tweede regel te printen.

In de `loop` functie schrijven we een variabele die bij iedere loop verhoogd wordt naar de tweede regel van het LCD.
