# Contador de Diamantes — Minecraft

Sitio estático (un solo `index.html`, sin build) con un marcador de diamantes
por equipo, sincronizado en vivo entre todos los dispositivos mediante
Supabase (Postgres + Realtime).

## Qué incluye

- `index.html` — la página completa (HTML + CSS + JS), lista para desplegar tal cual.
- Base de datos ya creada en Supabase (proyecto `minecraft-diamond-counter`),
  con las tablas `team_scores` (total actual por equipo) y `score_history`
  (historial de cambios, usado también para "Deshacer último").
- Las credenciales de Supabase (URL + clave pública `anon`) ya están
  incrustadas en `index.html`. Esto es normal y seguro en Supabase: esa clave
  está pensada para ir en el cliente; el control de acceso real vive en las
  políticas de la base de datos (RLS), no en ocultar la clave.

## Desplegar en Vercel

Cualquiera de estas tres opciones funciona (el sitio no necesita build ni
variables de entorno adicionales):

1. **Arrastrar y soltar**: entra a https://vercel.com/new, elige
   "Deploy" → sube esta carpeta (o solo `index.html`) directamente en el navegador.
2. **CLI de Vercel** (desde esta carpeta):
   ```
   npm i -g vercel
   vercel --prod
   ```
3. **Desde GitHub**: sube esta carpeta a un repositorio y conéctalo en
   Vercel (Import Project). Vercel detecta que es un sitio estático y no
   pide configuración extra.

Al terminar, Vercel te da una URL pública (algo como
`https://tu-proyecto.vercel.app`) — esa es la que compartes con los equipos.

## Cómo funciona la sincronización

- El total de cada equipo vive en la tabla `team_scores` de Supabase.
- Cuando alguien guarda un cambio desde el panel Admin, se actualiza esa
  fila y se agrega un registro a `score_history`.
- Todos los navegadores abiertos (marcador o admin) están suscritos en
  tiempo real a esos cambios (Supabase Realtime), así que el marcador se
  actualiza solo, sin recargar la página.

## Seguridad — léelo antes del evento

El PIN de administrador (**0026**, editable en `index.html` buscando la
línea `var PIN = "0026";`) es una protección **solo de interfaz**: evita que
alguien que abre el panel por curiosidad registre puntos por accidente. La
base de datos en sí tiene las tablas abiertas a lectura/escritura para
cualquiera que tenga la clave pública del sitio (igual que la clave, es
visible en el código fuente de la página). Para una dinámica informal esto
es suficiente, pero si te preocupa que alguien manipule el marcador
directamente contra la API de Supabase (sin pasar por la página), avísame y
lo reforzamos con autenticación real.

## Editar equipos o el PIN

Todo el contenido (nombres, colores, PIN) está al inicio del bloque
`<script>` dentro de `index.html`, en la constante `TEAM_META` y la
variable `PIN`. Cambia lo que necesites y vuelve a desplegar.
