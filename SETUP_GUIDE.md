# TechCell - Setup Guide

## Descripción General
TechCell es una aplicación full-stack para la gestión de ventas de celulares y accesorios. Consiste en:
- **Backend**: Spring Boot REST API (Puerto 8080)
- **Frontend**: Angular 20 SPA (Puerto 4200)

## Requisitos Previos
- Java 17+
- Node.js 18+
- Maven 3.8+
- npm o pnpm

## Estructura del Proyecto

```
DWI/
├── src/                           # Backend Spring Boot
│   ├── main/java/utp/workpagespringutp/
│   │   ├── controller/           # REST Controllers
│   │   ├── service/              # Servicios de negocio
│   │   ├── model/                # Entidades JPA
│   │   ├── repository/           # Acceso a datos
│   │   └── config/               # Configuración (SecurityConfig, CORS)
│   └── resources/
│       └── application.properties # Configuración BD
├── ang-front/                     # Frontend Angular
│   ├── src/app/
│   │   ├── pages/               # Componentes de páginas
│   │   ├── services/            # Servicios HTTP
│   │   ├── layouts/             # Layouts (client, admin)
│   │   └── components/          # Componentes reutilizables
│   ├── package.json
│   └── proxy.conf.json          # Proxy para desarrollo
└── pom.xml                        # Configuración Maven
```

## Configuración de la Base de Datos

### MySQL
La base de datos debe existir con el nombre `techchell`. Spring Boot creará las tablas automáticamente.

```sql
CREATE DATABASE techchell CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Configurar `application.properties`
Edita `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/techchell
spring.datasource.username=root
spring.datasource.password=tu_contraseña
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=false
server.port=8080
```

## Ejecución del Backend

1. Navega al directorio raíz del proyecto:
```bash
cd /vercel/share/v0-project
```

2. Compila y ejecuta con Maven:
```bash
mvn spring-boot:run
```

O si prefieres compilar primero:
```bash
mvn clean install
java -jar target/workpagespringutp-0.0.1-SNAPSHOT.jar
```

El backend estará disponible en: **http://localhost:8080**

## Ejecución del Frontend

1. Navega al directorio Angular:
```bash
cd ang-front
```

2. Instala dependencias:
```bash
npm install
# o
pnpm install
```

3. Ejecuta el servidor de desarrollo:
```bash
npm start
# o
pnpm start
```

El frontend estará disponible en: **http://localhost:4200**

**Nota**: El proxy está configurado en `proxy.conf.json` para redireccionar las peticiones API (http://localhost:4200/api/*) a http://localhost:8080 automáticamente.

## Rutas Disponibles

### Cliente (No requiere autenticación)
- `GET /` - Página de inicio
- `GET /productos` - Catálogo de productos
- `GET /productos?categoria=Celular` - Productos por categoría
- `GET /contacto` - Página de contacto
- `GET /nosotros` - Página acerca de nosotros

### Autenticación
- `POST /login` - Iniciar sesión
  ```json
  {
    "username": "usuario",
    "password": "contraseña"
  }
  ```
- `GET /logout` - Cerrar sesión

### Carrito (Requiere autenticación)
- `POST /carrito/agregar?productoId=1&cantidad=1` - Agregar al carrito
- `GET /carrito/datos` - Obtener datos del carrito
- `POST /carrito/actualizar/{itemId}?cantidad=2` - Actualizar cantidad
- `POST /carrito/eliminar/{itemId}` - Eliminar del carrito
- `POST /carrito/finalizar` - Finalizar compra

### Perfil (Requiere autenticación)
- `GET /perfil` - Ver perfil del usuario
- `POST /perfil/actualizar` - Actualizar perfil
- `POST /perfil/eliminar` - Eliminar cuenta

### Admin (Requiere ROLE_ADMIN)
- `GET /admin/productos` - Listar productos
- `POST /admin/productos/agregar` - Agregar producto
- `POST /admin/productos/actualizar/{id}` - Actualizar producto
- `POST /admin/productos/eliminar/{id}` - Eliminar producto
- `POST /admin/productos/actualizar-stock/{id}?stock=10` - Actualizar stock

- `GET /admin/usuarios` - Listar usuarios
- `POST /admin/usuarios/cambiar-rol/{id}?nuevoRol=ADMIN` - Cambiar rol
- `POST /admin/usuarios/eliminar/{id}` - Eliminar usuario

- `GET /admin/facturas` - Listar facturas
- `GET /admin/facturas/{id}` - Ver detalle de factura
- `POST /admin/facturas/eliminar/{id}` - Eliminar factura

## Características Implementadas

### Backend
- ✅ REST API completa con Spring Boot
- ✅ Autenticación con sesiones HTTP
- ✅ CORS configurado para Angular
- ✅ Gestión de productos, usuarios y carrito
- ✅ Generación de facturas
- ✅ Control de roles (ADMIN, CLIENTE)
- ✅ Validación de stock

### Frontend
- ✅ Componentes Angular standalone
- ✅ Routing con lazy loading
- ✅ Servicios HTTP con RxJS
- ✅ Señales (Signals) para estado reactivo
- ✅ Layouts para cliente y admin
- ✅ Formularios reactivos
- ✅ Integración con Bootstrap 5
- ✅ Proxy configurado para desarrollo

## Usuarios de Prueba

Los siguientes usuarios deberían ser creados en la base de datos:

### Cliente
- Username: `cliente`
- Email: `cliente@example.com`
- Contraseña: `password123`
- Rol: `CLIENTE`

### Administrador
- Username: `admin`
- Email: `admin@example.com`
- Contraseña: `admin123`
- Rol: `ADMIN`

## Troubleshooting

### CORS Error
Si ves errores CORS:
- Verifica que `http://localhost:4200` está en `allowed origins` en `SecurityConfig.java`
- Reinicia ambos servidores

### 404 on API calls
- Asegúrate que el backend está ejecutando en puerto 8080
- Verifica que `proxy.conf.json` tiene las rutas correctas

### BD no conecta
- Verifica credenciales en `application.properties`
- Asegúrate que MySQL está corriendo
- Crea la base de datos: `CREATE DATABASE techchell;`

## Comandos Útiles

```bash
# Backend
mvn clean compile          # Compilar solo
mvn clean test            # Ejecutar tests
mvn clean install         # Compilar e instalar en repo local
mvn spring-boot:run       # Ejecutar con Maven

# Frontend
npm install               # Instalar dependencias
npm start                 # Ejecutar en desarrollo
npm run build            # Build para producción
npm run test             # Ejecutar tests
ng generate component name  # Generar componente
```

## Notas Importantes

1. **Proxy Configuration**: El archivo `proxy.conf.json` en ang-front redirige las peticiones de API al backend. Esto es solo para desarrollo. Para producción, configura CORS adecuadamente.

2. **Sessions**: Las sesiones se mantienen usando cookies HTTP (`JSESSIONID`). Asegúrate que las cookies están habilitadas en el navegador.

3. **Stock**: El sistema valida el stock disponible antes de agregar items al carrito.

4. **Facturas**: Se generan automáticamente cuando se finaliza una compra.

## Documentación Adicional

- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [Angular Documentation](https://angular.io/docs)
- [Bootstrap 5 Documentation](https://getbootstrap.com/)

---

Para más información o problemas, contacta al equipo de desarrollo.
