# Portal TechAndes

Portal interno de ventas de TechAndes. Está hecho con Flask y se conecta a la base MySQL `techandes`.

La interfaz usa una paleta turquesa y muestra el banner de TechAndes en el encabezado del portal y en la pantalla de ingreso.

## Requisitos

- Python 3.10 o superior
- MySQL 8 en ejecución (en este equipo, el servicio MySQL 26.7)

## Base de datos

En MySQL Workbench, conectado como `root`, ejecute los scripts en este orden:

1. `sql/techandes-diego.sql` — crea la base y carga categorías, empleados, clientes, productos y pedidos. Al volver a ejecutarlo, reemplaza los datos de las tablas.
2. `sql/01_usuarios.sql` — crea la tabla `usuarios` y los 8 accesos del portal.
3. `sql/02_permisos.sql` — crea el usuario `techandes_api` con permiso de leer y modificar `techandes`.

`sql/01_usuarios.sql` también reemplaza la tabla `usuarios`; ejecútelo cuando esa tabla falte o cuando quiera restablecer las cuentas. El archivo `Tablas techandes.sql` es el volcado original.

## Instalación del portal

Abra PowerShell en la carpeta que contiene este README y ejecute:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

El último comando instala todas las dependencias del proyecto, incluido `python-dotenv` (el módulo `dotenv` que usa `app.py`). Si aparece un error `ModuleNotFoundError`, active `.venv` y vuelva a ejecutar `python -m pip install -r requirements.txt` desde esta carpeta. Use `python -m pip` para instalar con el mismo intérprete que luego ejecutará la aplicación.

Si `.venv` ya existe, no es necesario volver a crearla. En cada terminal nueva, actívela antes de ejecutar el portal.

Configure un archivo `.env` en esta misma carpeta. Si no existe, créelo con el Bloc de notas y complete los datos de conexión:

```dotenv
DB_HOST=localhost
DB_PORT=3306
DB_NAME=techandes
DB_USER=techandes_api
DB_PASSWORD=reemplazar_por_la_clave_configurada_en_MySQL
```

Reemplace el valor de ejemplo de `DB_PASSWORD` por la clave definida para `techandes_api` en `sql/02_permisos.sql`. No comparta ni suba `.env`.

## Ejecutar

```powershell
.\.venv\Scripts\Activate.ps1
python app.py
```

Abra http://127.0.0.1:5000 . Sin sesión, cualquier página lleva a `/login`. Para apagar el servidor, pulse **Ctrl + C** en la terminal.

## Usuarios de prueba

| Usuario | Empleado | Rol | Contraseña |
| --- | --- | --- | --- |
| mprieto | Martha Lucía Prieto Gaitán, gerente | ADMIN | Admin2026* |
| hbustos | Hernán Bustos Arango, supervisor | ADMIN | Admin2026* |
| crobayo | Claudia Robayo Méndez, supervisor | ADMIN | Admin2026* |
| klozada | Kevin Lozada Perdomo, asesor | VENDEDOR | Ventas2026* |
| ytafur | Yesica Tafur Bonilla, asesor | VENDEDOR | Ventas2026* |
| boviedo | Brayan Oviedo Calderón, asesor | VENDEDOR | Ventas2026* |
| lmurcia | Lorena Murcia Vanegas, asesor | VENDEDOR | Ventas2026* |
| fcuellar | Fabián Cuéllar Yepes, asesor | VENDEDOR | Ventas2026* |

## Módulos

- **Inicio:** ventas efectivas, pedidos pendientes, clientes, últimos pedidos y productos por reabastecer. Si algún producto activo tiene menos de 10 unidades, aparece la alerta «Próximo a solicitar unidades». El vendedor ve sus propias ventas; el administrador ve accesos rápidos.
- **Productos:** catálogo con búsqueda. Solo el administrador crea, edita y elimina.
- **Clientes:** búsqueda, registro e historial. El correo del taller `andres.gomez1@correo.com` ya existe.
- **Nueva venta:** pedido en una transacción. Descuenta stock y guarda el precio del momento.
- **Pedidos:** consulta y cambio de estado. Al cancelar, las unidades vuelven al inventario.
- **Informes:** solo para administradores. Los usuarios `VENDEDOR` no ven el enlace y reciben un error `403` si intentan abrir `/informes/` directamente. Muestra ventas por categoría, ranking de vendedores, Top 5 de productos y clientes sin compras. En el Top 5, los botones **Unidades** y **Total** ordenan de mayor a menor; **Total** es la suma de unidades vendidas por su precio unitario.

## Cifras del volcado

Con los datos cargados, antes de registrar ventas nuevas:

- Ventas efectivas: $ 66.538.300
- Pedidos pendientes: 2
- Productos con stock menor a 10: 8
- Kevin Lozada (`klozada`): 12 pedidos por $ 20.828.100
- Primer puesto del ranking: Brayan Oviedo Calderón, $ 21.491.600

El volcado incluye un cliente extra, Esteban Salas Posada (Ibagué, documento 1130504126). Por eso el inicio muestra **41 clientes** y el filtro Ibagué devuelve **11**, no los 40 y 10 del guion original. Ese cliente no tiene pedidos, así que las ventas no cambian. Si el profesor pide las cifras exactas del taller, borre esa fila en Workbench y vuelva a abrir Inicio.

## Prueba de una venta

Cliente Tomás Ortega Beltrán, 2 unidades del Mouse Logitech M280 (id 16, $ 69.000) y 1 SSD Kingston NV2 1TB (id 23, $ 289.000). El pedido queda en $ 427.000. Una venta con 50 portátiles ASUS TUF Gaming F15 (id 3, stock 5) se rechaza y no crea el pedido.
