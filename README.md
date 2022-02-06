# sec_emb
Reverse Engineering 
--------------

Nous allons compiler le programme sous forme binaire et tenter de modifier le programme compilé pour accepter n'importe quel mot de passe.

Building a Program
-------------- 
 Le code C pour la vérification des mots de pass est :
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

A Deeper Look
--------------

En utilisant hexdump, nous pouvons voir les informations binaires brutes contenues dans le fichier program:

![image](https://user-images.githubusercontent.com/46088690/152648969-96f37144-769b-4ac4-8101-1709cdcce2e5.png)

La colonne de gauche est l'offset en hexadécimal et les deux colonnes du milieu sont le contenu réel du fichier lui-même en hexadécimal. La colonne de droite affiche un équivalent ASCII pour le contenu du fichier, si possible.

Pour savoir quels morceaux de texte sont intégrés dans le fichier, nous utilisons la commande string:

![image](https://user-images.githubusercontent.com/46088690/152652008-8d860a98-cb7e-4df3-9975-c7357e0f52ce.png)

![image](https://user-images.githubusercontent.com/46088690/152653922-fc281b57-d626-4149-974e-1e5dcaea3a7d.png)

![image](https://user-images.githubusercontent.com/46088690/152651639-06ea407c-0e16-46e1-9c1c-1a9e3aa8444c.png)

On retrouve le mot de pass en clair ainsi que les fonctions printf, strcmp, et is_valid.

Disassembling the Program
-------------- 

Le processus de désassemblage nous ne permet pas récupérer le code source d'origine mais nous permet de récupérer les noms des instructions encodées en code machine.

exécutant objdump -S -l -C -F -t -w program | less pour obtenir le désaassemblage du binaire:

![image](https://user-images.githubusercontent.com/46088690/152651876-9c5fbe2a-0355-4602-986c-6422dd66ca8f.png)

Sur la gauche, nous avons les adresses mémoires auxquelles sont placés les codes binaires. Ensuite nous avons leur valeur et, enfin, sur la droite, nous avons du langage d’assemblage représentant les noms et les paramètres de chaque instruction.

En analysant les différentes instructions, on déduit que l'exécutable binaire a une fonction appelée is_valid, et cette fonction appelle strcmp avec certaines valeurs et renvoie un 1 ou un 0 en fonction de sa valeur de retour. 

Breaking the Program
-------------- 

Maintenant nous allons chercher à modifier le programme pour qu'il pense que tout mot de passe est correct.
Nous devons modifier le fichier programme en trouvant et en modifiant ces nombres hexadécimaux quelque part dans le fichier.
Pour arriver à retourner tout le temps 1, il faut modifier le code assembleur pour qu’il mette toujours la valeur 1 dans le registre correspondant.

![image](https://user-images.githubusercontent.com/46088690/152656790-2027192d-313b-4a34-88c3-04d4d7edc813.png)

Le code correspondant au retour de la valeur 1 est : b8 01 00 00 00
L'instruction qui nous intéresse donc est à "000007f0", nous n'avons besoin de changer que le sixième octet de l'instruction, correspondant à 2038 octets.

![image](https://user-images.githubusercontent.com/46088690/152657136-89077313-a4f7-4ad2-b491-b58bb765eec0.png)

On écrit dans le fichier programme en changent un octet et sans tronquer le reste du fichier.

![image](https://user-images.githubusercontent.com/46088690/152657207-03ec24d7-36b7-4e0a-965a-c96a4b3c8354.png)

On arrive à accepter tous les mots de pass.

Questions:
-------------- 
Quelle différence dans un environnement ARM?

ARM a une architecture Reduced Instruction Set Computing RISC, cela signifie que le CPU a un nombre limité d'instructions qu'il peut utiliser. De ce fait, chaque instruction s'exécute en un seul cycle et les instructions sont plus simples, alors que x86 est une architecture CISC, qui signifie Complex Instruction Set Computing, il a beaucoup plus d'instructions. Les instructions entre les deux environnement sont différentes.

Quelles sont les attaques par patching possibles sur une boucle for?

On peut utiliser l'instruction jmp pour sauter à l'instruction qui nous intéresse en modifiant l'adresse du saut et contourner la vérification de la condition.

Qu'elle défense est ce que je peux utiliser contre le patching?

On ne peut pas empêcher le patch mais on peut le rendre difficile en ajoutant des couches d'obscurcissement au code pour rendre le désassemblage compliquée à comprendre, en incorporant le fichier entier dans un autre fichier, en chiffrant le fichier d'origine, et en ne le déchiffrant et en le chargeant qu'au moment de l'exécution, ou effectuer une vérification de la somme de contrôle de celui-ci aux deux extrémités, avant de déchiffrer le fichier et après avoir déchiffré le fichier lors de l'exécution.
