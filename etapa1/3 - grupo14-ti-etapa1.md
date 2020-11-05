# Bases de Datos 1 2020 - Facultad de Informática - Universidad Nacional de La Plata

## Trabajo Integrador etapa Nro. 1 - Modelo de Entidades y Relaciones

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

## Ejercicio Fútbol 5 - Consigna

<ins>Fútbol 5</ins>

```
Se quiere crear una aplicación para reservar canchas de fútbol 5 online para un complejo con varias sedes. De cada sede se conoce la dirección, el nombre y cada una de las canchas de las que dispone. De cada cancha se conoce el número y una descripción.

Para poder hacer reservas, los clientes deben registrar una cuenta, de la cual se guarda correo electrónico, nombre, y contraseña. Para reservar una cancha, ingresa una fecha y horario, y con esta información, la aplicación le ofrece las canchas disponibles en las diferentes sedes y el cliente selecciona una (por ejemplo - cancha 1 / sede de calle 45). La reserva queda registrada con la información de la búsqueda (fecha, horario y cancha) a nombre del cliente, pero también pueden asociarse los jugadores, siempre y cuando ya tengan una cuenta en el sistema. Tener en cuenta que quien hace la reserva no necesariamente juega el partido.

Dentro de una reserva, se guarda para cada jugador asociado el resultado del partido (es decir, si ganó, perdió o empató), y el número de goles que hizo. En toda reserva se juega un partido.  
Según el promedio de goles y los puntos obtenidos (3 por partido ganado, 1 por empatado, 0 por perdido), a cada jugador registrado se le asigna una categoría. Se debe registrar en qué fecha cambió de categoría, de las cuales puede subir o bajar en cualquier momento.

El pago de la cancha se realiza después del partido, y la aplicación permite que cada jugador registrado realice el pago de su parte directamente desde la aplicación. Del pago se debe guardar la fecha, el monto, y el medio de pago, que puede ser con tarjeta de débito o crédito. Si quedó un saldo por pagar, se puede dejar el efectivo en el momento, cosa que también debe quedar registrado (y también se registra la fecha y monto, es un único pago para todos, no se separa en partes ni se conoce quién lo realizó).
```

---

<div style="page-break-after: always; visibility: hidden"> 
    \pagebreak 
</div>

# Modelo Conceptual (E/R)

## Notas

La aplicación utilizada para realizar el diagrama es [draw.io](https://app.diagrams.net/)

---

## Aclaraciones

### Duración de una reserva/partido
- Asumimos que todas las reservas tienen la misma duración estándar (en minutos), a partir de la hora de inicio (atributo `hora_inicio` de la entidad `DISPONIBILIDAD` en el modelo E/R).  
Esta se corresponde con la duración de un partido de fútbol 5.

### Categorías de Jugador
- Decidimos modelar un historial completo de las categorías del jugador. También se podría haber considerado únicamente la última fecha en la que un jugador obtuvo cada categoría.  

    El enunciado resulta un tanto ambiguo en el siguiente fragmento:

    ```
    Se debe registrar en qué fecha cambió de categoría, de las cuales puede subir o bajar en cualquier momento.
    ```

### Puntos y promedios de goles
- Decidimos agregar la cantidad de puntos (`puntos`) y el promedio de goles (`promedio_goles`) como atributos de la entidad `JUGADOR`.

    Según el enunciado:

    ```
    Según el promedio de goles y los puntos obtenidos (3 por partido ganado, 1 por empatado, 0 por perdido), a cada jugador registrado se le asigna una categoría.
    ```

   Si bien la categoría puede ser calculada a través de la relación `ASOCIA`, estos datos no tienen mucho recálculo, y es por esta razón que pueden ponerse como atributos almacenados de la entidad `JUGADOR`, en lugar de utilizar atributos derivados a través de `ASOCIA`.

### Método de pago
- Decidimos modelar el pago con tarjeta de débito y crédito mediante el atributo `medio_de_pago` de la entidad `PAGO_TARJETA`. Al ver que no se necesita almacenar información adicional o realizar funcionalidades específicas a cada medio de pago, no encontramos la necesidad de agregar una nueva entidad y complejizar el modelo.

## Diagrama

<p align="center">
    <img src="4 - grupo14-ti-etapa1.svg" alt="TI E1 E/R"/>
</p>
