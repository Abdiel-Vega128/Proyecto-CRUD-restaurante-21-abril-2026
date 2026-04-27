Aquí tienes un ejemplo claro y bien estructurado de cómo modelar una base de datos para un restaurante italiano usando un enfoque tipo NoSQL (como MongoDB), con **base de datos → colecciones → documentos → atributos y tipos de datos**.

---

# 🍝 Base de datos: `restaurante_italiano`

## 📂 Colección: `clientes`

**Documento ejemplo:**

```json
{
  "_id": ObjectId,
  "nombre": "Juan Pérez",
  "telefono": "6561234567",
  "email": "juan@email.com",
  "direccion": {
    "calle": "Av. Roma",
    "numero": 123,
    "ciudad": "Ciudad Juárez"
  },
  "fecha_registro": Date
}
```

**Atributos y tipos:**

* `nombre` → String
* `telefono` → String
* `email` → String
* `direccion` → Object
* `fecha_registro` → Date

---

## 🍕 Colección: `menu`

**Documento ejemplo:**

```json
{
  "_id": ObjectId,
  "nombre_plato": "Pizza Margherita",
  "categoria": "Pizza",
  "precio": 180.50,
  "ingredientes": ["queso", "tomate", "albahaca"],
  "disponible": true
}
```

**Atributos y tipos:**

* `nombre_plato` → String
* `categoria` → String
* `precio` → Number (Decimal)
* `ingredientes` → Array (String)
* `disponible` → Boolean

---

## 🧾 Colección: `pedidos`

**Documento ejemplo:**

```json
{
  "_id": ObjectId,
  "cliente_id": ObjectId,
  "fecha": Date,
  "estado": "en preparación",
  "total": 350.00,
  "detalles": [
    {
      "plato_id": ObjectId,
      "nombre": "Pizza Margherita",
      "cantidad": 2,
      "precio_unitario": 180.50
    }
  ]
}
```

**Atributos y tipos:**

* `cliente_id` → ObjectId (relación con clientes)
* `fecha` → Date
* `estado` → String
* `total` → Number
* `detalles` → Array de objetos

---

## 👨‍🍳 Colección: `empleados`

**Documento ejemplo:**

```json
{
  "_id": ObjectId,
  "nombre": "Mario Rossi",
  "puesto": "Chef",
  "telefono": "6569876543",
  "salario": 12000,
  "fecha_contratacion": Date
}
```

**Atributos y tipos:**

* `nombre` → String
* `puesto` → String
* `telefono` → String
* `salario` → Number
* `fecha_contratacion` → Date

---

## 🧀 Colección: `proveedores`

**Documento ejemplo:**

```json
{
  "_id": ObjectId,
  "nombre": "Distribuidora Italiana",
  "contacto": "Carlos López",
  "telefono": "6565555555",
  "productos": ["queso", "pasta", "vino"]
}
```

**Atributos y tipos:**

* `nombre` → String
* `contacto` → String
* `telefono` → String
* `productos` → Array (String)

---

# 🔗 Relaciones clave

* `pedidos.cliente_id` → referencia a `clientes`
* `pedidos.detalles.plato_id` → referencia a `menu`

---

# 💡 Notas importantes

* MongoDB usa documentos flexibles (no necesitas esquema rígido).
* Puedes incrustar datos (como `detalles` dentro de `pedidos`) para mejorar rendimiento.
* Ideal para apps móviles o sistemas rápidos de restaurante.

---

Si quieres, puedo también:

* Convertir este modelo a **SQL (MySQL/PostgreSQL)**
* Hacerte un **diagrama entidad-relación**
* O adaptarlo para una app móvil 📱
