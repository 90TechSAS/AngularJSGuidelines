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

- [Restangular](https://github.com/mgonto/restangular) - Restangular est une librairie qui remplace ngResources. Il faut bâtir sur cette solution pour les données récupérées depuis nos différentes API.

- [moment.js](http://momentjs.com/) - La librairie pour bricoler avec les temps, les dates et heures. Beaucoup plus simple et complète que n'importe quelle autre librairie de gestion du temps en Javascript.

##Arborescence des fichiers

Garder les fichiers organisés est une tâche prioritaire ! Un fichier mal rangé est une grosse perte de temps. Prenez donc la peine de correctement **nommer** et **ranger** fichiers et dossiers.

### Structure des dossiers

````
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
│               │       invoices-list.js
│               │
│               ├───directives
│               │   └───invoice-sum
│               │           .DS_Store
│               │           invoice-sum.html
│               │           invoice-sum.js
│               │           invoice-sum.less
│               │
│               └───services
└───assets
    ├───fonts
    ├───images
    └───less
````
