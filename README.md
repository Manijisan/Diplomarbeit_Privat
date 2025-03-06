# Diplomarbeit_Privat
__In diesem GitHub möchte ich Private überlegungen und irgendwelche überarbeitungen einfügen__
# Mechanik
# Sensorik
Unser Geländefahrzeug soll in der Lage sein verschiedene Umweltdaten auszulesen und auch diese Daten zu verarbeit. Bei der Wahl welche Daten wir bewerten wollen haben wir uns für die folgenden entschieden.
- GPS (Längengrad,Breitengrad,Höhe)
- Umgebungstemperatur und Luftfeuchtigkeit
- Luftqualität
Do muss noch überall des Züg wegs dena sensora ihe sprich alls wichtig was i noch uf one note hob
## GPS
## Temperatur und Luftfeuchtigkeit
Der DHT-Temperatursensor wird mit folgendem Code in betrieb genommen. Wie schon erwähnt kommuniziert der sensor mithilfe eines One-Wire Protocol. Der Pin der Bei uns die Daten überträgt ist der GPIO pin 0. Der Sensor verwendet hier noch die Library dht die wir auf dem ESP gespeichert haben.
```python
import time
import dht
from machine import Pin

# Configuration des OneWire Protocol ausgangsspin 
DHT_PIN = 0  # GPIO pin connected to the DHT sensor  #In unserem Fall Pin 0

# Initialisierung des DHT Sensors
sensor = dht.DHT22(Pin(DHT_PIN))

# Funktion um den Sensor auszulesen mit Exception Handeling
def read_sensor():
    try:
        # Erste Messung Starten
        sensor.measure()

        # Temperatur und Luftfeuchtigkeit ausmessen
        temperature = sensor.temperature()  # Temperature in Celsius
        humidity = sensor.humidity()        # Humidity in prozent

        # Als veranschaulichung in der Konsole Ausgeben
        print("Temperature: {}C".format(temperature))
        print("Humidity: {}%".format(humidity))
        
    except OSError as e:
        print("Failed to read DHT sensor:", e)

# Main loop
while True:
    read_sensor()  # Sensordaten lesen und ausgeben
    
```

## Luftqualität
# Finale Platine
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
## Inverter
Da wir nun genügend Zeit damit verbracht haben eine alternative softwarebasierte Lösung zu finden und ich leider nichts passendes, effizientes finden konnte, haben wir uns dazu entschieden einen Spannungsivertierer auf unsere Sensorik-platiene anzubringen. Dies ermöglicht uns nun Zeiteffizient und auch CPU schonend unser Motor PWM signal zu invertieren.

![Image](https://github.com/user-attachments/assets/5ff5ee6e-1391-4428-b1d5-b5fad6f1b997)

Wie man hier sehen kann konnten wir nun das Motor PWM singal erfoglreich Invertieren. Bei der Wahl des Spannungs - Invertierer haben wir uns aufgrund seiner schnellen Verfügbarkeit (Inventar des Magazin) für den SN7404N entschieden. 
**Weiter Infos zum Invertierer Folgen**
Da wir nun ein neues Element auf unserer Platine hatten mussten wir natürlich auch diese Überarbeiten

![Image](https://github.com/user-attachments/assets/c097cd23-2256-4d22-99de-ad9c09f14903)

Hier sieht man noch die überarbeitete Platine, wie man sehen kann haben wir unten links nun noch den SN70404N hinzugefügt

## SN7404N
Aufgrund seiner schnellen verfügbarkeit haben wir uns für diesen Invertierer entschieden. Das Datenblatt ist unter **Pfad zur PDF** zu finden. Dies ist ein Invertierer mit 6 invertier Kanälen. Jeweils A als eingang und Y als invertierter Ausgang.

![Image](https://github.com/user-attachments/assets/5aeffba3-ae25-47b6-baff-d453fc7d5540)   ![Image](https://github.com/user-attachments/assets/e36dfa20-7b42-43d2-a582-4c266205eb7e)

