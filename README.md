# Blocklists — Control de Acceso para Instituciones Educativas

Listas de bloqueo DNS para restringir el acceso a contenido no deseado dentro de la red de una institución educativa. Pensado para usarse con **Pi-hole** o **AdGuard Home** como servidor DNS del sector.

---

## Contenido del repositorio

### Redes sociales (`social-media/`)

| Archivo | Plataforma | Subdominios |
|---|---|---|
| `tiktok.txt` | TikTok / ByteDance | ~50 |
| `facebook-instagram.txt` | Facebook, Instagram, Meta, WhatsApp | ~70 |
| `twitter-x.txt` | X (Twitter), Grok, Periscope | ~55 |
| `reddit.txt` | Reddit | ~25 |
| `snapchat.txt` | Snapchat / Snap Inc | ~45 |
| `steam.txt` | Steam / Valve | ~30 |

### Contenido adulto (`contenido-adulto/`)

| Archivo | Categoría |
|---|---|
| `adulto.txt` | Tubes, cams, hentai, rule34, JAV, OnlyFans, etc. |

### Entretenimiento (`entretenimiento/`)

| Archivo | Categoría |
|---|---|
| `juegos.txt` | Juegos web, Roblox, navegadores remotos |
| `torrents.txt` | Torrents, P2P, descargas, LibGen |

### Publicidad y trackers (`publicidad-trackers/`)

| Archivo | Categoría |
|---|---|
| `ads-trackers.txt` | Google Ads, trackers, analytics, publicidad programática |

### Crypto (`crypto/`)

| Archivo | Categoría |
|---|---|
| `crypto-mining.txt` | Minería oculta, exchanges, faucets |

### Archivo consolidado

| Archivo | Descripción |
|---|---|
| `todos-los-dominios.txt` | Todos los dominios de todas las categorías en un solo archivo |

---

## Instalación

### Pi-hole

1. Panel web → **Group Management → Adlists**
2. Agregar la URL raw de cada archivo `.txt` que deseas usar
3. Click en **Update Gravity** para aplicar

### AdGuard Home

1. Panel web → **Filters → DNS blocklists**
2. **Add blocklist → Add a custom list**
3. Pegar la URL raw del archivo, guardar y sincronizar

---

## Forzar DNS en la red (importante)

Cargar las listas no sirve de nada si los equipos pueden:
- Configurar manualmente otro DNS (8.8.8.8, 1.1.1.1, etc.)
- Usar DNS-over-HTTPS (DoH) desde el navegador

Para que el bloqueo sea efectivo:
1. En el **router/firewall**, redirigir todo el tráfico del puerto 53 hacia tu servidor Pi-hole/AdGuard Home
2. Bloquear conexiones salientes a resolvers DoH conocidos y el puerto 853 (DNS-over-TLS)
3. En navegadores gestionados por política, desactivar el DoH automático

---

## Mantenimiento

Estas listas se actualizan manualmente. Para mantener la efectividad en el tiempo, se recomienda combinar con fuentes que se actualizan automáticamente:

- **[The Block List Project](https://github.com/blocklistproject/Lists)** — listas por categoría
- **[StevenBlack/hosts](https://github.com/StevenBlack/hosts)** — lista unificada de ads+tracking+malware
- **[HaGeZi DNS Blocklists](https://github.com/hagezi/dns-blocklists)** — listas tiered por nivel de agresividad
- **[OISD](https://oisd.nl/)** — listas grandes y mantenidas con categorías NSFW, gambling, social

---

## Licencia

Uso interno para administración de red institucional. Las listas de terceros referenciadas mantienen su propia licencia — revisar cada repositorio de origen antes de redistribuir públicamente.
