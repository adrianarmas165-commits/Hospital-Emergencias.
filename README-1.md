# Sistema de Emergencias del Hospital

Programa en Java que organiza los turnos de atención de una sala de emergencias. Los pacientes se ordenan según la gravedad de su caso.

## 1. Problema

En una sala de emergencias no se atiende por orden de llegada, sino por gravedad. Cada paciente tiene un nivel de prioridad del 1 al 5:

- **5:** mayor gravedad (se atiende primero)
- **1:** menor gravedad

El programa debe:

1. Guardar a los pacientes ordenados por prioridad. Si dos tienen la misma, va primero el que llegó antes.
2. Atender al primer paciente de la lista.
3. Mostrar a los pacientes que llevan más tiempo de espera que un límite.
4. Ordenar la lista por tiempo de espera, de mayor a menor.
5. Rechazar datos inválidos (prioridad fuera de rango, nombres vacíos, minutos negativos).

## 2. Estructura de datos

Se usa una **lista doblemente enlazada** implementada manualmente, sin usar `LinkedList` de Java.

Cada nodo (`Nodo`) guarda:

- `dato`: el paciente
- `next`: el nodo de adelante
- `prev`: el nodo de atrás

La clase `ListaEmergencia` guarda tres datos: `head` (primer nodo), `tail` (último nodo) y `size` (cantidad de pacientes).

```
null <- [Luis (5)] <-> [Pedro (4)] <-> [Ana (2)] <-> [Marta (1)] -> null
         head                                          tail
```

Se eligió porque el paciente más grave siempre queda al inicio y atenderlo es rápido. Para insertar solo hay que cambiar los enlaces de los nodos vecinos, sin mover a los demás pacientes. Tener `tail` permite también recorrer la lista de atrás hacia adelante, lo que sirve para comprobar que los enlaces `prev` quedaron bien.

## 3. Archivos

| Archivo | Descripción |
|---------|-------------|
| `src/Paciente.java` | Datos del paciente: historial clínico, nombre, prioridad y minutos de espera. Valida los datos al crearlo |
| `src/Nodo.java` | Nodo de la lista |
| `src/ListaEmergencia.java` | La lista doblemente enlazada y sus métodos |
| `src/HospitalEmergency.java` | Clase principal: ejecuta los casos de prueba |

## 4. Métodos

| Método | Qué hace |
|--------|----------|
| `InsertarPorPrioridad(Paciente)` | Inserta al paciente según su prioridad (mayor prioridad al inicio). Lanza error si el paciente es `null` |
| `AtenderSiguientePaciente()` | Saca y devuelve al primer paciente; devuelve `null` si la lista está vacía |
| `PacientesEnEsperaCritica(int)` | Devuelve los nombres de los pacientes que esperan más del límite indicado. Lanza error si el límite es negativo |
| `ReordenarPorTiempoEspera()` | Ordena de mayor a menor tiempo de espera, rehaciendo los enlaces `prev` y `next` |
| `mostrar()` | Imprime la lista de principio a fin |
| `mostrarInverso()` | Imprime la lista de fin a principio |

### Validaciones de `Paciente`

El paciente no se crea (se lanza `IllegalArgumentException`) si:

- el historial clínico es `null` o está vacío
- el nombre es `null` o está vacío
- el nivel de prioridad no está entre 1 y 5
- los minutos de espera son negativos

## 5. Cómo ejecutarlo

Se necesita tener el JDK instalado. Desde la carpeta del proyecto:

```
mkdir -p out
javac -d out src/*.java
java -cp out HospitalEmergency
```

## 6. Pruebas realizadas

Todas se ejecutaron con `HospitalEmergency.java` (4 casos positivos y 3 negativos) y dieron el resultado esperado. Los pacientes de prueba son Ana (prioridad 2, 30 min), Luis (5, 10 min), Marta (1, 50 min) y Pedro (4, 20 min).

### Casos positivos

| N.º | Prueba | Resultado esperado | Resultado |
|-----|--------|--------------------|-----------|
| 1 | Insertar pacientes con prioridades 2, 5, 1, 4 y recorrer la lista hacia adelante y hacia atrás | Adelante: Luis, Pedro, Ana, Marta. Atrás: Marta, Ana, Pedro, Luis | Correcto |
| 2 | Atender a todos los pacientes | Orden de atención: Luis, Pedro, Ana, Marta; la lista queda vacía | Correcto |
| 3 | Pacientes con espera mayor a 25 min | `[Ana, Marta]` | Correcto |
| 4 | Reordenar por tiempo de espera y recorrer en ambos sentidos | Adelante: Marta, Ana, Pedro, Luis. Atrás: Luis, Pedro, Ana, Marta | Correcto |

### Casos negativos

| N.º | Prueba | Resultado esperado | Resultado |
|-----|--------|--------------------|-----------|
| 1 | Insertar un paciente `null` | Error controlado: "El paciente no puede ser null." | Correcto |
| 2 | Crear un paciente con prioridad 7 | Error controlado: "El nivel de prioridad debe estar entre 1 y 5." | Correcto |
| 3 | Atender con la lista vacía | Devuelve `null`: "no hay pacientes para atender" | Correcto |
