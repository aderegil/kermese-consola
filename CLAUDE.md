# kermese-consola

Consola de juegos de feria "Kermese". Todo vive en `index.html`: un solo archivo, sin build,
sin dependencias locales. Se abre directo en el navegador o con cualquier servidor estático.
La interfaz y el código (nombres, comentarios, textos) están en español de México.

## Stack

- HTML + CSS + JavaScript (módulo ES) en `index.html`.
- Supabase con `supabase-js` 2.117.2 importado desde esm.sh
  (`https://esm.sh/@supabase/supabase-js@2.117.2`).
- La URL del proyecto y la publishable key ya están en `index.html` (`SUPABASE_URL`, `SUPABASE_KEY`).
  Es una key pública; los permisos los da RLS en Supabase.

## Cómo funciona

- Developers crean juegos web, cada uno en su propio repo de GitHub Pages llamado
  `kermese-juego-<palabra>` (URL típica: `https://usuario.github.io/kermese-juego-xyz/`).
  Los registran en esta consola.
- La consola abre cada juego en una pestaña nueva con `?jugador=<enterprise_id>&oficial=1`.
- El ranking se actualiza en tiempo real con Supabase Realtime y solo cuenta puntajes
  oficiales (`oficial = true`).

## Tablas en Supabase

Ya existen. No las crees ni las modifiques (ni columnas, ni políticas RLS).

- `apps` (id, nombre, autor, enterprise_id, url, icono, created_at): juegos registrados.
- `puntajes` (id, juego, jugador, puntaje, fecha, oficial): los escribe cada juego.

## Reglas del dominio

- **Enterprise ID de Accenture**: formato `nombre.apellido`, solo minúsculas, números y puntos,
  sin `@accenture.com`. Una sola función valida en toda la consola: `errorEnterpriseId()`.
  Se usa en el registro de juegos (`apps.enterprise_id`) y en "Tu Enterprise ID" del jugador.
- **Jugador**: es su Enterprise ID. Se guarda en localStorage (`kermese.jugador`) y se pasa a los
  juegos como `?jugador=<enterprise_id>`; los juegos lo escriben en `puntajes.jugador`.
- **Liga puntaje ↔ juego**: coincidencia exacta entre `puntajes.juego` y el nombre del repo
  (`kermese-juego-xyz`) que aparece en la ruta de `apps.url` (ver `repoDe()`).
  Sin comparar por nombre del juego, por id ni ignorando acentos o mayúsculas.

## Pruebas

No hay pruebas automatizadas. Para probar, sirve la carpeta con un servidor estático y ábrela
en el navegador. La consola se conecta a la base real: no insertes registros de prueba en
`apps` ni `puntajes` sin permiso; para probar escrituras, intercepta las peticiones a Supabase.
