#AngularJS Guidelines - ZenLabs

These guidelines are written primarily for the Zenlabs team but the developers wanted to share with you how they work.

Don't take anything that's written here to face value, take a global view and be smart vis-à-vis your development issues.

We don't provide a unique solution, just enough to put some order in your project.

We're a french team, so in a first phase, we write these guidelines in French (but we'll translate these later). Sorry about that :)

##Credits and Thanks

We read a lot of documents and style guidelines from the community but we really want to thank to [@john_papa](https://twitter.com/john_papa) for his awesome work in which we referred to. In same time, we want share with you some other guys who make a great great work for helping people to be better in angular coding approach :

 - [@toddmotto](https://twitter.com/toddmotto)
 - [@GoCardLess](https://twitter.com/GoCardless)
 - [@mgechev](https://twitter.com/mgechev)

It's done, from now we'll write in French, again sorry :)

##Sommaire

 1. [Objectifs du guide](#objectifs-du-guide)
 2. [Dépendances développeurs tiers](#dependances-developpeurs-tiers)
 3. [Arborescence des fichiers](#arborescence-des-fichiers)
 4. [Modules](#modules)
 5. [Gestion des dépendances](#gestion-des-dependances)
 6. [Controllers](#controllers)
 7. [Routes](#routes)


##Objectifs du guide

En mettant en place ce guide, l'objectif global est de permettre une certaine unité dans le développement des applications front-end. Pour cela, certains sous-objectifs sont à considérer :

1. Augmenter la lisibilité de votre code.
2. Définir explicitement fonctions, classes et variables.
3. Penser davantage modularité et développement orienté composant.
4. Documenter proprement pour qu'une seule lecture suffise.
5. Informer les autres développeurs de vos changements.
6. Penser à s'informer sur l'existence de composants déjà présents et reconnus.
7. Savoir se remettre en question vis-à-vis de ce guide.

##Dépendances développeurs tiers

- [Lodash](https://lodash.com/) - Cette petite librairie est d'une simplicité et permet d'user de magie un peu partout où cela est nécessaire. La concaténation d'objets JS est très simplifié grâce à cette librairie, elle est indispensable. Quand vous avez un travail à faire sur une collection ou un objet, regardez directement si une fonction n'existe pas pour vous sauver la vie.

- [AngularUI Router](https://github.com/angular-ui/ui-router) - Angular propose de base un router via son module ngRoute mais il n'est pas aussi permissif que AngularUI Router notamment à travers son système de vues imbriquées. Au niveau de la configuration, `$stateProvider` est bien plus pratique pour une application simple-page.

- [moment.js](http://momentjs.com/) - La librairie pour bricoler avec les temps, les dates et heures. Beaucoup plus simple et complète que n'importe quelle autre librairie de gestion du temps en Javascript.

##Arborescence des fichiers

Garder les fichiers organisés est une tâche prioritaire ! Un fichier mal rangé est une grosse perte de temps. Prenez donc la peine de correctement **nommer** et **ranger** fichiers et dossiers.

### Structure des dossiers

```
.
│   
│   index.html
│
├───app
│   │   app.js
│   │
│   ├───common
│   │   ├───controllers
│   │   ├───directives
│   │   ├───filters
│   │   └───services
│   ├───config
│   │   main.config.js
│   │
│   ├───layouts
│   │   │   default.html
│   │   │
│   │   └───partials
│   │           footer.html
│   │           header.html
│   │
│   ├───models
│   │       invoice.js
│   │
│   └───routes
│       └───invoices
│           └───list
│               │   invoices-list.route.js
│               │
│               ├───controllers
│               │       invoices-list.html
│               │       invoices-list.controller.js
│               │
│               ├───directives
│               │   └───invoice-sum
│               │           invoice-sum.html
│               │           invoice-sum.js
│               │           invoice-sum.less
│               │
│               └───services
└───assets
    ├───fonts
    ├───images
    └───less
```

[Retour au sommaire](#sommaire)

##Principe de responsabilité unique

C'est une pratique courante dans le monde du développement informatique. En appliquant ce principe, vous éviterez ainsi des défauts de conceptions et augmenterez significativement la lisibilité de votre code (cf. **Objectifs initiaux**).

Ce principe reste simple dans son idée de départ : une classe doit gérer une responsabilité (soit un ensemble de fonctionnalités qui vont dans le même sens). A appliquer dans notre cas, un composant (factory, controller, directive) est défini par fichier et **seulement un**. Voici un contre-exemple :

```javascript
/* Ne pas reproduire ! */
angular
    .module('app', ['ngAnimate'])
    .controller('InvoicesController' , InvoicesController)
    .service('InvoiceModel' , InvoiceModel);

function InvoicesController() {
	...
}

function InvoiceModel() {
	...
}
```

Pensez plutôt à découper en plusieurs fichiers qui, chacun, aura son rôle, sa responsabilité :

```javascript
/* A appliquer ! */

// app.module.js
angular
    .module('app', ['ngAnimate']);
```

```javascript
/* A appliquer ! */

// routes/invoices/list/controllers/invoices.controller.js
angular
    .module('app')
    .controller('InvoicesListController' , InvoicesListController);

function InvoicesListController() {
	...
}
```

```javascript
/* A appliquer ! */
angular
    .module('app')
    .service('InvoiceModel' , InvoiceModel);

// models/invoice.js
function InvoiceModel() {
	...
}
```

[Retour au sommaire](#sommaire)

##Modules

En tout premier lieu, ne surtout pas déclarer les modules dans des variables. JAMAIS. Préférez utiliser la notation suivante :

```javascript
angular
	.module('app')
	.controller(...);

angular
	.module('app')
	.service(...);
```

Ceci est dans la continuité de ce que nous avons vu juste avant. Le fait de séparer chaque responsabilité en un fichier permet de "modulariser" vos composants.

####IIFE

Derrière cet acrynonyme barbare se cache en réalité une fonctionnalité bien pratique. Ce système sert en finalité pour la portée de variables. Toute variable déclarée dans une fonction anonyme auto-appelante ne reste disponible que cette fonction. C'est un peu un namespace sans nom finalement (**attention nous raccourcissons volontairement l'explication**). Voici donc un IIFE pour que ça soit plus parlant :

```javascript
(function() {
...
})();
```

L'intérêt étant d'isoler les variables du scope global et donc de ne plus être embêté avec des histoires de variables en doublon notamment lors de la minification et de l'export vers un environnement de production. Exemple simple :

```javascript
// routes/invoices/list/controllers/invoices.controller.js
(function() {

	// Active le mode strict dans la fonction courante
	'use strict';

	angular
		.module('app')
		.controller(InvoicesListController);	

	function InvoicesListController() {
		...
	}
	
})();
```

####Nommage

Il faut d'abord éviter les problèmes liés aux collisions de nom. Nous avons choisi d'utiliser le "." pour séparer nos différents modules, ainsi voilà comment définir vos modules :

```javascript
angular
	.module('app', [
		'ngAnimate',
		'ngRoute',

		'app.invoices',
		'app.common'
	]);
```
L'idée étant d'imbriquer les modules comme autant de dépendances, ainsi en cas de manquement d'un fichier, vous serez immédiatement averti.

Comme vu précédemment, une fois vos modules définis, vous devez y accéder pour trier chacun de vos composants dans les modules correspondants.

[Retour au sommaire](#sommaire)

##Gestion des dépendances

Dans un but de minifier les fichiers par la suite, nous avons besoin de gérer correctement toutes les dépendances et imports. Il y a un sens d'écriture des différents modules à respecter : en premier lieu, les dépendances native d'Angular, ensuite les dépendances tierces et finir avec nos composants.

Pour chacun des composants, l'import se fait de la manière suivante :

```javascript
InvoicesListController.$inject = ['$q', '$timeout', 'Socket', 'InvoicesService'];

// $q et $timeout 	-> Angular
// Socket 			-> lib Socket.io
// InvoicesService 	-> Service qu'on a précédemment créé
function InvoicesListController($q, $timeout, Socket, InvoicesService) {
	...
}
```

[Retour au sommaire](#sommaire)

##Controllers

####"Controller As"
L'un des premiers points extrêmement important à aborder, la syntaxe "**controllerAs**" permet d'orienter le code du "controller" à la manière d'une Classe classique via un réel Constructeur. Du coup, nous pouvons utiliser ```this``` dans les "controllers".

Elle permet également de se passer du ```$scope``` qui reste finalement un anti-pattern et de l'accès aux différents "controllers" parents dans les vues à l'aide du "." des objets Javascript. Exemple : ```{{list.count}}```.

####Système View-model
L'accès au ```this``` dans le "controller" permet d'orienter le développement objet. Cependant la limitation Javascript avec la variable ```this``` nous bride vis-à-vis de l'utilisation dans des contextes particulier (méthode ou callback). Pour contrer cela, il suffit simplement d'instancier l'objet courant dans une variable et d'éviter ```.bind()``` d'Angular :
```javascript
var self = this;
```

####Visibilité des méthodes et attributs
A travers le système mis en place, nous avons orienté les "controllers" vers une démarche Orienté Objet à travers leur constructeur. Du coup, nous allons pouvoir utiliser le principe de visibilité des variables, attributs, méthodes et fonctions. Voyons rapidement comment ça fonctionne dans les controllers :

```javascript
function InvoicesListController(InvoicesService) {

	var self = this;
	
	var invoices = [
		{...},
		{...},
		{...},
		...
	];

	function sendInvoiceTotal(invoice) {
		InvoicesService.create(invoice)
			.then(success, error);
	}

	function success() {
		console.log('success');
	}
	
	function error() {
		console.log('error');
	}

	// On étend alors notre objet pour la visibilité 
	_.extends(self, {
		// Attributs publiques
		invoices: invoices
		
		// Méthodes publiques
		sendInvoiceTotal: sendInvoiceTotal
	});

}
```

####Délégation de la logique business aux services

A écrire...

####Pas de manipulation du DOM

A écrire...

[Retour au sommaire](#sommaire)

##Routes

Une route correspond à une page, donc théoriquement à un affichage différent. Il y a d'autres composants, directives qui seront alors à l'écran. Il faut alors, par route, créer un fichier de routing qui se chargera de configurer correctement le ```$stateProvider``` d'AngularUI Router. Voici un exemple :

```javascript

// routes/invoices/list/invoices-list.route.js
(function () {

    'use strict';

    angular
	    .module('app.invoices.list')
        .config([
            '$stateProvider',
            
            function($stateProvider) {
                $stateProvider
                    .state('invoices.list', {
                        url: '/invoices/list',
                        resolve: {
                            invoices: InvoicesPrepareService
                        },
                        views: {
                            'main': {
                                templateUrl: ...
                                controller: 'InvoicesListController',
                                controllerAs: 'list'
                            },
                            'sidebar': {
                                templateUrl: ...
                                controller: 'InvoicesSidebarController',
                                controllerAs: 'sidebar'
                            }
                        }
                    })
                    
                    .state(...) // On chain tranquillement les routes qui peuvent éventuellement en découler
                    
                    .state(...);
            }
        ]);


	// #############
	// # Resolvers #
	// #############

	InvoicesPrepareService.$inject = ['InvoicesService'];

    function InvoicesPrepareService(InvoicesService) {
        return InvoicesService.getAllFromLastMonth(); // On retour une promise
    }
})();
```
Nous allons redéfinir et expliquer plusieurs points.

####State & Routing
Le "State" se découpe à l'image des modules (utilisation des "." comme séparateur) puisqu'ils peuvent être imbriqués et fonctionner de manière abstraite. La documentation d'AngularUI Router est assez bien faite quant à l'utilisation des states et des routes ([voir plus de détails](https://github.com/angular-ui/ui-router/wiki/URL-Routing)).

####Vues imbriquées
La clé ```views``` contient finalement la liste des informations des différentes vues qui seront affichées sur la page en question.

**NB** : Il faut que l'ensemble des vues définies sur la page soit présent sur votre configuration de routes.

####"Controller As"
Comme décrit auparavant, on utilise ici la nomenclature ```controllerAs```. Encore une fois, choisissez intelligemment le nom de vos "controllers".

####Resolver & Controller
Le "resolver" est exécuté avant l'instanciation du "controller". Il peut y avoir plusieurs "resolvers" d'affilée et pour la plupart sont des promises afin de gérer le côté asynchrone des chargements du pré-affichage. Les résultats des dits "resolvers" sont ensuite passés comme paramètres de votre "controller".

```javascript
angular
	.module('app.invoices.list')
	.controller('InvoicesListController', InvoicesListController);

InvoicesListController.$inject = ['invoices'];

function InvoicesListController(invoices) { // Le résultat du resolver est en paramètre du constructeur
	
	var self = this;

	_.extend(self, {
		// Attributs publiques 
		invoices: invoices
		
		// Méthodes publiques
		...
	});
	
}
```

[Retour au sommaire](#sommaire)
