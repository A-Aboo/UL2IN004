# TME5

## Exercice 1

Somme des entiers de p a q non inclus, 0 si p>=q.

```mips
.data
p: .word 0
q: .word 0

.text
main:
    ori  $2, $0, 5
    syscall
    or   $8, $2, $0    # $8 = p

    ori  $2, $0, 5
    syscall
    or   $9, $2, $0    # $9 = q

    ori  $10, $0, 0     # $10 = somme
    or   $11, $8, $0     # $11 = i

boucle:
    slt  $12, $11, $9
    beq  $12, $0, fin
    addu $10, $10, $11
    addiu $11, $11, 1
    j    boucle

fin:
    or   $4, $10, $0
    ori  $2, $0, 1
    syscall

    ori  $2, $0, 10
    syscall
```

## Exercice 2 — PGCD

Soustractions successives : tant que tmpa != tmpb, on soustrait le plus petit du plus grand.

```mips
.data
a: .word 0
b: .word 0

.text
main:
    ori  $2, $0, 5
    syscall
    or   $8, $2, $0    # $8 = a

    ori  $2, $0, 5
    syscall
    or   $9, $2, $0    # $9 = b

    or   $10, $8, $0    # $10 = tmpa
    or   $11, $9, $0     # $11 = tmpb

boucle:
    beq  $10, $11, fin
    slt  $12, $11, $10
    beq  $12, $0, sinon
    subu $10, $10, $11
    j    boucle
sinon:
    subu $11, $11, $10
    j    boucle

fin:
    or   $4, $10, $0
    ori  $2, $0, 1
    syscall

    ori  $2, $0, 10
    syscall
```

## Exercice 3 — taille d'une chaine

```mips
.data
ch: .asciiz "This is a test"

.text
main:
    lui  $8, 0x1001
    ori  $8, $8, 0x0000   # $8 = adresse ch
    ori  $9, $0, 0          # $9 = taille

boucle:
    lb   $10, 0($8)          # $10 = caractere
    beq  $10, $0, fin
    addiu $9, $9, 1
    addiu $8, $8, 1
    j    boucle

fin:
    or   $4, $9, $0
    ori  $2, $0, 1
    syscall

    ori  $2, $0, 10
    syscall
```

## Exercice 4 — parcours de tableau

val avant tab, tab se termine par -1. On compte les éléments < val.

```mips
.data
val: .word 12
tab: .word 4, 23, 12, 3, 8, 1, -1

.text
main:
    lui   $8, 0x1001
    ori   $8, $8, 0x0000   # $8 = adresse val
    lw    $9, 0($8)          # $9 = val

    addiu $10, $8, 4           # $10 = adresse tab
    ori   $11, $0, 0            # $11 = compteur
    addiu $14, $0, -1             # $14 = sentinelle -1

boucle:
    lw    $12, 0($10)             # $12 = element courant
    beq   $12, $14, fin

    slt   $13, $12, $9
    beq   $13, $0, suite
    addiu $11, $11, 1
suite:
    addiu $10, $10, 4
    j     boucle

fin:
    or    $4, $11, $0
    ori   $2, $0, 1
    syscall

    ori   $2, $0, 10
    syscall
```

Avec val=12 et tab={4,23,12,3,8,1,-1}, ça affiche 4.

## Exercice 5

Renvoie au TD5 exercice 5 (comptage de bits à 1) : masquer le bit de poids faible avec `and 1`, l'ajouter au compteur, décaler à droite (logique), répéter 32 fois.

```mips
.data
n: .word 123

.text
main:
    lui   $8, 0x1001
    ori   $8, $8, 0x0000   # $8 = adresse n
    lw    $9, 0($8)          # $9 = n

    ori   $10, $0, 0           # $10 = compteur de bits
    ori   $11, $0, 32           # $11 = iterations

boucle:
    beq   $11, $0, fin
    andi  $12, $9, 1              # $12 = bit courant
    addu  $10, $10, $12
    srl   $9, $9, 1
    addiu $11, $11, -1
    j     boucle

fin:
    or    $4, $10, $0
    ori   $2, $0, 1
    syscall

    ori   $2, $0, 10
    syscall
```

n=123 -> 6 bits, n=-1 -> 32 bits, n=0xFEDCBA98 -> 20 bits.
