# cesar-asm-game

Asteroid game implemented in assembly for the hypothetical cesar cpu

## Notes

mov origem, destino<br>
mov direto, imediato e indexado usam 1 palavra (2 bytes) a mais por instrução<br>


### instruction size

Add (r7)+,r0\
Byte 0: add r7 r0\
Byte 1:\
Byte 2: 0\
Byte 3: 10\
<br><br>
ADD #10, #20 = add (r7)+,(r7)+ = 6 bytes\
<br><br>
1 byte = HLT, SCC, CCC<br>
2 byte = desvios<br>
4 byte = JMP, ...<br>
<br>
### controle de laço

- SOB r0, 10\
se r0 != 0, retorna 10 instrucoes, ie PC = PC - 10\
se r0 == 0, continua na proxima instrucao\

### subrotina

- JSR r4, 10\
vai para o addr 10 para executar a subrotina\
na subtorina eu uso RST r4 para ele retornar para a proxima instrucao apos JSR\

### teclado

Leio o valor do addr 65498\
se 128, uma tecla foi digitada e armazenada no endereco 65499\
se 0, nenhuma tecla foi pressionada\

### deslocamento

Tem 8 bits para representar o deslocamento [-128,127]\
Maximo para cima: e+2+128\
Maximo para baixo: e+2-127\

### pilha

- push\
decrementa e empurra o dado\
mov r0, -(r6)\
coloca o valor de r0 na pilha\
r6 = r6 - 2\
mem(r6) <= dado\
mov <dado>, -(r6)\

- pop\
remove dado e incrementa sp\
Mov (r6)+, r0\
r0 recebe o dado da pilha\

### increment

ADD #10,R0\
add (r7)+,r0 = 4 bytes\

R0 = r0 + 10\
add 10 no registrador r0\

### big enddian

cesar usa big endian\
O menor endereco tem o byte mais significante (256 * dado do byte mais significante)\
O numero 255 é representado assim no endereço 10:\
10: 0\
11: 255\
\
O numero 256 é representado assim no endereço 10:\
10: 1\
11: 0\


### carry

Depois de subtração, se deu carry, é porque deu borrow\
Depois de adição, se deu carry, é porque deu carry\

### Clear condition code Set condition code

SCC e CCC\
Scc NVCZ ou SCC ZCVN\
CCC N\
CCC NZCV\

### interrupcoes

65408 = Inicio da pilha (cresce diminuindo os enderecos)\
65470 = Byte do IVET = ENDERECO DA SUBROTINA DE INTERRUPCAO (ISR)\
65496 = byte do INTS (Programador ler para ver status)\
Bit 7 = IP = Fica ligado quando o cesar esta executando a ISR.\
Bit 1 = Fica 1 quando a interrupcao foi gerada pelo teclado\
Bit 0 = Fica 1 quando a interrupcao foi gerada pelo timer\
65497 = byte do INTE (Programador escreve para controlar status)\
Bit 7 = IE = Coloque 1 para permitir interrupções\
Bit 1 = IEStec = Coloque 1 para permitir interrupcao de teclado\
Bit 0 = IEStim = Coloque 1 para permitir interrupcao de timer\
65498 = STATUS do teclado\
65499 = BUFFER do teclado\
65500  = primeiro byte do visor\
65535 = ultimo byte do visor\
Ao atender uma interrupcao\

### Colocar r7 e NVCZ na pilha
IP = 1 para dizer que esta executando a ISR\
IE = 0 para evitar novas interrupcoes\
Copiar IVET para r7\
