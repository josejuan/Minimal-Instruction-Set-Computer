Minimal-Instruction-Set-Computer
================================


  Ésto es sólo un entretenimiento de un problema propuesto en solveet.

------------

  La forma en la que está implementado, guarda ciertas similitudes
  con el 8085 con el que teníamos que hacer prácticas en mis tiempos
  mozos en formación profesional.

  És una única página HTML, aunque el código en ensamblador a ejecutar
  se puede sacar a un recurso externo, cambiando

    <script type="text/template" id="source_code">

  Por algo como

  	<script type="text/template" id="source_code" src="http://...micodigo.asm">

  La simulación consiste en una máquina con 64k de memoria que no tiene registros,
  tanto IP como SP como un número arbitrario de registros y punteros base están en
  memoria mediante direcciones e indirecciones.

  Tiene un pequeño soporte "BIOS"

------------

  Se dispone de una memoria de 64K.

  Toda la memoria está inicializada a 0.

  La dirección de la pila se situa inicialmente en la dirección 65535.

  La dirección de instrucción (IP) se almacena en la dirección 0.
  La dirección de pila (SP) se almacena en la dirección 1.
  Las banderas se almacenan en la dirección 2.
  SP apunta a la primera dirección libre de la pila.

  La máquina empieza a ejecutar la posición 1000.
  El código, por defecto, empieza a escribirse en la dirección 1000.

  Cada instrucción ocupa una única posición en memoria.

  No hay registros, cualquier parámetro de cada registro puede ser de la forma

  	#constante, en que el valor es una constante en la instrucción.
  	@dirección, en que el valor es tomado de la dirección de memoria indicada.

  Y además se admiten direcciones con base de la forma

  	adr:adr

  	por ejemplo

  		#10000:@30
  		#11000:#40
  		@50:#10
  		@50:@5

  En la instrucciones en que se intente almacenar un valor en una constante se producirá una parada.

  El repertorio de instrucciones es:

	add src, dst           ; dst = src + dst
	and src, dst           ; dst = src & dst
	call adr               ; salta a la dirección "adr" dejando en la pila "ip + 1"
	div num, den           ; num = num / den
	jin adr                ; salta si el flag de test indica negativo.
	jip adr                ; salta si el flag de test indica positivo.
	jiz adr                ; salta si el flag de test indica zero.
	jmp adr                ; salta siempre.
	mov src, dst           ; dst = src
	mul src, dst           ; dst = src * dst
	or src, dst            ; dst = src | dst
	pop src                ; recupera un valor de la pila en "src"
	push src               ; almacena el valor "src" en la pila
	ret                    ; obtiene una dirección de la pila y salta a ella (ajustando la pila)
	shl dst, bits          ; dst = dst << bits
	shr dst, bits          ; dst = dst >> bits
	sub src, dst           ; dst = dst - src
	test src               ; realiza un test sobre el valor "src"
	xor src, dst           ; dst = src ^ dst
	neg dst                ; niega todos los bits de "dst"
	halt                   ; detiene la máquina

  En código, las siguientes líneas pueden aparecer:

  	; comentario           ; ignora esta línea en el código
	@cadena de texto       ; empieza a escribir en memoria la cadena indicada
	#0, 1, 2, 3, 4         ; empieza a escribir en memoria la secuencia indicada
	:adr                   ; la próxima instrucción (o dato) será escrito en esta dirección

  Funciones "BIOS":

        call printZString      ; imprime en la pantalla la cadena cuya dirección se indica en el primer parámetro de pila
        call printNum          ; imprime en la pantalla el número cuya dirección se indica en el primer parámetro de pila
        call readZString       ; pide al usuario una cadena que será almacenada a partir de la dirección indicada en el primer parámetro de pila
        call readNum           ; pide al usuario una cadena que será almacenada en la dirección indicada en el primer parámetro de pila
