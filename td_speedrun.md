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

Nous recevons le message.
![image](https://user-images.githubusercontent.com/46088690/152787779-a7a14521-800e-4bf1-9fa7-0d7e5448cb39.png)

En attachant gdb à l'exécutable, on essaye une entrée avec beaucoup d'AAAAA..., on trouve qu'il y a un défaut de segmentation, il s'agit d'un buffer overflow.

![image](https://user-images.githubusercontent.com/46088690/152689506-fe04445f-715f-4ebd-9d76-cb255170eea3.png)
![image](https://user-images.githubusercontent.com/46088690/152788452-9ea1d5bb-54bf-46a9-aa41-f7d690300e5d.png) 
 Maintenant que nous avons trouvé le buffer overflow nous devons trouver l'offset où nous contrôlerons le pointeur de retour sur la pile. 
 
On vérifie certaines protections comme NX en utilisant checksec:

 ![image](https://user-images.githubusercontent.com/46088690/152689953-ccbdbf46-28de-43a3-9487-8b83b3b3a261.png)
 
 On peut voir qu'il s'agit d'un binaire 64 bits avec NX, mais n'a pas de PIE ni de Stack Canary. En plus de cela, nous pouvons voir qu'il est lié statiquement (il y a donc beaucoup de gadgets ROP, et comme il n'y a pas de PIE, nous connaissons les adresses de chacun d'eux). On peut aussi voir qu'il y a un débordement de buffer.

On trouve également que le binaire est lié statiquement et n'a pas de bibliothèques dynamiques:

![image](https://user-images.githubusercontent.com/46088690/152691216-7f37266a-5d13-4504-8fb6-f1428f49dfc9.png)

Création de la ROP chaine:

![image](https://user-images.githubusercontent.com/46088690/152788788-e5a94185-b740-4f87-98eb-aed6ea1d5793.png)

Le résultat étant un code python, on copie la ROP chain:
![image](https://user-images.githubusercontent.com/46088690/152793535-1a1638ec-eb79-4ad6-ae92-72af3fb0e39b.png)

On ajoute le padding et on met le tout dans un fichier:
![image](https://user-images.githubusercontent.com/46088690/152794213-10f7120f-23e1-4f19-9ae5-c5a699a353be.png)

On réussit donc à ouvrir un shell command:

![image](https://user-images.githubusercontent.com/46088690/153298255-1e21eadc-316e-4caf-b06c-de1b80c3601c.png)

Après la lecture et l'envoie de l'exploit, le débordement de tampon se produit et la chaîne ROP finit par nous procurer le shell. Ensuite, le cat suivant sans arguments lit l'entrée que nous tapons et l'envoie à la sortie.




