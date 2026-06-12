 🍽️ Sistema de Restaurante — Documentación de Estudio

## ¿Qué es este proyecto?

Sistema web para la gestión de un restaurante. Permite manejar pedidos de mesa y para llevar, controlar pagos, administrar la carta (platos y entradas), registrar asistencias del personal y gestionar usuarios con distintos roles.

Está construido **sin frameworks** — PHP puro con patrón **MVC personalizado**.

---

## 🗂️ Estructura del proyecto

```
Proyecto-Restaurante2.0/
├── app/
│   ├── config/
│   │   └── config.php         ← Constantes: BD, BASE_URL
│   ├── core/
│   │   ├── App.php            ← Punto de arranque (inicia sesión + llama al Router)
│   │   ├── Router.php         ← Lee la URL y decide qué controlador/método ejecutar
│   │   ├── Controller.php     ← Clase base con helpers: view(), soloAdmin(), etc.
│   │   └── Database.php       ← Conexión PDO (Singleton)
│   ├── controllers/           ← Reciben la petición y coordinan modelo + vista
│   ├── models/                ← Hablan con la base de datos (SQL con PDO)
│   ├── views/                 ← HTML + PHP que se muestra al usuario
│   └── index.php              ← Único punto de entrada de la app
├── public/
│   ├── css/                   ← Estilos
│   ├── js/                    ← JavaScript del cliente
│   └── uploads/comprobantes/  ← Fotos de comprobantes Yape subidas
├── .env                       ← Variables de entorno (BD, URL)
├── .htaccess                  ← Redirige todas las peticiones a app/index.php
└── README.md
```

---

## 🔄 ¿Cómo funciona el patrón MVC?

**MVC = Modelo — Vista — Controlador**

```
Navegador  →  .htaccess  →  app/index.php  →  Router  →  Controlador  →  Modelo  →  BD
                                                                      ↓
                                                                    Vista  →  Navegador
```

### Flujo paso a paso con ejemplo real

El usuario entra a `/pedidos/croquis`:

1. **`.htaccess`** convierte la URL en `?url=pedidos/croquis` y la manda a `app/index.php`
2. **`App.php`** inicia la sesión y llama al `Router`
3. **`Router.php`** divide la URL: controlador = `Pedidos`, método = `croquis`
4. Carga `PedidosController.php`, crea una instancia y llama a `->croquis()`
5. **`PedidosController::croquis()`** llama al modelo para obtener los datos
6. **`Pedido::obtenerEstadoMesas()`** ejecuta una query SQL y devuelve los datos
7. El controlador llama a `$this->view('pedidos/croquis', $datos)` que renderiza la vista
8. **`croquis.php`** genera el HTML con los datos y lo envía al navegador

---

## 🗃️ Base de Datos — Tablas principales

| Tabla | ¿Para qué sirve? |
|-------|-----------------|
| `usuario` | Personas que usan el sistema (admin, mesero, cocina) |
| `plato` | Platos del menú (precio fijo S/ 8.00) |
| `entrada` | Entradas del menú (S/ 3.00 sueltas o incluidas con plato) |
| `pedido` | Cada pedido creado (Mesa o Llevar, con estado y total) |
| `detalle_pedido` | Items dentro de un pedido (qué plato, qué entrada, cantidad) |
| `detalle_entrada_extra` | Entradas sueltas dentro de un pedido |
| `pago` | Registro de pagos (Efectivo o Yape, con foto de comprobante) |
| `asistencia` | Registro de entrada/salida del personal |

### Relaciones clave
