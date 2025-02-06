# Diplomarbeit_Privat
In diesem GitHub möchte ich Private überlegungen und irgendwelche überarbeitungen einfügen

# PWM für motortreiber
```python
from machine import Pin, PWM
import time

pwm = PWM(Pin(15), freq=1000, duty=512)

while True:
    time.sleep(1)
```
