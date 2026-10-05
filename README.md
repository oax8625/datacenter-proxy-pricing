# Rotativo: IP nueva en cada petición
rotativo = f"http://{USUARIO}:{CLAVE}@{GATEWAY}"

# Sticky + país: misma IP durante la sesión, salida en España
sticky_es = f"http://{USUARIO}:{CLAVE}_country-es_session-miid1@{GATEWAY}"

proxies = {"http": rotativo, "https": rotativo}

resp = requests.get(
    "https://ejemplo.com/api/productos?page=1",
    proxies=proxies,
    headers={"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"},
    timeout=20,
)

print(resp.status_code, len(resp.text))


Tres cosas que marcan la diferencia entre scraping estable y scraping que se cae:

**Nunca hagas `while True` sin pausas.** Añade un `sleep` aleatorio de uno a tres segundos entre peticiones. Diez hilos machacando el mismo dominio queman IPs más rápido de lo que el pool las rota.

**Reintenta con IP nueva, no con la misma.** Si una petición devuelve 403 o 429, la siguiente conexión al gateway te da otra salida. Volver a insistir con la misma IP es tirar tráfico a la basura.

**Usa sticky para los flujos con estado.** Carrito, login, paginación con cookies: todo eso necesita la misma IP durante la sesión completa. Con rotación por petición, el carrito se vacía y el login se invalida a mitad de camino.

## Dónde se dispara la factura (y cómo evitarlo)

Casi todo el mundo se pasa de presupuesto por tres motivos:

**Usar residencial donde basta datacenter.** Si estás recorriendo documentación pública o un directorio sin protección, $1/GB es dinero tirado cuando hay salidas a $0,50/GB.

**Targeting avanzado por defecto.** Ciudad y ZIP cuestan el doble en residencial. Un crawl de precios nacionales que se hace con targeting de país sale la mitad que el mismo crawl con ciudad y código postal. Añade la precisión solo en la fase en la que de verdad la necesitas.

**Retry mal implementado.** Cada reintento consume tráfico. Un scraper que reintenta cinco veces en cada fallo multiplica su consumo por cinco en páginas problemáticas y no distingue entre "está bloqueado" y "esa URL no existe".

Un truco barato: antes de escalar, mide tu propio coste por petición exitosa con el paquete de entrada. Son $5 por 5 GB de residencial. Con eso tienes margen suficiente para lanzar tus URLs objetivo, contar cuántos GB consume cada 1.000 peticiones y ver la tasa de éxito real en tu nicho concreto. Es más fiable que cualquier benchmark de terceros, incluido el de esta página.

👉 [Empezar con 5 GB de proxies residenciales por $5](https://bit.ly/dataimPulse)

## Lo que no te va a gustar de DataImpulse

Ningún proveedor barato es barato en todo:

- **Sin SOC 2 ni ISO 27001.** Si tu departamento de compras exige certificaciones, esto bloquea la decisión.
- **Marca joven.** Lanzada a finales de 2022, con menos historial de auditoría externa que los proveedores con una década en el mercado.
- **Cobertura irregular en geografías secundarias.** En África subsahariana o Asia central, la profundidad del pool está por detrás de los grandes.
- **Objetivos muy duros.** Ese 65% de éxito en Instagram marca el límite. Para redes sociales con antbots agresivos o Cloudflare en modo estricto, hay proveedores especializados que rinden mejor.
- **No hay API de scraping.** Si esperabas una herramienta que devuelva JSON limpio, no es esto.

## Para quién tiene sentido

Encaja bien si estás scrapeando e-commerce, SERPs, precios o webs con protección media, tienes tu propio código, y tu consumo es irregular. El tráfico que no caduca resuelve el problema clásico de las bolsas de GB que se pudren en el panel.

Encaja mal si necesitas un desbloqueo gestionado, si tus objetivos son las plataformas más blindadas, o si tu equipo de compras pide certificaciones formales.

Y si tu scraper falla incluso con IPs residenciales limpias, revisa el fingerprint TLS antes de subir el presupuesto en proxies. Cambiar `requests` por un cliente que imite el handshake de Chrome puede darte más éxito que pasar de $1 a $5 por GB.

## Preguntas frecuentes

**¿Cuánto cuesta empezar a usar proxies para scraping?**
En DataImpulse, la recarga mínima son $5, que en residencial equivalen a 5 GB, 10 GB en datacenter o 2,5 GB en móvil. No hay cuota mensual ni suscripción.

**¿El tráfico caduca?**
No. El saldo permanece activo hasta que lo consumes. Es la diferencia principal frente a los proveedores que venden bolsas con plazo.

**¿Sirve con Playwright, Selenium o Scrapy?**
Sí. Con HTTP, HTTPS y SOCKS5 disponibles, se configura como cualquier proxy autenticado en estos frameworks.

**¿Puedo elegir ciudad o código postal?**
Sí, con coste adicional. El país está incluido; ciudad, estado, ZIP y ASN se facturan aparte y, en residencial, al doble de la tarifa base.

**¿Hay plan gratuito?**
No. Lo que existe es un paquete de entrada de $5 que puedes consumir sin compromiso. Se ha publicado además que la primera compra admite devolución en un plazo de 7 días, con los pagos en criptomonedas excluidos; conviene confirmarlo con soporte antes de pagar, porque las condiciones pueden cambiar.

**¿Cuánto dura una sesión sticky?**
Entre 1 y 120 minutos, configurable; por defecto 30 minutos.

👉 [Comparar los planes de DataImpulse y recargar saldo](https://bit.ly/dataimPulse)

## En resumen

El proxy correcto para scraping depende de tres cosas: lo protegido que esté el objetivo, si tu flujo necesita sesión persistente y cuánto tráfico vas a mover. Residencial rotativo para e-commerce y SERP, datacenter para lo que no protege, móvil solo cuando lo demás falla. Y antes de pagar tarifas premium, comprueba que el problema no sea tu cliente HTTP.

Con un paquete de $5 tienes suficiente para medir tu coste por petición exitosa en tus propias URLs. Ese número, y no el precio del folleto, es el que decide si un proveedor te sale caro o barato.
