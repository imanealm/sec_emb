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




