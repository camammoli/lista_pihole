# Caso de éxito  
## Eliminación de splash ads en aplicaciones de cámaras usando Pi-hole

---

## Introducción

La necesidad de documentar este caso surge de una situación concreta y repetida:
**publicidad intrusiva apareciendo en aplicaciones críticas de cámaras de seguridad,
sin una solución clara utilizando listas de bloqueo tradicionales**.

Tras semanas de pruebas, ajustes y diagnósticos, se logró una solución funcional.
En ese punto resultó evidente que el problema —y su resolución— no eran casos aislados,
sino un patrón que probablemente afecte a muchos otros usuarios de cámaras e IoT.

Este documento existe para **explicar por qué fue necesario crear una lista específica,
cómo se llegó a ella y en qué condiciones funciona**, con el objetivo de que otros
puedan reconocer el problema y reproducir la solución de forma informada.

---

## Contexto

El entorno estaba compuesto por **28 cámaras de seguridad**, gestionadas mediante
**dos aplicaciones móviles distintas**:

- **EyePlus** (aproximadamente 10 cámaras)
- **XMEye** (el resto de las cámaras)

Aunque se trata de apps diferentes, desarrolladas por proveedores distintos,
**ambas presentaban exactamente el mismo comportamiento publicitario**.

En las dos aplicaciones comenzaron a aparecer:

- **Anuncios de video forzados** al iniciar la app
- Publicidad mostrada **antes de acceder al video en vivo**
- **Cuentas regresivas falsas** o engañosas
- Redirecciones a más contenido publicitario

Este comportamiento era especialmente grave porque:
- retrasaba el acceso a las cámaras
- interfería con situaciones de monitoreo urgente
- degradaba una función vinculada directamente a seguridad

---

## Infraestructura

- **Servidor DNS**: Pi-hole sobre Raspberry Pi  
- **Clientes**: teléfonos móviles Android  
- **Red**: LAN doméstica / pequeña oficina  
- **Cámaras**:
  - 10 cámaras gestionadas con EyePlus
  - 18 cámaras gestionadas con XMEye
- **Restricciones clave**:
  - No era posible instalar bloqueadores (AdGuard, etc.) en los teléfonos
  - No era posible cambiar la app ni el firmware de las cámaras
  - Las cámaras **solo funcionaban con sus apps propietarias**
  - La solución debía ser **a nivel de red**

---

## Problema inicial

A pesar de contar con Pi-hole correctamente configurado y con múltiples listas de
bloqueo reconocidas (OISD, StevenBlack, Energized, etc.):

- La publicidad seguía apareciendo en **EyePlus y XMEye**
- El log de Pi-hole mostraba consultas DNS normales
- Bloquear dominios publicitarios evidentes no tenía impacto

Esto llevó inicialmente a una conclusión errónea pero común:
que **Pi-hole “no estaba funcionando”**, cuando en realidad el problema estaba
en la **forma moderna en que estas apps entregan publicidad**.

---

## Análisis técnico

El análisis del *Query Log* de Pi-hole reveló que ambas aplicaciones,
a pesar de ser diferentes, compartían **el mismo ecosistema publicitario**.

### 1. Uso de SDKs publicitarios modernos

Tanto EyePlus como XMEye utilizaban múltiples SDKs de monetización móvil, incluyendo:

- Vungle
- Mintegral
- Liftoff
- Rayjump
- Pangle / TikTok Ads

Estos SDKs emplean técnicas como:
- subdominios dinámicos
- CNAME cloaking
- tráfico HTTPS exclusivo

---

### 2. Publicidad servida desde CDNs compartidos

Los **videos splash** no provenían directamente de los dominios del SDK,
sino de infraestructuras CDN de gran escala, principalmente asociadas a ByteDance:

- dominios compartidos con contenido legítimo
- endpoints diseñados para parecer neutrales
- imposibilidad de diferenciar contenido “ad” vs “no ad” solo por hostname

Esto explica por qué:
- las listas estándar no bloqueaban los anuncios
- bloquear únicamente el SDK no eliminaba el video

---

### 3. Confirmación de los límites de Pi-hole

Se comprobó que:

- Pi-hole resolvía DNS correctamente
- Pi-hole bloqueaba dominios cuando correspondía
- Pero **no puede inspeccionar contenido HTTPS ni rutas internas**

Cuando se activaba **AdGuard en un teléfono**, los anuncios desaparecían,
confirmando que el bloqueo efectivo ocurría **a nivel de contenido**, no solo DNS.

---

## Restricciones críticas

El problema estaba condicionado por factores no negociables:

- ❌ No se podía instalar software adicional en los teléfonos
- ❌ No se podía usar proxy con inspección TLS
- ❌ No se podían reemplazar ni EyePlus ni XMEye
- ❌ No se podían cambiar las cámaras
- ❌ La solución debía ser inmediata y verificable

El plazo para resolver el problema era **muy limitado (horas)**.

---

## Solución implementada

### Enfoque adoptado

Se optó por una solución **agresiva pero controlada**, basada en:

- Bloqueo completo de SDKs publicitarios
- Bloqueo de CDNs específicos usados para videos splash
- Uso estricto de **grupos en Pi-hole**
- Impacto limitado exclusivamente a dispositivos móviles

---

### Implementación técnica

1. Se creó un **grupo específico** en Pi-hole (por ejemplo `Camaras`)
2. Se bloquearon de forma explícita:
   - SDKs de monetización
   - CDNs asociados a splash ads
3. Se permitieron explícitamente servicios críticos:
   - Google / Firebase
   - APIs necesarias para notificaciones y login
4. Las reglas se consolidaron en una **lista ABP-style dedicada**

Esta lista se publicó como repositorio p
