### Uso y manejo con Tmux
- Guía de atajos y comandos de Tmux: [https://hack4u.io/wp-content/uploads/2022/05/Tmux-Cheat-Sheet.pdf]([https://hack4u.io/wp-content/uploads/2022/05/Tmux-Cheat-Sheet.pdf](https://hack4u.io/wp-content/uploads/2022/05/Tmux-Cheat-Sheet.pdf))

*Tmux*  es muy útil ya que nos ayuda a mejorar la movilidad dentro de nuestro sistema operativo mediante terminales.

*Cómo instalar:*
~~~ bash
cd
git clone https://github.com/gpakosz/.tmux.git
ln -s -f .tmux/.tmux.conf
cp .tmux/.tmux.conf.local .
~~~

Si no te funciona el archivo de configuración es porque ya tenías abierta una sesión anterior, para cerrarla símplemente debemos escribir:
*Para hacer un update a una configuración escribimos en modo comando dentro de tmux **^+b+I *** 

>tmux new -s nombre_sesión = creará una sesión con el nombre asignado

> Ctrl + b = Nos sirve para entrar en el modo de gestión Tmux.
> Ctrl + b + , = Renombrará nuestra ventana
> Ctrl + b + c = Creará otra ventana dentro de la misma sesión.
> Ctrl + b + & = Cerrará la ventana actual.
> Ctrl + b + m = Entrarás con el modo del ratón
> Ctrl + b + % Divirá la consola verticalmente
> Ctrl + b + " = Divirá la consola para mejor movilización.


>Ctrl + b + d = Detatch una sesión de la consola
>tmux list-sessions = Listará las sesiones que estén corriendo.
 tmux attach -t nombresesion = Volverá a conectarnos con la sesión que queramos.

*Mi configuración:*
~~~ bash
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

echo "source -q ~/.tmux.conf.local
run 'cut -c3- ~/.tmux.conf | sh -s _apply_configuration'


# Plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'dracula/tmux'

set -g @dracula-show-powerline true
set -g @dracula-show-left-icon session

set -g @dracula-plugins 'cpu-usage ram-usage'

run '~/.tmux/plugins/tpm/tpm'" >> ~/.tmux.conf

~~~

#### Uso y configuración de la Kitty (Opcional)
La mejor forma para aprender y maniobrar por la Kitty es con la propia guía de los creadores de la misma, aquí dejo el enlace para indagar y entrar más en detalle con algunas configuraciones:

La kitty nos permitirá editar una shell cmpleta desde cero

- Overview - Kitty: [https://sw.kovidgoyal.net/kitty/overview/](https://sw.kovidgoyal.net/kitty/overview/)

> Para instalar la kitty simplemente deberemos usar nuestro instalador de paquetes por defecto y escribir *kitty* posteriormente:

~~~ bash
# Arch Linux
sudo pacman -Sy kitty

# Other Linux Distributions
sudo apt install kitty
~~~

Archivo de configuración normal para que funcione (en ~/.config/kitty/kitty.conf):
~~~ bash
enable_audio_bell no

include color.ini

font_family Iosevka Custom 9
font_size 10

disable_ligatures never

url_color #61afef
url_style curly

map ctrl+left neighboring_window left
map ctrl+right neighboring_window right
map ctrl+up neighboring_window up
map ctrl+down neighboring_window down

#map f1 copy_to_buffer a
#map f2 paste_from_buffer a
#map f3 copy_to_buffer b
#map f4 paste_from_buffer b

cursor_shape beam
cursor_beam_thickness 1.8

mouse_hide_wait 3.0
detect_urls yes

repaint_delay 10
input_delay 3
sync_to_monitor yes

map ctrl+shift+z toggle_layout stack
tab_bar_style powerline

inactive_tab_background #e06c75
inactive_tab_foreground #000000
active_tab_background #98c379
tab_bar_margin_color black

map ctrl+shift+enter new_window_with_cwd
map ctrl+shift+t new_tab_with_cwd

background_opacity 0.98

shell zsh


~~~

Para descargar los archivos de configuración del color:
~~~ bash
curl https://raw.githubusercontent.com/rxyhn/tokyo/main/config/kitty/color.ini > ~/.config/kitty/color.ini
~~~

#### Uso del editor VIM
Por aquí dejo una guía bastante buena donde se detallan varios atajos de teclado existentes para usar en Vim:
- Vim Cheat Sheet: [https://vim.rtorr.com/](https://vim.rtorr.com/)
> Recomiendo instalar nvim y posteriormente instalarle NvChad para mejor personalización

Vim tiene por defecto los controles para desplazarse (izquierda, derecha, arriba y abajo)que son: 
	   *k         
	h     l
	    j
	
Pero esto normalmente suele liar más a la gente, por lo que recomiendo usar las flechas que están en nuestro teclado y ya. 

**En modo comando:**
Para introducir datos -> i
*Entrar en modo selección(Visual)* -> ctrl+v
	- Seleccionar desde hasta el final de la línea -> shift+4

- Para deshacer cambios-> u
- Para rehacer cambios -> ctrl + r

- Ir al principio de la línea -> 0 (Esto es un cero)
- Ir al final de la línea -> $
- Crear una nueva línea -> o

>  Para poder mover, cortar, etc.. más de una línea, palabra símplemente debemos en modo comando introducir la cantidad que queremos seguido del comando:
> 	 *Ej:* 10000dd (esto cortará 10000 líneas)
 
- Moverte entre palabras -> w

- Copiar una palabra -> yw
- Cortar una palabra -> dw

- Pegar lo cortado o copiado -> p

- Copiar línea -> yy
- Cortar línea -> dd

Todo lo que escribamos con Vim se estará guardando en un registro temporal, por lo que, podríamos escribir directamente todo lo que hubieramos escrito antes, por ejemplo con esta palabra; *Probando ->*, para poder volver a copiarla símplemente deberemos usar un punto en modo comando *"."*

- Empezar a grabar una macro -> q + caracter_deseado (qa: se guardará en a)
	- Para repetir la macro símplemente seleccionamos cuantas veces quiere que lo haga -> 30@caractermacro
- Filtrar -> /

- Sustitur todas las palabras por otras -> *:%s/palabranterior/palabranueva*
	- Aunque recomiendo ponerle una */g* al final para evitar problemas
