# Plan de Trabajo: CRUD Doctores en Flutter + Firebase

## 🗂️ FASE 1: Estructura del Proyecto

### Carpeta y Subcarpeta
```
xflutterhernandezr0599/
└── crudhospital/
    ├── lib/
    │   ├── main.dart
    │   ├── models/
    │   │   └── doctor_model.dart
    │   ├── services/
    │   │   └── firestore_service.dart
    │   ├── screens/
    │   │   ├── doctor_list_screen.dart
    │   │   ├── doctor_form_screen.dart
    │   │   └── doctor_detail_screen.dart
    │   └── widgets/
    │       └── doctor_card.dart
    ├── pubspec.yaml
    ├── google-services.json
    └── android/
        └── app/
            └── build.gradle
```

---

## 🔥 FASE 2: Firebase Console — Crear Base de Datos

### Paso 1 — Crear Proyecto Firebase
```
1. Ir a: https://console.firebase.google.com
2. Click "Agregar proyecto"
3. Nombre del proyecto: crudhospital
4. Habilitar Google Analytics: Opcional
5. Click "Crear proyecto"
```

### Paso 2 — Agregar App Android
```
1. Click ícono Android (</>)
2. Package name: com.xflutterhernandezr0599.crudhospital
3. App nickname: CrudHospital
4. Descargar google-services.json
5. Colocarlo en: android/app/google-services.json
```

### Paso 3 — Crear Firestore Database
```
1. En sidebar izquierdo → "Firestore Database"
2. Click "Crear base de datos"
3. Seleccionar: "Iniciar en modo de prueba"
4. Región: us-central1 (o la más cercana)
5. Click "Listo"
```

### Paso 4 — Crear Colección "doctores"
```
Firestore → "Iniciar colección"
Collection ID: doctores

Primer documento (ejemplo):
├── nombre: "Dr. García"
├── especialidad: "Cardiología"  
└── salario: 45000
```

---

## 📦 FASE 3: Librerías e Integración

### pubspec.yaml completo
```yaml
name: crudhospital
description: CRUD Doctores con Flutter y Firebase
publish_to: 'none'
version: 1.0.0+1

environment:
  sdk: '>=3.0.0 <4.0.0'

dependencies:
  flutter:
    sdk: flutter

  # Firebase Core - Obligatorio, inicializa Firebase
  firebase_core: ^3.3.0

  # Firestore - Base de datos en tiempo real
  cloud_firestore: ^5.2.0

  # Íconos Material Design
  cupertino_icons: ^1.0.2

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^4.0.0

flutter:
  uses-material-design: true
```

### Cómo agregar firebase_core (paso a paso)
```bash
# PASO 1 — Instalar FlutterFire CLI
dart pub global activate flutterfire_cli

# PASO 2 — Instalar Firebase CLI
npm install -g firebase-tools

# PASO 3 — Login Firebase
firebase login

# PASO 4 — Dentro de la carpeta del proyecto
cd xflutterhernandezr0599/crudhospital
flutterfire configure --project=crudhospital

# PASO 5 — Instalar dependencias
flutter pub add firebase_core
flutter pub add cloud_firestore
flutter pub get
```

---

## 📁 FASE 4: Código Flutter — Archivos Dart

### `lib/models/doctor_model.dart`
```dart
class Doctor {
  final String? id;
  final String nombre;
  final String especialidad;
  final double salario;

  Doctor({
    this.id,
    required this.nombre,
    required this.especialidad,
    required this.salario,
  });

  // Convertir de Firestore a Doctor
  factory Doctor.fromMap(Map<String, dynamic> map, String id) {
    return Doctor(
      id: id,
      nombre: map['nombre'] ?? '',
      especialidad: map['especialidad'] ?? '',
      salario: (map['salario'] ?? 0).toDouble(),
    );
  }

  // Convertir de Doctor a Firestore
  Map<String, dynamic> toMap() {
    return {
      'nombre': nombre,
      'especialidad': especialidad,
      'salario': salario,
    };
  }
}
```

---

### `lib/services/firestore_service.dart`
```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/doctor_model.dart';

class FirestoreService {
  final FirebaseFirestore _db = FirebaseFirestore.instance;
  final String coleccion = 'doctores';

  // ✅ CREATE — Crear doctor
  Future<void> crearDoctor(Doctor doctor) async {
    await _db.collection(coleccion).add(doctor.toMap());
  }

  // ✅ READ — Leer todos los doctores (Stream en tiempo real)
  Stream<List<Doctor>> obtenerDoctores() {
    return _db.collection(coleccion).snapshots().map((snapshot) {
      return snapshot.docs
          .map((doc) => Doctor.fromMap(doc.data(), doc.id))
          .toList();
    });
  }

  // ✅ UPDATE — Actualizar doctor
  Future<void> actualizarDoctor(Doctor doctor) async {
    await _db
        .collection(coleccion)
        .doc(doctor.id)
        .update(doctor.toMap());
  }

  // ✅ DELETE — Eliminar doctor
  Future<void> eliminarDoctor(String id) async {
    await _db.collection(coleccion).doc(id).delete();
  }
}
```

---

### `lib/main.dart`
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'firebase_options.dart';
import 'screens/doctor_list_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  // Inicializar Firebase
  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'CRUD Hospital',
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.teal),
        useMaterial3: true,
      ),
      home: const DoctorListScreen(),
    );
  }
}
```

---

### `lib/screens/doctor_list_screen.dart`
```dart
import 'package:flutter/material.dart';
import '../models/doctor_model.dart';
import '../services/firestore_service.dart';
import '../widgets/doctor_card.dart';
import 'doctor_form_screen.dart';

class DoctorListScreen extends StatelessWidget {
  const DoctorListScreen({super.key});

  @override
  Widget build(BuildContext context) {
    final FirestoreService service = FirestoreService();

    return Scaffold(
      appBar: AppBar(
        title: const Text('🏥 Doctores'),
        backgroundColor: Colors.teal,
        foregroundColor: Colors.white,
      ),
      body: StreamBuilder<List<Doctor>>(
        stream: service.obtenerDoctores(),
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return const Center(child: CircularProgressIndicator());
          }

          if (!snapshot.hasData || snapshot.data!.isEmpty) {
            return const Center(
              child: Text('No hay doctores registrados',
                  style: TextStyle(fontSize: 18)),
            );
          }

          final doctores = snapshot.data!;

          return ListView.builder(
            padding: const EdgeInsets.all(12),
            itemCount: doctores.length,
            itemBuilder: (context, index) {
              return DoctorCard(
                doctor: doctores[index],
                onEdit: () {
                  Navigator.push(
                    context,
                    MaterialPageRoute(
                      builder: (_) =>
                          DoctorFormScreen(doctor: doctores[index]),
                    ),
                  );
                },
                onDelete: () async {
                  await service.eliminarDoctor(doctores[index].id!);
                  ScaffoldMessenger.of(context).showSnackBar(
                    const SnackBar(content: Text('Doctor eliminado')),
                  );
                },
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton.extended(
        onPressed: () {
          Navigator.push(
            context,
            MaterialPageRoute(builder: (_) => const DoctorFormScreen()),
          );
        },
        icon: const Icon(Icons.add),
        label: const Text('Nuevo Doctor'),
        backgroundColor: Colors.teal,
        foregroundColor: Colors.white,
      ),
    );
  }
}
```

---

### `lib/screens/doctor_form_screen.dart`
```dart
import 'package:flutter/material.dart';
import '../models/doctor_model.dart';
import '../services/firestore_service.dart';

class DoctorFormScreen extends StatefulWidget {
  final Doctor? doctor; // null = crear, con valor = editar

  const DoctorFormScreen({super.key, this.doctor});

  @override
  State<DoctorFormScreen> createState() => _DoctorFormScreenState();
}

class _DoctorFormScreenState extends State<DoctorFormScreen> {
  final _formKey = GlobalKey<FormState>();
  final FirestoreService _service = FirestoreService();

  late TextEditingController _nombreCtrl;
  late TextEditingController _especialidadCtrl;
  late TextEditingController _salarioCtrl;

  bool get _esEdicion => widget.doctor != null;

  @override
  void initState() {
    super.initState();
    _nombreCtrl =
        TextEditingController(text: widget.doctor?.nombre ?? '');
    _especialidadCtrl =
        TextEditingController(text: widget.doctor?.especialidad ?? '');
    _salarioCtrl = TextEditingController(
        text: widget.doctor?.salario.toString() ?? '');
  }

  @override
  void dispose() {
    _nombreCtrl.dispose();
    _especialidadCtrl.dispose();
    _salarioCtrl.dispose();
    super.dispose();
  }

  Future<void> _guardar() async {
    if (!_formKey.currentState!.validate()) return;

    final doctor = Doctor(
      id: widget.doctor?.id,
      nombre: _nombreCtrl.text.trim(),
      especialidad: _especialidadCtrl.text.trim(),
      salario: double.parse(_salarioCtrl.text.trim()),
    );

    if (_esEdicion) {
      await _service.actualizarDoctor(doctor);
    } else {
      await _service.crearDoctor(doctor);
    }

    if (mounted) Navigator.pop(context);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(_esEdicion ? 'Editar Doctor' : 'Nuevo Doctor'),
        backgroundColor: Colors.teal,
        foregroundColor: Colors.white,
      ),
      body: Padding(
        padding: const EdgeInsets.all(20),
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              _buildTextField(
                controller: _nombreCtrl,
                label: 'Nombre del Doctor',
                icon: Icons.person,
                validator: (v) =>
                    v!.isEmpty ? 'Ingresa el nombre' : null,
              ),
              const SizedBox(height: 16),
              _buildTextField(
                controller: _especialidadCtrl,
                label: 'Especialidad',
                icon: Icons.medical_services,
                validator: (v) =>
                    v!.isEmpty ? 'Ingresa la especialidad' : null,
              ),
              const SizedBox(height: 16),
              _buildTextField(
                controller: _salarioCtrl,
                label: 'Salario (MXN)',
                icon: Icons.attach_money,
                keyboardType: TextInputType.number,
                validator: (v) {
                  if (v!.isEmpty) return 'Ingresa el salario';
                  if (double.tryParse(v) == null) return 'Número inválido';
                  return null;
                },
              ),
              const SizedBox(height: 30),
              SizedBox(
                width: double.infinity,
                height: 50,
                child: ElevatedButton.icon(
                  onPressed: _guardar,
                  icon: Icon(_esEdicion ? Icons.save : Icons.add),
                  label: Text(
                    _esEdicion ? 'Actualizar Doctor' : 'Guardar Doctor',
                    style: const TextStyle(fontSize: 16),
                  ),
                  style: ElevatedButton.styleFrom(
                    backgroundColor: Colors.teal,
                    foregroundColor: Colors.white,
                  ),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }

  Widget _buildTextField({
    required TextEditingController controller,
    required String label,
    required IconData icon,
    TextInputType keyboardType = TextInputType.text,
    String? Function(String?)? validator,
  }) {
    return TextFormField(
      controller: controller,
      keyboardType: keyboardType,
      validator: validator,
      decoration: InputDecoration(
        labelText: label,
        prefixIcon: Icon(icon, color: Colors.teal),
        border: OutlineInputBorder(borderRadius: BorderRadius.circular(12)),
        focusedBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
          borderSide: const BorderSide(color: Colors.teal, width: 2),
        ),
      ),
    );
  }
}
```

---

### `lib/widgets/doctor_card.dart`
```dart
import 'package:flutter/material.dart';
import '../models/doctor_model.dart';

class DoctorCard extends StatelessWidget {
  final Doctor doctor;
  final VoidCallback onEdit;
  final VoidCallback onDelete;

  const DoctorCard({
    super.key,
    required this.doctor,
    required this.onEdit,
    required this.onDelete,
  });

  @override
  Widget build(BuildContext context) {
    return Card(
      margin: const EdgeInsets.only(bottom: 12),
      elevation: 3,
      shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(14)),
      child: ListTile(
        contentPadding:
            const EdgeInsets.symmetric(horizontal: 16, vertical: 10),
        leading: CircleAvatar(
          backgroundColor: Colors.teal.shade100,
          child: const Icon(Icons.local_hospital, color: Colors.teal),
        ),
        title: Text(
          doctor.nombre,
          style: const TextStyle(fontWeight: FontWeight.bold, fontSize: 16),
        ),
        subtitle: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text('🩺 ${doctor.especialidad}'),
            Text(
              '\$${doctor.salario.toStringAsFixed(2)} MXN',
              style: const TextStyle(color: Colors.green),
            ),
          ],
        ),
        trailing: Row(
          mainAxisSize: MainAxisSize.min,
          children: [
            IconButton(
              icon: const Icon(Icons.edit, color: Colors.orange),
              onPressed: onEdit,
            ),
            IconButton(
              icon: const Icon(Icons.delete, color: Colors.red),
              onPressed: () {
                showDialog(
                  context: context,
                  builder: (_) => AlertDialog(
                    title: const Text('¿Eliminar?'),
                    content: Text('¿Deseas eliminar a ${doctor.nombre}?'),
                    actions: [
                      TextButton(
                          onPressed: () => Navigator.pop(context),
                          child: const Text('Cancelar')),
                      ElevatedButton(
                        onPressed: () {
                          Navigator.pop(context);
                          onDelete();
                        },
                        style: ElevatedButton.styleFrom(
                            backgroundColor: Colors.red),
                        child: const Text('Eliminar',
                            style: TextStyle(color: Colors.white)),
                      ),
                    ],
                  ),
                );
              },
            ),
          ],
        ),
      ),
    );
  }
}
```

---

## 🤖 FASE 5: Antigravity — Agentes, Roles, Skills y Flujo

### ¿Qué es Antigravity?
Antigravity es una plataforma de agentes de IA donde defines roles especializados que colaboran en un proyecto. Aquí el diseño completo para este CRUD:

---

### 🧩 Agentes y Roles

```
╔══════════════════════════════════════════════════════╗
║           PROYECTO: crudhospital                     ║
╠══════════════════════════════════════════════════════╣
║  AGENTE 1: ProjectManager                            ║
║  Rol: Coordina tareas y valida entregables           ║
║  Skills: planificacion, seguimiento, documentacion   ║
╠══════════════════════════════════════════════════════╣
║  AGENTE 2: FlutterDev                                ║
║  Rol: Desarrolla UI y lógica Dart                    ║
║  Skills: dart, widgets, forms, navegacion            ║
╠══════════════════════════════════════════════════════╣
║  AGENTE 3: FirebaseEngineer                          ║
║  Rol: Configura y gestiona Firestore                 ║
║  Skills: firestore, reglas_seguridad, colecciones    ║
╠══════════════════════════════════════════════════════╣
║  AGENTE 4: QATester                                  ║
║  Rol: Prueba CRUD y reporta bugs                     ║
║  Skills: testing, validacion_formularios, logs       ║
╚══════════════════════════════════════════════════════╝
```

---

### 📋 Flujo de Trabajo en Antigravity

```
INICIO
  │
  ▼
[ProjectManager] → Crea tareas en el workspace:
  ├── TAREA-01: Configurar Firebase
  ├── TAREA-02: Crear modelo Doctor
  ├── TAREA-03: Implementar FirestoreService
  ├── TAREA-04: Construir UI (List + Form)
  └── TAREA-05: Pruebas CRUD
  │
  ▼
[FirebaseEngineer] ←── TAREA-01
  ├── Crea proyecto en Firebase Console
  ├── Configura Firestore (colección doctores)
  ├── Descarga google-services.json
  └── Entrega: archivo de configuración listo ✅
  │
  ▼
[FlutterDev] ←── TAREA-02, 03, 04
  ├── Crea doctor_model.dart
  ├── Crea firestore_service.dart
  ├── Crea pantallas (list, form, card)
  └── Entrega: código funcional ✅
  │
  ▼
[QATester] ←── TAREA-05
  ├── Prueba crear doctor
  ├── Prueba listar doctores en tiempo real
  ├── Prueba actualizar campos
  ├── Prueba eliminar con confirmación
  └── Entrega: reporte de pruebas ✅
  │
  ▼
[ProjectManager] → Cierra sprint ✅
```

---

### ⚙️ Configuración android/app/build.gradle
```gradle
android {
    compileSdk 34
    defaultConfig {
        applicationId "com.xflutterhernandezr0599.crudhospital"
        minSdk 21
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }
}

dependencies {
    implementation 'com.google.firebase:firebase-bom:33.0.0'
}

apply plugin: 'com.google.gms.google-services'
```

---

### ✅ Comandos Finales para Ejecutar
```bash
# Dentro de xflutterhernandezr0599/crudhospital/

# 1. Obtener dependencias
flutter pub get

# 2. Verificar dispositivo conectado
flutter devices

# 3. Ejecutar proyecto
flutter run

# 4. Para web (opcional)
flutter run -d chrome
```

---

## 📊 Resumen de Archivos del Proyecto

| Archivo | Función |
|---|---|
| `main.dart` | Inicializa Firebase y arranca la app |
| `doctor_model.dart` | Modelo de datos + serialización Firestore |
| `firestore_service.dart` | CRUD completo con Firestore |
| `doctor_list_screen.dart` | Pantalla principal con StreamBuilder |
| `doctor_form_screen.dart` | Formulario crear/editar |
| `doctor_card.dart` | Tarjeta con botones editar/eliminar |
| `pubspec.yaml` | Dependencias Firebase |
| `google-services.json` | Configuración Firebase (descargado de consola) |

> 💡 **Tip Antigravity:** Cada agente trabaja en su rama de Git. El `ProjectManager` hace el merge final al branch `main` cuando todos los entregables están aprobados por `QATester`.
