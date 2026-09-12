# TME4



## Exercice 1

### Question 1

```mips
.data
o1: .byte 1
o2: .byte 2
o3: .byte 3
o4: .byte 4
m1: .word 0xAABBCCDD

.text
main:
    ori $2, $0, 10
    syscall
```

### Question 2

o1=0x10010000, o2=0x10010001, o3=0x10010002, o4=0x10010003, m1=0x10010004 (déjà multiple de 4, pas de padding).

### Question 3

Settings > Show Labels Window : affiche toutes les étiquettes avec leur adresse, ça confirme le calcul.

## Exercice 2

### Question 1

```mips
.data
v1: .word -1
v2: .word 0xFF

.text
main:
    lui  $8, 0x1001
    ori  $8, $8, 0x0000    # $8 = adresse v1

    lw   $9, 0($8)           # $9 = v1
    lw   $10, 4($8)          # $10 = v2

    or   $4, $9, $0
    ori  $2, $0, 1
    syscall
    or   $4, $10, $0
    ori  $2, $0, 1
    syscall

    ori  $2, $0, 10
    syscall
```

### Question 2

```mips
.data
v1: .word -1
v2: .word 0xFF

.text
main:
    lui   $8, 0x1001
    ori   $8, $8, 0x0000   # $8 = adresse v1
    lw    $9, 0($8)          # $9 = v1
    lw    $10, 4($8)          # $10 = v2

    addiu $9, $9, 1
    addiu $10, $10, 1

    sw    $9, 0($8)
    sw    $10, 4($8)

    or    $4, $9, $0
    ori   $2, $0, 1
    syscall
    or    $4, $10, $0
    ori   $2, $0, 1
    syscall

    ori   $2, $0, 10
    syscall
```

v1 devient 0, v2 devient 256, visible dans le Data Segment.

### Question 3

```mips
.data
octet: .byte 0xFF

.text
main:
    lui  $8, 0x1001
    ori  $8, $8, 0x0000   # $8 = adresse octet
    lb   $9, 0($8)          # $9 = signe
    lbu  $10, 0($8)          # $10 = non signe

    or   $4, $9, $0
    ori  $2, $0, 1
    syscall
    or   $4, $10, $0
    ori  $2, $0, 1
    syscall

    ori  $2, $0, 10
    syscall
```

lb étend le bit de signe : $9 = -1. lbu étend avec des 0 : $10 = 255.

## Exercice 3

### Question 1

```mips
.data
ch: .asciiz "coucou"

.text
main:
    lui  $4, 0x1001
    ori  $4, $4, 0x0000    # $4 = adresse ch
    ori  $2, $0, 4
    syscall

    ori  $2, $0, 10
    syscall
```

### Question 2

```mips
.data
ch: .asciiz "coucou"

.text
main:
    lui  $8, 0x1001
    ori  $8, $8, 0x0000    # $8 = adresse ch

    lb   $9, 0($8)           # $9 = 'c'
    lb   $10, 1($8)           # $10 = 'o'
    sb   $10, 0($8)
    sb   $9, 1($8)

    or   $4, $8, $0
    ori  $2, $0, 4
    syscall

    ori  $2, $0, 10
    syscall
```

"coucou" -> "ocucou" (les 2 premières lettres échangées).

## Exercice 4

### Question 1

Codage ASCII de "123456" + fin de chaine : 0x31 0x32 0x33 0x34 0x35 0x36 0x00

```mips
.data
tab: .byte 0x31, 0x32, 0x33, 0x34, 0x35, 0x36, 0x00
```

Vérification :

```mips
.text
main:
    lui $4, 0x1001
    ori $4, $4, 0x0000
    ori $2, $0, 4
    syscall

    ori $2, $0, 10
    syscall
```

3e caractère dans $16 (tab[2]='3'=0x33), affiché en décimal :

```mips
    lui  $8, 0x1001
    ori  $8, $8, 0x0000
    lb   $16, 2($8)      # $16 = caractere '3'

    or   $4, $16, $0
    ori  $2, $0, 1
    syscall
```

Ça affiche 51 (le code ASCII de '3'), pas 3.

Les caractères '0'-'9' valent 0x30-0x39 : le dernier quartet donne direct le chiffre. Il suffit de masquer avec 0x0F.

```mips
    andi $17, $16, 0x0F   # $17 = chiffre

    or   $4, $17, $0
    ori  $2, $0, 1
    syscall
```

Ça affiche 3. Pareil pour '4' : 0x34 & 0x0F = 4.

## Exercice 5

```c
int tab[] = {4, 23, 12, 3, 8, 1};
int s;
int p;
void main() {
   s = tab[3];
   p = tab[4];
   tab[0] = s + 1;
   tab[1] = s + p;
   tab[2] = tab[5];
   exit();
}
```

tab fait 24 octets : si tab=0x10010000, s=0x10010018 et p=0x1001001C.

```mips
.data
tab: .word 4, 23, 12, 3, 8, 1
s:   .word 0
p:   .word 0

.text
main:
    lui  $8, 0x1001
    ori  $8, $8, 0x0000     # $8 = adresse tab

    lw   $9, 12($8)           # $9 = s = tab[3]
    lw   $10, 16($8)           # $10 = p = tab[4]

    lui  $11, 0x1001
    ori  $11, $11, 0x0018     # $11 = adresse s
    sw   $9, 0($11)

    lui  $12, 0x1001
    ori  $12, $12, 0x001C     # $12 = adresse p
    sw   $10, 0($12)

    addiu $13, $9, 1            # $13 = s+1
    sw    $13, 0($8)

    addu  $14, $9, $10            # $14 = s+p
    sw    $14, 4($8)

    lw    $15, 20($8)              # $15 = tab[5]
    sw    $15, 8($8)

    ori   $2, $0, 10
    syscall
```

A la fin : s=3, p=8, tab={4, 11, 1, 3, 8, 1}.
