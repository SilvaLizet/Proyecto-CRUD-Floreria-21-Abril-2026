¡Hola! Como desarrollador de software, me encanta este desafío. Vamos a construir **DBCrudFloreria0376**. 

Mencionas **Antigravity**, que en el contexto de desarrollo moderno y flujos de trabajo con IA, se refiere a una metodología de **Agentes Autónomos** (basada en el framework de Agentic Workflow). Aquí no solo programamos, sino que diseñamos entidades que "saben" realizar tareas.

---

## 1. Metodología de Agentes (Antigravity Flow)

Para este proyecto, definiremos un flujo de trabajo donde cada agente tiene una responsabilidad clara:

| Agente | Rol | Skill (Habilidad) |
| :--- | :--- | :--- |
| **Arquitecto** | Definir Estructura | Configuración de `pubspec.yaml` y carpetas. |
| **Firebase Admin** | Configurar Backend | Enlace con Consola Firebase y Firestore. |
| **Data Developer** | Lógica CRUD | Implementación de funciones en Dart. |
| **UI Designer** | Interfaz de Usuario | Creación de formularios y listas en Flutter. |

---

## 2. Plan de Trabajo Paso a Paso

### Paso 1: Creación de Carpeta y Proyecto
Abre tu terminal y ejecuta:
```bash
mkdir crudfloreria
cd crudfloreria
flutter create db_crud_floreria_0376
```

### Paso 2: Configuración de Firebase (Consola)
1. Ve a [Firebase Console](https://console.firebase.google.com/).
2. Crea un proyecto llamado `DBCrudFloreria0376`.
3. En el menú lateral, ve a **Firestore Database** y haz clic en **Crear base de datos**.
4. Inicia en **Modo de prueba** (para desarrollo) y elige una ubicación de servidor.
5. Registra tu app (Android/iOS) siguiendo los pasos para descargar el archivo `google-services.json`.

### Paso 3: Integración de Librerías
Para que Flutter hable con Firebase, necesitamos "paquetes". Edita tu archivo `pubspec.yaml`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^3.0.0  # Librería base
  cloud_firestore: ^5.0.0 # Librería para la base de datos
```
*Luego ejecuta `flutter pub get` en la terminal.*

---

## 3. Estructura de Carpetas del Proyecto
Siguiendo una arquitectura limpia (Clean Architecture) para que los agentes trabajen en orden:

```text
lib/
├── models/
│   └── cliente_model.dart
├── services/
│   └── firebase_service.dart
├── screens/
│   ├── home_screen.dart
│   └── add_cliente_screen.dart
└── main.dart
```

---

## 4. Implementación del Código (Funcional)

### A. El Modelo (Data Developer)
`lib/models/cliente_model.dart`
```dart
class Cliente {
  String id;
  String nombre;
  String fechaNacimiento;
  String numTelefonico;

  Cliente({required this.id, required this.nombre, required this.fechaNacimiento, required this.numTelefonico});

  // Convertir de Firestore a Objeto
  factory Cliente.fromMap(Map<String, dynamic> data, String id) {
    return Cliente(
      id: id,
      nombre: data['Nombre'] ?? '',
      fechaNacimiento: data['Fecha_nacimiento'] ?? '',
      numTelefonico: data['Num_telefonico'] ?? '',
    );
  }

  // Convertir de Objeto a Map para enviar a Firestore
  Map<String, dynamic> toMap() {
    return {
      'Nombre': nombre,
      'Fecha_nacimiento': fechaNacimiento,
      'Num_telefonico': numTelefonico,
    };
  }
}
```

### B. El Servicio CRUD (Firebase Admin)
`lib/services/firebase_service.dart`
```dart
import 'cloud_firestore/cloud_firestore.dart';
import '../models/cliente_model.dart';

FirebaseFirestore db = FirebaseFirestore.instance;

// LEER (Read)
Future<List<Cliente>> getClientes() async {
  List<Cliente> clientes = [];
  QuerySnapshot querySnapshot = await db.collection('Clientes').get();
  for (var doc in querySnapshot.docs) {
    clientes.add(Cliente.fromMap(doc.data() as Map<String, dynamic>, doc.id));
  }
  return clientes;
}

// CREAR (Create)
Future<void> addCliente(String nombre, String fecha, String tel) async {
  await db.collection('Clientes').add({
    'Nombre': nombre,
    'Fecha_nacimiento': fecha,
    'Num_telefonico': tel,
  });
}

// ACTUALIZAR (Update)
Future<void> updateCliente(String id, String nombre, String fecha, String tel) async {
  await db.collection('Clientes').doc(id).update({
    'Nombre': nombre,
    'Fecha_nacimiento': fecha,
    'Num_telefonico': tel,
  });
}

// BORRAR (Delete)
Future<void> deleteCliente(String id) async {
  await db.collection('Clientes').doc(id).delete();
}
```

### C. La Interfaz (UI Designer)
`lib/main.dart` (Simplificado para la práctica)
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'services/firebase_service.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Inicialización vital
  runApp(const FloreriaApp());
}

class FloreriaApp extends StatelessWidget {
  const FloreriaApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Florería CRUD 0376',
      home: const HomeScreen(),
    );
  }
}

class HomeScreen extends StatefulWidget {
  const HomeScreen({super.key});
  @override
  State<HomeScreen> createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Clientes Florería 0376")),
      body: FutureBuilder(
        future: getClientes(),
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            return ListView.builder(
              itemCount: snapshot.data?.length,
              itemBuilder: (context, index) {
                return ListTile(
                  title: Text(snapshot.data![index].nombre),
                  subtitle: Text(snapshot.data![index].numTelefonico),
                  trailing: IconButton(
                    icon: const Icon(Icons.delete, color: Colors.red),
                    onPressed: () async {
                      await deleteCliente(snapshot.data![index].id);
                      setState(() {}); // Refrescar lista
                    },
                  ),
                );
              },
            );
          } else {
            return const Center(child: CircularProgressIndicator());
          }
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () async {
          // Aquí llamarías a un diálogo para agregar datos
          await addCliente("Nuevo Cliente", "2026-04-21", "12345678");
          setState(() {});
        },
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

---

## Flujo de Trabajo Final
1. **Agente Arquitecto**: Verifica que la carpeta `crudfloreria` tenga el `pubspec.yaml` correcto.
2. **Agente Firebase**: Asegura que el `main.dart` llame a `Firebase.initializeApp()`.
3. **Agente Skill**: El flujo CRUD se dispara cuando el usuario presiona el botón "Add" o el icono de basurero.

¿Te gustaría que desarrollemos el formulario detallado para capturar la fecha con un calendario (`DatePicker`)?
