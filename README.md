# Modelo Predictivo: Mejor Canal de Contacto

**Autor:** Jose Francisco Ceron Ponzead  
**Objetivo:** Predecir el mejor canal de contacto (WhatsApp vs Línea Móvil) para clientes en mora

---

## Resumen del Modelo

- **Algoritmo:** XGBoost Optimizado
- **Métricas:** ROC-AUC >0.75, Threshold óptimo 0.360
- **Variables clave:** Historial RPC por canal, días de mora, datos demográficos
- **Validación:** Out-of-Time exitosa

---

## Arquitectura de Sistema (Diseño Conceptual)

### 1. API de Predicción

**Servicio REST** que expone el modelo para consumo externo:
- `POST /predict` → Devuelve canal recomendado + confianza
- `POST /predict/batch` → Predicciones masivas
- Consumible por: Web, móvil, contact center

### 2. Sistema de Actualización de Datos

**Problema:** Clientes con datos desactualizados (hasta 5 años)

**Solución:**
- **Banca Móvil:** Formulario auto-servicio con incentivos
- **WhatsApp Bot:** Conversacional para actualización guiada
- **Portal Web:** Acceso directo desde SMS/email
- **Durante Gestión:** Gestor valida y corrige en tiempo real

**Campos prioritarios:** Teléfono, email, dirección, situación laboral, ingresos

### 3. Sistema de Trazabilidad

**Base de datos** que registra cada intento de contacto:
- Canal utilizado vs canal recomendado
- Resultado (éxito/fallo + motivo)
- Días de mora, saldo vencido
- Gestor, duración, resultado de gestión
- Promesas de pago y montos comprometidos

**Dashboard en tiempo real:**
- Efectividad por canal y segmento de mora
- Comparativa modelo vs decisión manual
- Mejor hora/día para contactar
- ROI de estrategias

### 4. Sistema de Feedback

**Ciclo de mejora continua:**
- Feedback automático post-contacto
- Feedback manual del gestor (cuando cambia canal recomendado)
- Preferencias del cliente
- Reentrenamiento mensual con nuevos datos

### 5. Enfoque en Clientes en Mora

**Segmentación:**
- Mora temprana (1-30 días)
- Mora media (31-90 días)  
- Mora avanzada (>90 días)

**Métricas específicas:**
- Tasa de contacto por segmento
- Efectividad de promesas de pago
- Recuperación de cartera

---

## Flujo de Uso

```
1. Gestor abre ficha de cliente
   ↓
2. Sistema consulta API → obtiene canal recomendado
   ↓
3. Gestor contacta cliente (puede seguir o no recomendación)
   ↓
4. Sistema registra: canal usado, resultado, gestión
   ↓
5. Feedback alimenta reentrenamiento del modelo
```

---

## Stack Tecnológico (Propuesta)

| Componente | Tecnología |
|------------|------------|
| API Backend | FastAPI (Python) |
| Base de datos | ORACLE, IMPALA |
| Frontend | Power apps |
| Cloud | Azure |
| Monitoreo | Grafana + Prometheus |

---

## KPIs Esperados

- ✅ Contacto exitoso: **+20%** vs baseline
- ✅ Intentos fallidos: **-30%**
- ✅ Datos actualizados: **>80%** de clientes en 6 meses
- ✅ ROI: **>150%** en 12 meses

---

## Ejemplo de Request/Response

**Request:**
```json
{
  "id_cliente": "CLI-12345",
  "dias_mora": 45,
  "saldo_vencido": 5000,
  "rpc_whatsapp_hist": 0.35,
  "rpc_linea_hist": 0.12
}
```

**Response:**
```json
{
  "recommended_channel": "WhatsApp",
  "confidence": 0.78,
  "probability_whatsapp": 0.78,
  "probability_linea": 0.22
}
```no