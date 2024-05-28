El código se basa en varios tutoriales de youtube:
https://youtu.be/0yO3gqeoMJg?si=7pRGXnme_382ySYB
https://youtu.be/IPTwxFj6TqU?si=BZKo9Lzk9KVUYVD5

Aunque funciona, es dificil que sea consistente, se considera que puede ser más fácil hacerlo con un Pulse Sensor
https://youtu.be/RkLuvv_fR6o?si=kqQx8UOlzGWsqdjM

Sin embargo, igualmente les comparto mi trabajo con el módulo.

```arduino
// boceto de codigo para ecg AD8232 con debounce basico
// es importante que los electrodos esten bien pegados al cuerpo, con mucha presion
// GND al GND
// 3.3v al 3.3v
// Output a A0
// LO+ a pin 10
// LO- a pin 11
// por AndresMartinM 2024
// en Arduino Mega 2560 - IDE 2.2.1
// usando el modulo de electro-cardio-grama AD8232 Heart Monitor


//variables
int valor = 0; // se usara para guardar el valor del ecg
int umbral = 350; // umbral para filtrar los picos, se debe ajustar
int last = 0; // nos indicara la ultima vez que encontamos un pico
int ahora = 0; // nos dira cuantos milisegundos han transcurrido
int bpt = 0; // contador de latidos (picos)
int bpm = 0; // contador de latidos por minuto
int reseg = 12; // resolucion del muestreo en segundos, se puede ajustar

// cofiguramos el serial USB a 9600 baud, y los pines 10 y 11 como entrada
void setup() {
  Serial.begin(9600);
  pinMode(10, INPUT);
  pinMode(11, INPUT);
}

// comenzamos la rutina
void loop() {
  if (digitalRead(10) == 1 || digitalRead(11) == 1) { // verificamos que el modulo funcione
    Serial.println('alerta'); // si no se imprime una alerta en la consola
  } else {
    //Serial.println(analogRead(A0));
    valor = analogRead(A0); // guardamos el valor del ecg
  }
  if (valor > 400 && valor < 700 && (ahora - last) > 400){ // filtramos los valores para identificar un pico
    bpt += 1; // sumamos un latido al contador
    last = ahora; // guardamos el momento
    //Serial.println(bpt);
  }
  // cada reseg segundos
  if (ahora == reseg*1000){ // si han pasado reseg * 1000 milisegundos
    bpm = bpt * (60/reseg); // calculamos los latidos por minuto
    Serial.println(bpm); // imprimimos los latidos por minuto en la consola
    bpt = 0; // reseteamos el contador de latidos
    ahora = 0; // reseteamos el contador de tiempo
    last = 0; // reseteamos el momento
  }
  delay(1); // el loop se realiza cada 1 milisegundo
  ahora += 1; // sumamos un milisegundo al contador de tiempo
}

// en el caso de no estar bien puestos los electrodos en la consola se llena del valor 29793
// es probable que se pueda resolver 
```

Ojalá les funcione
Saludos ✌️
