# Instrucciones de Despliegue - TechCell Angular/Spring Boot

## Descripción General

Este documento proporciona instrucciones para ejecutar y desplegar la aplicación TechCell, que consiste en:
- **Backend**: Spring Boot (Java) - API REST en `http://localhost:8080`
- **Frontend**: Angular 16+ - SPA compilada servida por Spring Boot

---

## Requisitos Previos

- **Java**: JDK 11 o superior
- **Maven**: 3.6.0 o superior
- **Node.js**: 18.0 o superior
- **npm**: 8.0 o superior
- **Git**: Para clonar y hacer commits

---

## Opciones de Ejecución

### Opción 1: Ejecución en Producción (Recomendado)

Spring Boot sirve la SPA compilada de Angular. Esta es la forma más simple.

#### Paso 1: Compilar Angular (si hay cambios)

```bash
cd /vercel/share/v0-project/ang-front
npm install
npm run build
cp -r dist/ang-front/* ../src/main/resources/public/
```

#### Paso 2: Ejecutar Spring Boot

```bash
cd /vercel/share/v0-project
mvn spring-boot:run
# O si tienes Maven instalado globalmente:
# mvn clean spring-boot:run
```

**Acceso**: http://localhost:8080

---

### Opción 2: Desarrollo (Frontend + Backend Separados)

Útil para desarrollo con Hot Reload en ambos lados.

#### Terminal 1 - Backend

```bash
cd /vercel/share/v0-project
mvn spring-boot:run
# Backend en http://localhost:8080
```

#### Terminal 2 - Frontend

```bash
cd /vercel/share/v0-project/ang-front
npm install
npm start
# Frontend con HMR en http://localhost:4200
# Proxy automático a backend en http://localhost:8080
```

**Acceso**: http://localhost:4200

---

## Variables de Entorno (Opcional)

Si necesitas configurar el puerto o la base de datos, edita:

### Para Spring Boot:
`src/main/resources/application.properties`

```properties
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/celltech
spring.datasource.username=root
spring.datasource.password=password
```

### Para Angular:
`ang-front/proxy.conf.json` (solo para desarrollo)

```json
{
  "/api": {
    "target": "http://localhost:8080",
    "secure": false
  }
}
```

---

## Estructura del Proyecto

```
/vercel/share/v0-project/
├── src/
│   ├── main/
│   │   ├── java/utp/workpagespringutp/
│   │   │   ├── config/
│   │   │   │   ├── SecurityConfig.java
│   │   │   │   └── WebConfig.java (✨ NUEVO - SPA Routing)
│   │   │   ├── controller/
│   │   │   │   ├── AdminController.java
│   │   │   │   ├── ProductoController.java
│   │   │   │   ├── LoginController.java
│   │   │   │   └── ... más controllers
│   │   │   ├── service/
│   │   │   ├── model/
│   │   │   └── repository/
│   │   └── resources/
│   │       ├── application.properties
│   │       └── public/ (📦 Archivos compilados de Angular)
│   │           ├── index.html
│   │           ├── browser/
│   │           │   ├── main-*.js
│   │           │   ├── styles-*.css
│   │           │   └── IMG/
│   │           └── ...
│   └── test/
├── ang-front/ (📱 Proyecto Angular)
│   ├── src/
│   │   ├── app/
│   │   │   ├── pages/
│   │   │   │   ├── admin/
│   │   │   │   │   ├── dashboard/
│   │   │   │   │   ├── productos/
│   │   │   │   │   ├── usuarios/
│   │   │   │   │   └── facturas/ (✨ NUEVO)
│   │   │   │   ├── home/
│   │   │   │   ├── productos/
│   │   │   │   ├── perfil/
│   │   │   │   └── ...
│   │   │   ├── services/
│   │   │   ├── components/
│   │   │   └── layouts/
│   │   └── styles.css
│   ├── dist/ (Salida de build)
│   ├── package.json
│   └── proxy.conf.json
├── pom.xml
├── FIXES_IMPLEMENTED.md (📝 Documentación de correcciones)
├── CHECKLIST_ISSUES_FIXED.md (✅ Verificación de errores)
└── DEPLOYMENT_INSTRUCTIONS.md (Este archivo)
```

---

## Endpoints Principales

### Públicos (Sin Autenticación)
- `GET /` - Home
- `GET /productos` - Catálogo de productos
- `GET /contacto` - Página de contacto
- `GET /nosotros` - Información de la empresa
- `POST /login` - Autenticación

### Autenticados (Usuarios)
- `GET /perfil` - Perfil de usuario
- `POST /carrito/agregar/{id}` - Agregar producto al carrito
- `GET /carrito` - Ver carrito

### Administrador (`/admin/*`)
- `GET /admin/dashboard` - Panel de administración
- `GET /admin/productos` - Gestión de productos
- `POST /admin/productos/agregar` - Agregar producto
- `POST /admin/productos/actualizar/{id}` - Actualizar producto
- `POST /admin/productos/eliminar/{id}` - Eliminar producto
- `GET /admin/usuarios` - Gestión de usuarios
- `POST /admin/usuarios/cambiar-rol/{id}` - Cambiar rol de usuario
- `GET /admin/facturas` - Ver facturas
- `GET /admin/facturas/{id}` - Detalles de factura

---

## Características Principales

### Frontend (Angular)

✅ **SPA (Single Page Application)**
- Routing completo sin necesidad de refrescar
- Navegación fluida entre páginas

✅ **Componentes Funcionales**
- Home con hero section
- Catálogo de productos con filtros
- Carrito de compras dinámico
- Perfil de usuario
- Panel administrativo completo
- Gestión de facturas

✅ **Servicios Implementados**
- AuthService: Autenticación y perfil
- ProductService: Productos y filtros
- CartService: Gestión del carrito
- AdminService: Operaciones admin
- FacturaService: Facturas

### Backend (Spring Boot)

✅ **Endpoints REST**
- CORS configurado
- Autenticación por sesión HTTP
- Autorización basada en roles

✅ **Seguridad**
- Spring Security configurado
- Validación de sesiones
- Control de acceso por roles

✅ **Base de Datos**
- JPA/Hibernate para ORM
- MySQL como base de datos
- Repositorios para cada entidad

---

## Proceso de Build

### Compilar Angular

```bash
cd ang-front
npm install
npm run build
```

**Salida**: `ang-front/dist/ang-front/`

### Copiar a Spring Boot

```bash
# Copia los archivos compilados
cp -r ang-front/dist/ang-front/* src/main/resources/public/
```

### Compilar/Ejecutar Spring Boot

```bash
# Opción 1: Desarrollo
mvn clean spring-boot:run

# Opción 2: Build completo
mvn clean package
java -jar target/celltech-0.0.1-SNAPSHOT.jar
```

---

## Solución de Problemas

### Problema: "Cannot GET /" al abrir aplicación

**Causa**: Los archivos de Angular no se compilaron o copiaron correctamente.

**Solución**:
```bash
cd ang-front
npm run build
cp -r dist/ang-front/* ../src/main/resources/public/
```

### Problema: API no responde

**Causa**: El backend no se está ejecutando o hay problema de CORS.

**Solución**:
- Verifica que Spring Boot esté corriendo en terminal 1
- Revisa que el puerto 8080 esté disponible
- Verifica que `SecurityConfig.java` tenga CORS habilitado

### Problema: "Cannot find module" en npm

**Causa**: Dependencias no instaladas.

**Solución**:
```bash
cd ang-front
rm -rf node_modules package-lock.json
npm install
```

### Problema: Puerto 8080 ya está en uso

**Causa**: Otra aplicación ocupa el puerto.

**Solución** - Opción A: Cambiar puerto
```properties
# En src/main/resources/application.properties
server.port=8081
```

**Solución** - Opción B: Liberar puerto
```bash
# En Linux/Mac
lsof -ti:8080 | xargs kill -9

# En Windows
netstat -ano | findstr :8080
taskkill /PID <PID> /F
```

---

## Testing

### Test Funcionalidades

1. **Navegación**
   - [ ] Ir a http://localhost:8080 (o 4200)
   - [ ] Hacer clic en "Productos"
   - [ ] Verificar que carga la página de productos

2. **Productos**
   - [ ] Filtrar por "Celulares"
   - [ ] Filtrar por "Accesorios"
   - [ ] Agregar producto al carrito
   - [ ] Verificar que el stock disminuye

3. **Admin**
   - [ ] Navegar a /admin/dashboard
   - [ ] Acceder a Gestión de Productos
   - [ ] Acceder a Gestión de Usuarios
   - [ ] Acceder a Facturas
   - [ ] Volver desde cada sección

4. **Persistencia**
   - [ ] Recargar página (F5) en cualquier ruta
   - [ ] Verificar que NO muestra JSON
   - [ ] Verificar que muestra el diseño correctamente

---

## Mantenimiento

### Actualizar Angular

```bash
cd ang-front
npm update
npm run build
cp -r dist/ang-front/* ../src/main/resources/public/
```

### Actualizar Dependencies de Spring Boot

```bash
# Editar pom.xml y cambiar versiones
# Luego ejecutar
mvn clean install
```

### Limpiar Cache/Build

```bash
# Angular
cd ang-front
rm -rf dist node_modules
npm install
npm run build

# Spring Boot
mvn clean
mvn package
```

---

## Información de Contacto

Para reportar problemas o sugerencias:
- Crear un issue en el repositorio Git
- Contactar al equipo de desarrollo
- Revisar la documentación de correcciones en `FIXES_IMPLEMENTED.md`

---

## Changelog

### Versión 1.1 (Correcciones Implementadas)

✅ Corregido: JSON en lugar de HTML al recargar páginas
✅ Corregido: Filtros de categorías no funcionaban
✅ Corregido: Stock no se actualizaba inmediatamente
✅ Corregido: Botón volver en admin no funcionaba
✅ Corregido: Facturas no funcionaban
✅ Corregido: Error 404 en /admin/dashboard

Ver `FIXES_IMPLEMENTED.md` para detalles técnicos.

---

## Versión

- **Proyecto**: TechCell
- **Versión Frontend**: Angular 16+
- **Versión Backend**: Spring Boot 2.7+
- **Base de Datos**: MySQL 5.7+
- **Java**: JDK 11+

---

**¡Proyecto listo para desplegar!** 🚀
