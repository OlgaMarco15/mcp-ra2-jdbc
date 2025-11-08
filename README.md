# MCP Server RA2 - Acceso a Datos mediante JDBC PURO

Servidor educacional MCP (Model Context Protocol) para enseñanza de JDBC vanilla en el módulo de Acceso a Datos de 2º DAM.

## 📋 Descripción

Proyecto educativo que proporciona un **esqueleto de aplicación JDBC PURO** donde los estudiantes implementan operaciones de base de datos usando JDBC vanilla (sin JPA/Hibernate/Spring DataSource). El proyecto expone 13 herramientas MCP que los estudiantes deben completar.

## ⚡ IMPORTANTE: JDBC Puro vs Spring DataSource

**Este proyecto usa JDBC VANILLA deliberadamente para máximo aprendizaje:**

✅ **SÍ usamos:**
- `DriverManager.getConnection()` - Conexiones directas
- `Class.forName()` - Carga explícita del driver
- Gestión manual de conexiones
- Try-with-resources obligatorio

❌ **NO usamos:**
- Spring `DataSource` (inyección de dependencias)
- Spring `JdbcTemplate`
- Connection pools automáticos de Spring
- Inicialización automática de BD por Spring

**¿Por qué?** Los estudiantes aprenden:
1. El ciclo completo de JDBC desde cero
2. Cómo funcionan las conexiones realmente
3. La importancia del cierre manual de recursos
4. Los fundamentos antes de usar abstracciones

**Estado actual:**
- ✅ **5 métodos EJEMPLOS implementados** (para aprender el patrón)
- ⚠️ **8 métodos TODO** (para que estudiantes implementen)

## 🎯 Resultado de Aprendizaje

**RA2**: Desarrolla aplicaciones que gestionan información almacenada mediante conectores

### Criterios de Evaluación

| CE | Descripción | Métodos |
|----|-------------|---------|
| **CE2.a** | Gestión de conexiones a bases de datos | `testConnection()` |
| **CE2.b** | Operaciones CRUD con JDBC | `createUser()`, `findUserById()`, `updateUser()`, `deleteUser()`, `findAll()` |
| **CE2.c** | Consultas avanzadas y paginación | `findUsersByDepartment()`, `searchUsers()` |
| **CE2.d** | Gestión de transacciones | `transferData()`, `batchInsertUsers()` |
| **CE2.e** | Metadatos de bases de datos | `getDatabaseInfo()`, `getTableColumns()` |
| **CE2.f** | Funciones de agregación | `executeCountByDepartment()` |

## 🏗️ Estructura del Proyecto

```
mcp-server-ra2-jdbc/
├── src/
│   ├── main/
│   │   ├── java/com/dam/accesodatos/
│   │   │   ├── McpAccesoDatosRa2Application.java  [COMPLETO - Main Spring Boot]
│   │   │   ├── config/                            [COMPLETO - Configuración]
│   │   │   ├── model/                             [COMPLETO - User, DTOs]
│   │   │   └── ra2/                               [IMPLEMENTACIÓN ESTUDIANTES]
│   │   │       ├── DatabaseUserService.java       [COMPLETO - Interface con @Tool]
│   │   │       ├── DatabaseUserServiceImpl.java   [5 EJEMPLOS + 8 TODOs]
│   │   │       └── package-info.java              [COMPLETO - Documentación RA2]
│   │   └── resources/
│   │       ├── application.yml                    [COMPLETO - Config H2 + MCP]
│   │       ├── schema.sql                         [COMPLETO - CREATE TABLE users]
│   │       └── data.sql                           [COMPLETO - Datos de prueba]
│   └── test/
│       ├── java/com/dam/accesodatos/ra2/
│       │   └── DatabaseUserServiceTest.java       [TODO - Tests TDD]
│       └── resources/
│           ├── test-schema.sql                    [COMPLETO - Schema de tests]
│           └── test-data.sql                      [COMPLETO - Datos de tests]
├── build.gradle                                   [COMPLETO - Spring Boot + JDBC + H2]
├── settings.gradle                                [COMPLETO]
└── README.md                                      [Este archivo]
```

## 🚀 Inicio Rápido

### Pre-requisitos

- **Java 17** o superior
- **Gradle** (incluido via wrapper)
- **IntelliJ IDEA** recomendado (o cualquier IDE con soporte Gradle)

### Compilar el Proyecto

```bash
# Desde línea de comandos
./gradlew clean compileJava

# Desde IntelliJ IDEA
Panel Gradle → Tasks → build → build
```

### Ejecutar la Aplicación

```bash
# Desde línea de comandos
./gradlew bootRun

# Desde IntelliJ IDEA
Run → McpAccesoDatosRa2Application
```

El servidor arranca en **http://localhost:8082**

### Consola H2 Database

Para inspeccionar la base de datos:

1. Abrir: http://localhost:8082/h2-console
2. Configuración:
   - **JDBC URL**: `jdbc:h2:mem:ra2db`
   - **User Name**: `sa`
   - **Password**: (dejar vacío)
3. Conectar

## 🤖 Integración con Claude Code via MCP

Este proyecto implementa un **servidor MCP (Model Context Protocol)** totalmente funcional que permite interactuar con las 13 herramientas JDBC mediante Claude Code o cualquier cliente MCP compatible.

### ✅ Estado Actual: Completamente Funcional

- ✅ **Conexión MCP**: STDIO via adaptador Python
- ✅ **Claude Code**: ✓ Connected
- ✅ **13 herramientas JDBC**: Todas accesibles via MCP
- ✅ **Auto-start**: El servidor Spring Boot se inicia automáticamente

### Arquitectura del Servidor MCP

El proyecto utiliza una arquitectura híbrida probada y funcional:

```
┌─────────────────┐      STDIO       ┌──────────────────┐
│                 │  (JSON-RPC 2.0)  │                  │
│  Claude Code    ├─────────────────►│  mcp_adapter.py  │
│                 │                   │   (Python)       │
└─────────────────┘                   └────────┬─────────┘
                                               │
                                               │ HTTP REST
                                               │
                                      ┌────────▼─────────┐
                                      │                  │
                                      │  Spring Boot     │
                                      │  (port 8082)     │
                                      │                  │
                                      └────────┬─────────┘
                                               │
                                               │ JDBC
                                               │
                                      ┌────────▼─────────┐
                                      │                  │
                                      │  H2 Database     │
                                      │  (in-memory)     │
                                      │                  │
                                      └──────────────────┘
```

**Componentes clave:**

1. **`mcp_adapter.py`** - Adaptador Python que:
   - Implementa el protocolo MCP (JSON-RPC sobre STDIO)
   - Auto-inicia el servidor Spring Boot si no está corriendo
   - Traduce llamadas MCP a peticiones HTTP REST
   - Maneja las 13 herramientas JDBC

2. **Spring Boot REST API** - Backend que:
   - Expone endpoints HTTP en `localhost:8082/mcp`
   - Ejecuta operaciones JDBC usando JDBC vanilla
   - Usa anotaciones `@Tool` personalizadas para registro automático

3. **McpToolRegistry** - Componente que:
   - Escanea métodos anotados con `@Tool` al iniciar
   - Registra automáticamente las 13 herramientas JDBC
   - Proporciona metadatos para el protocolo MCP

### Configuración Automática

El proyecto incluye `.mcp.json` con la configuración completa. **Claude Code lo detecta automáticamente** al abrir el proyecto.

**Contenido de `.mcp.json`:**
```json
{
  "mcpServers": {
    "mcp-server-ra2-jdbc": {
      "type": "stdio",
      "command": "python3",
      "args": ["/ruta/absoluta/mcp_adapter.py"],
      "cwd": "/ruta/absoluta/del/proyecto",
      "description": "Servidor MCP educacional para RA2 - Acceso a Datos mediante JDBC"
    }
  }
}
```

**No requiere configuración manual** - el adaptador Python maneja todo automáticamente:
- ✅ Verifica si el servidor está corriendo
- ✅ Inicia `./gradlew bootRun` si es necesario
- ✅ Espera hasta que el servidor esté listo (health check)
- ✅ Traduce mensajes MCP a HTTP REST

### Verificar Conexión

```bash
# Listar servidores MCP configurados
claude mcp list
```

Deberías ver:
```
mcp-server-ra2-jdbc: python3 /ruta/mcp_adapter.py - ✓ Connected
```

### Herramientas MCP Disponibles

Una vez conectado, Claude Code tiene acceso a **13 herramientas JDBC**:

#### ✅ Implementadas (5 herramientas ejemplo)

1. **`test_connection`** - Prueba conexión JDBC básica
   - Valida conectividad con H2
   - Retorna versión de BD y driver
   - Ejemplo: *"Prueba la conexión a la base de datos"*

2. **`create_user`** - INSERT con PreparedStatement
   - Parámetros: name, email, department, role
   - Retorna usuario creado con ID generado
   - Ejemplo: *"Crea un usuario llamado Ana con email ana@empresa.com del departamento IT como Developer"*

3. **`find_user_by_id`** - SELECT con parámetros
   - Parámetro: userId
   - Retorna objeto User o null
   - Ejemplo: *"Busca el usuario con ID 1"*

4. **`update_user`** - UPDATE statement
   - Parámetros: userId, name, email, department, role
   - Actualiza campos y retorna usuario actualizado
   - Ejemplo: *"Actualiza el usuario 2 cambiando su rol a Manager"*

5. **`transfer_data`** - Transacción manual con commit/rollback
   - Inserta múltiples usuarios en una transacción
   - Hace rollback si hay algún error
   - Ejemplo: *"Inserta estos 3 usuarios en una transacción..."*

#### ⚠️ TODO (8 herramientas para implementar por estudiantes)

6. **`delete_user`** - DELETE statement
7. **`find_all_users`** - SELECT all con iteración ResultSet
8. **`find_users_by_department`** - Filtro WHERE
9. **`search_users`** - Query dinámica con múltiples filtros y paginación (LIMIT/OFFSET)
10. **`batch_insert_users`** - Batch operations con executeBatch()
11. **`get_database_info`** - DatabaseMetaData completo
12. **`get_table_columns`** - ResultSetMetaData
13. **`execute_count_by_department`** - COUNT query con agregación

### Uso Interactivo con Claude Code

Una vez configurado, puedes pedirle a Claude Code de forma natural:

```
"Usa el servidor MCP para probar la conexión JDBC"
→ Claude ejecutará: test_connection()

"Muéstrame todos los usuarios de la base de datos"
→ Claude ejecutará: find_all_users() [si está implementado]

"Busca usuarios del departamento IT"
→ Claude ejecutará: find_users_by_department("IT") [si está implementado]

"Elimina el usuario con ID 5"
→ Claude ejecutará: delete_user(5) [si está implementado]
```

### Endpoints REST del Servidor

El servidor Spring Boot expone estos endpoints:

- **Health check**: `GET http://localhost:8082/mcp/health`
- **Lista de herramientas**: `GET http://localhost:8082/mcp/tools`
- **Operaciones JDBC**: `POST http://localhost:8082/mcp/{operation}`
- **H2 Console**: `http://localhost:8082/h2-console`

Puedes probar los endpoints directamente:

```bash
# Verificar que el servidor está activo
curl http://localhost:8082/mcp/health

# Ver todas las herramientas disponibles
curl http://localhost:8082/mcp/tools

# Ejecutar test_connection
curl -X POST http://localhost:8082/mcp/test_connection
```

### Cómo Funciona mcp_adapter.py

El adaptador Python actúa como puente entre Claude Code y Spring Boot:

**1. Inicialización:**
```python
# Verifica si Spring Boot está corriendo
if not check_server_running():
    # Inicia servidor con ./gradlew bootRun
    start_spring_server()
    # Espera hasta que /mcp/health retorne 200
```

**2. Protocolo MCP:**
```python
# Lee mensajes JSON-RPC desde stdin
for line in sys.stdin:
    request = json.loads(line)

    # Maneja mensajes MCP estándar
    if request["method"] == "initialize":
        # Retorna capabilities del servidor
    elif request["method"] == "tools/list":
        # Obtiene lista desde /mcp/tools
    elif request["method"] == "tools/call":
        # Traduce a POST /mcp/{tool_name}
```

**3. Mapeo de Herramientas:**
```python
endpoint_map = {
    "test_connection": "/test_connection",
    "create_user": "/create_user",
    "find_user_by_id": "/find_user_by_id",
    # ... mapeo completo de 13 herramientas
}
```

### Desarrollo y Testing

**Para estudiantes - Probar implementaciones:**

1. **Implementar método en `DatabaseUserServiceImpl.java`**
2. **Reiniciar servidor**: `./gradlew bootRun`
3. **Probar con Claude Code**:
   ```
   "Ejecuta find_all_users para ver si mi implementación funciona"
   ```

**Para debugging avanzado:**

```bash
# Ver logs del servidor
tail -f build/logs/spring.log

# Probar endpoint directamente
curl -X POST http://localhost:8082/mcp/find_all_users

# Ver en H2 Console
open http://localhost:8082/h2-console
```

### Troubleshooting

**"Failed to connect" en claude mcp list**
- ✅ Verificar que `python3` está instalado: `python3 --version`
- ✅ Verificar ruta absoluta en `.mcp.json`
- ✅ Comprobar permisos del archivo: `chmod +x mcp_adapter.py`
- ✅ Revisar logs: Agregar `MCP_DEBUG=1` en env del .mcp.json

**Servidor no inicia automáticamente**
- Verificar que `./gradlew` tiene permisos de ejecución
- Comprobar puerto 8082 disponible: `lsof -i :8082`
- Revisar Java 17+ instalado: `java -version`

**Herramienta no funciona**
- Verificar que el método esté anotado con `@Tool` en `DatabaseUserService.java`
- Comprobar que el método esté implementado (no throw UnsupportedOperationException)
- Revisar logs del servidor para ver errores SQL
- Probar directamente el endpoint REST con curl
- Verificar bean `McpToolsConfiguration` está activo
- Revisar anotaciones `@Tool` en `DatabaseUserService`

## 📚 Implementación para Estudiantes

### Métodos Implementados (Ejemplos para Aprender)

#### 1. ✅ `testConnection()` - CE2.a
Ejemplo básico de conexión JDBC.

**Conceptos que muestra:**
- Try-with-resources
- Obtener Connection del DataSource
- Ejecutar query simple
- Procesar ResultSet
- Usar DatabaseMetaData

**Ubicación:** `DatabaseUserServiceImpl.java:55`

#### 2. ✅ `createUser()` - CE2.b
INSERT con PreparedStatement y getGeneratedKeys.

**Conceptos que muestra:**
- PreparedStatement para prevenir SQL injection
- Setear parámetros con tipos específicos
- `RETURN_GENERATED_KEYS`
- Manejar errores específicos (email duplicado)

**Ubicación:** `DatabaseUserServiceImpl.java:145`

#### 3. ✅ `findUserById()` - CE2.b
SELECT con navegación de ResultSet.

**Conceptos que muestra:**
- Query parametrizada con WHERE
- Navegar ResultSet con `rs.next()`
- Mapear columnas SQL a objeto Java
- Conversión de tipos (Long, String, Timestamp)

**Ubicación:** `DatabaseUserServiceImpl.java:203`

#### 4. ✅ `updateUser()` - CE2.b
UPDATE statement con validación.

**Conceptos que muestra:**
- Validar existencia antes de actualizar
- UPDATE con múltiples campos
- Actualizar timestamp automático
- Verificar filas afectadas

**Ubicación:** `DatabaseUserServiceImpl.java:242`

#### 5. ✅ `transferData()` - CE2.d
Transacción manual con commit/rollback.

**Conceptos que muestra:**
- Desactivar auto-commit: `conn.setAutoCommit(false)`
- Ejecutar múltiples operaciones
- COMMIT si todo OK
- ROLLBACK si hay error
- Restaurar auto-commit en finally

**Ubicación:** `DatabaseUserServiceImpl.java:453`

### Métodos TODO (Para Implementar)

| # | Método | CE | Dificultad | Prioridad |
|---|--------|----|-----------| ---------|
| 1 | `deleteUser()` | CE2.b | ⭐ Básica | Alta |
| 2 | `findAll()` | CE2.b | ⭐ Básica | Alta |
| 3 | `findUsersByDepartment()` | CE2.c | ⭐⭐ Media | Alta |
| 4 | `executeCountByDepartment()` | CE2.f | ⭐⭐ Media | Alta |
| 5 | `searchUsers()` | CE2.c | ⭐⭐⭐⭐ Muy Alta | Alta |
| 6 | `batchInsertUsers()` | CE2.d | ⭐⭐⭐ Alta | Media |
| 7 | `getDatabaseInfo()` | CE2.e | ⭐⭐ Media | Media |
| 8 | `getTableColumns()` | CE2.e | ⭐⭐⭐ Alta | Media |

**Cada método TODO incluye:**
- ✅ Descripción detallada de lo que debe hacer
- ✅ Pasos a seguir (algoritmo step-by-step)
- ✅ Clases JDBC requeridas
- ✅ Ejemplo de estructura de código
- ✅ Notas pedagógicas

## 🧪 Testing

### Ejecutar Tests

```bash
# Todos los tests (cuando se implementen)
./gradlew test

# Ver resultados detallados
./gradlew test --info

# Desde IntelliJ
Clic derecho en test/ → Run All Tests
```

### Estrategia TDD

1. **RED**: Ejecutar test → Falla (UnsupportedOperationException)
2. **GREEN**: Implementar método → Test pasa
3. **REFACTOR**: Mejorar código → Tests siguen pasando

## 📖 Clases JDBC Clave

### Connection Management con DatabaseConfig (JDBC Puro)
```java
// PATRÓN JDBC VANILLA - Sin Spring DataSource
try (Connection conn = DatabaseConfig.getConnection()) {
    // Trabajar con la conexión
    // DatabaseConfig usa DriverManager internamente
}
```

**Ventajas pedagógicas:**
- Los estudiantes ven `DriverManager.getConnection()` en acción
- No hay "magia" de inyección de dependencias
- Se aprende gestión manual de recursos

### PreparedStatement (Previene SQL Injection)
```java
String sql = "SELECT * FROM users WHERE id = ?";
try (Connection conn = DatabaseConfig.getConnection();
     PreparedStatement pstmt = conn.prepareStatement(sql)) {

    pstmt.setLong(1, userId);

    try (ResultSet rs = pstmt.executeQuery()) {
        while (rs.next()) {
            Long id = rs.getLong("id");
            String name = rs.getString("name");
            // ...
        }
    }
}
```

### Transacciones (Control Manual)
```java
// JDBC PURO - Sin transacciones de Spring
Connection conn = DatabaseConfig.getConnection();
try {
    conn.setAutoCommit(false);  // Inicio transacción MANUAL

    // Operación 1
    pstmt1.executeUpdate();

    // Operación 2
    pstmt2.executeUpdate();

    conn.commit();  // Confirmar si todo OK

} catch (SQLException e) {
    conn.rollback();  // Deshacer si error
    throw new RuntimeException(e);
} finally {
    conn.setAutoCommit(true);
    conn.close();
}
```

**Nota educativa**: Los estudiantes gestionan transacciones manualmente,
sin usar `@Transactional` de Spring.

### Batch Operations
```java
try (PreparedStatement pstmt = conn.prepareStatement(sql)) {
    for (User user : users) {
        pstmt.setString(1, user.getName());
        pstmt.setString(2, user.getEmail());
        pstmt.addBatch();  // No ejecutar aún
    }

    int[] results = pstmt.executeBatch();  // Ejecutar todos
}
```

## 🔍 Debugging

### Ver Queries SQL Ejecutadas

En `application.yml`, logging está configurado en DEBUG:

```yaml
logging:
  level:
    org.springframework.jdbc: DEBUG
```

Verás en consola:
```
Executing SQL statement [INSERT INTO users ...]
```

### Verificar Datos en H2 Console

1. Abrir http://localhost:8082/h2-console
2. Ejecutar queries directas:
```sql
SELECT * FROM users;
SELECT * FROM users WHERE department = 'IT';
SELECT COUNT(*) FROM users GROUP BY department;
```

### Common Issues

**Error: "Table not found"**
- Verificar que `schema.sql` se ejecutó
- Revisar logs de inicio de aplicación

**Error: "Unique index violation"**
- Email duplicado (campo UNIQUE)
- Verificar constraint en `schema.sql`

**Error: "Parameter index out of range"**
- Índices de `pstmt.setXXX()` empiezan en 1, no en 0
- Contar placeholders `?` en SQL

## 📁 Base de Datos

### Schema

```sql
CREATE TABLE users (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    department VARCHAR(50) NOT NULL,
    role VARCHAR(50) NOT NULL,
    active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Datos Iniciales

El archivo `data.sql` inserta 8 usuarios de prueba en diferentes departamentos:
- IT: 3 usuarios
- HR: 2 usuarios
- Finance, Marketing, Sales: 1 usuario cada uno

## 🎓 Recursos Adicionales

### Documentación Java JDBC
- [JDBC Tutorial (Oracle)](https://docs.oracle.com/javase/tutorial/jdbc/)
- [PreparedStatement API](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/PreparedStatement.html)
- [ResultSet API](https://docs.oracle.com/en/java/javase/17/docs/api/java.sql/java/sql/ResultSet.html)

### H2 Database
- [H2 Documentation](http://www.h2database.com/html/main.html)
- [H2 SQL Grammar](http://www.h2database.com/html/grammar.html)

### Spring Boot
- [Spring Boot JDBC](https://docs.spring.io/spring-boot/docs/current/reference/html/data.html#data.sql)

## 💡 Tips para Estudiantes

1. **Usa try-with-resources SIEMPRE** - Evita leaks de conexiones
2. **PreparedStatement > Statement** - Previene SQL injection
3. **Verifica filas afectadas** - `executeUpdate()` retorna int
4. **Mapea tipos correctamente** - SQL BIGINT → Java Long, SQL VARCHAR → Java String
5. **Maneja excepciones descriptivas** - `throw new RuntimeException("Error al buscar usuario: " + e.getMessage(), e)`
6. **Prueba en H2 Console primero** - Valida tus queries antes de implementar
7. **Lee los ejemplos implementados** - Sigue los patrones mostrados
8. **Commit frecuente** - Cada método que pase sus tests
9. **No uses JPA/Hibernate** - Este proyecto es sobre JDBC puro


### Entrega

- **Archivo**: `DatabaseUserServiceImpl.java` con todos los TODOs implementados
- **Tests**: Todos los tests en GREEN
- **Demo**: Mostrar funcionamiento vía H2 Console o tests

## 🤝 Soporte

- **Consultar ejemplos**: Revisar los 5 métodos implementados
- **Leer TODOs**: Cada método tiene instrucciones paso a paso
- **Debugging**: Usar H2 Console para validar queries
- **Profesor**: Consultar en clase sobre conceptos JDBC

---

**Proyecto educativo - Acceso a Datos 2º DAM**
**Versión**: 1.0.0
**Basado en**: Spring Boot 3.3.0 + H2 Database + JDBC
