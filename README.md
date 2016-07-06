6 HANNA CODE (scripts PHP)  
6.2 Búsqueda  
Script de búsqueda  
Crear el script en PHP (hanna code)  
Asignar el script a una página  
llamar al script desde JS a través del factory  
Usarlo desde un controlador.  
Crear un botón para que lo use.  

1.-Crear el script en PHP (hanna code) [[busca]]  
```
<?php

	header("Content-type: application/json");

	$request = file_get_contents('php://input');
	$data = json_decode($request,true);
	$query = $data["query"];

	// $selector = wire("sanitizer")->text($selector);
	try {
		$paginas = wire('pages')->find($query)->toJSON();
	} catch (Exception $e) {
		echo ("Comprobar que los campos enviados y los de la BD son del mismo tipo INT, TEXT, DATE...");
	}
		
	echo $paginas; return;

```  


2.- Asignar el script a una página  

Bajo /web-service, crear una página con template hanna_code y en su textarea pegamos [[busca]]  

3.- llamar al script desde JS a través del factory  
En factories.js añadimos este código:

```
busca: function(query){ 
	return $http.post( webService + "busca/", {'query':query} ).then(function(response){
		return response.data;
	});
},

```


4.- Usarlo desde un controlador.  

 En el controlador que queramos usarlo creamos un function de este tipo  
```
app.controller('HomeCtrl',function($scope, $http, pw){

	$scope.busca = function(){
		pw.busca('template=recetas, title|body~='+$scope.termino).then(function(data){
      		   console.log("ENCONTRADOS",data);
      		   $scope.encontrados = data;
    	});
	} 
```  

5.- Crear un botón para que lo use.  

```
<input type="text" ng-model="termino" />
<button type="submit" ng-click="busca()" class="btn btn-danger">busca</button>
```

6.- Visualizar los datos  

```
<ul>
	<li ng-repeat="encontrado in encontrados">{{encontrado.title}}</li>
</ul>
```
