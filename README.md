### Información del Equipo
- **Integrantes:**
  - Francisca Guzman - Rol 1
  - Martina Sanchez - Rol 2
  - Vitalia Simon - Rol 3

  
- **ODS Seleccionado:** ODS 12: Producción y Consumo Responsables
- **Problema a resolver:** Los vasos de café desechables de cartón pueden ocupar hasta un más de un 60% de volumen en aire por su forma cónica y hueca, lo que hace que  los basureros se llenen fácilmente en la universidad en horarios punta y forzando retiros constantes de bolsas plásticas que tienen más aire que residuos. 

### Descripción del Proyecto
Un aparato manual que se pone junto a los basureros, con el propósito de aplastar los vasos de café para que queden planos para poder botarlos, así el basurero se llena realmente de residuos, menos aire, y se usan menos bolsas del plastico.  
![Uploading image.png…]()



### Estado del Proyecto
- **Versión actual:** v3.0
- **Última actualización:** 30-10-2025
- **Estado:** Borrador


---

##  Estructura del Repositorio
```
├── hardware/          # Circuitos, esquemas y BOM
├── software/          # Código Arduino y librerías
├── diseno_3d/        # Modelos Fusion 360 y renders
├── testing/          # Validación con usuarios
├── documentacion/    # Reportes y presentación
└── iteraciones/      # Historial de versiones
```

---

##  Inicio Rápido

### Requisitos Previos
- Arduino IDE 2.x
- Fusion 360
- [Otras herramientas necesarias]

### Instalación
1. Clonar el repositorio
2. Instalar librerías (ver `software/librerias/`)
3. Cargar código en ESP32/Arduino
4. Código Arduino:
#include <HX711.h>

HX711 balanza;

const byte ROJO = 8;
const byte AMARILLO = 9;
const byte VERDE = 10;

// Conservamos tu calibracion
const float FACTOR = 391.6224;

float cero = 0;
bool lista = false;

void apagarLuces() {
  digitalWrite(ROJO, LOW);
  digitalWrite(AMARILLO, LOW);
  digitalWrite(VERDE, LOW);
}

bool medir(float &resultado) {
  float suma = 0;

  for (int i = 0; i < 5; i++) {
    if (!balanza.wait_ready_timeout(1000)) {
      apagarLuces();
      Serial.println("ERROR: HX711 no responde.");
      return false;
    }

    long dato = balanza.read();

    if (dato == -1) {
      apagarLuces();
      Serial.println("ERROR de lectura.");
      return false;
    }

    suma += dato;
    delay(150);
  }

  resultado = suma / 5.0;
  return true;
}

// Poner en cero automaticamente o mediante 't'
void hacerCero() {
  lista = false;
  apagarLuces();

  Serial.println("Realizando tara...");

  float lectura;

  if (medir(lectura)) {
    cero = lectura;
    lista = true;
    Serial.println("CERO LISTO.");
    Serial.println("Semaforo funcionando.");
  } else {
    Serial.println("ERROR: Reinicia con la plataforma vacia.");
  }
}

void setup() {
  Serial.begin(9600);

  pinMode(ROJO, OUTPUT);
  pinMode(AMARILLO, OUTPUT);
  pinMode(VERDE, OUTPUT);

  apagarLuces();

  // DT al pin 2, SCK al pin 3
  balanza.begin(2, 3);

  // Esperar a que el sensor se estabilice
  delay(1500);

  // AHORA LA TARA ES AUTOMATICA
  hacerCero();

  Serial.println("Sistema iniciado.");
  Serial.println("Comando t: nueva tara.");
  Serial.println("Comando p: consultar peso.");
}

void loop() {
  bool mostrarPeso = false;

  // Comandos opcionales cuando hay computador
  while (Serial.available()) {
    char comando = Serial.read();

    if (comando == 't' || comando == 'T') {
      hacerCero();
    }

    if (comando == 'p' || comando == 'P') {
      mostrarPeso = true;
    }
  }

  if (!lista) {
    apagarLuces();
    return;
  }

  float lectura;

  if (!medir(lectura)) {
    return;
  }

  float peso = (lectura - cero) / FACTOR;

  digitalWrite(AMARILLO, LOW);

  if (peso > 32.0) {
    // MAYOR A 32 GRAMOS: ROJO
    digitalWrite(VERDE, LOW);
    digitalWrite(ROJO, HIGH);

  } else if (peso >= 2.0) {
    // ENTRE 2 Y 32 GRAMOS: VERDE
    digitalWrite(ROJO, LOW);
    digitalWrite(VERDE, HIGH);

  } else {
    // MENOS DE 2 GRAMOS: APAGADO
    apagarLuces();
  }

  if (mostrarPeso) {
    Serial.print("Peso: ");
    Serial.print(peso, 2);
    Serial.println(" g");
  }
}

 Modelo 3d subido.
Ambos hechos con ayuda de inteligencia artificial***

##  Checklist de Entrega

### Hardware ✓
- [ ] Esquema del circuito (Fritzing/Wokwi)
- [ ] BOM completo
- [ ] Fotos de alta resolución

### Software ✓
- [ ] Código comentado
- [ ] Librerías documentadas
- [ ] Manual de instalación

### Diseño 3D ✓
- [ ] Archivos Fusion 360 (.f3d) con TODOS los componentes
- [ ] Renders de alta calidad (1+ ángulos)
- [ ] Planos técnicos

### Testing 
- [ ] Reporte de testing con usuarios (mín. 5)
- [ ] Evidencias fotográficas/videos
- [ ] Datos cuantitativos

### Documentación 
- [ ] Reporte final (máx. 15 páginas)
- [ ] Presentación (10 min)

---


---

##  Licencia
Este proyecto es desarrollado como parte del curso TEI201 - Facultad de Ingenieria y Ciencias - Universidad Adolfo Ibañez
