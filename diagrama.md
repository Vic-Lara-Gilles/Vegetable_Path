# Sistema de Gestión de Cosecha y BINS

## Diagrama de Flujo Completo del Sistema

```mermaid
flowchart LR
    Start(("INICIO"))
    
    %% ===== PROCESO 1: CREACIÓN DE PERFILES =====
    Start --> P1["<b>1. PERFILES</b><br/>📍 Sistema"]
    
    subgraph Perfiles ["👥 PERFILES"]
        direction TB
        P1_Super["Superusuario"]
        P1_Calidad["Control Calidad"]
        P1_Pistol["Pistolero"]
        P1_Cosech["Cosechero"]
        P1_Desp["Despachador"]
        P1_Visita["Visita"]
    end
    
    P1 --> Perfiles
    Perfiles --> P2
    
    %% ===== PROCESOS 2-4: PLANIFICACIÓN =====
    P2["<b>2. LIBERACIÓN<br/>CUARTEL</b><br/>👤 Agrónomo<br/>📍 Sistema"]
    P3["<b>3. ORDEN<br/>COSECHA</b><br/>👤 Agrónomo<br/>📍 Sistema"]
    P4["<b>4. CREACIÓN<br/>BIN</b><br/>👤 Enc. Acopio<br/>📍 Sistema"]
    
    P2 --> P3 --> P4 --> P5
    
    %% ===== PROCESO 5: LLENADO DE BINS =====
    P5["<b>5. LLENADO<br/>BINS</b><br/>👤 Pistolero<br/>📍 Campo"]
    P5c{"<b>CONTROL<br/>CALIDAD</b>"}
    
    P5 --> P5c
    P5c -->|"OK"| P6
    P5c -->|"NO"| P5
    
    %% ===== PROCESOS 6-7: TRASLADO Y RECEPCIÓN =====
    P6["<b>6. TRASLADO</b><br/>👤 Tractoristas<br/>📍 Campo"]
    P7["<b>7. RECEPCIÓN</b><br/>👤 Enc. Bodega<br/>📍 Bodega"]
    P7a{"¿Etiqueta?"}
    
    P6 --> P7 --> P7a
    P7a -->|"SÍ"| P8
    P7a -->|"NO"| P7b["Genera ID"]
    P7b --> P8
    
    %% ===== PROCESOS 8-12: HYDROCOOLER Y DESPACHO =====
    P8["<b>8. REDUCCIÓN<br/>TEMPERATURA</b><br/>👤 Hydrocooler<br/>📍 Hydrocooler"]
    P9["<b>9. PESAJE</b><br/>👤 Hydrocooler<br/>📍 Hydrocooler"]
    P10["<b>10. ALMACENAJE</b><br/>👤 Hydrocooler<br/>📍 Hydrocooler<br/>FIFO"]
    P11["<b>11. DESPACHO</b><br/>👤 Despacho<br/>📍 Bodega Salida"]
    P12["<b>12. GENERACIÓN<br/>GUÍA DESPACHO</b><br/>👤 Despacho<br/>📍 Sistema"]
    
    P8 --> P9 --> P10 --> P11 --> P12 --> End
    
    End(("FIN"))
    
    %% ===== ESTILOS =====
    classDef inicio fill:#4CAF50,stroke:#2E7D32,stroke-width:3px,color:#fff
    classDef proceso fill:#2196F3,stroke:#1565C0,stroke-width:2px,color:#fff
    classDef decision fill:#FF9800,stroke:#E65100,stroke-width:2px,color:#fff
    classDef resultado fill:#9C27B0,stroke:#6A1B9A,stroke-width:2px,color:#fff
    classDef perfil fill:#607D8B,stroke:#37474F,stroke-width:1px,color:#fff,font-size:11px
    
    class Start,End inicio
    class P1,P2,P3,P4,P5,P6,P7,P8,P9,P10,P11,P12 proceso
    class P5c,P7a decision
    class P7b resultado
    class P1_Super,P1_Calidad,P1_Pistol,P1_Cosech,P1_Desp,P1_Visita perfil
```

---

## 📋 Descripción de Actores

| Rol | Responsabilidad | Ubicación |
|-----|----------------|-----------|
| **Superusuario** | Modificación de datos del sistema | Sistema |
| **Control de Calidad** | Control de calidad en BIN virtual | Sistema/Campo |
| **Pistolero** | Crear y llenar BINS | Campo |
| **Cosechero** | Cosechar fruta | Campo |
| **Despachador** | Pesar y despachar fruta | Bodega |
| **Visita** | Ver proceso y reportes | Sistema |
| **Agrónomo** | Liberar cuarteles y crear órdenes | Sistema |
| **Enc. de Acopio** | Supervisar llenado y trazabilidad | Campo |
| **Tractoristas** | Transportar BINS campo → bodega | Campo |
| **Enc. Patio Bodega** | Recibir y verificar BINS | Bodega Entrada |
| **Personal Hydrocooler** | Temperatura, pesaje, almacenaje | Hydrocooler |
| **Personal Despacho** | Salida y guías de despacho | Bodega Salida |

---

## 📍 Ubicaciones del Sistema

```mermaid
flowchart LR
    Sistema["🖥️ SISTEMA<br/>Gestión Digital"]
    Campo["🌳 CAMPO<br/>Cosecha y Acopio"]
    BodegaEnt["📦 BODEGA ENTRADA<br/>Recepción"]
    Hydro["❄️ HYDROCOOLER<br/>Frío y Almacenaje"]
    BodegaSal["🚚 BODEGA SALIDA<br/>Despacho"]
    
    Sistema -.-> Campo
    Campo --> BodegaEnt
    BodegaEnt --> Hydro
    Hydro --> BodegaSal
    
    classDef ubicacion fill:#E3F2FD,stroke:#1976D2,stroke-width:2px
    class Sistema,Campo,BodegaEnt,Hydro,BodegaSal ubicacion
```

---

## ⚠️ Puntos Críticos de Control

### 🔴 Control de Calidad (Proceso 5)
- **Decisión**: Aprobación o rechazo de BINS
- **Criterios**: Parámetros de calidad ingresados
- **Impacto**: BIN rechazado debe ser rellenado

### 🔴 Verificación de Etiquetas (Proceso 7)
- **Validación**: Presencia y correcta información en etiquetas
- **Sin etiqueta**: Se genera ID desde sistema
- **Trazabilidad**: Crítico para seguimiento

### 🔴 Manejo FIFO (Proceso 10)
- **Principio**: First In, First Out
- **Control**: Inventario y rotación de BINS
- **Objetivo**: Evitar pérdida de calidad por tiempo

### 🔴 Guía de Despacho (Proceso 12)
- **Legal**: Documento tributario obligatorio
- **Información**: BINS, sello seguridad, destino
- **Copias**: 2 ejemplares para trazabilidad

---

## 🔄 Flujo de Datos Clave

```mermaid
flowchart LR
    QR1["QR Cosechero<br/>+ Bandeja"]
    QR2["QR BIN<br/>+ Etiqueta"]
    QR3["QR Pesaje<br/>+ Peso"]
    QR4["QR Salida<br/>+ GD"]
    
    QR1 --> DB[("💾 BASE<br/>DE DATOS")]
    QR2 --> DB
    QR3 --> DB
    QR4 --> DB
    
    DB --> Trazabilidad["📊 TRAZABILIDAD<br/>COMPLETA"]
    
    classDef qr fill:#FFF9C4,stroke:#F57F17,stroke-width:2px
    classDef data fill:#C8E6C9,stroke:#388E3C,stroke-width:2px
    
    class QR1,QR2,QR3,QR4 qr
    class DB,Trazabilidad data
```

---

## 🗄️ Modelo de Datos del Sistema

### Diagrama Entidad-Relación

```mermaid
erDiagram
    USUARIO ||--o{ ORDEN_COSECHA : "crea"
    USUARIO ||--o{ BIN : "gestiona"
    USUARIO ||--o{ CONTROL_CALIDAD : "realiza"
    USUARIO ||--o{ GUIA_DESPACHO : "genera"
    
    HUERTO ||--o{ CUARTEL : "contiene"
    CUARTEL ||--o{ ORDEN_COSECHA : "tiene"
    CUARTEL }o--|| VARIEDAD : "cultiva"
    
    ORDEN_COSECHA ||--o{ BIN : "produce"
    
    BIN ||--o{ BANDEJA : "contiene"
    BIN ||--|| CONTROL_CALIDAD : "pasa por"
    BIN ||--|| PESAJE : "registra"
    BIN }o--|| GUIA_DESPACHO : "incluye en"
    
    COSECHERO ||--o{ BANDEJA : "llena"
    
    USUARIO {
        int id_usuario PK
        string nombre
        string apellido
        string email
        string tipo_perfil "Superusuario, Agrónomo, Pistolero, etc."
        string password
        datetime fecha_creacion
        boolean activo
    }
    
    HUERTO {
        int id_huerto PK
        string nombre
        string ubicacion
        float hectareas
        string region
        datetime fecha_creacion
    }
    
    CUARTEL {
        int id_cuartel PK
        int id_huerto FK
        int id_variedad FK
        string codigo_cuartel
        float superficie
        date fecha_plantacion
        string estado_liberacion "Liberado, Bloqueado"
        date fecha_liberacion
        int carencia_dias
        string nivel_madurez
    }
    
    VARIEDAD {
        int id_variedad PK
        string nombre_variedad
        string tipo_fruta
        string descripcion
        int dias_cosecha
    }
    
    ORDEN_COSECHA {
        int id_orden PK
        int id_cuartel FK
        int id_usuario_creador FK
        date fecha_inicio
        date fecha_termino
        int cantidad_bins_estimada
        int cantidad_cosecheros
        float peso_estimado_kg
        string estado "Pendiente, En Proceso, Completada"
        datetime fecha_creacion
    }
    
    BIN {
        int id_bin PK
        string numero_etiqueta UK
        int id_orden_cosecha FK
        int id_cuartel FK
        int id_pistolero FK
        date fecha_cosecha
        datetime fecha_creacion_bin
        datetime fecha_llenado_completo
        string estado "Vacio, Llenando, Completo, En Transito, En Bodega, Despachado"
        string ubicacion_actual "Campo, Transito, Bodega Entrada, Hydrocooler, Bodega Salida"
        float peso_bruto_kg
        float peso_tara_kg
        float peso_neto_kg
        datetime fecha_pesaje
        datetime fecha_ingreso_hydrocooler
        datetime fecha_despacho
        boolean tiene_etiqueta
        string qr_code
    }
    
    BANDEJA {
        int id_bandeja PK
        int id_bin FK
        int id_cosechero FK
        string codigo_bandeja
        datetime fecha_hora_llenado
        string qr_cosechero
        string qr_bandeja
        float peso_estimado_kg
    }
    
    COSECHERO {
        int id_cosechero PK
        string nombre
        string apellido
        string rut
        string codigo_qr UK
        boolean activo
        datetime fecha_registro
    }
    
    CONTROL_CALIDAD {
        int id_control PK
        int id_bin FK
        int id_usuario_inspector FK
        datetime fecha_hora_inspeccion
        string tipo_control "Campo, Bodega"
        string resultado "Aprobado, Rechazado"
        string observaciones
        float calibre_promedio
        float firmeza
        float brix
        int defectos_cantidad
        string nivel_calidad "A, B, C, Rechazo"
    }
    
    PESAJE {
        int id_pesaje PK
        int id_bin FK
        int id_usuario_operador FK
        datetime fecha_hora_pesaje
        float peso_bruto_kg
        float peso_tara_kg
        float peso_neto_kg
        string equipo_pesaje
        string ubicacion "Hydrocooler"
    }
    
    GUIA_DESPACHO {
        int id_guia PK
        string numero_guia UK
        int id_usuario_despachador FK
        date fecha_emision
        datetime hora_despacho
        string destino
        string transportista
        string patente_camion
        string conductor
        int cantidad_bins
        float peso_total_kg
        string centro_costo
        string paking_destino
        string sello_seguridad
        string estado "Emitida, En Transito, Recibida"
        string observaciones
    }
    
    BIN_GUIA_DESPACHO {
        int id_bin_guia PK
        int id_bin FK
        int id_guia FK
        int orden_carga
    }
    
    GUIA_DESPACHO ||--o{ BIN_GUIA_DESPACHO : "incluye"
    BIN ||--o{ BIN_GUIA_DESPACHO : "se incluye en"
```

### 📊 Descripción de Entidades Principales

#### 🔹 USUARIO
Representa todos los actores del sistema con diferentes perfiles de acceso (Superusuario, Agrónomo, Pistolero, Control de Calidad, Despachador, etc.).

#### 🔹 HUERTO y CUARTEL
Estructura la ubicación física de la cosecha. Un huerto contiene múltiples cuarteles, cada uno con una variedad específica de fruta.

#### 🔹 ORDEN_COSECHA
Plan de cosecha creado por el agrónomo que especifica qué cuartel se cosechará, fechas, y recursos necesarios.

#### 🔹 BIN
Entidad central del sistema. Contenedor que almacena la fruta cosechada y cuyo ciclo de vida se rastrea desde el campo hasta el despacho.

#### 🔹 BANDEJA
Unidades individuales de recolección asociadas a un cosechero específico, que se agrupan en un BIN.

#### 🔹 COSECHERO
Trabajadores que realizan la cosecha y llenan las bandejas. Cada uno tiene un código QR único.

#### 🔹 CONTROL_CALIDAD
Registros de inspecciones de calidad realizadas a los BINS en diferentes etapas (campo y bodega).

#### 🔹 PESAJE
Registro del peso de los BINS en el hydrocooler, calculando peso bruto, tara y neto.

#### 🔹 GUIA_DESPACHO
Documento legal de salida que agrupa múltiples BINS para su transporte al destino final.

### 🔑 Relaciones Clave

- Un **CUARTEL** puede tener múltiples **ÓRDENES DE COSECHA**
- Una **ORDEN** produce múltiples **BINS**
- Un **BIN** contiene múltiples **BANDEJAS**
- Cada **BANDEJA** es llenada por un **COSECHERO**
- Cada **BIN** pasa por un **CONTROL DE CALIDAD**
- Cada **BIN** tiene un registro de **PESAJE**
- Una **GUÍA DE DESPACHO** incluye múltiples **BINS**

### 📈 Flujo de Estados del BIN

```mermaid
stateDiagram-v2
    [*] --> Vacio: Creación
    Vacio --> Llenando: Pistolero inicia llenado
    Llenando --> Llenando: Agregar bandejas
    Llenando --> Completo: Última bandeja
    Completo --> Rechazado: Control Calidad NO
    Rechazado --> Llenando: Reintentar
    Completo --> EnTransito: Control Calidad OK
    EnTransito --> EnBodega: Llega a recepción
    EnBodega --> EnHydrocooler: Pasa a frío
    EnHydrocooler --> Pesado: Registro peso
    Pesado --> Almacenado: Espera despacho
    Almacenado --> ListoDespacho: Orden de salida
    ListoDespacho --> Despachado: Guía emitida
    Despachado --> [*]: Entregado
    
    note right of Completo
        Control de Calidad
        es punto crítico
    end note
    
    note right of Almacenado
        Manejo FIFO
        obligatorio
    end note
```