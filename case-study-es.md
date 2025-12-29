# Caso de éxito  
## Eliminación de splash ads en aplicaciones de cámaras usando Pi-hole

---

## Contexto

En un entorno de red con **28 cámaras de seguridad**, todas ellas dependientes de una
**única aplicación móvil propietaria**, comenzaron a aparecer **anuncios de video forzados**
en la pantalla de inicio de la app.

Estos anuncios presentaban las siguientes características:

- Aparecían **antes de acceder al video en vivo**
- Mostraban **cuentas regresivas falsas**
- Redirigían a más publicidad
- Bloqueaban el acceso rápido a las cámaras en situaciones urgentes

Dado el contexto de **seguridad y monitoreo**, este comportamiento era **operativamente inaceptable**.

---

## Infraestructura

- **Servidor DNS**: Pi-hole sobre Raspberry Pi  
- **Clientes**: teléfonos móviles Android  
- **Red**: LAN doméstica / pequeña oficina  
- **Cámaras**: dependientes exclusivamente de una app móvil (sin RTSP / ONVIF)
- **Restricciones clave**:
  - No era posible instalar bloqueadores (AdGuard, etc.) en los teléfonos
  - No era posible cambiar la app ni el firmware de las cámaras
  - La solución debía ser **a nivel de red**

---

## Problema inicial

A pesar de contar con Pi-hole correctamente configurado y múltiples listas de bloqueo
(OISD, StevenBlack, Energized, etc.):

- Los anuncios **seguían apareciendo**
- El log de Pi-hole mostraba actividad DNS normal
- Bloquear dominios publicitarios “clásicos” no tenía efecto

Esto llevó inicialmente a la percepción de que **Pi-hole no estaba funcionando**, cuando en
realidad el problema era más profundo.

---

## Análisis técnico

Tras un análisis detallado del *Query Log* de Pi-hole, se identificó que:

### 1. Uso de SDKs publicitarios modernos
La app utilizaba múltiples SDKs de monetización móvil, entre ellos:

- Vungle
- Mintegral
- Liftoff
- Rayjump
- Pangle / TikTok Ads

Estos SDKs empleaban:
- subdominios dinámicos
- CNAME cloaking
- tráfico HTTPS exclusivo

---

### 2. Publicidad servida desde CDNs “neutrales”
El **video publicitario en sí** no provenía de dominios típicos de ads, sino de:

- CDNs de gran escala (principalmente ByteDance)
- Dominios compartidos con contenido legítimo
- Infraestructura diseñada para evadir bloqueos DNS simples

Esto explicaba por qué:
- las listas estándar no funcionaban
- bloquear solo los SDKs no eliminaba el video

---

### 3. Confirmación del límite de Pi-hole
Se comprobó que:

- Pi-hole **sí resolvía DNS correctamente**
- Pi-hole **sí bloqueaba dominios cuando correspondía**
- Pero **no puede filtrar contenido HTTPS ni rutas internas**

Cuando se activaba **AdGuard en un teléfono**, los anuncios desaparecían,
confirmando que el bloqueo exitoso ocurría **después del DNS**.

---

## Restricciones críticas

El problema se agravaba por condiciones no negociables:

- ❌ No se podía instalar software adicional en los teléfonos
- ❌ No se podía usar un proxy con inspección TLS
- ❌ No se podía reemplazar la app ni las cámaras
- ❌ La solución debía ser inmediata y demostrable

El plazo para resolver el problema era **de pocas horas**.

---

## Solución implementada

### Enfoque adoptado

Se decidió implementar una solución **agresiva pero controlada**, basada en:

- Bloqueo selectivo de SDKs publicitarios
- Bloqueo de CDNs específicos usados para videos splash
- Uso estricto de **grupos en Pi-hole**
- Aislamiento del impacto únicamente a dispositivos móviles

---

### Implementación técnica

1. Se creó un **grupo específico** en Pi-hole (por ejemplo `Camaras` o `Telefonos`)
2. Se identificaron y bloquearon:
   - SDKs publicitarios completos
   - CDNs de video asociados a splash ads
3. Se permitieron explícitamente servicios críticos:
   - Google / Firebase
   - Servicios de notificaciones
4. Se consolidaron las reglas en una **lista ABP-style dedicada**

Esta lista fue posteriormente publicada como un repositorio público para facilitar su mantenimiento y reutilización.

---

## Resultado

Tras aplicar la solución:

- ✅ Los anuncios de video **dejaron de reproducirse**
- ✅ El acceso a las cámaras volvió a ser inmediato
- ✅ La app continuó funcionando correctamente
- ⚠️ En algunos casos apareció una breve pantalla negra (comportamiento esperado)

El resultado fue considerado **operativamente exitoso**.

---

## Lecciones aprendidas

1. **No toda la publicidad es bloqueable por DNS**
2. Muchas apps modernas están diseñadas explícitamente para **resistir Pi-hole**
3. El uso de **CDNs compartidos** complica el bloqueo selectivo
4. Pi-hole sigue siendo útil, pero **tiene límites claros**
5. Las listas agresivas deben:
   - ser pequeñas
   - estar bien documentadas
   - aplicarse solo por grupo

---

## Conclusión

Este caso demuestra que, incluso con fuertes restricciones, es posible mitigar
publicidad intrusiva en aplicaciones críticas **si se entiende el modelo de entrega
de contenido moderno** y se acepta un enfoque **quirúrgico y controlado**.

La solución no es universal, pero sí **reproducible** para escenarios similares
con aplicaciones de cámaras, IoT y CCTV.

---

## Nota final

Este caso no pretende promover el bloqueo indiscriminado, sino documentar
una **respuesta técnica a una implementación de monetización agresiva**
que interfiere con funciones de seguridad.

Use este enfoque con criterio.
