# TP Binwalk

Execution de qemu:

![image](https://user-images.githubusercontent.com/46088690/152815369-74a9f580-fda8-4c57-8003-90f1bbb22a3a.png)

Lancement de la démo:

![image](https://user-images.githubusercontent.com/46088690/152705927-4eaff1c2-0e37-4719-8ce1-68fd48da005e.png)
![image](https://user-images.githubusercontent.com/46088690/152705762-14a0bf99-93c9-408c-a95d-2de0d63442ad.png)

Chercher l'image tux:
Charge les règles d'extraction communes --dd à partir d'un fichier prédéfini et les règles spécifiques à l'utilisateur, l'option -M analysera de manière récursive les fichiers extraits lors d'une analyse --signature.

Extraction des fichiers et analyse récursive:

![image](https://user-images.githubusercontent.com/46088690/152816650-4acf34ae-0d6c-4423-b57f-92a7bcf4bb56.png)

![image](https://user-images.githubusercontent.com/46088690/152819546-2c4e8ed5-fcde-4147-bda9-3542a91b894e.png)

![image](https://user-images.githubusercontent.com/46088690/152820741-590371e6-b6fd-409f-a059-232f34de8423.png)

On trouve donc l'image tux.png

Trouvez et modifiez le pingouin. Qu'est ce qui empêche de modifier le pingouin directement?

![image](https://user-images.githubusercontent.com/46088690/153476570-8d649425-85f1-45f7-9b05-1b3e1796c3ce.png)

Le fichier étant chiffré on ne peut pas le modifier directement.

Qu'est ce que je peux faire pour contourner cette protection?

Déchiffrer le fichier

Quelle propriété de sécurité est garantie?


Bonus: Quelle propriété de sécurité n'est pas garantie? Que peut on faire pour obtenir cette garantie?

La confidentialité car on a accès à des images de systèmes qui devaient être secret. On peut le résoudre en modifiant la politique de sécurité du système lui disant de ne pas donner accès aux fichiers du système.

L’intégrité car des limites ont été défini pour éviter la modification du système par n’importe qui.

