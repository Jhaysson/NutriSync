# 🥗 NutriSync

# 📘 Documentación — Parte 5

## Seguimiento Corporal + Notas Clínicas

| 👤 Integrante | 📅 Fecha | 🌿 Rama Git |
|---|---|---|
| Jhaysson | 21/05/2026 | `parte5/seguimiento` |

---

## 📋 Resumen General

Esta parte implementa el módulo completo de seguimiento corporal y notas clínicas de NutriSync, incluyendo:

- 📏 Registro de medidas corporales con IMC auto-calculado
- 📈 Historial de medidas con indicadores de cambio (▲/▼)
- 📝 Gestión de notas clínicas vinculables a citas
- 📊 Dashboards generales de seguimiento y notas
- 🕐 Timeline cronológico unificado del paciente
- 🔒 Aislamiento multi-nutricionista en todas las vistas

---

## 🎯 Funcionalidades Implementadas

| # | Funcionalidad | Ruta | Estado |
|---|---|---|---|
| 1 | 📏 Registrar medidas corporales | `/seguimiento/medidas/nueva/<paciente_pk>/` | ✅ Completo |
| 2 | 📊 Historial de medidas con evolución | `/seguimiento/medidas/<paciente_pk>/` | ✅ Completo |
| 3 | 📝 Crear nota clínica | `/seguimiento/notas/nueva/<paciente_pk>/` | ✅ Completo |
| 4 | 📋 Lista de notas por paciente | `/seguimiento/notas/lista/<paciente_pk>/` | ✅ Completo |
| 5 | 👁️ Detalle de nota clínica | `/seguimiento/notas/detalle/<pk>/` | ✅ Completo |
| 6 | 🕐 Timeline del paciente | `/seguimiento/historial/<paciente_pk>/` | ✅ Completo |
| 7 | 📈 Dashboard general de seguimiento | `/seguimiento/` | ✅ Completo |
| 8 | 📋 Dashboard general de notas | `/seguimiento/notas/` | ✅ Completo |
| 9 | ⚙️ Admin de Medidas Corporales | `/admin/` | ✅ Completo |
| 10 | ⚙️ Admin de Notas Clínicas | `/admin/` | ✅ Completo |
| 11 | 🔍 Filtro de notas por tipo | `/seguimiento/notas/lista/<pk>/?tipo=consulta` | ✅ Completo |
| 12 | 🧭 Sidebar con accesos directos | Sidebar | ✅ Completo |

---

## 🖼️ Capturas del Sistema

*(Espacio para capturas — agregar manualmente)*

### 📏 Formulario de Medidas Corporales

*Insertar imagen: seguimiento/medida_form.png*

### 📊 Historial de Medidas con Indicadores

*Insertar imagen: seguimiento/medidas.png*

### 📝 Formulario de Nota Clínica

*Insertar imagen: seguimiento/nota_form.png*

### 📋 Lista de Notas con Filtro

*Insertar imagen: seguimiento/notas.png*

### 👁️ Detalle de Nota Clínica

*Insertar imagen: seguimiento/nota_detalle.png*

### 🕐 Timeline del Paciente

*Insertar imagen: seguimiento/historial.png*

### 📈 Dashboard General de Seguimiento

*Insertar imagen: seguimiento/dashboard.png*

### 📋 Dashboard General de Notas

*Insertar imagen: seguimiento/notas_dashboard.png*

---

## 🗃️ Arquitectura de Modelos

### 📏 Modelo: MedidaCorporal

```python
class MedidaCorporal(models.Model):
    paciente = ForeignKey(Paciente, related_name="medidas")
    fecha = DateField()
    peso_kg = DecimalField(max_digits=5, decimal_places=2)
    talla_cm = DecimalField(max_digits=5, decimal_places=1)
    imc = DecimalField(max_digits=4, decimal_places=1, editable=False)
    grasa_corporal_pct = DecimalField(null=True, blank=True)
    cintura_cm = DecimalField(null=True, blank=True)
    cadera_cm = DecimalField(null=True, blank=True)
    notas = TextField(blank=True)
    fecha_registro = DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ["-fecha", "-fecha_registro"]
        indexes = [models.Index(fields=["paciente", "fecha"])]
```

### 📝 Modelo: NotaClinica

```python
class NotaClinica(models.Model):
    paciente = ForeignKey(Paciente, related_name="notas_clinicas")
    cita = ForeignKey(Cita, null=True, blank=True, on_delete=SET_NULL)
    fecha = DateField()
    titulo = CharField(max_length=200)
    contenido = TextField()
    tipo = CharField(max_length=20, choices=TipoNota.CHOICES)
    fecha_creacion = DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ["-fecha", "-fecha_creacion"]
        indexes = [models.Index(fields=["paciente", "tipo"])]
```

---

## 🖥️ Vistas Implementadas

| Vista | Tipo | Template | Descripción |
|---|---|---|---|
| `MedidaCreateView` | CBV (CreateView) | `seguimiento/medida_form.html` | Formulario para registrar medidas |
| `MedidaListView` | CBV (ListView) | `seguimiento/medidas.html` | Historial con indicadores de cambio |
| `NotaCreateView` | CBV (CreateView) | `seguimiento/nota_form.html` | Crear nota clínica con cita opcional |
| `NotaListView` | CBV (ListView) | `seguimiento/notas.html` | Lista de notas con filtro por tipo |
| `NotaDetailView` | CBV (DetailView) | `seguimiento/nota_detalle.html` | Detalle completo de nota |
| `historial_paciente` | FBV | `seguimiento/historial.html` | Timeline unificado (medidas + notas + citas) |
| `seguimiento_dashboard` | FBV | `seguimiento/dashboard.html` | Dashboard general de seguimiento |
| `notas_dashboard` | FBV | `seguimiento/notas_dashboard.html` | Dashboard general de notas clínicas |

---

## 🌐 URLs Registradas

| 🌍 Ruta | 🔗 Nombre | Vista |
|---|---|---|
| `/seguimiento/` | `seguimiento:dashboard` | `seguimiento_dashboard` |
| `/seguimiento/notas/` | `seguimiento:notas_dashboard` | `notas_dashboard` |
| `/seguimiento/medidas/nueva/<paciente_pk>/` | `seguimiento:medidas_nueva` | `MedidaCreateView` |
| `/seguimiento/medidas/<paciente_pk>/` | `seguimiento:medidas_lista` | `MedidaListView` |
| `/seguimiento/notas/nueva/<paciente_pk>/` | `seguimiento:notas_nueva` | `NotaCreateView` |
| `/seguimiento/notas/lista/<paciente_pk>/` | `seguimiento:notas_lista` | `NotaListView` |
| `/seguimiento/notas/detalle/<pk>/` | `seguimiento:notas_detalle` | `NotaDetailView` |
| `/seguimiento/historial/<paciente_pk>/` | `seguimiento:historial` | `historial_paciente` |

---

## 🧠 Lógica de Negocio Implementada

### Cálculo Automático de IMC

Fórmula estándar OMS: `IMC = peso(kg) / talla(m)²`

Se calcula en el método `save()` del modelo para consistencia de datos:

```python
def save(self, *args, **kwargs):
    if self.peso_kg and self.talla_cm and self.talla_cm > 0:
        talla_m = self.talla_cm / 100
        self.imc = round(self.peso_kg / (talla_m ** 2), 1)
    super().save(*args, **kwargs)
```

### Indicadores de Cambio (Evolución)

En el historial de medidas, cada registro se compara con el anterior:
- ▲ `trending-up` (ámbar) = el valor subió
- ▼ `trending-down` (verde) = el valor bajó
- Sin icono = primera medición o sin cambios

### Aislamiento Multi-Nutricionista

Todas las vistas filtran por `paciente__nutricionista=request.user` a través del mixin `NutricionistaSeguimientoMixin`:

```python
class NutricionistaSeguimientoMixin(LoginRequiredMixin):
    def get_queryset(self):
        return super().get_queryset().filter(
            paciente__nutricionista=self.request.user
        )
```

### Timeline Unificado

La vista `historial_paciente` recolecta medidas, notas y citas del paciente, las unifica en una lista de eventos y las ordena por fecha descendente para mostrar una línea de tiempo completa.

---

## ⚡ Buenas Prácticas Aplicadas

| Técnica | Implementación | Beneficio |
|---|---|---|
| `select_related` | En queries de `MedidaListView`, `NotaListView`, `NotaDetailView` y admin | Evita N+1 en relaciones FK |
| `IMC en save()` | Cálculo automático al guardar | Consistencia de datos, evita recalcular en cada request |
| `NutricionistaSeguimientoMixin` | Mixin base para filtrado | DRY — no repetir filtro en cada vista |
| `get_object_or_404` | En todas las vistas con filtro de nutricionista | 404 automático si no existe o no pertenece |
| `Composite indexes` | `(paciente, fecha)` y `(paciente, tipo)` | Acelera búsquedas frecuentes |
| `Validadores de rango` | `MinValueValidator` / `MaxValueValidator` en campos numéricos | Previene datos inválidos (peso negativo, etc.) |
| `TipoNota.CHOICES` desde `config/choices.py` | Choices centralizados | Mantenibilidad, cambios en un solo lugar |
| `@login_required` | En todas las vistas | Seguridad — solo usuarios autenticados acceden |
| `Empty states` | En todas las listas y dashboards | UX — mensajes amigables cuando no hay datos |

---

## 🗄️ Migraciones

```bash
docker compose exec web python manage.py makemigrations seguimiento
docker compose exec web python manage.py migrate
```

**Migración creada:** `seguimiento/migrations/0001_initial.py`

- Create model `MedidaCorporal`
- Create model `NotaClinica`

---

## 🧪 Pruebas Realizadas

| # | Caso | Resultado |
|---|---|---|
| 1 | Crear medida corporal con peso y talla | ✅ IMC calculado automáticamente |
| 2 | Historial muestra indicadores de cambio | ✅ ▲/▼ según evolución |
| 3 | Crear nota clínica sin cita | ✅ Nota guardada correctamente |
| 4 | Crear nota clínica vinculada a cita | ✅ Relación opcional funciona |
| 5 | Filtrar notas por tipo | ✅ Solo muestra el tipo seleccionado |
| 6 | Timeline muestra medidas + notas + citas | ✅ Eventos ordenados cronológicamente |
| 7 | Dashboard general de seguimiento | ✅ Muestra todos los pacientes con última medida |
| 8 | Dashboard general de notas | ✅ Muestra todas las notas con filtro |
| 9 | Acceso a datos de otro nutricionista | ✅ 404 — get_object_or_404 filtra por nutricionista |
| 10 | System checks | ✅ 0 silenced |
| 11 | URLs resuelven correctamente | ✅ Todas las 8 rutas verificadas |
| 12 | Migraciones aplicadas | ✅ 0001_initial aplicado |

---

## 🔒 Casos de Seguridad

| Caso | Resultado |
|---|---|
| Acceder a medidas de paciente de otro nutricionista | ✅ 404 |
| Acceder a nota de paciente de otro nutricionista | ✅ 404 |
| URL inválida | ✅ 404 personalizado |
| Sin autenticación | ✅ Redirige a login |
| IMC con datos inválidos (talla 0) | ✅ Validación de rango evita división por cero |

---

## 🔗 Dependencias con Otras Partes

| Relación | Parte | Descripción |
|---|---|---|
| 🔽 Requiere | Parte 1 | Auth + Base + Sidebar |
| 🔽 Requiere | Parte 2 | Modelo Paciente |
| 🔽 Requiere | Parte 3 | Modelo Cita (FK opcional en NotaClinica) |
| 🔽 Requiere | Parte 4 | Planes nutricionales (contexto en ficha de paciente) |
| 🔼 Alimenta | Parte 6 | Dashboard completo con métricas de seguimiento |

---

## 📝 Notas Técnicas

- ✅ IMC se almacena en BD (no se calcula en cada request) para permitir ordenamiento y consultas eficientes
- ✅ `editable=False` en campo IMC para evitar modificaciones manuales
- ✅ FK a Cita con `on_delete=SET_NULL` para preservar notas aunque la cita se elimine
- ✅ `related_name` descriptivo en todas las FK (`medidas`, `notas_clinicas`, `notas`)
- ✅ Los dashboards generales (`/seguimiento/` y `/seguimiento/notas/`) permiten navegación directa desde el sidebar sin requerir un paciente específico
- ✅ Filtro por tipo en notas usa query params (`?tipo=consulta`) y persiste en la paginación

---

## ✅ Estado Final

- ✔️ Parte 5 completamente funcional
- 📏 Medidas corporales con IMC auto-calculado
- 📈 Historial con indicadores de evolución
- 📝 Notas clínicas vinculables a citas
- 🕐 Timeline cronológico del paciente
- 📊 Dashboards generales en sidebar
- 🔒 Aislamiento multi-nutricionista
- ⚡ Optimización ORM con select_related
- 🎨 Diseño consistente con Tailwind + Inter + Lucide
