# Camera Splash Ads – Lista ABP agresiva para Pi-hole

## Descripción general

Este repositorio provee una **lista de bloqueo agresiva en formato ABP** diseñada para eliminar
**anuncios de video en pantallas de inicio (splash ads)** y **publicidad forzada**
comúnmente presente en **aplicaciones móviles de cámaras, IoT y CCTV**.

Este tipo de anuncios suele aparecer **antes de permitir el acceso al video en vivo**,
muchas veces con **cuentas regresivas falsas** o botones de “omitir” engañosos, lo que los
vuelve especialmente problemáticos en **contextos de seguridad y videovigilancia**.

Esta lista surge para resolver un **problema operativo real**, donde el bloqueo tradicional
basado en DNS (incluyendo listas estándar de Pi-hole) resultó insuficiente.

---

## Por qué existe esta lista

Muchas aplicaciones modernas de cámaras e IoT implementan publicidad de forma
**deliberadamente resistente al bloqueo por DNS**:

- Los anuncios se sirven desde la **misma infraestructura CDN** que el contenido legítimo
- Los videos publicitarios se entregan mediante **CDNs neutrales y de gran escala**
  (por ejemplo ByteDance / Pangle)
- Los SDKs publicitarios utilizan **CNAME cloaking, dominios compartidos y tráfico HTTPS**
- Bloquear únicamente dominios “obvios” de publicidad ya no es suficiente

Como resultado:

- Las listas de bloqueo estándar (OISD, StevenBlack, Energized, etc.) **no detienen estos anuncios**
- Pi-hole puede parecer “no funcionar”, aun estando correctamente configurado
- Los bloqueadores a nivel dispositivo (por ejemplo AdGuard en el teléfono) sí funcionan,
  pero **no siempre es posible desplegarlos**

Esta lista fue creada tras **extensas pruebas en entornos reales**, para ofrecer una
**mitigación a nivel de red** cuando no es posible usar soluciones en el dispositivo final.

---

## Qué hace esta lista

Esta lista bloquea de forma agresiva:

- Principales SDKs de publicidad móvil y plataformas de mediación:
  - Vungle
  - Mintegral
  - Liftoff
  - Rayjump
  - Pangle / TikTok Ads
- CDNs utilizados específicamente para **anuncios de video splash e intersticiales**
- Dominios CDN asociados a ByteDance, usados frecuentemente para servir videos publicitarios

El objetivo **no es un bloqueo estético de anuncios**, sino:

- Evitar que los videos publicitarios de inicio se carguen
- Forzar que las solicitudes de anuncios fallen o expiren por timeout
- Permitir que la aplicación continúe hacia la interfaz principal **sin mostrar publicidad**

---

## Qué NO hace esta lista

Es importante ser explícito:

- ❌ Esta lista **NO garantiza compatibilidad** con todas las aplicaciones
- ❌ Esta lista **NO es apta para uso global** en toda la red
- ❌ Esta lista **puede romper** aplicaciones con publicidad no relacionadas con cámaras
- ❌ Esta lista **NO reemplaza** el filtrado de contenido a nivel dispositivo

Esta lista es una **solución puntual y agresiva**, no un bloqueador de propósito general.

---

## Uso recomendado (IMPORTANTE)

⚠️ **NO APLICAR ESTA LISTA DE FORMA GLOBAL**

Buenas prácticas recomendadas:

- Utilizar **gestión de grupos en Pi-hole**
- Aplicar esta lista **únicamente** a:
  - Teléfonos móviles
  - Tablets
  - Dispositivos dedicados a monitoreo de cámaras
- Mantenerla aislada del tráfico general de usuarios

Esto reduce efectos colaterales mientras resuelve el problema específico.

---

## Cómo agregar esta lista a Pi-hole

### 1. Copiar la URL RAW del archivo

Utilizá la URL RAW del archivo, por ejemplo:

https://raw.githubusercontent.com/camammoli/lista_pihole/refs/heads/master/camera-splash-ads-aggressive.txt


*(Ajustar el nombre del archivo si fuese necesario)*

---

### 2. Agregar la lista en Pi-hole

1. Abrí la interfaz de administración de Pi-hole
2. Andá a **Settings → Adlists**
3. Pegá la URL RAW
4. Hacé clic en **Save**
5. Luego andá a **Tools → Update Gravity**

---

### 3. Asignar la lista a un grupo (recomendado)

1. Ir a **Group Management → Groups**
2. Crear un grupo (por ejemplo `Cameras` o `Mobile-Cameras`)
3. Asignar únicamente los dispositivos relevantes
4. Asociar esta lista a ese grupo

---

## Comportamiento esperado

Cuando se aplica correctamente:

- Los anuncios de video en pantalla de inicio **no deberían cargarse**
- La aplicación puede:
  - mostrar una pantalla negra brevemente
  - pausar 1–2 segundos
  - luego pasar directamente a la vista de cámaras
- La funcionalidad principal de las cámaras debería mantenerse operativa

Este comportamiento es **intencional y aceptable**.

---

## Descargo de responsabilidad

Esta lista se proporciona **tal cual**, sin garantía alguna.

Existe debido a que ciertos proveedores adoptan modelos de monetización agresivos
que interfieren con funcionalidades críticas (por ejemplo cámaras de seguridad).
Usala de forma responsable y bajo tu propio riesgo.

---

## Contribuciones

Las contribuciones son bienvenidas, pero tené en cuenta:

- Mantener la lista **pequeña y enfocada**
- Evitar agregar dominios publicitarios genéricos
- Documentar por qué cada dominio nuevo es necesario
- Evitar romper servicios esenciales de Google / Firebase
