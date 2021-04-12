+++
draft = false
date = 2021-02-01T03:06:38-03:00
title = "[Polybar] Feriados en Argentina"
description = "Modulo de Feriados en Argentina para Polybar"
slug = ""
authors = ["gabox"]
tags = ["polybar", "linux", "feriado", "python"]
categories = ["scripts"]
externalLink = ""
series = []
+++

La idea es tener un módulo en `Polybar` que muestre información del próximo feriado en Argentina y se vea algo así:

![modulo-feriado](/img/module-feriado.png)

## Requisitos
- [Polybar](https://polybar.github.io/) 🙃.
- Script [feriados](https://github.com/centaurialpha/feriado-polybar) ™️.
- [Dunst](https://dunst-project.org/) (opcional).

Vamos a usar [éste script](https://github.com/centaurialpha/feriado) que escribí hace un tiempo usando la
[API pública de Feriados Argentinos](https://pjnovas.gitbooks.io/no-laborables/content/).

Ejecutando el script sin argumentos, nos tira algo así:

```
$ ./feriado
Próximo feriado: 15 Feb
Motivo: Carnaval
Tipo: inamovible
Más información: https://es.wikipedia.org/wiki/Carnaval
```

Pasándole un `--help`:
```
$ ./feriado --help
usage: feriados [-h] [-f] [-i] [--open-info] [-m]

optional arguments:
  -h, --help    show this help message and exit
  -f, --fecha   Muestra la fecha del feriado
  -i, --info    Muestra la URL a la información sobre el feriado
  --open-info   Abre la URL de la info en el navegador
  -m, --motivo  Muestra el motivo del feriado
```
Para el módulo de Polybar vamos a usar el argumento `-f`/`--fecha`, que va a mostrar la fecha del próximo feriado:

Copiamos el script a un lugar adecuado. Yo suelo tener una carpeta `~/.scripts`.

Agregamos un nuevo módulo en las configuraciones de Polybar, en mi caso tengo un archivo
`~/.config/polybar/modules.ini`:

```ini
[module/feriado]
type = custom/script
exec = $HOME/.scripts/feriado -f
format = <label>
format-prefix = " "
format-prefix-foreground = ${color.teal}
format-padding = 1
interval = 43200
```

Podemos aprovechar los otros argumentos del script para darle más funcionalidad al módulo:

- `-i`/`--info`: Para abrir el navegador cuando hagamos click izquierdo (opcional).
- `-m`/`--motivo`: Para mostrar una notificación cuando hagamos click derecho (opcional).

Entonces la configuración del módulo quedaría:
```diff
[module/feriado]
type = custom/script
exec = $HOME/.scripts/feriado -f
format = <label>
format-prefix = " "
format-prefix-foreground = ${color.teal}
format-padding = 1
interval = 43200
+click-left = $HOME/.scripts/feriado -i
+click-right = dunstify $($HOME/.scripts/feriado -m)
```

Usamos la salida de `feriado -m` y se la pasamos a dunst con dunstify para tener una notificación que se verá
más o menos así:

![notificación-feriado](/img/notification_feriado.png)
