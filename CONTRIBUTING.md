# Contribuir a lista_pihole

## Criterios para agregar una entrada

Solo se aceptan dominios que cumplan **todos** estos requisitos:

1. **Específicamente relacionados con ads en apps de cámara/IoT** — no es una lista de propósito general
2. **Documentados**: incluir en el PR qué app, qué SDK y cómo se verificó
3. **No rompen servicios esenciales**: evitar dominios de Google, Firebase, o CDNs de uso general
4. **Probados**: verificar que bloquear el dominio elimina el ad sin romper la funcionalidad core de la app

## Formato

Usar formato ABP estándar:

```
! Comentario explicativo — SDK y app afectada
||dominio.ejemplo.com^
```

Ejemplo:
```
! Vungle SDK — splash ads en app de cámara Reolink
||api.vungle.com^
||cdn-lb.vungle.com^
```

## Cómo contribuir

1. Fork del repositorio
2. Crear una rama: `git checkout -b agregar/nombre-sdk`
3. Agregar las entradas con comentario explicativo
4. Abrir un Pull Request describiendo:
   - Qué app genera el ad
   - Qué SDK o red publicitaria
   - Cómo se verificó (tcpdump, Pi-hole logs, etc.)
   - Si bloquearlo rompe algo más

## Qué NO agregar

- Dominios de tracking genérico (ya cubiertos por OISD, StevenBlack, etc.)
- Dominios de Google Ads, Meta Ads — colateral demasiado amplio
- Entradas sin documentar

## Reportar un dominio que falta

Si encontrás un ad que esta lista no bloquea, abrí un [Issue](https://github.com/camammoli/lista_pihole/issues) con:
- App afectada (nombre + versión)
- Dominio detectado (via Pi-hole logs o tcpdump)
- Screenshot o video del ad si es posible
