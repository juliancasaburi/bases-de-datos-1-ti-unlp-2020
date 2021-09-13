# Bases de Datos 1 2020 - Facultad de Informática - Universidad Nacional de La Plata

## Trabajo Integrador etapa Nro. 2 - Modelo Relacional

---

## Información del grupo

Número de grupo: 14  

<ins>Integrantes:</ins>  
Casaburi, Julián (14471/6)  
Velis, Tadeo Martín (15020/5)  
Ramirez, Sergio Daniel (16372/2)  

Ayudante asignado: Grigera, Julián

---

<div style="page-break-after: always; visibility: hidden"> 
    \pagebreak 
</div>

# Pautas de resolución

Realizar la transformación al modelo relacional del modelo E/R realizado en la etapa Nro. 1, con las siguientes consideraciones:

* ​Todas​ las entidades y relaciones ​deben ser pasadas a relaciones del modelo relacional​, independientemente de su cardinalidad.
* En el caso de las generalizaciones y especializaciones aplicar alguno de los criterios vistos en la teoría. Explicar su decisión.
* ​Para cada relación del modelo relacional marcar la clave primaria

---

<div style="page-break-after: always; visibility: hidden"> 
    \pagebreak 
</div>

# Diagrama de la etapa 1 - Modelo Conceptual (E/R)

<p align="center">
    <img src="grupo14-ti-etapa1.svg" alt="TI E1 E/R"/>
</p>

---

<div style="page-break-after: always; visibility: hidden"> 
    \pagebreak 
</div>

# Etapa 2 - Transformación al modelo relacional

SEDE(**<ins>nombre_sede</ins>**, direccion)  

POSEE(**<ins>numero_cancha</ins>**, nombre_sede)  

CANCHA(**<ins>numero_cancha</ins>**, descripcion)  

TIENE_DISPONIBILIDAD(**<ins>numero_cancha, fecha, hora_inicio</ins>**)  

DISPONIBILIDAD(**<ins>fecha, hora_inicio</ins>**)  

DE_CANCHA_DISPONIBILIDAD(**<ins>numero_reserva</ins>**, numero_cancha, fecha, hora_inicio)  

RESERVA(**<ins>numero_reserva</ins>**)  

# Jerarquia especialización estrategia 1 "Una relación para el conjunto de entidades de nivel más alto"
USUARIO(**<ins>email</ins>**, nombre, contraseña, tipo_usuario, puntos, promedio_goles)  

---

# Jerarquia generalización estrategia 2 "Una relación para cada conjunto de entidades del nivel más bajo"

PAGO_TARJETA(**<ins>numero_pago</ins>**, fecha, monto, medio_de_pago)  

PAGO_EFECTIVO(**<ins>numero_pago</ins>**, fecha, monto)  

REALIZA_PAGO_TARJETA(**<ins>numero_pago</ins>**, email, numero_reserva)  

TIENE_PAGO_EFECTIVO(**<ins>numero_pago</ins>**, numero_reserva)  

---

ASOCIA(**<ins>email, numero_reserva</ins>**, resultado, goles)  

RESERVO(**<ins>numero_reserva</ins>**, email)  

CATEGORIA(**<ins>nombre_categoria</ins>**)  

TIENE_CATEGORIA(**<ins>email, nombre_categoria</ins>**)  

CATEGORIA_DESDE(**<ins>dia, mes, año, hora, email, nombre_categoria</ins>**)  

FECHA(**<ins>dia, mes, año, hora</ins>**)  

--- 

<div style="page-break-after: always; visibility: hidden"> 
    \pagebreak 
</div>

# Estrategias utilizadas en la transformación del modelo E/R al modelo Relacional

1. **Especialización Usuario**

    Se aplicó la **primera estrategia**:  
    **Una relación para el conjunto de entidades del nivel más alto**

    Como resultado de la transformación, en el modelo relacional, existe la relación USUARIO, con los atributos de la especialización JUGADOR que pueden ser nulos.
    Además, se agrega el atributo 'discriminante' tipo_usuario.

    USUARIO(**<ins>email</ins>**, nombre, contraseña, puntos, promedio_goles, tipo_usuario)

    ---

    **Consideraciones:**

    - El objetivo de la aplicación es permitir a los usuarios reservar canchas, y mostrar información relacionada a los partidos de las reservas. Pensando en cambios futuros, no encontramos muchas más posibles entidades hijas de USUARIO (más que algún posible administrador o algún rol similar).

    - La mayoría de las entidades Usuario están también en Jugador (mismo **<ins>email</ins>**), dado que la mayoría de los usuarios jugarán al menos un partido de alguna reserva.

    **Ventajas:**

    - Es la solución más simple desde el punto de vista de las relaciones resultantes.

    - Se facilitan las consultas. Se ahorran Joins entre JUGADOR e USUARIO, que habría que realizar si se utilizara la **segunda estrategia** **("Una relación para el conjunto de entidades de nivel más alto, y una relación para cada conjunto de entidades del nivel más bajo")**

    **Desventajas:**

    - Esta estrategia puede generar una gran cantidad de valores nulos para los atributos que se aplican sólo a las subentidades de la jerarquía. No afecta en este caso, dado que solo existen 2 atributos que podrían ser nulos (puntos y promedio_goles), y casi todos los usuarios son del tipo Jugador.

    ---

    <div style="page-break-after: always; visibility: hidden"> 
        \pagebreak 
    </div>


2. **Generalización Pago**

    Se aplicó la **segunda estrategia**:  
    **Una relación para cada conjunto de entidades del nivel más bajo**

    Como resultado de la transformación, en el modelo relacional, existen todas las subentidades (que existían en el modelo E/R) de la jerarquía en relaciones:

    PAGO_TARJETA(**<ins>numero_pago</ins>**, fecha, monto, medio_de_pago)  

    PAGO_EFECTIVO(**<ins>numero_pago</ins>**, fecha, monto)  

    ---

    **Ventajas:**

    - El esquema resultante es simple. Se evita tener que realizar dos operaciones de inserción y eliminación, además de tener que realizar una operación de JOIN en las búsquedas como es el caso de la **tercera estrategia** **(Una relación para el conjunto de entidades de nivel más alto, y una relación para cada conjunto de entidades del nivel más bajo)**.

    - En las consultas se trabaja con un conjunto de tuplas más chico, para los casos específicos (consultas sobre pagos en efectivo para una reserva y consultas sobre pagos de parte de reserva de un jugador). Caso contrario a la primera estrategia (**Una relación para el conjunto de entidades de nivel más alto**).

    **Desventajas:**

    - Si el número de atributos de la entidad padre (comunes a todas las entidades) es grande, su duplicación en el esquema de cada entidad hija no se justifica. No es el caso, ya que además de la clave primaria (**<ins>número_pago</ins>**), solo existen otros 2 atributos (fecha y monto).