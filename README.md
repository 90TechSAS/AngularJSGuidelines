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
 4. [Principe de responsabilité unique](#principe-de-responsabilite-unique)
 5. [Modules](#modules)
 6. [Gestion des dépendances](#gestion-des-dependances)
 7. [Controllers](#controllers)
 8. [Directives](#directives)
 9. [Services, Factories et Models](#services-factories-et-models)
 10. [Routes](#routes)
 11. [Encapsuleurs natifs](#encapsuleurs-natifs)
 12. Exceptions
 13. Constantes
 13. Commentaires
 14. JSHint
 15. Gulp et déploiement
 16. Contributions


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

####Nommage

Les noms de "controllers", à l'instar d'autres composants, devront être écrits en UpperCamelCase et finir par "Controller" à la fin, voici quelques exemples simples :

```javascript
InvoicesListController
UserCreationController
EventUpdateController
```
Nommez vos composants Angular de la même manière que vos constructeurs (voir ci-après).

####"Controller As"
L'un des premiers points extrêmement important à aborder, la syntaxe "**controllerAs**" permet d'orienter le code du "controller" à la manière d'une Classe classique via un réel Constructeur. Du coup, nous pouvons utiliser ```this``` dans les "controllers".

Elle permet également de se passer du ```$scope``` qui reste finalement un anti-pattern et de l'accès aux différents "controllers" parents dans les vues à l'aide du "." des objets Javascript. Exemple : ```{{list.count}}```.

#### Pas d'instanciation de controller dans les vues

Pour retrouver et utiliser efficacement les controllers, pensez à instancier uniquement les "controllers" dans les routes et directives uniquement (voir ci-après).

```html
<!-- Ne pas reproduire -->
<div ng-controller="InvoicesListController as list">...</div>
```

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

####Délégation de la logique métier aux services

Attaquons-nous désormais à la logique métier et données (dite business) de votre "controller". Si vous avez besoin d'utiliser des données qui sont amenées à évoluer etc, n'écrivez rien en rapport avec celle-ci dans vos "controllers" !

L'idée est de séparer vos données et méthodes liées à celles-ci dans un service de manière à ce que plusieurs "controllers" puissent réutiliser le service.

A conforter que cela permet de garder le "controller" propre et chaque composant possède du coup une [responsabilité unique](#principe-de-responsabilite-unique).


####Logique graphique

A contrario, il ne faut pas tout mettre dans les services non plus. Tout ce qui touche au "controller", c'est-à-dire que tous les attributs et méthodes que vous allez utiliser dans votre vue est indispensable et indissociable de votre "controller".

Evitez au maximum les méthodes abstraites, il se peut cependant que certaines de vos méthodes ou attributs aient besoin d'être privées, pensez alors à ce que nous avons dit sur la [visibilité des attributs et méthodes](#visibilite-des-methodes-et-attributs).

####Pas de manipulation du DOM

Voici une règle simple. Vous ne devez, sous aucun prétexte, accéder au DOM dans votre "controller" à l'aide de jqLite ou autre ! Si vous pensez devoir le faire, c'est que vous avez besoin d'une **directive**. 

**Aucune manipulation du DOM dans un "controller" est l'une des règles d'or !**

####Responsabilité

Il faut garder vos "controllers" dédiés uniquement à une vue et essayer de ne pas les réutiliser dans d'autres vues. Si vous en ressentez le besoin, décalez votre logique dans une "factory" et laissez le "controller" simple, propre et uniquement dédié à ce qu'il doit faire : gérer sa propre vue.

[Retour au sommaire](#sommaire)

##Directives

Les directives sont là pour les modifications avancées du DOM. Si vous avez besoin de faire des modifications simples etc, pensez en priorité au ```ngHide```et ```ngShow```que propose Angular ou à des animations CSS. Les modifications du DOM sont difficiles à tester, autant en faire le moins possible.

####Responsabilité unique

Comme tout autre composant, les "directives" ne doivent remplir qu'une mission et une seule. Elles sont donc censées être écrite dans un fichier séparé à chaque fois. Cependant si un controller est nécessaire, il est possible de le faire dans ce même fichier.

####Restriction des déclarations
Par simplicité, nous n'utiliseront pas l'instanciation de "directives" via la possibilité offerte par les classes. En d'autres termes, utilisez uniquement ```restrict: 'A'```, ```restrict: 'E'``` ou```restrict: 'AE'``` au maximum.

Du coup, nous les utilisons uniquement dans les vues de cette manière :
```html
<my-directive></my-directive>

<div my-directive></div>
```

####Template

Il faut privilégier les templates externes, exportez vos fichiers html en dehors des fichiers directives.

Si jamais vous jugez que la directive est vraiment petite, utilisez la forme qui suit :

```javascript
function myDirective() {

	return {
		template: [
			'<div>',
				'<p>...</p>',
			'</div>'
		].join();
	};

}
```

####Nommage & Ecriture

N'utilisez jamais le préfixe ```ng-*```,  préférez ```zl-*```. A savoir également que le nom doit être écrit simplement en ```camelCase```. Voici un exemple complet :

```javascript
angular
	.module('app')
	.directive('zlDragUpload', DragUploadDirective);

function DragUploadController() {
	...
}

function DragUploadDirective() {

	return {
		restrict: 'E',
		templateUrl: 'common/directives/drag-upload.directive.js',
		controller: DragUploadController,
		controllerAs: 'drag',
		link: link
	};
	
	function link(scope, element, attributes, controller) {
		// Faire les manipulations du DOM ici uniquement !
	}
	
}
```

[Retour au sommaire](#sommaire)

##Services, Factories et Models

La distinction entre "factory", "service" et "provider" est très petite. Sans rentrer dans le détails, finalement chacune de ces formes est un Service (cf. documentation Angular : service > new factory > new provider). Nous allons nous permettre de laisser tomber le "provider" pour se concentrer uniquement sur les deux premières formes.

Les services en général contiennent l'ensemble de la logique métier et données.

####Utilisation des services

Les services seront principalement dédiés et amenés à discuter avec les "controllers", dans un esprit de responsabilité unique encore une fois.

Les services se trouvent dans chacun des sous-dossiers de routes quand ils concernent les "controllers" courants. Pour des "services" plus globaux et réutilisables sur plusieurs routes, pensez alors au dossier ```common/services/```.

Gardez à l'esprit l'histoire des composants et des modules, n'utilisez pas un service qui n'est pas dans son "range".

####Utilisation des "factories"

Les "factories" peuvent être "instanciées" (attention, pour ce que nous venons de dire, nous pouvons nous faire lyncher). En soit une factory est également un "singleton" mais elle peut retourner un constructeur (ou plusieurs d'ailleurs) qui lui sera instanciable !

C'est pourquoi, nous utilisons les "factories" comme des "models". Nous essayons de nous rapprocher du système de "beans" sur Java. Ces "beans" sont créés et contiennent les données auparavant, nous ne traitons donc jamais les données en brut comme elles arrivent dans un "controller" ou dans une vue, mais bien au niveau des "services" et "models".

####Nommage et écriture

#####Service

Dans un premier temps, regardons ensemble, à travers un exemple, à quoi pourrait ressembler un service pour gérer les factures sur notre page qui les liste simplement.

```javascript

// routes/invoices/list/services/invoices-list.service.js
angular
	.module('app.invoices.list')
	.service('InvoicesListService', InvoicesListService);

function InvoicesListService() {

	var self = this;

	function getLastMonthInvoices() {
		...
	}

	_.extends(self, {
		// Attributs publiques
		...
		// Méthodes publiques
		getLastMonthInvoices: getLastMonthInvoices
	});
}
```
Plusieurs points découlent de cet exemple. Premièrement, le nom est également en UpperCamelCase et se finit par "Service". La plupart du temps, il aura le même nom que le "controller" auquel il est relié (sauf cas où plusieurs "controllers" sont dans le même module et ont besoin des mêmes méthodes métiers).

Il utilise également le même principe que le controller pour la visibilité de ses attributs et méthodes à travers ```_.extends``` que propose **lodash**.

#####Model

Voyons ensuite comment se comporte un "model" :

```javascript

// models/invoice.js
angular
	.module('app.models.invoice')
	.factory('InvoiceService', InvoiceService);

function InvoiceService() {

	function Invoice(options) {

		this.client = null;
		this.amount = 0;
		this.products = [];
		
		_.extends(this, options);
	}

	Invoice.prototype = {

		validate: function() {
			...
		}

	};

	return Invoice;
}
```

Le "model" n'est pas très compliqué en soit. C'est une "factory" qui renvoie un constructeur correspondant à un jeu de données. Via ce système, l'objet en question peut contenir des méthodes prototypes qui pourront aider à valider les données par exemple.

A noter également que le constructeur prend un objet en paramètre qui correspondrait à une liste de propriétés qui lui serait simplement passée en paramètre. Cela évite dans certains cas, d'avoir des listes de ```null``` lors de l'instanciation de certains objets jusqu'à arriver au paramètre que l'on connait. Le but étant d'éviter ceci : ```new Invoice(123, null, null, null, 1023.42, 'DUTRONC', null, null, true);```.

####Contraites et utilités

Après avoir déjà expliqué comment fonctionnent les "factories" ("models") et les "services", nous allons juste simplifier ce qui a été dit :

 - Les "services" s'utilisent dans les "controllers" à travers de simple méthodes.
 - Les "models" s'utilisent dans les "services" pour manipuler les données.
 - Les "services" s'occupent de gérer l'enregistrement, la récupération et le stockage des données, il y a donc une abstraction de la forme des données pour chaque "model" quelque soit le type de récupération (socket, cache, storage, API json, API xml, etc.).

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
                    
                    // On chain les routes qui peuvent en découler
                    .state(...)
                    
                    .state(...);
            }
        ]);


	// #############
	// # Resolvers #
	// #############

	InvoicesPrepareService.$inject = ['InvoicesService'];

    function InvoicesPrepareService(InvoicesService) {
	    // On retourne une promise
        return InvoicesService.getAllFromLastMonth();
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

// Le résultat du resolver est en paramètre du constructeur
function InvoicesListController(invoices) { 
	
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


##Encapsuleurs natifs

Angular propose une série d'helpers, il faut absolument les privilégier :

 - **$timeout** au lieu de setTimeout
 - **$interval** au lieu de setInterval
 - **$window** au lieu de window
 - **$document** au lieu de document
 - **$http** au lieu de $.ajax
 - **$q (promises)** au lieu des callbacks

[Retour au sommaire](#sommaire)

##Exceptions

...

[Retour au sommaire](#sommaire)

##Constantes

...

[Retour au sommaire](#sommaire)

##Commentaires

...

[Retour au sommaire](#sommaire)

##JSHint

...

[Retour au sommaire](#sommaire)

##Gulp et déploiement

...

[Retour au sommaire](#sommaire)

##Contributions

...

[Retour au sommaire](#sommaire)
