# sec_emb
Ensuite, je compilerais le programme sous forme binaire (la manière dont la plupart des programmes sont distribués) et tenterais de modifier le programme compilé pour accepter n'importe quel mot de passe.

-------------- Building a Program --------------

En regardant le fichier C, le code est :
```C

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
int is_valid(const char* password)
{
    if (strcmp(password, "pass") == 0) {
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
Résultat de compilation:
![image](https://user-images.githubusercontent.com/46088690/152654101-599679b0-bd34-41be-90c9-99594ed2a21d.png)

-------------- A Deeper Look --------------

En utilisant hexdump, nous pouvons voir les informations binaires brutes contenues dans le fichier program:
![image](https://user-images.githubusercontent.com/46088690/152648969-96f37144-769b-4ac4-8101-1709cdcce2e5.png)

La colonne de gauche est l'offset en hexadécimal et les deux colonnes du milieu sont le contenu réel du fichier lui-même en hexadécimal. La colonne de droite affiche un équivalent ASCII pour le contenu du fichier, si possible.

Pour savoir quels morceaux de texte sont intégrés dans le fichier, nous utilisons la commande string:

![image](https://user-images.githubusercontent.com/46088690/152652008-8d860a98-cb7e-4df3-9975-c7357e0f52ce.png)

![image](https://user-images.githubusercontent.com/46088690/152653922-fc281b57-d626-4149-974e-1e5dcaea3a7d.png)

![image](https://user-images.githubusercontent.com/46088690/152651639-06ea407c-0e16-46e1-9c1c-1a9e3aa8444c.png)

On retrouve le mot de pass en clair ainsi que les fonctions printf, strcmp, et is_valid.

-------------- Disassembling the Program --------------

Ce processus est appelé "désassemblage" au lieu de "décompilation" car nous ne pouvons pas récupérer le code source d'origine ; à la place, nous pouvons récupérer les noms des instructions encodées en code machine.

exécutant objdump -S -l -C -F -t -w program | less pour obtenir le démontage:

![image](https://user-images.githubusercontent.com/46088690/152651876-9c5fbe2a-0355-4602-986c-6422dd66ca8f.png)

La colonne la plus à gauche contient les offsets. La colonne suivante est les instructions binaires elles-mêmes en hexadécimal. Après cela sont les noms et les paramètres de chaque instruction.

En analysant les différentes instructions, on déduit que l'exécutable binaire a une fonction appelée is_valid, et cette fonction appelle strcmp avec certaines valeurs et renvoie un 1 ou un 0 en fonction de sa valeur de retour. 

-------------- Breaking the Program --------------

Maintenant nous allons chercher à modifier le programme pour qu'il pense que tout mot de passe est correct.
Nous devons modifier le fichier programme lui-même en trouvant et en modifiant ces nombres hexadécimaux quelque part dans le fichier.

