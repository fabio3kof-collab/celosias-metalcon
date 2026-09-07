# Trazado de celosías Metalcon

Herramienta de taller para el despiece de vigas de entramado (celosías) en perfilería
Metalcon. Entrega los largos de corte de montantes y diagonales, el ángulo de corte,
el desfase de trazado y las marcas exactas sobre el canal, descontando el ancho real
de las alas para que los perfiles queden a tope en cada nodo.

Aplicación de un solo archivo: `index.html`. No necesita servidor ni dependencias —
se abre directo en el navegador. Las tipografías se cargan desde Google Fonts y hay
fallback del sistema si no hay internet.

## Qué resuelve

- **Celosía zig-zag o Pratt**, con dirección de la primera diagonal y diagonales
  simétricas o paralelas.
- **Criterio de nodo**: `a tope` (los perfiles topan entre sí, es el trazado de taller)
  o `ejes` (los ejes concurren en la división teórica, para comparar con un plano de cálculo).
- **Lista de corte**: cantidad, largo, ángulo, desfase y trozo bruto por tipo de pieza.
- **Ejes de junta**: puntos equiespaciados donde topan dos perfiles, para replantear
  con huincha corrida sobre el canal.
- **Trazado del corte sin transportador**: dos marcas sobre los cantos del perfil,
  corridas una distancia fija; se unen con regla y se pasa el esmeril.
- **Solver del triángulo de corte** (`c`, `α`, `b`, `a`): cambia cualquiera de los
  cuatro y los otros se acomodan. Sirve para cualquier perfil, no solo para esta viga.
- **Marcado sobre el canal**: distancias desde el extremo izquierdo, canal superior
  e inferior por separado.
- **Rendimiento por tira** y metraje total de material.
- **Exportación DXF y SVG**, con descarga directa: los botones bajan el archivo con el
  nombre de la viga (`zigzag-2620x600-5div.dxf`). El DXF lleva la geometría real en
  milímetros por capas (`PERFILES`, `CANAL`, `EJES`, `MARCAS`, `TEXTO`), con sus tablas
  `LTYPE` y `STYLE` declaradas.

## Geometría

Con largo total `L`, alto total `H`, `n` divisiones, ala del montante `a` y holgura `z`:

    base = (L - 2a)/n               zig-zag
    base = (L - a)/n - a            Pratt
    θ    = atan(H / r)              ángulo de la diagonal respecto de la horizontal
    w    = a / sin(θ)               huella horizontal del perfil diagonal
    r    = base - w                 (modo "a tope"; en modo "ejes" r = base)
    Lm   = H - z                    largo de corte del montante
    Ld   = H / sin(θ) - z           largo de corte de la diagonal, sobre cada canto
    b    = a / tan(θ)               desfase entre las marcas de los dos cantos

El ángulo lo fija la geometría de la viga (`H`, alma a alma). La holgura `z` solo
acorta cada pieza sobre su propio eje, así que no mueve el trazado ni los ejes de junta.

En modo *a tope*, `θ` y `r` se resuelven por iteración de punto fijo, porque la huella
`w` depende del ángulo y el ángulo depende de la huella. El corte de la diagonal es un
paralelogramo: ambos cantos miden `Ld` y quedan corridos `b` entre sí.

## Supuestos

- Alto y largo son medidas **exteriores**, de alma a alma del canal.
- En la elevación se ve el **ala** de cada perfil (38 mm montante, 25 mm canal son los
  valores Metalcon corrientes).
- Las diagonales se cortan con ambos extremos **paralelos al canal**, no en escuadra.
- La **holgura de corte** acorta las piezas sobre su eje; se ve en la elevación y sale
  reflejada en el DXF.
- Esto resuelve la **geometría** del despiece. El dimensionamiento estructural
  —espesor de lámina, altura de alma, cantidad de tornillos por nodo— es otra pega.

## Uso

Abre `index.html` en el navegador. Los botones de exportar bajan el DXF y el SVG
directo, y el contenido queda además en un panel por si prefieres copiarlo a mano.

El mismo archivo corre publicado como artifact de Claude, donde la página no puede
descargar por su cuenta: ahí detecta que va dentro de un iframe y pide el guardado a
la plataforma (SVG) o cae al portapapeles (DXF, extensión que el visor no acepta). Los parámetros quedan guardados en `localStorage`
bajo la clave `celosia-metalcon`.
