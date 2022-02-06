# TD3 speedrun 
Setup 
--------------
Vérification que notre image docker a été construite:

![image](https://user-images.githubusercontent.com/46088690/152688056-48ed8559-32eb-4688-884b-06e2fe2149f6.png)

Démarrage du conteneur:

![image](https://user-images.githubusercontent.com/46088690/152688227-094bad93-73a9-4c46-bf4f-f013ba67d7db.png)

![image](https://user-images.githubusercontent.com/46088690/152688350-ac3cff3f-d2ac-49d2-919c-90f38921ab08.png)

The Speedrun
--------------

Nous recevons le message.
![image](https://user-images.githubusercontent.com/46088690/152688868-4240f7f9-c2da-46e2-8d94-8ebd1378c2e5.png)

J'ai attaché gdb à l'exécutable, et cette fois nous pouvons essayer une entrée avec beaucoup d'AAAAA..., Nous avons un défaut de segmentation ; en effet, il s'agissait d'un simple débordement de buffer.

![image](https://user-images.githubusercontent.com/46088690/152689506-fe04445f-715f-4ebd-9d76-cb255170eea3.png)
![image](https://user-images.githubusercontent.com/46088690/152689417-d1727c8a-5061-416a-9ff8-e7b251f53dc9.png)

 Au lieu d'utiliser une génération de motif et de vérifier le décalage, j'ai simplement essayé de le faire à la main ;
 
 Maintenant que nous avons trouvé le buffer overflow nous devons trouver le décalage où nous contrôlons le pointeur de retour sur la pile, 
