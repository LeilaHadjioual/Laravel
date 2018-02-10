#Les relations  

##**Relation 1:n   --->  hasMany():**

Une boisson a plusieurs ventes,
  
**Dans le model Drink, ajouter :** 

    public function ventes(){
        	return $this->hasMany('App\Vente');
        }
        
        
**Dans le model Vente, ajouter :**

    public function drink(){
        	return $this->belongsTo('App\Drink');
        }
        

La méthode drink (au singulier) permet de trouver la boisson qui appartient (belongsTo) à la vente.
C'est donc la réciproque de la méthode précédente.  


**dans le fichier de migration vente:**

    public function up()
        {
            Schema::create('ventes', function (Blueprint $table) {
                $table->increments('id');
                $table->integer('nbSucre'); 
                $table->integer('drink_id')->unsigned();
                $table->foreign('drink_id')->references('id')->on('drinks');            
                $table->timestamps();
            });

! ne rien ajouter dans la migration drink car relation crééé  

**Dans le fichier controller vente :**  


    ajouter dans l'entête:
            use App\Vente;
            use App\Drink; 
            
            //permet d'utiliser les models 
        
et modifier le code

    //ajouter une commande
    public function store(Request $request){
            $ventes = new Vente();
            //récupère dans le model Drink un tableau avec la liste des boissons. [0]:position 0 du tableau
            $boisson = Drink::whereNom(request('inputBoisson'))->get()[0];
            $ventes->drink_id = $boisson->id;
            $ventes->nbSucre = request('inputNbSucre');
            $ventes->save();
    
            return redirect('commandes');
        }
    
**Dans le fichier vente.blade :**

    @foreach($ventes as $vente)
            <tr>
                <td>{{$vente->id}} </td>
                <td>{{$vente->drink->nom}} </td>  //utilise la table drink
                <td>{{$vente->drink->prix}} </td>
                <td>{{$vente->nbSucre}}</td>
                <td>{{$vente->drink_id}}</td>
                <td>{{$vente->created_at}}</td>
            </tr>
    @endforeach

**Dans le fichier route** 

    Route::post('commandes/create','CommandeController@store');

----
##**Relation n:n  ---> belongsToMany()**  

une boisson peut avoir plusieurs ingrédients et un ingrédient peut appartenir à plusieurs boissons  

**Dans le model Drink :**  

    public function ingredients()
        {
        	return $this->belongsToMany('App\Ingredient')->withPivot('nbDose','id');
        }  
        
        
**Dans le model Ingredient :** 

    public function boissons()
        {
        	return $this->belongsToMany('App\Drink')->withPivot('nbDose','id');
        }  
        

###AFFICHER TOUTES LES RECETTES DE TOUTES LES BOISSONS

**Dans le fichier recettecontroller**  

    public function showRecipes() 
    {
        //Récupèrer les ingrédients d'une boisson
        $drinks= Drink::all()->load('ingredients');

        //Retourner la vue avec les données 
        return view('back_office/recettes', ["drinks"=> $drinks]);
    }

**Dans le fichier recette.blade**  

    @foreach ($drinks as $drink)
        @foreach($drink->ingredients as $ingredient)
    <tr>
        <td>{{$drink->nom}}</td>
        <td>{{$ingredient->nomIngredient}}</td>
        <td>{{$ingredient->pivot->nbDose}}</td>
    </tr> 
        @endforeach 
    @endforeach 


**Dans le fichier route**

    Route::get('/recettes','recipesController@showRecipes');    

###AFFICHER LE TABLEAU DES INGREDIENTS DANS CHAQUE BOISSON (SUR LA MEME PAGE)  

**Dans le fichier resultBoisson.blade**  

    @foreach ($drink->ingredients as $ingredient)
		<tr>

			<td>{{$ingredient->pivot->id}}</td> 
			<td>{{$drink->nom}}</td> 
			<td>{{$ingredient->pivot->ingredient_id}}</td>
			<td>{{$ingredient->nomIngredient}}</td>
			<td>{{$ingredient->pivot->nbDose}}</td> 
			<td>
				<form id= "delete{{$ingredient->pivot->id}}" method="post" action="/ingredient/{{$drink->id}}/{{$ingredient->id}}">
					{{ csrf_field() }}
					{{method_field('DELETE')}}
					<div class="btn-group">
						<button type="submit" form="delete{{$ingredient->pivot->id}}" class="btn btn-outline-danger"> X </button>
					</div>
				</form>
			</td>
		</tr>
		@endforeach  


**Dans le fichier controller**   

*(attention : cette methode effectue plusieurs actions)*  

    function lien(Drink $id)
        {	/*$boisson =Drink::find($id);*/
            $drink = $id->load('ingredients'); // load relation avec ingredients
            $allIngredients = Ingredient::all(); // récupère toutes les données ingredients
            $allIngredients= $allIngredients->diff($drink->ingredients); //différence entre les données présentes et absentes (affiche les ingrédients qui ne sont pas déjà présents dans la recette)
            $data = [
                'drink' => $drink,
                'allIngredients' => $allIngredients,
            ];

            return view('back_office/resultBoisson', $data);
        }


###CREER UNE LISTE DEROULANTE AVEC TOUS LES INGREDIENTS DISPO HORMIS CEUX DEJA UTILISES   

**Dans le controller drink**  

    function lien(Drink $id)
	{	$drink = $id->load('ingredients'); // load relation avec ingredients
		$allIngredients = Ingredient::all(); // récupère toutes les données ingredients
		$allIngredients= $allIngredients->diff($drink->ingredients); //différence entre les données présentes et absentes (affiche les ingrédients qui ne sont pas déjà présents dans la recette)
		$data = [
			'drink' => $drink,
			'allIngredients' => $allIngredients,
		];

		return view('back_office/resultBoisson', $data);
	}


**Dans le fichier resultBoisson**

    <form class="form-inline col-sm-12 push-3" method ="post" action="/recipe/create/{{$drink->id}}">
			{{ csrf_field() }}

		<select class="form-control col-sm-3" name="ingredient">
			@foreach($allIngredients as $ingredient)
			<option value="{{$ingredient->id}}">{{$ingredient->nomIngredient}}</option>
			@endforeach
		</select>
		</br>
		<input type="text" name="nbDose" class="form-control" placeholder="quantité" required ="required">
				
		<button type="submit" class="btn btn-outline-secondary" name="valider">valider</button>
				
	</form>

**Dans le fichier route**  

    Route::get('/boissons/{id}', 'DrinkController@lien');  



###AJOUTER UNE RECETTE DEPUIS LA BOISSON SELECTIONNEE  

**Dans le fichier controller**  

    public function addRecipe(Request $request, Drink $drink)
	{	// $ingredient récupère l'id de l'ingredient via la requete
		$ingredient = Ingredient::find($request->input('ingredient'));
		$nbDose = $request ->input('nbDose');
		
		$drink->ingredients()->attach($ingredient, ['nbDose'=> $nbDose]);

		return redirect()->back();
	}   


**Dans le fichier .blade** 

    <form class="form-inline col-sm-12 push-3" method ="post" action="/recipe/create/{{$drink->id}}">
			{{ csrf_field() }}

		<select class="form-control col-sm-3" name="ingredient">
			@foreach($allIngredients as $ingredient)
			<option value="{{$ingredient->id}}">{{$ingredient->nomIngredient}}</option>
			@endforeach
		</select>
		</br>
		<input type="text" name="nbDose" class="form-control" placeholder="quantité" required ="required">
				
		<button type="submit" class="btn btn-outline-secondary" name="valider">valider</button>
				
	</form>


**Dans le fichier route**  

    Route::post('recipe/create/{drink}','DrinkController@addRecipe');

    



    