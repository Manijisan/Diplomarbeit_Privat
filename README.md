# Diplomarbeit_Privat
In diesem GitHub möchte ich Private überlegungen und irgendwelche überarbeitungen einfügen

# PWM für motortreiber
Um den Motortreiber erfolgreich anzusteuern müssen wir 4 PWM signal erzeugen. Zwei Rechteck signale und 2 invertierte Rechteck signale. Ein PWM kann man ganz einfach mit der PWM funktion generieren. Hier werden z.B zwei PWM signale auf Pin 14 und 15 ausgegben.

```python
from machine import Pin, PWM
import time

pwm = PWM(Pin(15), freq=1000, duty=512)
pwm2 = PWM(Pin(14),freq=1000, duty=512)

while True:
    time.sleep(1)
```
Wie man jetzt jeoch auf die beiden Invertierten Signale kommt ist ein wenig schwiriger. Hardware technisch gesehen wäre dies einfach lösbar mit einem Invertierer. Dies will ich stand jetzt jedoch vermeiden. Ich habe verschiedene möglichkeiten Probiert. 
## PWM Problem
Sollte ich die PWM funktion verwenden (siehe oben) wird nicht wirklich eine möglichkeit zur verfügung gestellt mit der ich das PWM auf Pin 15 z.B Invertiert auf pin 16 ausgebe kann. Im Internet gab es folgende lösungsvorschläge
```python
pwm = PWM(Pin(15), freq=1000, duty=512, invert=True)
```
```python
esp32.GPIO.func_out_sel_cfg[15].inv_sel = 1
```
```python
from machine import Pin, PWM
import time

# Initialize PWM on GPIO 15 with a frequency of 1 kHz
pwm = PWM(Pin(15), freq=1000)

# Function to set inverted duty cycle
def set_inverted_duty(pwm, duty):
    inverted_duty = 1023 - duty
    pwm.duty(inverted_duty)

# Example usage
while True:
    for duty in range(0, 1024, 100):  # Increase duty cycle
        set_inverted_duty(pwm, duty)
        time.sleep(0.5)
```
Keine dieser Varianten haben jedoch bis jetzt für uns funktioniert
## PIN ein und ausschalten
Es ist natürlich auch möglich ein PWM auszugeben in dem mann einen Pin einfach nur zum richting zeitpunkt ein und Ausschaltet dies ist führt jedoch zu einem PWM das schlicht und einfach zu langsam und ungenau ist.

**Aktuelle Lösung:** Invertierer Kaufen
