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
