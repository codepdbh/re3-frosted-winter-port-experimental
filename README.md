# RE3 Frosted Winter Port Experimental

Trabajo experimental para correr el mod de GTA III **Frosted Winter Remastered** (de Pistukas Mods) sobre el port a Android de [RE3 Android Evolved](https://github.com/codepdbh/re3-android-port-evolved) (mismo motor, mismo autor).

## ⚠️ Estado: experimental, no jugable todavía

No crashea más al iniciar partida (llegamos a in-game real: controles táctiles, HUD y posición del jugador se renderizan bien), pero **el mundo 3D se dibuja como estática/ruido** en vez de las calles y edificios del mod. Ese es el problema pendiente.

## Qué es esto

Un product flavor de Gradle nuevo (`fwr`) agregado sobre el mismo código de `re3-android-port-evolved`:
- Mismo motor (código C++ compartido), mismos arreglos de Android.
- App separada (`com.re3.game.fwr`, ícono propio) que lee sus archivos de juego de una carpeta distinta en el celular (`re3GTA_FWR`), para convivir instalada junto al juego base sin pisarlo.
- El mod se instala como overlay: primero una copia completa del GTA III base (para traer `anim/`, config, etc. que el paquete del mod no incluye por sí solo — asume que lo copiás encima de una instalación existente), y encima los archivos propios del mod (mapas, texturas, modelos, audio, `MAIN.scm`).
- Los `.dll`/`.asi`/scripts `.cleo` del paquete original del mod (para PC, vía inyección en el `.exe`) no sirven en Android y fueron excluidos — no hay intérprete de CLEO en este motor.

## Bugs de compatibilidad con mods encontrados y arreglados acá

Varios límites y faltas de verificación del motor original, nunca antes disparados con el juego base, que un mod "total conversion" (mapas/vehículos mucho más pesados que los originales) sí dispara:

- Varios límites de memoria fijos (`NUMOBJECTINFO`, `NUMPTRNODES`, `NUMBUILDINGS`, `NUMPHONES`, etc. en `config.h`) dimensionados para los mapas originales.
- Modelos de vehículo con listas de materiales mal formadas (entradas nulas o punteros basura) que el motor no esperaba.
- Un bug real en el parser de modelos `.dff` de librw (índice de material compartido sin verificar límites) — parcheado en el fork [librw-re3-android](https://github.com/codepdbh/librw-re3-android).
- Un sprite de pantalla de carga compartido (`LoadSplash()`) cuyo TXD puede ser reciclado por el sistema de streaming sin avisar — causaba varios crashes distintos en puntos distintos del arranque.
- Sistema de variables de debug (`CTweakVars`) deshabilitado para Android — no se usa (no hay menú de debug táctil) y crasheaba al registrarse.

Ver el historial de commits para el detalle completo de cada uno.

## Instalación

Requiere una copia legítima de GTA III ya instalada y funcionando con `re3-android-port-evolved` (para tener `re3GTA` con `anim/`, `data/CAPS.DAT`, etc.), más los archivos del mod Frosted Winter Remastered (sin las carpetas `CLEO/`, `scripts/`, `mss/` ni los `.dll` sueltos).

## Building

Igual que `re3-android-port-evolved`, pero usando el flavor `fwr`:

```
./gradlew assembleFwrDebug
```

## Relación con el repo principal

Este repo parte de una copia completa de [re3-android-port-evolved](https://github.com/codepdbh/re3-android-port-evolved) (mismo historial) para no depender de mantenerlos sincronizados a mano. Los arreglos de robustez del motor que no son específicos de este mod (los límites de memoria, los chequeos de punteros) también fueron subidos al repo principal por separado. El flavor `fwr` y los arreglos más frágiles/específicos de esta investigación (el manejo de `LoadSplash`, el MTE de diagnóstico, `CTweakVars`) quedan solo acá mientras siguen siendo experimentales.
