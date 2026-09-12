# TME6



## Exercice 1

### Question 1 — sans optimisation

```mips
.data
chaine: .space 11

.text
main:
    addiu $sp, $sp, -12

    ori  $2, $0, 5
    syscall
    sw   $2, 0($sp)     # nb

    lui  $8, 0x1001
    ori  $8, $8, 0x0000    # $8 = adresse chaine
    sb   $0, 10($8)

    addiu $9, $0, 9
    sw    $9, 8($sp)          # i

    addiu $13, $0, 10           # constante 10

boucle:
    lw   $9, 8($sp)                # $9 = i
    slt  $10, $9, $0
    bne  $10, $0, fin_boucle

    lw   $11, 0($sp)                 # $11 = nb
    div  $11, $13
    mflo $14
    mfhi $15
    sw   $14, 0($sp)                   # nb = nb/10
    sw   $15, 4($sp)                     # r = reste

    addiu $16, $15, 0x30
    lui   $17, 0x1001
    ori   $17, $17, 0x0000
    addu  $17, $17, $9
    sb    $16, 0($17)

    addiu $9, $9, -1
    sw    $9, 8($sp)
    j     boucle

fin_boucle:
    lui  $4, 0x1001
    ori  $4, $4, 0x0000
    ori  $2, $0, 4
    syscall

    addiu $sp, $sp, 12
    ori   $2, $0, 10
    syscall
```

Version optimisée (i, nb, r en registres) :

```mips
.data
chaine: .space 11

.text
main:
    ori   $2, $0, 5
    syscall
    or    $9, $2, $0     # $9 = nb

    lui   $8, 0x1001
    ori   $8, $8, 0x0000    # $8 = adresse chaine
    sb    $0, 10($8)

    addiu $10, $0, 9           # $10 = i
    addiu $13, $0, 10            # constante 10

boucle:
    slt   $11, $10, $0
    bne   $11, $0, fin_boucle

    div   $9, $13
    mflo  $9                       # nb = nb/10
    mfhi  $12                        # $12 = reste

    addiu $12, $12, 0x30
    addu  $14, $8, $10
    sb    $12, 0($14)

    addiu $10, $10, -1
    j     boucle

fin_boucle:
    or    $4, $8, $0
    ori   $2, $0, 4
    syscall

    ori   $2, $0, 10
    syscall
```

### Question 2 — compter les zéros au début

```mips
    ori   $15, $0, 0    # $15 = nbzero
    ori   $16, $0, 0     # $16 = i

boucle_zero:
    slti  $17, $16, 9
    beq   $17, $0, fin_zero
    addu  $18, $8, $16
    lb    $19, 0($18)
    addiu $20, $0, 0x30
    bne   $19, $20, fin_zero
    addiu $15, $15, 1
    addiu $16, $16, 1
    j     boucle_zero

fin_zero:
    or    $4, $15, $0
    ori   $2, $0, 1
    syscall

    ori   $2, $0, 10
    syscall
```

### Question 3 — enlever les zéros (recopie)

```mips
fin_zero:
    or    $4, $15, $0
    ori   $2, $0, 1
    syscall

    addiu $21, $0, 11
    subu  $21, $21, $15    # $21 = limite

    ori   $16, $0, 0         # $16 = i

boucle_copie:
    slt   $17, $16, $21
    beq   $17, $0, fin_copie

    addu  $18, $16, $15
    addu  $19, $8, $18
    lb    $20, 0($19)
    addu  $22, $8, $16
    sb    $20, 0($22)

    addiu $16, $16, 1
    j     boucle_copie

fin_copie:
    or    $4, $8, $0
    ori   $2, $0, 4
    syscall

    ori   $2, $0, 10
    syscall
```

### Question 4 — chaine en variable locale

Réserver 12 octets de plus sur la pile, remplacer l'adresse fixe par `or $8, $sp, $0`. Le reste ne change pas, tout passe déjà par $8.

## Exercice 2 — struct point

```c
struct point {
    char[2] nom;
    int abs;
    int ord;
};
```

Un int doit être aligné sur 4 : 2 octets de padding après nom. Taille totale : 12 octets (nom 0-1, padding 2-3, abs 4-7, ord 8-11).

```mips
.data
p1:  .byte 'X', 0
     .align 2
     .word 2
     .word 6
p2:  .byte 'Y', 0
     .align 2
     .word 4
     .word 4
p3:  .space 12
ptr: .word 0

.text
main:
    lui  $8, 0x1001
    ori  $8, $8, 0x0018   # $8 = adresse p3
    lui  $9, 0x1001
    ori  $9, $9, 0x0024    # $9 = adresse ptr
    sw   $8, 0($9)

    lw   $11, 0($9)          # $11 = ptr

    lui  $10, 0x1001           # $10 = adresse p1
    lw   $12, 0x0004($10)        # $12 = p1.abs
    lw   $13, 0x0010($10)         # $13 = p2.abs
    addu $14, $12, $13
    srl  $14, $14, 1                # $14 = ptr->abs
    sw   $14, 4($11)

    lw   $12, 0x0008($10)             # $12 = p1.ord
    lw   $13, 0x0014($10)              # $13 = p2.ord
    addu $14, $12, $13
    srl  $14, $14, 1                     # $14 = ptr->ord
    sw   $14, 8($11)

    addiu $15, $0, 0x5A
    sb    $15, 0($11)
    sb    $0, 1($11)

    or   $4, $11, $0
    ori  $2, $0, 4
    syscall

    lw   $4, 4($11)
    ori  $2, $0, 1
    syscall

    lw   $4, 8($11)
    ori  $2, $0, 1
    syscall

    ori  $2, $0, 10
    syscall
```

Résultat : p3.nom="Z", p3.abs=3, p3.ord=5.
