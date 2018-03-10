## AJOUTER LE NOM DES UTILISATEURS DANS LE TABLEAU DES VENTES
---
1-CREER UNE RELATION ENTRE LA TABLE VENTE ET TABLE USER

- Dans le model Vente.php :

        protected $fillable = ['nbSucre', 'drink_id', 'user_id'];

     et  

        public function user()
        {
            return $this->belongsTo('App\User');
        }  

- Dans le model User.php :  

        public function ventes()  
        {  
            return $this->hasMany('App\Vente');  
        }  


- Dans le fichier controller de la function store (ici commandeController), modifier la fonction :

        public function store(Request $request)
        {   
            $vente = new Vente();
            $vente-> nbSucre = request('inputNbSucre');
            $vente-> drink_id = request('inputBoisson');
            $vente -> user()->associate(Auth::user());
            $vente->save();
            return redirect()->back();
        }

    
- Dans le fichier .blade (ventes.blade)  
      
        </td>
            @if($vente->user)  
                {{$vente->user->name}}  
            @else  
                Guest  
            @endif
        </td>  
---

## Afficher les ventes de l'utilisateur connecté et permettre l'accès de toutes les ventes à l'admin
----

Dans le fichier ventecontroller  

    function index()  
        {if (\Auth::user()->role === 'admin') {  
            $ventes = Vente::with('drink')->orderBy('created_at', 'asc')->get();  
        } else {  
            $ventes = Vente::where('user_id', Auth::user()->id)->get();  
        }  
        return view('back_office.ventes', ['ventes' => $ventes]);  
        }  
    }  

utiliser le foreach dans la vue ventes.blade.php

    @foreach($ventes as $vente)  etc... 