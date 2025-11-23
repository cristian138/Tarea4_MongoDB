# Tarea4 MongoDB: Gestión de Base de Datos RUV (Registro de Víctimas)

Este repositorio contiene scripts de **MongoDB Shell (`mongosh`)** diseñados para la gestión, administración y análisis de la base de datos **RUV**. El proyecto se centra en la colección `victimas` para realizar operaciones CRUD, optimización mediante índices y generación de estadísticas avanzadas.

## 📋 Requisitos Previos

* **MongoDB Server** (v4.4 o superior).
* **MongoDB Shell (`mongosh`)** o **MongoDB Compass**.
* Archivo de datos fuente (JSON) - `victimas.json`.

---

## 🚀 1. Configuración Inicial

### Crear Base de Datos y Colección

```javascript
// Seleccionar la base de datos
use RUV

// Crear la colección de usuarios (si se requiere configuración específica)
db.createCollection("usuarios");

// Nota: La colección 'victimas' se creará automáticamente al insertar documentos.
```

### Importación de Datos
Existen dos formas de cargar la información inicial de las víctimas.

#### Importación Gráfica (Compass)
1. Abrir MongoDB Compass.
2. Ir a `RUV` > `victimas`.
3. Seleccionar **Add Data** > **Import JSON or CSV file**.

## 🛠️ 2. Consultas Operativas

Scripts para el manejo diario de los registros.

### Búsqueda y Filtrado (Read)

```javascript
// Filtrar por estado del registro
db.victimas.find({ estado_registro: "Aceptado" });

// Búsqueda de documento único por ID interno
db.victimas.findOne({ _id: "UUID_DEL_DOCUMENTO" });

// Filtrar por Género
db.victimas.find({ genero: "Masculino" });

// Filtrar por Rango de Fechas (Junio a Diciembre 2024)
db.victimas.find({ 
    fecha_registro: { 
        $gte: "2024-06-01", 
        $lte: "2024-12-31" 
    }
});
```

### Actualización (Update)

```javascript
// Actualizar estado y fecha usando el ID (_id)
db.victimas.updateOne(
    { _id: "UUID" },
    { 
        $set: { 
            estado_registro: "Revisado", 
            ultima_actualizacion: new Date() 
        } 
    }
);

// Actualizar estado usando el Número de Documento
db.victimas.updateOne(
    { numero_documento: "123456789" }, 
    { 
        $set: { 
            estado_registro: "Aceptado", 
            ultima_actualizacion: new Date() 
        }
    }
);
```

### Eliminación (Delete)

```javascript
// Eliminar un registro específico por documento de identidad
db.victimas.deleteOne({ numero_documento: "123456789" });
```

---

## ⚡ 3. Índices y Rendimiento

Para mejorar la velocidad de las consultas frecuentes, se han configurado los siguientes índices:

```javascript
// Índice de texto para búsquedas parciales por nombre
db.victimas.createIndex({ nombre_completo: "text"});

// Índice único para evitar duplicidad de documentos de identidad
db.victimas.createIndex({ numero_documento: 1 }, { unique: true });
```

**Ejemplo de uso del índice de texto:**
```javascript
db.victimas.find({ $text: { $search: "Juan" } });
```

---

## 📊 4. Estadísticas y Agregaciones

Pipelines de agregación para extraer métricas de negocio.

### Métricas Generales

```javascript
// 1. Total de víctimas registradas
db.victimas.aggregate([
    { $count: "total_victimas" }
]);

// 2. Conteo de víctimas por género
db.victimas.aggregate([
    { 
        $group: { 
            _id: "$genero", 
            total: { $sum: 1 } 
        }
    }
]);
```

### Métricas Avanzadas

```javascript
// 3. Promedio de personas en el núcleo familiar
// (Agrupado por estado del registro)
db.victimas.aggregate([
    { 
        $group: {
            _id: "$estado_registro", 
            promedio_nucleo: { $avg: { $size: "$nucleo_familiar" } },
            total_registros: { $sum: 1 }
        }
    }, 
    { 
        $project: { 
            _id: 1, 
            total_registros: 1, 
            promedio_nucleo: { $round: ["$promedio_nucleo", 2] } 
        }
    }
]);

// 4. Ranking por Estado de Registro (Orden descendente)
db.victimas.aggregate([
    { 
        $group: { 
            _id: "$estado_registro", 
            total: { $sum: 1 } 
        }
    },
    { $sort: { total: -1 } }
]);
```

#### DATOS DE ESTUDIANTE:
* **NOMBRE COMPLETO:** CRISTIAN CAMILO PRIETO ROA
* **DOCUMENTO:** 1003587719
* **CORREO INSTITUCIONAL:** ccprietor@unadvirtual.edu.co
* **CURSO:** BIG-DATA (2025 II 16-04)
