# Repositorio-de-Lista-de-Bloqueo-de-Paginas-Web-por-AdGuard-Home-
Repositorio donde recopila las paginas web tanto de contenido Explicito como paginas web de sitios de juegos clandestino/entretenimiento para una institucion (Fase Beta) 

# Blocklists — Control de Acceso Institucional (Red LAN)

Repositorio de listas de bloqueo DNS para restringir el acceso a distintas categorías de contenido y plataformas dentro de una red institucional. Pensado para usarse con **Pi-hole** o **AdGuard Home** como servidor DNS del sector.

## Contenido del repositorio

| Archivo | Categoría | Formato |
|---|---|---|
| `adult-content-blocklist.txt` | Contenido para adultos (tubes, cams, hentai, rule34, JAV, etc.) | Hosts plano |
| `twitter-gore-blocklist.txt` | Twitter/X + sitios de gore/shock | Hosts plano |
| `twitter-blocklist-esencial.txt` | Subconjunto reducido de Twitter/X (solo lo necesario para bloquear acceso) | Hosts plano |
| `tiktok-blocklist.txt` | TikTok y CDNs asociados | Hosts plano |
| `instagram-facebook-blocklist.txt` | Instagram + Facebook/Meta | Hosts plano |
| `torrent-blocklist.txt` | Sitios de torrents/P2P | Hosts plano |
| `ads-blocklist.txt` | Publicidad y trackers | Hosts plano |
| `crypto-blocklist.txt` | Cryptomining oculto + exchanges/trading | Hosts plano |
| `steam-blocklist.txt` | Steam (tienda, comunidad, descargas) | Hosts plano |
| `games-tlds.txt` | TLDs de juegos (`.gg`, `.fun`, `.lol`, etc.) | **AdBlock/uBlock** |
| `games-regex.txt` | Patrones de texto para nombres de juegos | **AdBlock/uBlock** |
| `games-cosmetic.txt` | Oculta minijuegos embebidos (Google, chess.com, etc.) | **AdBlock/uBlock (solo extensión de navegador)** |

---

## 1. Instalación en Pi-hole

1. Entrá al panel web → **Group Management → Adlists**.
2. Si vas a alojar los archivos en un repo propio (GitHub, GitLab, servidor interno), pegá la URL raw de cada archivo `.txt` de formato **hosts plano** (todos menos los tres de `games-*`).
3. Si preferís cargarlos localmente sin repo externo, andá a **Tools → Update Gravity** después de copiar los archivos a `/etc/pihole/` y agregarlos como fuente local, o usá la opción "Import" del panel si tu versión lo soporta.
4. Click en **Update Gravity** para aplicar.

Los archivos `games-tlds.txt` y `games-regex.txt` **también son compatibles con Pi-hole**, porque Pi-hole soporta una sintaxis tipo regex/wildcard adaptada — pero revisá la sección 3 antes de cargarlos, porque son agresivos.

## 2. Instalación en AdGuard Home

1. Panel web → **Filters → DNS blocklists**.
2. **Add blocklist → Add a custom list**, pegá la URL raw de cada archivo hosts plano.
3. Los archivos `games-tlds.txt` y `games-regex.txt` usan sintaxis AdBlock nativa — AdGuard Home la soporta directamente, se cargan igual que cualquier otra lista.
4. Aplicar y esperar a que sincronice (o forzar con **Check for updates**).

## 3. Advertencia sobre `games-tlds.txt` y `games-regex.txt`

Estos dos archivos son **agresivos por diseño**: bloquean por patrón de texto o por TLD completo, no por dominio exacto. Por ejemplo:
- `games-tlds.txt` bloquea *todo* `*.io` salvo una lista de excepciones (GitHub, Docker, Home Assistant, etc.) — si tu institución usa algún servicio `.io` no incluido en las excepciones, se bloqueará también.
- `games-regex.txt` bloquea cualquier dominio que *contenga* palabras como `game`, `craft`, `school`, `class`, `math`, etc. — esto puede bloquear sitios educativos o de uso legítimo que casualmente compartan esas palabras.

**Recomendación**: cargalos primero en un entorno de prueba, revisá los logs de consultas bloqueadas durante unos días, y ajustá las excepciones (`denyallow`) según lo que tu red realmente necesita.

## 4. `games-cosmetic.txt` — Este archivo NO funciona en Pi-hole/AdGuard Home

Es una lista de **reglas cosméticas** (oculta elementos dentro de una página web, no bloquea el dominio). Un servidor DNS no puede aplicar este tipo de regla porque actúa sobre el DOM de la página, no sobre resolución de nombres.

Para que esta lista tenga efecto, necesitás desplegar una **extensión de navegador** (uBlock Origin o AdGuard) en cada equipo del sector, y cargarla ahí como lista de filtros personalizada. Si tu institución administra los navegadores de forma centralizada (política de Chrome/Firefox por dominio), se puede forzar la instalación y configuración de la extensión de manera remota — es un tema aparte de las blocklists DNS.

## 5. Forzar el DNS en la red (importante)

Cargar las listas en Pi-hole/AdGuard Home no sirve de nada si los equipos de la LAN pueden:
- Configurar manualmente otro DNS (8.8.8.8, 1.1.1.1, etc.)
- Usar DNS-over-HTTPS (DoH) desde el navegador, que **evita por completo** tu servidor DNS local

Para que el bloqueo sea efectivo:
1. En el **router/firewall del sector**, redirigí todo el tráfico saliente por el puerto 53 (DNS) hacia tu servidor Pi-hole/AdGuard Home, sin importar qué DNS tenga configurado cada equipo (esto se llama *DNS interception* o *forced DNS*).
2. Bloqueá a nivel de firewall las conexiones salientes a resolvers DoH conocidos (Cloudflare, Google, Quad9) y el puerto 853 (DNS-over-TLS).
3. En Chrome/Firefox gestionados por política, desactivá explícitamente el DoH automático (`DnsOverHttpsMode: off` en políticas de Chrome).

Sin este paso, cualquier usuario con conocimientos básicos puede saltarse todo el bloqueo cambiando el DNS del dispositivo.

## 6. Mantenimiento

Estas listas están armadas manualmente y **no se actualizan solas**. Los dominios de redes sociales, torrents y sitios de contenido cambian con el tiempo (nuevos CDNs, dominios espejo, etc.). Para mantener esto sostenible en el tiempo, considerá combinar estos archivos con fuentes que sí se actualizan automáticamente:

- **[The Block List Project](https://github.com/blocklistproject/Lists)** — listas por categoría (porn, gambling, torrent, social, ads, etc.), la misma fuente que usaste para varias de las listas de este repo.
- **[StevenBlack/hosts](https://github.com/StevenBlack/hosts)** — lista unificada de ads+tracking+malware, con variantes combinables por categoría.
- **[OISD](https://oisd.nl/)** — listas grandes y muy mantenidas, con categorías NSFW, gambling, y redes sociales por separado.

Referenciar esas URLs directamente como fuente externa en Pi-hole/AdGuard Home (en vez de copiar el contenido a mano) hace que tu servidor se sincronice solo cada vez que la lista original se actualiza.

## 7. Estructura sugerida del repo

```
/blocklists
├── README.md
├── adult-content-blocklist.txt
├── twitter-gore-blocklist.txt
├── twitter-blocklist-esencial.txt
├── tiktok-blocklist.txt
├── instagram-facebook-blocklist.txt
├── torrent-blocklist.txt
├── ads-blocklist.txt
├── crypto-blocklist.txt
├── steam-blocklist.txt
└── games/
    ├── games-tlds.txt
    ├── games-regex.txt
    └── games-cosmetic.txt
```

## Licencia / uso

Uso interno para administración de red institucional. Las listas de terceros referenciadas (Block List Project, StevenBlack, IREK-szef/games-blocklist) mantienen su propia licencia — revisar cada repositorio de origen antes de redistribuir públicamente.
