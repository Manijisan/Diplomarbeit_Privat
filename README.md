# Diplomarbeit_Privat
In diesem GitHub möchte ich Private überlegungen und irgendwelche überarbeitungen einfügen
# Mechanik
# Sensorik

## Finale Platine
Da nun alle Sensoren erfoglreich angesprochen werden und ein json file via ESP now erfolgreich an unser crow panel übermittelt wird, können wir nun an einer Finalen Platine arbeiten auf der wir schlussendlich unseren Main Prozessor und die ganze Sensorik aufstecken.

![Image](https://github.com/user-attachments/assets/c360948d-a8a5-41f6-8eb9-f1140ebc984a)




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
## Lösung
Nach viel beratung mit verschiedenen lehrern denke ich das es nicht möglich ist dies effizietn und gut via Sowftware zu lösen deshalb werden wir warscheinlich auf unsere Sensorik Platine zusätzlich einen Spannugngsinvertierer einbauen. Dies löst nicht nur unser invertier Problem sonndern es verringert ebenfalls den Leistungsaufwand den unser ESP-32 C3 benötigt.
**Aktuelle Lösung:** Invertierer Kaufen
