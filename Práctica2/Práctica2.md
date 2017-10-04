# Práctica 2
## Antonio Jesús Peláez Priego

## Diario
### Día 4/10/2017 (Primera sesión)
Tuve algunos problemas al compilar el fichero suma.s inicial debido a la funcion printf, tras preguntar al profesor, resolví el problema, la forma correcto de compilarlo sería:
~
gcc -m32 -nostartfiles suma.s -o suma
~
No conseguí acabar el primer problema (5.1) en clase pero me falto poco y lo conseguí acabar después en cafetería.


## Ficheros

### Sumar N enteros sin signo de 32 bits en una plataforma de 32 bits sin perder precisión. (suma.s)

~

.section .data
lista:		.int 1,2,4000000000,4000000000
longlista:	.int (.-lista)/4
resultado:	.quad -1										#cambiamos resultado a quad para que pueda almacenar 64 bits
formato:	.ascii "suma = %llu \n\0"		#usamos %llu para imprimir numeros de 64 bits

.section .text
.extern printf
_start:	.global _start

	mov    $lista, %ebx			#guardamos la lista en %ebx
	mov longlista, %ecx			#guardamos la longitud de la lista en %ecx
	call suma								#llamamos a la función suma
	mov %eax, resultado			#Guardamos los valores de la suma obtenidos en resultado
	mov %edx, resultado+4

	push resultado+4				#Metemos resultado en la pila para usarlo como parámetro de printf
	push resultado					#Primero los bytes menos significativos y luego los más (little-endian)
	push $formato
	call printf							#printf(formato,resultado)
	add $12, %esp

	mov $1, %eax						#exit
	mov $0, %ebx						#parámetro 0 para exit
	int $0x80								#llamada a exit(0)

suma:
	push %esi 						#usamos el registro %esi como indice
	mov $0, %esi					#ponemos a 0 el índice
	mov $0, %eax					#ponemos a 0 %eax y %edx (registros donde se realizara la suma)
	mov $0, %edx
bucle:
	add (%ebx,%esi,4), %eax		#suma eax+=lista[i]
	adc	$0, %edx							#suma del acarreo en %edx
	inc       %esi						# i+1 (incrementamos el inidice)
	cmp  %esi,%ecx						#comparamos si i=longlista
	jne bucle									#if i!=longlista, hacemos el bucle de nuevo

	pop %esi									#ponemos %esi a su valor original
	ret

~
