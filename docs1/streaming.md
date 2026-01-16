# Streaming

## 1. Definición de streaming
El *streaming* es una tecnología que permite la reproducción continua de contenidos multimedia (audio y vídeo) mientras estos se reciben a través de una red, sin necesidad de descargar el archivo completo previamente.

A diferencia de la descarga tradicional, el streaming permite iniciar la reproducción casi de inmediato gracias al uso de memoria intermedia (*buffer*), lo que mejora la experiencia del usuario.

### Funcionamiento básico
1. El cliente se conecta al servidor o plataforma de streaming.
2. El servidor comienza a enviar pequeños fragmentos del contenido.
3. El cliente almacena temporalmente estos fragmentos en un *buffer*.
4. Cuando el *buffer* alcanza un tamaño mínimo, comienza la reproducción.
5. Si la velocidad de red disminuye, el reproductor consume el contenido del *buffer*. Si este se vacía, la reproducción se pausa hasta que vuelva a llenarse.

## 2. Componentes del streaming
Los sistemas de streaming modernos se basan en los siguientes elementos:

- **Códecs**: algoritmos de compresión y descompresión de audio y vídeo (H.264, H.265/HEVC, AV1, VP9, AAC, Opus).
- **Protocolos de transporte y aplicación**: HTTP/HTTPS, RTP, RTSP, QUIC, UDP y TCP.
- **Buffering o precarga**: almacenamiento temporal para evitar cortes.
- **Red de datos**: generalmente redes IP sin garantía nativa de calidad de servicio (QoS).
- **Segmentación**: el contenido se divide en pequeños fragmentos para facilitar su transmisión y adaptación a la red.
- **Adaptación dinámica de bitrate (ABR)**: ajuste automático de la calidad del vídeo según el ancho de banda disponible.

## 3. Tipos de servicio de streaming

### 3.1 Streaming en directo (Live)
Orientado a emisiones en tiempo real, como televisión, radio o eventos en vivo.

- Puede ser **unicast** (un flujo por usuario) o **multicast** (un flujo para múltiples usuarios).
- Suele permitir pausas limitadas o *time-shift*.
- Es sensible a la latencia.

### 3.2 Streaming bajo demanda (On‑Demand)
El usuario selecciona un contenido previamente almacenado.

- Permite pausar, avanzar y retroceder.
- Es el modelo habitual de plataformas como Netflix o Amazon Prime Video.

### 3.3 Casi bajo demanda (Near On‑Demand)
El contenido se emite de forma periódica (por ejemplo, cada 15 o 30 minutos), permitiendo al usuario incorporarse a una emisión programada.

## 4. Arquitecturas de streaming

### 4.1 Arquitectura clásica
Incluye servidores de streaming dedicados y clientes específicos que reciben y reproducen el contenido.

### 4.2 Arquitectura basada en servidor web
Utiliza servidores HTTP/HTTPS estándar y protocolos de streaming adaptativo como HLS o MPEG‑DASH.

Ejemplo con HTML5:

```html
<video controls width="640" height="360">
  <source src="https://servidor.com/stream.m3u8" type="application/x-mpegURL">
  Tu navegador no soporta streaming HLS.
</video>
```

**Ventajas**
- Compatibilidad multiplataforma.
- Bajo coste.
- Escalabilidad mediante CDN.

**Desventajas**
- Normalmente no soporta multicast.
- Mayor latencia frente a otros protocolos.

### 4.3 Arquitectura sin cliente dedicado
El reproductor está integrado en el navegador o aplicación mediante tecnologías web modernas (HTML5). Hoy en día, Flash y plugins similares están completamente obsoletos.

## 5. Elementos de un sistema de streaming

### 5.1 Sistema de producción
Genera el flujo de datos a partir de:
- Contenidos almacenados.
- Captura en directo (cámaras, micrófonos, codificadores).

Incluye la codificación y segmentación del contenido.

### 5.2 Infraestructura intermedia
- **CDN (Content Delivery Network)** para cachear y distribuir contenido.
- **Proxies y balanceadores** para mejorar rendimiento y seguridad.

### 5.3 Clientes
Dispositivos o aplicaciones que reproducen el contenido:
- Navegadores web.
- Aplicaciones móviles.
- Smart TV.
- Reproductores multimedia.

## 6. Protocolos de streaming

### 6.1 HTTP/HTTPS
Base de la mayoría de sistemas actuales.

- Fiable (TCP).
- Compatible con CDNs.
- Escalable.
- Mayor latencia.

### 6.2 RTMP
Protocolo histórico orientado a baja latencia.

- Basado en TCP.
- Hoy en día en desuso para distribución final.
- Aún utilizado en ingestión hacia plataformas de streaming.

### 6.3 UDP
Permite baja latencia y multicast.

- No fiable.
- Usado en entornos controlados (IPTV, redes privadas).

### 6.4 RTP / RTCP / RTSP
Conjunto de protocolos para transmisión en tiempo real:

- **RTSP**: control de sesión.
- **RTP**: transporte de audio o vídeo.
- **RTCP**: control de calidad.

Usados en sistemas profesionales, videovigilancia y entornos corporativos.

### 6.5 Protocolos modernos
- **HLS (HTTP Live Streaming)**.
- **MPEG‑DASH**.
- **WebRTC** (muy baja latencia, tiempo real).

## 7. Seguridad y aspectos actuales
- Uso de **HTTPS** obligatorio.
- Control de accesos y autenticación.
- Cifrado de contenidos.
- Cumplimiento del **RGPD** en plataformas que gestionan datos personales.
- Monitorización de calidad (latencia, jitter, pérdida de paquetes).

