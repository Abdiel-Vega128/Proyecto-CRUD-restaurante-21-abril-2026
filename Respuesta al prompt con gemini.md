¡Hola! Como desarrollador de software, me encanta este enfoque. Vamos a estructurar este proyecto de forma profesional. 

Para que los estudiantes entiendan la magnitud de lo que estamos construyendo, integraremos la lógica de **Antigravity** (un framework conceptual de orquestación de agentes) para que el desarrollo no sea solo escribir código, sino gestionar "entidades inteligentes" que manejan el flujo de datos.

---

## 💡 Metodología de Trabajo: El Enfoque de Agentes

Antes de tocar el código, definiremos nuestro **Ecosistema de Agentes** para el proyecto `crudrestaurante0395`:

### 1. Roles y Skills (Habilidades)
* **Agente de Persistencia (Firebase Specialist):** Encargado de la comunicación con Firestore. Sus skills incluyen `connect()`, `pushData()`, y `streamUpdates()`.
* **Agente de Interfaz (UX Designer):** Encargado de capturar datos del empleado. Sus skills son `validateForm()` y `renderList()`.
* **Agente de Orquestación (Antigravity Core):** El "cerebro" que recibe una orden (ej: "Actualizar salario") y decide qué agente debe actuar.

### 2. Flujo de Trabajo (Workflow)
1.  **Trigger:** El usuario pulsa "Guardar".
2.  **Agente Interfaz:** Valida que el nombre no esté vacío.
3.  **Agente Orquestador:** Recibe el objeto `Empleado` y lo envía al Agente de Persistencia.
4.  **Agente Persistencia:** Escribe en Firestore y devuelve una señal de éxito.

---

## 📂 Estructura de Carpetas del Proyecto

```text
crudrestaurante/
├── lib/
│   ├── agents/            # Lógica de Antigravity (Roles y Skills)
│   │   ├── employee_agent.dart
│   ├── models/            # Estructura de datos
│   │   ├── employee_model.dart
│   ├── services/          # Conexión directa con Firebase
│   │   ├── firebase_service.dart
│   ├── ui/                # Pantallas y Widgets
│   │   ├── home_screen.dart
│   │   ├── employee_form.dart
│   └── main.dart          # Punto de entrada
├── pubspec.yaml           # Dependencias
└── android/ios/           # Archivos de configuración nativa
```

---

## 🛠️ Paso 1: Configuración de Firebase y Dependencias

### Crear el proyecto en la Consola de Firebase
1.  Ve a [Firebase Console](https://console.firebase.google.com/).
2.  Crea un proyecto llamado `bdcrudrestaurante0395`.
3.  Habilita **Cloud Firestore** en modo prueba.
4.  Crea una colección llamada **"empleados"**.

### Integración de Librerías (`pubspec.yaml`)
Para que Flutter hable con Firebase, añade estas líneas bajo `dependencies`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.27.0
  cloud_firestore: ^4.15.8
```
*Ejecuta en consola: `flutter pub get`*

---

## 👨‍💻 Implementación de Código (Capa Funcional)

### 1. El Modelo (`models/employee_model.dart`)
Define cómo luce un empleado en nuestro sistema.

```dart
class Employee {
  String id;
  String nombre;
  DateTime fechaNacimiento;
  double salario;

  Employee({required this.id, required this.nombre, required this.fechaNacimiento, required this.salario});

  // Convertir de Map (Firestore) a Objeto
  factory Employee.fromMap(Map<String, dynamic> map, String id) {
    return Employee(
      id: id,
      nombre: map['nombre'] ?? '',
      fechaNacimiento: (map['fechaNacimiento'] as dynamic).toDate(),
      salario: (map['salario'] ?? 0).toDouble(),
    );
  }

  // Convertir de Objeto a Map (Para enviar a Firestore)
  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'fechaNacimiento': fechaNacimiento,
      'salario': salario,
    };
  }
}
```

### 2. El Agente de Persistencia (`services/firebase_service.dart`)
Este es el "Skill" principal de nuestro flujo.

```dart
import 'cloud_firestore/cloud_firestore.dart';
import '../models/employee_model.dart';

class FirebaseService {
  final FirebaseFirestore _db = FirebaseFirestore.instance;

  // READ - Obtener empleados en tiempo real
  Stream<List<Employee>> getEmployees() {
    return _db.collection('empleados').snapshots().map((snapshot) =>
        snapshot.docs.map((doc) => Employee.fromMap(doc.data(), doc.id)).toList());
  }

  // CREATE
  Future<void> addEmployee(Employee emp) {
    return _db.collection('empleados').add(emp.toMap());
  }

  // UPDATE
  Future<void> updateEmployee(Employee emp) {
    return _db.collection('empleados').doc(emp.id).update(emp.toMap());
  }

  // DELETE
  Future<void> deleteEmployee(String id) {
    return _db.collection('empleados').doc(id).delete();
  }
}
```

---

## 🤖 Implementación con Filosofía Antigravity

En una práctica guiada, enseñamos a los alumnos que el UI no debe llamar directamente a Firebase. Llamamos al **Agente**.

### UI de Ejemplo: Lista de Empleados (`ui/home_screen.dart`)

```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';
import '../models/employee_model.dart';

class HomeScreen extends StatelessWidget {
  final FirebaseService _service = FirebaseService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("CRUD Restaurante Agente")),
      body: StreamBuilder<List<Employee>>(
        stream: _service.getEmployees(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          
          var employees = snapshot.data!;
          return ListView.builder(
            itemCount: employees.length,
            itemGenerator: (context, index) {
              return ListTile(
                title: Text(employees[index].nombre),
                subtitle: Text("Salario: \$${employees[index].salario}"),
                trailing: IconButton(
                  icon: Icon(Icons.delete, color: Colors.red),
                  onPressed: () => _service.deleteEmployee(employees[index].id),
                ),
                onTap: () {
                  // Aquí se dispararía el flujo de actualización
                },
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () {
          // Acción del Agente: Crear nuevo empleado de prueba
          _service.addEmployee(Employee(
            id: '',
            nombre: 'Nuevo Empleado',
            fechaNacimiento: DateTime.now(),
            salario: 1200.0
          ));
        },
      ),
    );
  }
}
```

---

## 🚀 Guía de Inicialización (`main.dart`)

Es vital inicializar Firebase antes de que la app arranque:

```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'ui/home_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Conexión con el backend
  runApp(MaterialApp(
    home: HomeScreen(),
    theme: ThemeData(primarySwatch: Colors.orange),
  ));
}
```

### Resumen para el Estudiante:
1.  **Crea la carpeta**: `flutter create crudrestaurante`.
2.  **Configura Firebase**: Descarga el archivo `google-services.json` (Android) o `GoogleService-Info.plist` (iOS) y colócalo en las carpetas correspondientes.
3.  **Añade las librerías**: Usa el `pubspec.yaml`.
4.  **Aplica los Agentes**: Divide tu código en Modelos, Servicios (Agentes) y UI.

¿Te gustaría que desarrollemos el formulario de entrada de datos con validaciones específicas para la fecha de nacimiento?
