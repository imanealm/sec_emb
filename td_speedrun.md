# TD3 speedrun 
Setup 
--------------
Vérification que notre image docker a été construite:

![image](https://user-images.githubusercontent.com/46088690/152787131-6bf922dd-cf66-44ab-a2bf-e5938bd3dd4d.png)


Démarrage du conteneur:

![image](https://user-images.githubusercontent.com/46088690/152787390-64a23f54-9934-48d9-8076-6388f03a0cb7.png)

![image](https://user-images.githubusercontent.com/46088690/152787617-50ce9075-f69c-4822-8912-18bbf53e4689.png)

The Speedrun
--------------

Nous recevons le message suivant :

![image](https://user-images.githubusercontent.com/46088690/152787779-a7a14521-800e-4bf1-9fa7-0d7e5448cb39.png)

En attachant gdb à l'exécutable, on essaye une entrée avec beaucoup d'AAAAA..., on trouve qu'il y a un défaut de segmentation, il s'agit d'un buffer overflow.

![image](https://user-images.githubusercontent.com/46088690/152689506-fe04445f-715f-4ebd-9d76-cb255170eea3.png)
![image](https://user-images.githubusercontent.com/46088690/152788452-9ea1d5bb-54bf-46a9-aa41-f7d690300e5d.png) 

 Maintenant que nous avons trouvé le buffer overflow nous devons trouver l'offset où nous contrôlerons le pointeur de retour sur la pile. 
 
On vérifie certaines protections comme NX en utilisant checksec:

 ![image](https://user-images.githubusercontent.com/46088690/152689953-ccbdbf46-28de-43a3-9487-8b83b3b3a261.png)
 
 On peut voir qu'il s'agit d'un binaire avec NX, mais n'a pas de PIE ni de Stack Canary. 
 En plus de cela, nous pouvons voir qu'il est lié statiquement et n'a pas de bibliothèques dynamiques, il y a donc beaucoup de gadgets ROP, et comme il n'y a pas de PIE, nous connaissons les adresses de chacun d'eux:

![image](https://user-images.githubusercontent.com/46088690/152691216-7f37266a-5d13-4504-8fb6-f1428f49dfc9.png)

Nous avons donc un débordement de buffer. Trouvons le décalage depuis le début de notre entrée. Pour ce faire, on utilise gdb pour générer une chaîne, l'envoyer, voir quelle valeur se trouve dans l'adresse de retour et voir où se trouve cette valeur dans la chaîne.

![image](https://user-images.githubusercontent.com/46088690/153299630-b0b8f30b-def2-40c9-aa63-9f1afa42d166.png)

Maintenant nous savons comment écrire sur l'adresse de retour et puisqu'il s'agit d'un binaire lié statiquement sans PIE, nous pouvons simplement opter pour une chaîne ROP facilement.
Création de la ROP chaine:

![image](https://user-images.githubusercontent.com/46088690/153300477-7d1ff757-d858-440e-a5a5-e022ad804cd1.png)

Le résultat étant un code python, on copie la ROP chain:

![image](https://user-images.githubusercontent.com/46088690/153300760-9d790ae4-048e-4285-883f-92b67f163737.png)

On ajoute le padding et on met le tout dans un fichier:

![image](https://user-images.githubusercontent.com/46088690/153300315-41e42c9f-aa7b-4649-94aa-6f02fd84aead.png)

On réussit donc à ouvrir un shell command:

![image](https://user-images.githubusercontent.com/46088690/153298255-1e21eadc-316e-4caf-b06c-de1b80c3601c.png)

Après la lecture et l'envoie de l'exploit, le débordement de tampon se produit et la chaîne ROP finit par nous procurer le shell. Ensuite, le cat suivant sans arguments lit l'entrée que nous tapons et l'envoie à la sortie.

Questions:
--------------
Devenez root. Qu'est ce qu'un attaquant peut faire une fois root?

En tant que root l'attaquant aura des privilèges lui permettant d'accéder à des données confidentielles, il pourra naviguer dans l'arborescence, analyser les droits, télécharger des fichiers, en déposer, déployer des logiciels malveillants et executer des commandes.

Qu'est ce que je dois prendre en compte dans mon modèle d'attaque?

Ce qu'il faut prendre en compte c'est la taille du buffer, et également le mode d'allocation de mémoire utiliser soit le stack overflow qui est rapide et fonctionne comme une pile et le heap overflow qui est moins rapide et désorganisé mais adapté quand la manipulation doit être gérée de façon explicite ou implique de gros morceaux de données.

Comprendre le lien avec les bugs / cette méthode est-elle applicable dans le cas d'un use after free? Pourquoi?

Les vulnérabilités use after free exploitent des pointeurs qui référencent un endroit de la mémoire qui ne contient plus l'objet supposé être contenu dans cet espace. Si après avoir libéré un emplacement mémoire, un programme n'efface pas le pointeur vers cette mémoire, un attaquant peut utiliser l'erreur pour pirater le programme.
Pour les bogues "use-after-free", le processus d'exploitation est largement similaire à celui des exploits de débordement de tas. Avec une disposition de mémoire de tas appropriée, les objets vulnérables tombant dans l'un des trous de mémoire sont libérés et occupés par des données vectorielles contrôlées. 

Qu'est ce que je peux faire pour diminuer / contrer les bugs?

Il faut adopter de bonnes pratiques telles que le recours général au bounds checking et éviter des fonctions employées dangereusement, comme strcpy en C, utiliser des outils qui permettent aux développeurs de détecter en amont ces problèmes comme Valgrind (Memcheck), ASan (Address Sanitizer), KASAN (KernelAdressSAnitizer pour Linux), utiliser des langages de haut niveau implémentant un garbage collector qui s'occupe de nettoyer la mémoire allouée qui n'est plus référencée, utiliser des canaries au niveau du compilateur, au niveau du système, utiliser la technique ASLR pour arranger aléatoirement la mémoire d'un programme de telle sorte à limiter la possibilité qu'un buffer overflow soit exploité. au niveau du hardware, utiliser MTE est l'acronyme de Memory Tagging Extension qui permet que chaque allocation de mémoire soit taguée, et son accès ne soit possible que par un pointeur avec le tag correspondant.

Bonus: Quelle différence si les canary et l'ASLR sont présents?
L'ASLR est une protection dans le noyau qui rendra certains espaces d'adressage aléatoires. Généralement, la pile, le tas et les bibliothèques sont impactés. Il n'est alors plus possible de trouver l'adresse d'un shellcode placé sur la pile, ni l'adresse de la systemfonction dans la libc.
Les canary sont des morceaux de mémoire qui sont ajoutés de façon adjacente et dont la valeur peut être vérifiée, ce qui rend des overflow détectables du fait de la destruction de ces canaries le cas échéant.

