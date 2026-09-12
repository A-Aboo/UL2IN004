# TME2

## Exercice 1

### Question 1

```c
#include <stdio.h>

int main(void)
{
    char  c, c_prev = 0;
    short s, s_prev = 0;
    int   i, i_prev = 0;

    c = 0;
    while (c + 1 > 0) {
        c_prev = c;
        c = c + 1;
    }
    printf("char  : min = %d, max = %d\n", c, c_prev);

    s = 0;
    while (s + 1 > 0) {
        s_prev = s;
        s = s + 1;
    }
    printf("short : min = %d, max = %d\n", s, s_prev);

    i = 0;
    while (i + 1 > 0) {
        i_prev = i;
        i = i + 1;
    }
    printf("int   : min = %d, max = %d\n", i, i_prev);

    return 0;
}
```

char: min -128, max 127
short: min -32768, max 32767
int: min -2147483648, max 2147483647

### Question 2

```c
#include <stdio.h>

int main(void)
{
    unsigned char uc, uc_prev = 0;
    unsigned int  ui, ui_prev = 0;

    uc = 0;
    do {
        uc_prev = uc;
        uc = uc + 1;
    } while (uc > uc_prev);
    printf("unsigned char : min = 0, max = %u\n", uc_prev);

    ui = 0;
    do {
        ui_prev = ui;
        ui = ui + 1;
    } while (ui > ui_prev);
    printf("unsigned int  : min = 0, max = %u\n", ui_prev);

    return 0;
}
```

unsigned char: 0 a 255
unsigned int: 0 a 4294967295 (la boucle prend quelques secondes)

## Exercice 2

### Question 1

`0x41200000` = `0100 0001 0010 0000 0000 0000 0000 0000`

S=0, EXPONENT=`10000010`=130 (donc +3), MANTISSA=0.25 -> 1.25 x 2^3 = 10.0

### Question 2

`0x41950e56`: S=0, EXPONENT=`10000011`=131 (donc +4), MANTISSA≈0.1645 -> environ 18.63

### Question 3

25.5 = 1.10011 x 2^4 -> mot `0x41CC0000` = 1103888384

0.008 ≈ 1.024 x 2^-7 -> mot ≈ `0x3806126F` ≈ 939922031 (approché, 0.008 n'est pas exact en binaire)

```c
#include <stdio.h>

int main(void)
{
    int   n1, n2;
    float *pf1 = (float *) &n1;
    float *pf2 = (float *) &n2;
    float sum;

    printf("mot representant 25.5  : ");
    scanf("%d", &n1);
    printf("mot representant 0.008 : ");
    scanf("%d", &n2);

    printf("verif : %f et %f\n", *pf1, *pf2);

    sum = *pf1 + *pf2;
    printf("somme = %f\n", sum);

    return 0;
}
```

## Exercice 3

### Question 1

```c
#include <stdio.h>

int main(void)
{
    float u = 1.0f;
    int n;

    printf("u0 = %f\n", u);
    for (n = 1; n <= 128; n = n + 1) {
        u = 2.0f * u + 1.0f;
        printf("u%d = %f\n", n, u);
    }

    return 0;
}
```

### Question 2

un = 2^(n+1) - 1 double a chaque fois. Le plus grand float fait environ 2^128. Donc vers n=127 ça dépasse et l'affichage devient `inf`.

### Question 3

Un float ne garde que 24 bits de précision (2^24 = 16 777 216). A partir de n=24, un dépasse cette valeur et le pas devient 2 : le résultat calculé (33554432) tombe pair au lieu de l'impair attendu (33554431).

## Exercice 4

### Question 1

```c
#include <stdio.h>

#define TAILLE_MAX 100

int main(void)
{
    char ch[TAILLE_MAX];
    int i = 0;

    printf("Entrez une chaine : ");
    scanf("%s", ch);

    while (ch[i] != '\0') {
        if (ch[i] >= 'a' && ch[i] <= 'z') {
            ch[i] = ch[i] - 32;
        }
        i = i + 1;
    }

    printf("Resultat : %s\n", ch);
    return 0;
}
```

"abcd" -> "ABCD"

### Question 2

```c
#include <stdio.h>

#define TAILLE_MAX 20

int main(void)
{
    char ch[TAILLE_MAX];
    int i = 0;
    int val = 0;

    printf("Entrez un nombre : ");
    scanf("%s", ch);

    while (ch[i] != '\0') {
        val = val * 10 + (ch[i] - '0');
        i = i + 1;
    }

    printf("Valeur : %d\n", val);
    return 0;
}
```

"1234" -> 1234

## Exercice 5

### Question 1

```c
#include <stdio.h>

int main(void)
{
    unsigned int a, b, resultat, i;

    printf("a = ");
    scanf("%u", &a);
    printf("b = ");
    scanf("%u", &b);

    resultat = 0;
    for (i = 0; i < 32; i = i + 1) {
        if ((b >> i) & 1) {
            resultat = resultat + (a << i);
        }
    }

    printf("%u * %u = %u\n", a, b, resultat);
    return 0;
}
```

Pour chaque bit a 1 de b, on ajoute a décalé de la position du bit.
