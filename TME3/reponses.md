# TME3



## Exercice 1

### Question 1

Menu Help de MARS : liste des instructions, directives et syscalls.

### Question 2

1. `addi $12, $18, 33` dans un fichier test.s
2. Non, taper du texte ne change pas Execute
3. Non, sauvegarder non plus
4. Assemble : ça marche, une seule instruction sans exit
5. Le code hexa est dans Execute > Text Segment, colonne Code. Adresse 0x00400000.

### Question 3

Format I : opcode(6) rs(5) rt(5) immediat(16)

addi $12,$18,33 -> opcode 001000, rs=10010($18), rt=01100($12), imm=0000000000100001

Mot : `0010 0010 0100 1100 0000 0000 0010 0001` = 0x224C0021

On inverse le bit 29 (1 -> 0), l'opcode devient 000000 (format R).

Nouveau mot : 0x044C0021

Décodage : rs=$18, rt=$12, rd=$0, shamt=0, funct=100001=0x21 -> ADDU

Donc : `addu $0, $18, $12`

A l'exécution : addi met $12=33, puis addu tente d'écrire dans $0. $0 reste à 0 (câblé à zéro, écriture ignorée).

### Question 4

En mettant `12` au lieu de `$12`, MARS refuse d'assembler (erreur de syntaxe).

## Exercice 2

### Question 1

```mips
.text
main:
    ori  $8, $0, 137   # $8 = valeur
    or   $4, $8, $0
    ori  $2, $0, 1
    syscall
    ori  $2, $0, 10
    syscall
```

Exécuter pas à pas, reset, relancer sans arrêt (affiche 137), reset, ralentir et pauser après le syscall d'affichage. En reculant d'une instruction puis changeant $8, l'affichage reste 137 : le syscall lit $a0, déjà chargé avant.

### Question 2 — charger 65537 dans $8

L'immédiat fait 16 bits, 65537 ne rentre pas : il faut lui + ori.

```mips
.text
main:
    lui  $8, 1
    ori  $8, $8, 1      # $8 = 65537

    or   $4, $8, $0
    ori  $2, $0, 1
    syscall
    ori  $2, $0, 10
    syscall
```

## Exercice 3

### Question 1

```mips
.text
main:
    ori  $9, $0, 84    # $9 = 84
    ori  $10, $0, 10    # $10 = 10

    div  $9, $10
    mflo $11             # $11 = quotient
    mfhi $12             # $12 = reste

    or   $4, $11, $0
    ori  $2, $0, 1
    syscall

    or   $4, $12, $0
    ori  $2, $0, 1
    syscall

    ori  $2, $0, 10
    syscall
```

Reconstruire 84 (quotient*10+reste) :

```mips
    mult $11, $10
    mflo $13
    addu $13, $13, $12   # $13 = 84 reconstruit

    or   $4, $13, $0
    ori  $2, $0, 1
    syscall
```

### Question 2 — valeurs au clavier

```mips
.text
main:
    ori  $2, $0, 5
    syscall
    or   $9, $2, $0      # $9 = premier entier

    ori  $2, $0, 5
    syscall
    or   $10, $2, $0      # $10 = second entier

    div  $9, $10
    mflo $11               # quotient
    mfhi $12               # reste

    mult $11, $10
    mflo $13
    addu $13, $13, $12       # reconstruction

    or   $4, $11, $0
    ori  $2, $0, 1
    syscall
    or   $4, $12, $0
    ori  $2, $0, 1
    syscall
    or   $4, $13, $0
    ori  $2, $0, 1
    syscall

    ori  $2, $0, 10
    syscall
```

## Exercice 4

```mips
.data
.text
ori $8, $0, 0x00FF   # $8 = 0x000000FF
ori $9, $0, 0xFFF0   # $9 = 0x0000FFF0
and $10, $9, $8       # $10 = 0x000000F0
xor $11, $9, $8       # $11 = 0x0000FF0F
xor $11, $11, $11     # $11 = 0

ori $9, $0, 25

sll $10, $9, 1          # 50
sll $11, $9, 2          # 100
sll $12, $9, 3          # 200

srl $10, $9, 1          # 12
srl $10, $9, 2          # 6
srl $10, $9, 3          # 3

addi $9, $0, -25         # 0xFFFFFFE7

srl $10, $9, 1           # 0x7FFFFFF3
srl $11, $9, 2           # 0x3FFFFFF9

sra $12, $9, 1           # 0xFFFFFFF3 = -13
sra $13, $9, 2           # 0xFFFFFFF9 = -7
sra $14, $9, 3           # 0xFFFFFFFC = -4

ori $9, $0, 2
ori $8, $0, 4
slt $11, $8, $9          # 0
slt $12, $9, $8          # 1

ori $2, $0, 10
syscall
```

sll multiplie par 2^k. srl (logique) insère des 0 : bon pour un naturel, faux pour un relatif négatif. sra (arithmétique) insère le bit de signe : c'est celui-là qu'il faut utiliser pour diviser un relatif par 2^k.

## Exercice 5

$3 = 0xAABBCCDD = o3 o2 o1 o0. On veut o0 o2 o3 o1 = 0xDDBBAACC dans $5.

```mips
.text
main:
    lui  $3, 0xAABB
    ori  $3, $3, 0xCCDD   # $3 = mot a melanger

    or   $4, $3, $0
    ori  $2, $0, 34
    syscall

    srl  $8, $3, 24        # $8 = o3
    srl  $9, $3, 16
    andi $9, $9, 0x00FF    # $9 = o2
    srl  $10, $3, 8
    andi $10, $10, 0x00FF  # $10 = o1
    andi $11, $3, 0x00FF   # $11 = o0

    sll  $12, $11, 24
    sll  $13, $9, 16
    sll  $14, $8, 8

    or   $5, $12, $13
    or   $5, $5, $14
    or   $5, $5, $10        # $5 = resultat

    or   $4, $5, $0
    ori  $2, $0, 34
    syscall

    ori  $2, $0, 10
    syscall
```
