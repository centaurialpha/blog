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

La idea es tener un m贸dulo en `Polybar` que muestre informaci贸n del pr贸ximo feriado en Argentina y se vea algo as铆:

![modulo-feriado](/img/module-feriado.png)

## Requisitos
- [Polybar](https://polybar.github.io/) 馃檭.
- Script [feriados](https://github.com/centaurialpha/feriado-polybar) 鈩笍.
- [Dunst](https://dunst-project.org/) (opcional).

Vamos a usar [茅ste script](https://github.com/centaurialpha/feriado) que escrib铆 hace un tiempo usando la
[API p煤blica de Feriados Argentinos](https://pjnovas.gitbooks.io/no-laborables/content/).

Ejecutando el script sin argumentos, nos tira algo as铆:

```
$ ./feriado
Pr贸ximo feriado: 15 Feb
Motivo: Carnaval
Tipo: inamovible
M谩s informaci贸n: https://es.wikipedia.org/wiki/Carnaval
```

Pas谩ndole un `--help`:
```
$ ./feriado --help
usage: feriados [-h] [-f] [-i] [--open-info] [-m]

optional arguments:
  -h, --help    show this help message and exit
  -f, --fecha   Muestra la fecha del feriado
  -i, --info    Muestra la URL a la informaci贸n sobre el feriado
  --open-info   Abre la URL de la info en el navegador
  -m, --motivo  Muestra el motivo del feriado
```
Para el m贸dulo de Polybar vamos a usar el argumento `-f`/`--fecha`, que va a mostrar la fecha del pr贸ximo feriado:

Copiamos el script a un lugar adecuado. Yo suelo tener una carpeta `~/.scripts`.

Agregamos un nuevo m贸dulo en las configuraciones de Polybar, en mi caso tengo un archivo
`~/.config/polybar/modules.ini`:

```ini
[module/feriado]
type = custom/script
exec = $HOME/.scripts/feriado -f
format = <label>
format-prefix = "顕? "
format-prefix-foreground = ${color.teal}
format-padding = 1
interval = 43200
```

Podemos aprovechar los otros argumentos del script para darle m谩s funcionalidad al m贸dulo:

- `-i`/`--info`: Para abrir el navegador cuando hagamos click izquierdo (opcional).
- `-m`/`--motivo`: Para mostrar una notificaci贸n cuando hagamos click derecho (opcional).

Entonces la configuraci贸n del m贸dulo quedar铆a:
```diff
[module/feriado]
type = custom/script
exec = $HOME/.scripts/feriado -f
format = <label>
format-prefix = "顕? "
format-prefix-foreground = ${color.teal}
format-padding = 1
interval = 43200
+click-left = $HOME/.scripts/feriado -i
+click-right = dunstify $($HOME/.scripts/feriado -m)
```

Usamos la salida de `feriado -m` y se la pasamos a dunst con dunstify para tener una notificaci贸n que se ver谩
m谩s o menos as铆:

![notificaci贸n-feriado](/img/notification_feriado.png)
