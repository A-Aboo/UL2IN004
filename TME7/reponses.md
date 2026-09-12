# TME7



## Exercice 1

### Question 1 — sans fonction

```mips
.data
ch: .asciiz "1 exemple d'exemple\n"

.text
main:
    lui  $4, 0x1001
    ori  $4, $4, 0x0000
    ori  $2, $0, 4
    syscall

    lui  $8, 0x1001
    ori  $8, $8, 0x0000   # $8 = adresse ch
    ori  $9, $0, 0          # $9 = i

boucle:
    addu  $10, $8, $9
    lb    $11, 0($10)         # $11 = caractere
    beq   $11, $0, fin_boucle

    slti  $12, $11, 0x61
    bne   $12, $0, suite
    slti  $13, $11, 0x7B
    beq   $13, $0, suite

    addiu $11, $11, -0x20
    sb    $11, 0($10)

suite:
    addiu $9, $9, 1
    j     boucle

fin_boucle:
    lui   $4, 0x1001
    ori   $4, $4, 0x0000
    ori   $2, $0, 4
    syscall

    ori   $2, $0, 10
    syscall
```

### Question 2 — fonction avec un pointeur

```mips
.data
ch1: .asciiz "1 exemple d'exemple\n"
ch2: .asciiz "Hello world!\n"

.text
min_to_maj_chaine:
    or    $8, $4, $0    # $8 = adresse ch
    ori   $9, $0, 0       # $9 = i

boucle_f:
    addu  $10, $8, $9
    lb    $11, 0($10)        # $11 = caractere
    beq   $11, $0, fin_f

    slti  $12, $11, 0x61
    bne   $12, $0, suite_f
    slti  $13, $11, 0x7B
    beq   $13, $0, suite_f

    addiu $11, $11, -0x20
    sb    $11, 0($10)

suite_f:
    addiu $9, $9, 1
    j     boucle_f

fin_f:
    jr    $31

main:
    lui  $4, 0x1001
    ori  $4, $4, 0x0000
    ori  $2, $0, 4
    syscall

    lui  $4, 0x1001
    ori  $4, $4, 0x0000
    jal  min_to_maj_chaine

    lui  $4, 0x1001
    ori  $4, $4, 0x0000
    ori  $2, $0, 4
    syscall

    lui  $4, 0x1001
    ori  $4, $4, 0x0015
    ori  $2, $0, 4
    syscall

    lui  $4, 0x1001
    ori  $4, $4, 0x0015
    jal  min_to_maj_chaine

    lui  $4, 0x1001
    ori  $4, $4, 0x0015
    ori  $2, $0, 4
    syscall

    ori  $2, $0, 10
    syscall
```

0x0015 = taille de ch1 (20 caractères + fin de chaine), donc l'adresse de ch2.

### Question 3 — fonction avec un char

```mips
min_to_maj_char:
    slti  $8, $4, 0x61
    bne   $8, $0, pas_minuscule
    slti  $9, $4, 0x7B
    beq   $9, $0, pas_minuscule

    addiu $2, $4, -0x20
    jr    $31

pas_minuscule:
    or    $2, $4, $0
    jr    $31

main:
    lui  $4, 0x1001
    ori  $4, $4, 0x0000
    ori  $2, $0, 4
    syscall

    lui  $16, 0x1001
    ori  $16, $16, 0x0000   # $16 = adresse ch
    ori  $17, $0, 0           # $17 = i

boucle_m:
    addu $18, $16, $17
    lb   $19, 0($18)            # $19 = caractere
    beq  $19, $0, fin_m

    or    $4, $19, $0
    jal   min_to_maj_char
    sb    $2, 0($18)

    addiu $17, $17, 1
    j     boucle_m

fin_m:
    lui  $4, 0x1001
    ori  $4, $4, 0x0000
    ori  $2, $0, 4
    syscall

    ori  $2, $0, 10
    syscall
```

$16/$17 sont persistants donc ils survivent au jal sans être sauvegardés.

### Question 4 — fonction avec adresse d'un char

```mips
min_to_maj_ptr_char:
    lb    $8, 0($4)
    slti  $9, $8, 0x61
    bne   $9, $0, fin_p
    slti  $10, $8, 0x7B
    beq   $10, $0, fin_p
    addiu $8, $8, -0x20
    sb    $8, 0($4)
fin_p:
    jr    $31

main:
    lui  $4, 0x1001
    ori  $4, $4, 0x0000
    ori  $2, $0, 4
    syscall

    lui  $16, 0x1001
    ori  $16, $16, 0x0000   # $16 = adresse ch
    ori  $17, $0, 0           # $17 = i

boucle_m2:
    addu $18, $16, $17
    lb   $19, 0($18)             # $19 = caractere
    beq  $19, $0, fin_m2

    or   $4, $18, $0
    jal  min_to_maj_ptr_char

    addiu $17, $17, 1
    j     boucle_m2

fin_m2:
    lui  $4, 0x1001
    ori  $4, $4, 0x0000
    ori  $2, $0, 4
    syscall

    ori  $2, $0, 10
    syscall
```

## Exercice 2 — variables locales

Mêmes fonctions qu'avant, seul main change : ch1/ch2 sont des tableaux locaux sur la pile, lus au clavier.

### Question 1

```mips
.text
main:
    addiu $sp, $sp, -32

    addiu $4, $sp, 0
    ori   $2, $0, 8
    addiu $5, $0, 16
    syscall

    addiu $4, $sp, 16
    ori   $2, $0, 8
    addiu $5, $0, 16
    syscall

    addiu $4, $sp, 0
    ori   $2, $0, 4
    syscall

    addiu $4, $sp, 0
    jal   min_to_maj_chaine

    addiu $4, $sp, 0
    ori   $2, $0, 4
    syscall

    addiu $4, $sp, 16
    ori   $2, $0, 4
    syscall

    addiu $4, $sp, 16
    jal   min_to_maj_chaine

    addiu $4, $sp, 16
    ori   $2, $0, 4
    syscall

    addiu $sp, $sp, 32
    ori   $2, $0, 10
    syscall
```

(reprend min_to_maj_chaine de l'exercice 1)

### Question 2

```mips
.text
main:
    addiu $sp, $sp, -16

    addiu $4, $sp, 0
    ori   $2, $0, 8
    addiu $5, $0, 16
    syscall

    addiu $4, $sp, 0
    ori   $2, $0, 4
    syscall

    addiu $16, $sp, 0    # $16 = adresse ch
    ori   $17, $0, 0       # $17 = i

boucle:
    addu $18, $16, $17
    lb   $19, 0($18)          # $19 = caractere
    beq  $19, $0, fin

    or    $4, $19, $0
    jal   min_to_maj_char
    sb    $2, 0($18)

    addiu $17, $17, 1
    j     boucle

fin:
    addiu $4, $sp, 0
    ori   $2, $0, 4
    syscall

    addiu $sp, $sp, 16
    ori   $2, $0, 10
    syscall
```

### Question 3

Pareil mais on appelle min_to_maj_ptr_char avec l'adresse de ch[i] :

```mips
.text
main:
    addiu $sp, $sp, -16
    addiu $4, $sp, 0
    ori   $2, $0, 8
    addiu $5, $0, 16
    syscall

    addiu $4, $sp, 0
    ori   $2, $0, 4
    syscall

    addiu $16, $sp, 0    # $16 = adresse ch
    ori   $17, $0, 0       # $17 = i

boucle:
    addu $18, $16, $17
    lb   $19, 0($18)          # $19 = caractere
    beq  $19, $0, fin

    or    $4, $18, $0
    jal   min_to_maj_ptr_char

    addiu $17, $17, 1
    j     boucle

fin:
    addiu $4, $sp, 0
    ori   $2, $0, 4
    syscall

    addiu $sp, $sp, 16
    ori   $2, $0, 10
    syscall
```

Différence globale/locale : l'adresse change (lui+ori vs addiu $r,$sp,offset), mais les fonctions ne changent jamais, elles ne connaissent que l'adresse/valeur reçue.
