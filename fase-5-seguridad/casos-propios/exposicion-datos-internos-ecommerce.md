# Exposición de datos internos en API pública de e-commerce

**Fecha:** noviembre 2026
**Contexto:** proyecto personal de práctica, stack Next.js + Supabase

## Qué encontré

Usando Burp Suite para interceptar el tráfico de un sitio de e-commerce,
noté que la respuesta de la API pública de productos incluía campos que
no deberían ser visibles para el público:

- `costPrice` — costo interno del producto
- `wholesalePrice` — precio mayorista
- `suppliers` — nombre y costo unitario del proveedor
- `stock` — cantidad exacta disponible por talle

## Por qué pasaba

El backend construía el objeto completo de la base de datos y lo
enviaba entero al frontend, sin filtrar qué campos eran realmente
necesarios para mostrar la página al público.

## Cómo lo encontré

1. Intercepté el tráfico con Burp Suite mientras navegaba el sitio
   como cliente normal.
2. Busqué en las respuestas JSON palabras clave como "cost", "price",
   "supplier".
3. Confirmé el alcance revisando el código fuente del backend.

## Cómo lo corregí

Separé un tipo `PublicProduct` con solo los campos necesarios para el
frontend público, distinto del tipo `Product` completo usado en el
panel de administración. Para el stock, en vez de eliminarlo, lo
transformé en un booleano `inStock`, ya que el frontend sí necesita
saber disponibilidad (aunque no la cantidad exacta).

## Lección

Nunca enviar al cliente el objeto completo que viene de la base de
datos. Siempre construir explícitamente la forma de respuesta pública,
eligiendo campo por campo.