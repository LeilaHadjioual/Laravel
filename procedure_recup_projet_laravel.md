    ##RECUPERER LE PROJET LARAVEL

1. créer un repository sous git 
 
2. ouvrir bash dans dossier C:/www  
        
        git clone fichier

3. copier le projet machineàcafé et coller dans le dossier cloné  

        git status  
        
        git add *  
        
        git commit -m "first commit"
        
        git push
        
*le fichier .env n'est jamais rappatrié, il faut le créer* (cf. installer composer)

4. se connecter au serveur   

        ssh root@167.130.....  
        

5. se positionner dans le dossier serveur (dossier Leila)  

        git clone du projet laravel     
    

 
    ##INSTALLER COMPOSER  

SE POSITIONNER DANS LE DOSSIER PROJET LARAVEL (ex : dossier Leila)  

        mkdir .composer  
        curl -sS https://getcomposer.org/installer | php -d allow_url_fopen=On  
        composer install
    

CREER UN FICHIER .env (copier le fichier .env.example et le renommer en .env)

    cp .env.example .env   
    php artisan key:generate  


MODIFIER LE CHEMIN DANS LE FICHIER .conf   

     DocumentRoot /var/www/html/Leila/machine_cafe_laravel/public
            DirectoryIndex index.php
            

REDEMARRER

	service apache2 restart  
	

    ##MODIFIER LES DROITS USER (dans le dossier laravel)  

verifier les droits du fichier storage    
    
    chown -R user/groupe dir nom fichier    

    ex : chown -R www-data:www-data *
    
-R = récursive :le dossier et tous les fichiers enfants   


    ##FAIRE UNE MIGRATION DE LA BDD  

    créer une base de données sur phpmyadmin (coté serveur)
    modifier le fichier .env : username et mdp de la BDD
    php artisan migrate:fresh    

ou importer la BDD via phpmyadmin en local sur phpmyadmin serveur  


    ##ENLEVER LA DEBUGBAR   

    dans le fichier .env  
    nano .env  
    modifier : debugbar = false   
    
    
------

**SI problème de version PHP**

en local, vérifier la version PHP (bash)

    php -v

si mauvaise version modifier le path : 

    path
    modifier
    parcourir
    selectionner la bonne version
      
dans serveur :  

    composer update
    composer install
    
    

    