### GERER LES ROLES ADMIN-CLIENT  
---
1-Dans la migration de la table user, ajouter :

    $table->string('role')->default('Client');  

2-Dans le modèle vente.php  

    public function user()
    {
        return $this->belongsTo('App\User');
    }

3-Dans le modèle user.php  

    public function ventes()
    {
        return $this->hasMany('App\Vente');
    }

4-créer un middleware isadmin 

    $ php artisan make:middleware IsAdmin  

5-Modifier dans le middleware IsAdmin 

    use Auth;  

    et 

    public function handle($request, Closure $next)
    {
        if (\Auth::user() &&  \Auth::user()->role === 'admin') {
            return $next($request);
        }

        return redirect('/');
    }


6-Ajouter dans le fichier kernel.php

    protected $routeMiddleware =  
        'admin' => \App\Http\Middleware\IsAdmin::class,  

7-Modifier la route  

    Route::group(['middleware' => ['admin'], function () {

ou ajouter à chaque fin de route  

    ->middleware('admin');

8-restreindre l'accès au menu dans la navbar 

    @if (\Auth::user()->role === 'admin') //menu accès refusé// @endforeach 

    
