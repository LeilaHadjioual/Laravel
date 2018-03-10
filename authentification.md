## AUTHENTIFICATION LARAVEL   
----
ressource : **https://laravel.com/docs/master/authentication**

I/GENERALITES  

Fichiers localisés dans :  
App \ Http \ Controllers \ Auth   
Le RegisterController gère l'enregistrement des nouveaux utilisateurs   
Le LoginController gère l'authentification  
Le ForgotPasswordController gère les liens d'e-mailing pour la réinitialisation des mots de passe   
Le ResetPasswordController contient la logique pour réinitialiser les mots de passe.  

*Pour de nombreuses applications, pas besoin de modifier ces contrôleurs.*  
  

II/CREATION DES FICHIERS AUTHENTIFICATION  

- ROUTING  

créer toutes les routes et vues dont on a besoin via la commande :

        php artisan make: auth  

installe une vue de mise en page, des vues d'enregistrement et de connexion, des routes pour tous les points d'extrémité d'authentification, génère un HomeController pour gérer les demandes post-connexion au tableau de bord de l'application.  


*PS : pour afficher toutes les routes dans la commande :* php artisan route:list    

modifier la route **si besoin** dans le fichier route : web.php

- VIEWS  

La commande précédente a créé toute les vues et les a placées dans : **répertoire resources / views / auth**  

Elle a aussi créé un **répertoire resources / views / layouts** contenant une mise en page de base pour l'application. 


- AUTHENTIFICATION

**EXEMPLE POUR PROJET MACHINE A CAFE**

-> récupérer le code html de la navbar:  

        OUVRIR LE TEMPLATE DE LA NAVBAR ET COLLER LE CODE TROUVE DANS LE FICHIER app.blade qui récupère le code de la navbar du registre (navbar right)

1. modifier la route web.php

        Route::get('/back_office', 'HomeController@index')->name('home');  

2. modifier le retour vue du fichier homeController  

        public function index()
        {
            return view('back_office.index');
        }  

3. modifier loginController  

        protected $redirectTo = '/';  

4. modifier registreController  

       protected $redirectTo = '/';

5. modifier les routes pour y avoir accès seulement si l'on est authentifié.  
ajouter à chaque route :

        ->middleware('auth');  

        ex : Route::post('boissons/create','DrinkController@store')->middleware('auth');

ou ajouter :

        Route::group(['middleware'=> ['auth']], function (){
            //inclure toutes les routes sécurisées
        });

**POUR AFFICHER LE NOM DE LA MACHINE DANS NAVBAR DU REGISTRE**  

Dans le fichier app.blade 

modifier :  

    <title>{{ config('app.name', 'Laravel') }}</title> en <title>Machine à Café</title> 

    <!-- Branding Image -->
                    <a class="navbar-brand" href="{{ url('/') }}">Ilot 8
                        {{-- {{ config('app.name', 'Laravel') }} --}}
                    </a>   
        en :   
                   <a class="navbar-brand" href="{{ url('/') }}">
                        {{ 'Machine à Café' }}
                    </a>    



