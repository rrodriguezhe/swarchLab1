# Laboratorio 1 — Diseño Arquitectónico

Raúl Felipe Rodríguez Hernández

## Estructura del sistema

![Diagrama de arquitectura](images/architectureDiagram.png)

El sistema sigue una arquitectura monolítica compuesta por dos contenedores Docker: el monolito (`swarch-mo`), desarrollado en Python con Flask y organizado internamente en capas (`templates > controllers > services > repositories > models`), y la base de datos (`swarch-db`), que corre MySQL 8.0. La comunicación entre el navegador y el monolito se hace vía HTTP, mientras que el monolito se conecta a la base de datos a través de la variable de entorno `DATABASE_URL`.

## Propiedades del sistema

1. **Bajo acoplamiento entre la base de datos y el código**
Debido a que la base de datos y el código están en dos contenedores diferentes, comunicándose únicamente a través de la red (vía `DATABASE_URL`), es posible cambiar el motor de base de datos, escalarlo o moverlo a un host diferente sin tener que tocar el código del monolito en ningún momento.

2. **Separación de responsabilidades**
La arquitectura por capas utilizada en el sistema garantiza que cada uno de estos elementos tenga una única responsabilidad definida, facilitando así entender, modificar o reemplazar una capa sin afectar a otras de manera directa.

3. **Portabilidad sencilla**
El uso de Docker permite que el sistema sea portable a casi cualquier sistema operativo, o por lo menos los más comunes (Windows, Linux y macOS). Además, asegura que el proyecto se pueda trabajar en diferentes máquinas sin importar el entorno local de cada desarrollador.

4. **Consistencia transaccional**
Usar un motor de base de datos relacional como MySQL asegura que los principios ACID (Atomicidad, Consistencia, Aislamiento y Durabilidad) se cumplan en cada operación sobre los datos.

5. **Facilidad de desarrollo**
Flask es un framework de desarrollo bastante sencillo, fácil de usar y de entender, lo cual es importante en caso de que más personas se quisieran unir al equipo de desarrollo en el futuro.

## Cómo ejecutar el sistema

### Prerrequisitos

- Tener instalado [Docker](https://www.docker.com/) y Docker Compose (viene incluido con Docker Desktop en Windows/macOS).
- Tener [Git](https://git-scm.com/) instalado para clonar el repositorio.

### Pasos

1. **Clonar el repositorio**
```bash
   git clone https://github.com/rrodriguezhe/swarchLab1.git
   cd swarchLab1
```

2. **Construir y levantar los contenedores**
```bash
   docker-compose up --build
```
   Este comando construye la imagen del monolito, descarga la imagen de MySQL, y levanta ambos contenedores (`swarch-mo` y `swarch-db`). La primera vez puede tardar varios minutos mientras se instalan las dependencias.

3. **Esperar a que ambos servicios estén listos**
   En la terminal verás los logs de ambos contenedores. Espera a ver un mensaje similar a:

```
   swarch-mo-1  |  * Running on all addresses (0.0.0.0)
   swarch-mo-1  |  * Running on http://127.0.0.1:5000
```
   Esto indica que el servidor Flask ya está activo y aceptando conexiones.

4. **Abrir el sistema en el navegador**
   Ve a [http://localhost:8080](http://localhost:8080). Deberías ver el formulario "Gestor de Calificaciones".

5. **Usar el sistema**
   - Completa el formulario con nombre del estudiante, asignatura y calificación, y haz clic en "Agregar" para crear un nuevo registro.
   - Cada registro creado aparece listado debajo del formulario.
   - Haz clic en "Eliminar" junto a cualquier registro para borrarlo.

6. **(Opcional) Verificar los datos directamente en la base de datos**
```bash
   docker exec -it swarch-swarch-db-1 sh
   mysql -u root -p
```
   Contraseña: `123`
```sql
   USE swarch-db;
   SELECT * FROM grades;
```

7. **Detener el sistema**
   Presiona `Ctrl+C` en la terminal donde corre `docker-compose`, o desde otra terminal en la carpeta del proyecto:
```bash
   docker-compose down
```
