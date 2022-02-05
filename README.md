# sec_emb
Ensuite, je compilerais le programme sous forme binaire (la manière dont la plupart des programmes sont distribués) et tenterais de modifier le programme compilé1 pour accepter n'importe quel mot de passe.

Ici, nous voyons un fichier binaire et un fichier C.

En regardant le fichier C, le code est :
```C

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
int is_valid(const char* password)
{
    if (strcmp(password, "pw") == 0) {
        return 1;
    } else {
        return 0;
    }
}
int main()
{
    char* input = NULL;
    input = malloc(256);
    printf("Please input a word: ");
    scanf("%s", input);

    if (is_valid(input)) {
        printf("That's correct!\n");
    } else {
        printf("That's not correct!\n");
    }

    free(input);
    return 0;
}
```
Le programme accepte un mot de passe et indique si le mot de passe est correct ou non.

![image](https://user-images.githubusercontent.com/46088690/152648812-9f2ec91d-695d-4339-8be4-bc430f1a0b71.png)


