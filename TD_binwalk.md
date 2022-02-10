# TP Binwalk

Execution de qemu:
-----------
![image](https://user-images.githubusercontent.com/46088690/152815369-74a9f580-fda8-4c57-8003-90f1bbb22a3a.png)

Lancement de la démo:
------------
![image](https://user-images.githubusercontent.com/46088690/152705927-4eaff1c2-0e37-4719-8ce1-68fd48da005e.png)
![image](https://user-images.githubusercontent.com/46088690/152705762-14a0bf99-93c9-408c-a95d-2de0d63442ad.png)

Recherche de l'image tux:
--------------
Extraction des fichiers et analyse récursive:

![image](https://user-images.githubusercontent.com/46088690/152816650-4acf34ae-0d6c-4423-b57f-92a7bcf4bb56.png)

L'option -M analyse de manière récursive les fichiers extraits lors d'une analyse --signature.

![image](https://user-images.githubusercontent.com/46088690/152819546-2c4e8ed5-fcde-4147-bda9-3542a91b894e.png)

![image](https://user-images.githubusercontent.com/46088690/152820741-590371e6-b6fd-409f-a059-232f34de8423.png)

On trouve donc l'image tux.png

![image](https://user-images.githubusercontent.com/46088690/153487994-7b7dc09c-e060-402e-9b94-bd96ad4b228d.png)

![image](https://user-images.githubusercontent.com/46088690/153476570-8d649425-85f1-45f7-9b05-1b3e1796c3ce.png)

Qu'est ce qui empêche de modifier le pingouin directement?

Le fichier étant chiffré on ne peut pas le modifier directement.

Qu'est ce que je peux faire pour contourner cette protection?

N'ayant pas de clé de chiffrement on ne peut pas déchiffrer le fichier mais on pourra le convertir en hexadécimale et accéder au code de l'image pour le modifier.

Quelle propriété de sécurité est garantie?

La confidentialité et la signature du fichier.

Bonus: Quelle propriété de sécurité n'est pas garantie? Que peut on faire pour obtenir cette garantie?

L’intégrité et on peut l'avoir en définissant en contrôlant les permissions et les droits d’accès et de modification au fichier.

