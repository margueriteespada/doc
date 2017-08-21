# Cas concrets d'utilisation dans vMap

Ce document décrit des exemples d'utilisation du studio dans vMap, il est très utile pour comprendre le fonctionnement du studio ainsi que de vMap

## Affichage d'onglets dans un formulaire

Dans cet exemple, nous avons mis en place deux onglets dans un formulaire: l’onglet *Attributs* dans lequel nous avons mis les attributs de type textuels dans la base de données et l'onglet *Documents* qui contient les attributs de type document.

![image][1]

Ceci est facile à faire grâce au studio avec l'outil *Gestion des onglets* utilisable en cliquant sur **Édition > Gestion des onglets**.

Une fois l'outil affiché, il est possible le cocher ou décocher les attributs à afficher sur les différents onglets tout en ayant un aperçu dans la zone de prévisualisation.

![image][3]

Le bouton *Ajouter un onglet* permet l'insertion de nouveaux onglet et on peut également effectuer des opérations comme renommer, supprimer ou déplacer des onglets en cliquant sur le nom de l'onglet.

**Remarque: un attribut peut se situer sur plusieurs onglets à la fois, ceci est utile pour afficher un label par exemple**


## Lien personnalisé vers un service externe

Il est souvent utile lors de l'utilisation d'un objet métier de mettre en place des liens vers d'autres plateformes.

Dans vMap ceci est possible à deux endroits:

### Dans l'info-bulle

![image][4]

Au quel cas il faut modifier l'attribut **SQL Summary** dans la définition de l'objet métier et utiliser des balises *"bo_link"*.

Exemple:
```sql
select nom as "Nom", '[bo_link href="https://www.google.fr/?gws_rd=cr&ei=h3hvWbHuJIORaPe3ofAG#q='||nom||'" target="_blank"]Lien vers une autre application[/bo_link]' as "Link", route_id as "Route id", auteur as "Auteur", image as "[bo_image]"  from sig.lampe
```

**Il est possible de concaténer une des valeurs de l'enregistrement avec le lien: ** dans l'exemple ci-dessus la valeur *"nom"* est concaténée à la fin de l'URL pour effectuer une recherche Google du nom de l'enregistrement sélectionné.

On peut également définir l'attribut *target* qui permettra de choisir un nouvel onglet à chaque fois si on donne pour valeur *"_blank"* où alors de stipuler un nom pour utiliser toujours le même onglet.


### Dans le formulaire

![image][5]

On peut effectuer la même opération dans le formulaire en utilisant un attribut de type *"Lien"* et en utilisant les fonction *"concat"* et *"getFormValue"* dans le champ *"Valeur"*.

```
concat('https://www.google.fr/?gws_rd=cr&ei=h3hvWbHuJIORaPe3ofAG#q=', getFormValue('nom'))
```

En utilisant les champs *"Texte"* et *"Cible"* on peut également modifier le texte à afficher ainsi que l'onglet cible.


## Liste déroulante avec source de données

Il est possible dans un formulaire d'afficher une liste déroulante avec un contenu récupéré en base de données.

![image][6]

Pour cela il faut s'intéresser aux **Sources de données**, un bouton en bas à droite du studio permettra d'afficher le gestionnaire à partir du quel les administrateurs pourront créer, modifier, supprimer des sources de données.

Il y a plusieurs types de sources de données qui permettront à l'administrateur d'aller chercher des donnés: texte, valeurs d'une table locale, service web, objet métier, base de données externe. Dans notre exemple il s'agit d'afficher l'ensemble des routes contenues dans la table *"route"* et dont l'auteur est *"laurent"*.

On peut utiliser le bouton *"+"* pour ajouter des nouveaux filtres et le bouton *"Test"* pour tester la source de données.

![image][7]

Une fois la source de données renseignée, on pourra créer un attribut de type "*Liste déroulante*" (ou autre type de liste d'ailleurs) et choisir la source de données que l'on a mis en place précédemment.

Une liste est définie par une "*Clé*" qui est la valeur retournée lorsqu'on sélectionne un élément de la liste et d'un "*Libellé*" qui est ce que l'utilisateur voit dans la liste. 

Dans cet exemple on veut sélectionner une route à associer à la lampe en édition, chaque route est définie par un identifiant numérique (route_id) et elle possède un nom textuel (nom), on sélectionnera donc "*nom*" en tant que libellé et "*route_id*" en tant que clé.

![image][13]

### Type texte

Le type texte permet de renseigner soi-même le contenu de la source de données, pour cela une règle d'écriture s'impose:

```
libellé 1|clé 1
libellé 2|clé 2
libellé 3|clé 3
```

Chaque entité est composée d'une **clé** qui sera la valeur retenue et d'un **libellé** qui sera le contenu affiché, les deux seront séparées (sans espace) par le caractère "|" et on pourra répéter l'opération autant de fois que l'on veut en allant à la ligne pour chaque élément.


![image][8]

### Type valeurs d'une table locale

Type utilisé lors de l'exemple précédent, il permet d'aller directement chercher en base de données (sur le serveur en cours) le contenu d'une table.

On peut également ajouter une ou plusieurs conditions à l'aide de filtres, pour cela il suffit de renseigner une "*Valeur Clé*" qui sera un nom de colonne sur la table en question, un "*Opérateur*" dans le liste fournie et une "*Valeur*" qui sera la valeur à utiliser pour la condition. Le bouton "*+*" permettra d'ajouter des conditions et on pourra également décider si les conditions sont de type "*AND*" ou "*OR*" grâce à une liste déroulante.

**Important:** lors de son utilisation, ce genre de source de données utilisera le token de connexion de l'utilisateur, il faut donc faire attention que **tous les utilisateurs susceptibles d'utiliser le formulaire aient des droits en consultation sur la table en question.**

![image][9]

### Type service web

Parfois le type "*Valeurs d'une table locale*" ne suffit pas car on veut utiliser une ressource d'un service web précédemment crée affin d'effectuer des requêtes complexes ou alors on souhaite simplement se servir d'un de ceux de l'application.

Pour cela il faudra utiliser le type "*Service web*" qui va effectuer une requête de type "*GET*" à la ressource en question.

![image][10]

### Type objet métier

Il est également possible d'interroger directement un objet métier selon une des trois solutions suivantes:

* **Form:** renvoie l’ensemble des colonnes de la table associée à l'objet métier
* **SQL Summary:** renvoie de résultat de la requête définie par SQL Summary
* **SQL List:** renvoie de résultat de la requête définie par SQL List

![image][11]

### Type base de données externe

Plus complexe mais plus puissant, il permet d'interroger des bases de données situées à l’extérieur du serveur selon un login et un mot de passe fourni.

**Important: les login et mot de passe renseignés doivent être publics** car les utilisateurs finaux pourraient avoir accès à cette information.

![image][12]


## Affichage d'une carte personnalisé

Il est possible dans un formulaire d'afficher une carte permettant à l'utilisateur de voir ou saisir de la donnée géométrique.

![image][14]

Trois types de cartes sont disponibles:

* **Carte OSM:** simple carte contenant une couche OSM
* **Carte Bing:** simple carte contenant une couche Bing (nécessite une clé)
* **Carte vMap:** carte complexe pouvant contenir plusieurs couches et définie par un fichier JSON téléchargeable depuis **Mode vMap > Cartes > Gestion des cartes > Ma carte > Télécharger** 

Une fois la carte décidée, l'administrateur peut définir l'emprise de la carte en navigant simplement dessus ou en renseignant les champs "*Long*" pour la longitude, "*Lat*" pour la latitude et "*1:*" pour l'échelle ou alors "*XMin*", "*YMin*", "*XMax*", "*YMax*" si le mode de centrage de la carte est défini sur "*Étendue*".

Les outils disponibles lors de l'utilisation sont configurables graphiquement via les boites à cocher de la zone "*Définition*".

![image][16]


## Document/Image propre à l'enregistrement

Il est possible d'associer des documents ainsi que des images aux enregistrements liés à l'objet métier en utilisant respectivement les types "*Document - Objet métier*" et "*Image - Objet métier*".

Une boite à cocher "*Uniquement en consultation*" permet de définir si l'utilisateur pourra visualiser et éditer ou alors uniquement visualiser.

Si elles existent, les images seront automatiquement affichées à l'utilisateur tandis que les documents seront disponibles en téléchargement.


| Studio        | Résultat      |
|:-------------:|:-------------:|
| ![image][17]  | ![image][18]  |


Les documents résultants seront stockés dans le répertoire suivant et seul leur nom sera stocké en base:

```
{dossier vMap}/vas/ws_data/vitis/{nom de l'objet métier}/{identifiant de l'enregistrement}/{nom de l'attribut}/{nom du fichier}
```

**Remarque: seulement un fichier peut être associé à un attribut**, si plusieurs fichiers doivent être téléversés il faudra soit créer plusieurs attributs soit les compresser dans un fichier .zip


## Grille de sous-objets avec possibilité d'ajout, de suppression et d'édition

Il est assez régulier d'avoir plusieurs objets métiers qui dépendent les uns des autres, dans ce cas là il est très utile lors de l'édition d'un objet parent de voir la liste des sous-objets liés à ce parent.

Dans notre exemple c'est l'objet métier "*Route*" qui joue le rôle du parent car un enregistrement constituée de plusieurs "*Lampes*".

Il est possible dans les formulaires de vMap de pouvoir afficher cette liste en donnant la possibilité d'ajout, d'édition et de suppression en fonction des droits de l'utilisateur sur le sous-objet.

![image][19]

Cela est assez simple à mettre en œuvre: dans le studio, il faudra créer un élément de type "*Grille - Objet métier*", sélectionner l'objet métier qui jouera le rôle d'enfant et renseigner le lien qu'il existe entre les deux objets.

Dans le champ "*Lien avec l'objet métier*" le premier champ désigne la colonne de l'enfant tandis que le deuxième celle de l'enregistrement parent.

![image][20]

## JavaScript associé au formulaire permettant la conversion rgb/rgba

vMap est un logiciel personnalisable, pour cela il est parfois utile d'associer du code JavaScript aux différents formulaires.

Le code écrit dans ces formulaires sera lancé lors de l'édition, l'insertion et la visualisation d'un objet métier, il peut servir par exemple à convertir des données avant et après saisie, faire des concaténations, des requêtes de type Ajax et bien d'autres.

Pour ce faire, il y a une section "*Édition JavaScript*" dans la partie "*Prévisualisation du studio*":

![image][21]

Ce script doit être composé d'une fonction **constructor_form** appelée lors du chargement, cette fonction est lancée avec le **scope** du formulaire en paramètre.

Testons le code suivant:
```javascript
/**
 * constructor_form
 * Fonction appelé à l'initialisation du formulaire
 * @param {type} scope
 */
var constructor_form = function (scope) {
    console.log("constructor_form");
        
    alert('Hello world');

    console.log('scope:', scope);
};
```

Ceci va afficher à l'utilisateur une popup "Hello world" lors de l'affichage du formulaire, et va écrire le contenu de l'objet scope dans la console du navigateur (affichable dans les outils de développement).


Analysons le contenu de l'objet **scope**:
```
"": undefined$$
ChildScope: function b()
$$childHead: b
$$childTail: m
$$destroyed: false
$$isolateBindings: Object
$$listenerCount: Object
$$listeners: Object
$$nextSibling: m
$$phase: null
$$prevSibling: m
$$watchers: Array(13)
$id: 273
$parent: m
$root: mcloseModal: function (identifier)
compileTemplate: function ()
ctrl: formReader.formReaderController
custom-form: wd
executeButtonEvent: function ($event, buttonEvent)
getLinkFileName: function (url)
getValidationCssClass: function (sFieldName)
getWabField: function (oField)
iDisplayedTab: 0
initSubformGrid
Event_Element_0: function ()
initSubformGridEvent_counter: 9
isButtonPresent: function (oButton, oField, oTab)
isFieldPresent: function (oField, oTab, bCheckButtons)
isFormTextElement: function (sFormElementType)
isStringNotEmpty: function (element)
loadSubForm: function (opt_options)
oFormDefinition: Object
oFormEventsContainer: m
oFormValues: Object
oProperties: Object
oSubformValues: null
reloadSelectField: function (oParentSelect, sFormDefinitionName)
resetFileInputs: function ()
sFormDefinitionName: "update"
sFormUniqueName: 1500541427008
sendForm: function ()
setFormValues: function (oValues)
showTabs: true
submitButton: false
switchSelectedOptions: function (sFormDefinitionName, oFieldDefinition, sFromSelectName, sToSelectName)
testElementsValidityTab: function (callback)
useWab: function ()
wabGroup: null
wabState: null
__proto__: Object
```

Dans cet objet, trois variables sont essentielles: 

* **sFormDefinitionName:** nom du formulaire utilisé (update, display, insert etc..)
* **oFormDefinition:** définition JSON du formulaire
* **oFormValues:** valeurs courantes du formulaire

Dans notre cas nous voulons convertir les couleurs de "*rgba*" vers "*rgb*" et vise versa pour avoir un formulaire en "*rgba*" et une base de données en "*rgb*".

Ces couleurs sont contenues en base dans les attributs "*background_color*", "*contour_color*" et "*color_label*", sur mon formulaire j'ai mis ces variables dans des champs cachés et j'ai également crée les attributs "*background_color_rgba*", "*contour_color_rgba*" et "*color_label_rgba*" qui serviront lors de l'utilisation.


![image][22]

Passons à l'édition du JavaScript, j'ai dans une première partie crée les fonctions de conversion suivantes:

```javascript
var parseColorFromRGBA = function (rgba) {
    if (isRGBA(rgba)) {
        var matchColors = /rgba\((\d{1,3}),(\d{1,3}),(\d{1,3}),(\d{1,3})\)/;
        var match = matchColors.exec(rgba);
        var color = match[1] + ' ' + match[2] + ' ' + match[3];
    } else {
        color = rgba;
    }
    return color;
};

var parseColorToRGBA = function (color) {
    if (isRGBA(color))
        var rgba = color;
    else
        var rgba = 'rgba(' + color.replace(/ /g, ',') + ',1)';
    return rgba;
};

var isRGBA = function (color) {
    if (color.substring(0, 4) === 'rgba')
        return true;
    else
        return false;
};
```

Pour convertir de "*rgb*" vers "*rgba*" lors du chargement du formulaire j'effectue le code suivant:

```javascript
scope['oFormValues']['update']['background_color_rgba'] = parseColorToRGBA(scope['oFormValues']['update']['background_color']);
scope['oFormValues']['update']['contour_color_rgba'] = parseColorToRGBA(scope['oFormValues']['update']['contour_color']);
scope['oFormValues']['update']['color_label_rgba'] = parseColorToRGBA(scope['oFormValues']['update']['color_label']);
```

Et pour convertir le "*rgba*" vers "*rgb*" je devrais effectuer le code suivant:
```javascript
scope['oFormValues']['update']['background_color'] = parseColorFromRGBA(scope['oFormValues']['update']['background_color_rgba']);
scope['oFormValues']['update']['contour_color'] = parseColorFromRGBA(scope['oFormValues']['update']['contour_color_rgba']);
scope['oFormValues']['update']['color_label'] = parseColorFromRGBA(scope['oFormValues']['update']['color_label_rgba']);
```

Le problème avec ce deuxième code c'est qu'il doit être lancé juste avant que le formulaire ne soit soumis par l'utilisateur car sinon les changements effectués par ce dernier ne seront pas appliqués.

**Comment effectuer des opérations juste avant l'envoi du formulaire?**

Dans l'objet "*oFormDefinition*" il est possible de renseigner des événements:

* **beforeEvent:** événement appelé avant envoi au serveur
* **afterEvent:** événement appelé après l'envoi au serveur

De cette façon j'écris le code complet:

```javascript
/**
 * constructor_form
 * Fonction appelé à l'initialisation du formulaire
 * @param {type} scope
 */
 var constructor_form = function (scope) {
    console.log("constructor_form");

    var parseColorFromRGBA = function (rgba) {
        if (isRGBA(rgba)) {
            var matchColors = /rgba\((\d{1,3}),(\d{1,3}),(\d{1,3}),(\d{1,3})\)/;
            var match = matchColors.exec(rgba);
            var color = match[1] + ' ' + match[2] + ' ' + match[3];
        } else {
            color = rgba;
        }
        return color;
    };

    var parseColorToRGBA = function (color) {
        if (isRGBA(color))
            var rgba = color;
        else
            var rgba = 'rgba(' + color.replace(/ /g, ',') + ',1)';
        return rgba;
    };

    var isRGBA = function (color) {
        if (color.substring(0, 4) === 'rgba')
            return true;
        else
            return false;
    };

    // Lance la conversion de rgb vers rgba au chargement si on est en mode update
    if (angular.isDefined(scope['oFormValues']['update'])) {
        scope['oFormValues']['update']['background_color_rgba'] = parseColorToRGBA(scope['oFormValues']['update']['background_color']);
        scope['oFormValues']['update']['contour_color_rgba'] = parseColorToRGBA(scope['oFormValues']['update']['contour_color']);
        scope['oFormValues']['update']['color_label_rgba'] = parseColorToRGBA(scope['oFormValues']['update']['color_label']);
    }

    // Lance la convertion de rgba vers rgb au beforeEvent
    var beforeEvent = function (sMode) {
        scope['oFormValues'][sMode]['background_color'] = parseColorFromRGBA(scope['oFormValues'][sMode]['background_color_rgba']);
        scope['oFormValues'][sMode]['contour_color'] = parseColorFromRGBA(scope['oFormValues'][sMode]['contour_color_rgba']);
        scope['oFormValues'][sMode]['color_label'] = parseColorFromRGBA(scope['oFormValues'][sMode]['color_label_rgba']);
    };

    // Ajoute BeforeEvent
    scope['oFormDefinition']['update']['beforeEvent'] = function () {
        beforeEvent('update');
    };
    scope['oFormDefinition']['insert']['beforeEvent'] = function () {
        beforeEvent('insert');
    };
};
```


## Bouton avec événement JavaScript

Nous avons vu dans l'exemple précédent comment intégrer du code dans un formulaire objet métier via "*constructor_form*", dans cet exemple nous allons créer une fonction qui sera appelée depuis un bouton dans l'interface.

### Bouton Hello world

Dans une première partie nous allons afficher une popup "Hello world" lors du clic sur le bouton, pour cela il faudra ajouter un attribut de type "*Interface - Bouton*" auquel nous allons donner en événement la fonction **sayHello()**.

![image][23]

Côté JavaScript, il est important de placer la fonction sur le bon objet: il faudra la placer sur **le scope de la Main Directive de Vitis**. 

Pour y parvenir il suffit d'appeler **angular.element(vitisApp.appMainDrtv).scope()**:

```javascript
/**
 * constructor_form
 * Fonction appelé à l'initialisation du formulaire
 * @param {type} scope
 */
var constructor_form = function (scope) {
    console.log("constructor_form");

};

/**
 * Fonction à appeler par le bouton
 */
angular.element(vitisApp.appMainDrtv).scope()["sayHello"] = function(){
    alert('Hello world');
}
```

**Remarque:** il est important de vérifier via la console du navigateur que la fonction n’existe pas déjà car vous pourriez remplacer par erreur une fonction déjà existante.

Voici le résultat côté client:

![image][24]

### Bouton Ajax

Dans une deuxième partie nous allons lors du clic sur le bouton effectuer une requête Ajax qui permettra de récupérer les routes donc l'auteur est "laurent" en base, puis l'on va les écrire dans un champ texte.

Pour cela je crée un bouton "*Charger les routes*" auquel j'associe la fonction **loadLaurentRoutes**, et je crée un champ de type "*Texte en édition - Multiligne*" que j'appelle **routes_laurent**.

![image][25]

Pour effectuer la requête Ajax il faut utiliser la fonction **ajaxRequest()** de vMap, au moment de la réponse de la requête je vais concaténer chacun des résultats dans **oFormValues.update.routes_laurent** afin de voir apparaître le résultat sur l'interface.

Pour avoir accès au scope depuis ma fonction **loadLaurentRoutes**, je crée une variable globale **oFormRequired** dans laquelle je place mon scope depuis **constructor_form**.

Voici le code final:
```javascript
var oFormRequired = {
    scope_: {}
};

/**
 * constructor_form
 * Fonction appelé à l'initialisation du formulaire
 * @param {type} scope
 */
 constructor_form = function (scope) {
    console.log("constructor_form");

    oFormRequired.scope_ = scope;
};

/**
 * Fonction à appeler par le bouton
 */
 angular.element(vitisApp.appMainDrtv).scope()["loadLaurentRoutes"] = function(){
    console.log('loadLaurentRoutes');

    showAjaxLoader();
    ajaxRequest({
        'method': 'GET',
        'url': oVmap['properties']['api_url'] + '/vitis/genericquerys',
        'headers': {
            'Accept': 'application/x-vm-json'
        },
        'params': {
            'schema':'sig',
            'table':'route',
            'filter':{"relation":"AND","operators":[{"column":"auteur","compare_operator":"=","value":"laurent"}]}
        },
        'scope': oFormRequired.scope_,
        'success': function (response) {
            hideAjaxRequest();
            console.log('response', response);

            oFormRequired.scope_['oFormValues']['update']['routes_laurent'] = '';

            if (angular.isDefined(response['data'])){
                if (angular.isDefined(response['data']['data'])){
                    for (var i = 0; i < response['data']['data'].length; i++) {
                        oFormRequired.scope_['oFormValues']['update']['routes_laurent'] += response['data']['data'][i]['nom'] + ', ';
                    }
                }
            }
        },
        'error': function (error){
            hideAjaxRequest();
            console.log('error', error);
        }
    });
};
```

Désormais quand je clique sur le bouton "*Charger les routes*", cela remplit le champ "*Routes de laurent*"
![image][26]


[1]: ../../images/exemple_studio_onglets.png
[2]: ../../images/exemple_studio_onglets_2.png
[3]: ../../images/exemple_studio_onglets_3.png

[4]: ../../images/exemple_studio_lien_1.png
[5]: ../../images/exemple_studio_lien_2.png

[6]: ../../images/exemple_studio_datasource_1.png
[7]: ../../images/exemple_studio_datasource_3.png

[8]: ../../images/exemple_studio_datasource_4.png
[9]: ../../images/exemple_studio_datasource_5.png
[10]: ../../images/exemple_studio_datasource_6.png
[11]: ../../images/exemple_studio_datasource_7.png
[12]: ../../images/exemple_studio_datasource_8.png
[13]: ../../images/exemple_studio_datasource_9.png

[14]: ../../images/exemple_studio_carte_1.png
[15]: ../../images/exemple_studio_carte_2.png
[16]: ../../images/exemple_studio_carte_3.png

[17]: ../../images/exemple_studio_document_1.png
[18]: ../../images/exemple_studio_document_2.png

[19]: ../../images/exemple_studio_grille_1.png
[20]: ../../images/exemple_studio_grille_2.png

[21]: ../../images/exemple_studio_js_1.png
[22]: ../../images/exemple_studio_js_2.png

[23]: ../../images/exemple_studio_button_1.png
[24]: ../../images/exemple_studio_button_2.png
[25]: ../../images/exemple_studio_button_3.png
[26]: ../../images/exemple_studio_button_4.png
