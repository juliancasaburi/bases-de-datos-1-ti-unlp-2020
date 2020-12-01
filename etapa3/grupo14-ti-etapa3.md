# Bases de Datos 1 2020 - Facultad de Informática - Universidad Nacional de La Plata

## Trabajo Integrador etapa Nro. 3 - Consultas en Álgebra Relacional

---

## Información del grupo

Número de grupo: 14  

<ins>Integrantes:</ins>  
Casaburi, Julián (14471/6)  
Velis, Tadeo Martín (15020/5)  
Ramirez, Sergio Daniel (16372/2)  

Ayudante asignado: Grigera, Julián

---

<div style="page-break-after: always; visibility: hidden"></div>

# Etapa 1 (Modelo E/R)

<p align="center">
    <img src="grupo14-ti-etapa1.svg" alt="TI E1 E/R"/>
</p>

---

<div style="page-break-after: always; visibility: hidden"></div>

# Etapa 2 (Transformación del modelo E/R al modelo relacional)

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

<div style="page-break-after: always; visibility: hidden"></div>

# Etapa 3 - Consultas en Álgebra Relacional

## Resolver los siguientes ejercicios sobre el modelo relacional entregado en la etapa 2, aplicando las operaciones del álgebra relacional vistas en la materia

### Fundamentales

- Selección ​ σ

- Proyección ​ π

- Producto Cartesiano ​ X

- Renombre ​ ρ

- Unión ​U

- Diferencia o Resta ​ –

### Adicionales

- Intersección ​ ∩

- Producto Theta ​ |X|θ

- Producto Natural ​ |X|

- División ​%

- Asignación ​ ← ​

---

<div style="page-break-after: always; visibility: hidden"></div>

## Fútbol 5

1. Listar las canchas que no se reservaron en un mes determinado.

    Se podría realizar la siguiente consulta si la fecha del modelo estuviera particionada en 3 atributos:

    - `dia`
    - `mes`
    - `año`

    ```
    CANCHAS_RESERVADAS_MES ←
    (
        CANCHA
        ⨝
        (
            π numero_cancha (σ mes = 'octubre' (DE_CANCHA_DISPONIBILIDAD))
        )
    )

    CANCHA - CANCHAS_RESERVADAS_MES
    ```

    ---

    <div style="page-break-after: always; visibility: hidden"></div>

2. Listar el email de los usuarios que jugaron al menos una vez en cada una de las canchas del complejo.

    ```
    SEDE_A_UTILIZAR ← σ nombre_sede = 'Sede uno' (SEDE)


    JUGADORES_CANCHAS ← π email, numero_cancha
    (
        (
            π email (USUARIO)
            ⨝
            π email, numero_reserva (ASOCIA)
        )
        ⨝
        (
            π numero_reserva, numero_cancha
            (
                DE_CANCHA_DISPONIBILIDAD
            )
        )
    )

    EMAIL_JUGADORES_TODAS_LAS_CANCHAS_SEDE_A_UTILIZAR ← JUGADORES_CANCHAS % π numero_cancha
    (
        POSEE |X| POSEE.nombre_sede = SEDE_A_UTILIZAR.nombre_sede
    )

    EMAIL_JUGADORES_TODAS_LAS_CANCHAS_SEDE_A_UTILIZAR
    ```

    ---

    <div style="page-break-after: always; visibility: hidden"></div>

3. Listar el email de los usuarios que realizaron pagos sólo con tarjeta de débito.

    ```
    EMAIL_USUARIOS_PAGARON_TARJETA_NO_DEBITO ←
    π email
    (
        π email
        (
            σ tipo_usuario = 'JUGADOR' (USUARIO)
        )
        ⨝
        (
            π numero_pago, email (REALIZA_PAGO_TARJETA)
            ⨝
            π numero_pago (
                σ medio_de_pago ≠ 'DEBITO' (PAGO_TARJETA)
            )
        )
    )

    EMAIL_USUARIOS_PAGARON_TARJETA_DEBITO ←
    π email
    (
        π email
        (
            σ tipo_usuario = 'JUGADOR' (USUARIO)
        )
        ⨝
        (
            π numero_pago, email (REALIZA_PAGO_TARJETA)
            ⨝
            π numero_pago (
                σ medio_de_pago = 'DEBITO' (PAGO_TARJETA)
            )
        )
    )

    EMAIL_USUARIOS_PAGARON_SOLO_TARJETA_DEBITO ← EMAIL_USUARIOS_PAGARON_TARJETA_DEBITO - EMAIL_USUARIOS_PAGARON_TARJETA_NO_DEBITO

    EMAIL_USUARIOS_PAGARON_SOLO_TARJETA_DEBITO
    ```

    ---

    <div style="page-break-after: always; visibility: hidden"></div>

4. Listar el email de los usuarios que pagaron su parte en todos los partidos que jugaron.

    ```
    JUGADORES_PARTIDOS ←
    π email
    (
        (
            π email
            (
                σ tipo_usuario = 'JUGADOR' (USUARIO)
            )
            ⨝
            π email, numero_reserva (ASOCIA)
        )
    )

    JUGADORES_NO_PAGARON_ALGUNA_RESERVA ←
    π email
    (
        π email, numero_reserva
        (
            (
                π email
                (
                    σ tipo_usuario = 'JUGADOR' (USUARIO)
                )
                ⨝
                π email, numero_reserva (ASOCIA)
            )
        )

        -

        π email, numero_reserva
        (
            (
                π email
                (
                    σ tipo_usuario = 'JUGADOR' (USUARIO)
                )
                ⨝
                π email, numero_reserva (ASOCIA)
            )
            ⨝
            (
                (REALIZA_PAGO_TARJETA)
                ⨝
                π numero_pago (PAGO_TARJETA)
            )
        )
    )

    JUGADORES_PARTIDOS - JUGADORES_NO_PAGARON_ALGUNA_RESERVA
    ```
    
    ---

    <div style="page-break-after: always; visibility: hidden"></div>
    
5. Listar el email de los usuarios que reservaron y además pagaron su parte al menos una vez.

    ```
    π email
    (
        (
            (
                π email
                (
                    σ tipo_usuario ='JUGADOR' (USUARIO)
                )
                ⨝
                π email (RESERVO)
            )
        )
        ⨝
        REALIZA_PAGO_TARJETA
    )
    ```

    ---

    <div style="page-break-after: always; visibility: hidden"></div>
    
6. Listar el email de los usuarios con cuenta registrada que nunca pagaron su parte por medio de la app (pero que sí jugaron/fueron agregados a algún partido).

    ```
    EMAIL_USUARIOS_ASOCIADOS_RESERVA ←

    (
        π email
        (
            σ tipo_usuario = 'JUGADOR' (USUARIO)
        )
        ⨝
        π email (ASOCIA)
    )

    EMAIL_USUARIOS_PAGARON_PARTE ← 

    (
        EMAIL_USUARIOS_ASOCIADOS_RESERVA
        ⨝
        π email (REALIZA_PAGO_TARJETA)
    )

    EMAIL_USUARIOS_ASOCIADOS_RESERVA - EMAIL_USUARIOS_PAGARON_PARTE

    ```

    ---

    <div style="page-break-after: always; visibility: hidden"></div>
    
7. Listar el nombre de los jugadores que nunca cambiaron de categoría.


    ---

    <div style="page-break-after: always; visibility: hidden"></div>
    
8. Listar el nombre de los jugadores que hicieron goles en todos los partidos que jugaron.

    ```
    π nombre
    (
        π email, nombre
        (
            (
                π email, nombre
                (
                    σ tipo_usuario = 'JUGADOR' (USUARIO)
                )
                ⨝
                π email, numero_reserva
                (
                    σ goles > 0 (ASOCIA)
                )
            )
        )
        
        -
        
        π email, nombre
        (
            (
                π email, nombre
                (
                    σ tipo_usuario='JUGADOR' (USUARIO)
                )
                ⨝
                π email, numero_reserva
                (
                    σ goles = 0 (ASOCIA)
                )
            )
        )
    )
    ```

    ---

    <div style="page-break-after: always; visibility: hidden"></div>
    
9. Listar los nombres de las categorías que tuvieron movimientos en la última semana.

    Tomando la fecha **23/11/2020**

    ```
    π nombre_categoria
    (
        σ anio = 2020 AND mes = 11 AND dia >= 16 AND dia < 23 (CATEGORIA_DESDE)
    )


    ```
    
    ---

    <div style="page-break-after: always; visibility: hidden"></div>
    
10. Listar nombre y email de los jugadores que no ganaron ningún partido.

    ```
    JUGADORES_GANARON_PARTIDO ← π nombre, email
    (
        (
            π nombre, email
            (
                σ tipo_usuario = 'JUGADOR' (USUARIO)
            )
            ⨝
            π email
            (
                σ resultado = 'VICTORIA' (ASOCIA)
            )
        )
    )

    π nombre, email ((σ tipo_usuario = 'JUGADOR' (USUARIO)) - JUGADORES_GANARON_PARTIDO)
    ```
    
    ---

    <div style="page-break-after: always; visibility: hidden"></div>
    
11. Listar nombre y email de los jugadores que cambiaron al menos 2 veces de categoría en el último mes.