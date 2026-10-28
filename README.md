# quito-flash-drops-java
# QuitoDrops - Sistema Algorítmico de Gestión de Cupos Relámpago y Eventos en Quito (Java)
**Curso:** Programación III | **Progreso 1**

## 1. Descripción del Proyecto
Solución computacional en Java orientada a eliminar la capacidad ociosa de microempresas gastronómicas y eventos culturales en Quito mediante lotes de promoción relámpago ("Los 15 primeros inscritos"), listas de espera con prioridad (`PriorityQueue`), validación instantánea de pases QR (`HashMap`) y rutas óptimas entre eventos y locales aliados (`Grafo + Dijkstra`).

## 2. Estructura de Módulos Funcionales
1. **Catálogo y Ordenamiento Multicriterio** (`MergeSort` / Búsqueda por filtros).
2. **Asignación de Lotes (Top 15) y Smart Waitlist** (`PriorityQueue` - Max-Heap).
3. **Validación de Pases QR en Tiempo Constante** (`HashMap` en $O(1)$).
4. **Recomendación Geográfica y Plan Cruzado** (`Grafo Lista de Adyacencia` + `Dijkstra` / `BFS`).
5. **Motor de Reputación de Usuarios y Simulador de Rentabilidad**.

## 3. Diagrama UML Preliminar de Dominio
```mermaid
classDiagram
    class Participante {
        <<abstract>>
        -String id
        -String nombre
        -String correo
        -String nodoUbicacionId
        +actualizarUbicacion(String nodoId) void
    }
    class EstudianteUsuario {
        -int puntajeReputacion
        -boolean penalizado
        +solicitarCupo(CampaniaDrop c) PaseQR
        +cancelarReserva(String token) boolean
        +actualizarReputacion(boolean asistio) void
    }
    class ComercioOrganizador {
        -String nombreComercial
        -String tipoNegocio
        -int aforoTotal
        +crearCampania(int cupos, double desc) CampaniaDrop
        +simularRentabilidad(double costo, double precio, int cupos) double
        +validarPaseQR(String token) boolean
    }
    class CampaniaDrop {
        -String idCampania
        -String titulo
        -int limitePrimerosVIP
        -Map ganadoresConfirmados
        -PriorityQueue colaEspera
        +inscribirUsuario(EstudianteUsuario u) PaseQR
        +reasignarCupoSiguiente() EstudianteUsuario
    }
    class LoteBeneficio {
        -String nombreNivel
        -int cuposTotales
        -int cuposRestantes
        -double porcentajeDescuento
        +ocuparCupo() boolean
    }
    class PaseQR {
        -String tokenHash
        -String estado
        +esValido() boolean
        +canjear() void
    }
    class GrafoSectorQuito {
        -Map listaAdyacencia
        +dijkstraRutaMinima(String origen, String destino) List
        +bfsLocalesEnRadio(String nodoEvento, int radioMax) List
    }
    Participante <|-- EstudianteUsuario
    Participante <|-- ComercioOrganizador
    ComercioOrganizador "1" o-- "0..*" CampaniaDrop : publica
    CampaniaDrop "1" *-- "1..*" LoteBeneficio : contiene
    EstudianteUsuario "1" --> "0..*" PaseQR : posee
    CampaniaDrop "1" --> "0..15" PaseQR : emite
    GrafoSectorQuito ..> ComercioOrganizador : localiza
