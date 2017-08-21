# Objets métiers

![image][1]

## 1. Définition


Un objet métier est une entité qui associe à un calque, les attributs d’une table de base de données. De la sorte, les attributs associés au calque sont affichables et éditables, dans le requêteur et dans le formulaire de création d’objet, accessibles dans le mode Carte.

Le mode Développement permet l’ajout, l’édition et la suppression d’objets métier.

La création d’un objet métier s’opère en deux temps :

* La déclaration de l’objet et des paramètres d’affichage du requêteur.
* La construction des formulaires d’affichage, de création, d’édition et de recherche de l’objet métier via le studio.

## 2. Création d’un objet métier 

![image][2]

Renseigner les champs suivants :

-   Titre: nom de l’objet métier tel qu’il apparaîtra dans le requêteur et dans le formulaire de création d’objet

![Titre de l'objet tel qu'il apparaît dans le requêteur]

![Titre de l'objet tel qu'il apparaît dans le formulaire de création d'objet]

-   Champs id : champ identifiant de la table.
-   Base de données : nom de la base de données à laquelle se connecter
-   Schéma : schéma de la base de données
-   Table : table de la base de données
-   SQL Summary : requête SQL pour définir les champs à afficher dans l’infobulle d’un objet :

![image][3]

-   SQL List : requête SQL pour définir les champs à afficher dans la liste des objets sélectionnés d’un requêteur :

![image][4]

## 3. Formulaires

### 3.1. Définitions

Pour chaque objet métier, plusieurs formulaires sont utilisables et paramétrables pour plusieurs cas d'utilisation:

### 3.1.1. Formulaire de recherche de l’objet métier (search)

Utilisable dans le requêteur et disponible pour les utilisateurs ayant des **droits en consultation** sur la table liée, il permet de faire des recherches filtrées sur les enregistrements de l'objet métier.

![image][5]

### 3.1.2. Formulaire d’affichage de l’objet métier (display)

Utilisable par les personnes ayant des **droits en consultation** sur la table liée, il permet d'afficher des informations en consultation pour l'enregistrement sélectionné.

![image][6]

### 3.1.3. Formulaire de mise à jour de l’objet métier (update)

Utilisable par les personnes ayant des **droits de mise à jour** sur la table liée, il permet de mettre à jour les arguments de l'enregistrement en édition.

![image][7]

### 3.1.4. Formulaire de création de l’objet métier (insert)

Utilisable par les personnes ayant des **droit en insertion** sur la table liée et accessible par le bouton **"Éditer les attributs"**, il permet à l'utilisateur de renseigner les arguments de l'enregistrement à insérer.

![image][8]

### 3.2 Studio

Afin d'administrer ces formulaires pouvant être très complexe, nous avons développé un studio permettant à l'administrateur de gérer graphiquement les différents formulaires des différents objets métier.

Pour accéder au studio, il suffit de cliquer sur la section Formulaires lors de l'édition d'un objet métier.

![image][16]


#### 3.2.1. Génération automatique des formulaires

La première chose à faire lorsqu'on veut créer un ensemble de formulaire est de demander à l'application de les générer en fonction des colonnes présentes sur la table liée.
Si le typage en base de données est bien fait et que cela est possible, le type de champ affiché dans le formulaire sera également implémenté (texte, nombre, date etc...).

Pour cela, il suffit de cliquer **confirmer** lors de l'affichage du message suivant:

![image][9]

Ou bien, dans le **formulaire par défaut** de cliquer sur **Régénérer le formulaire par défaut**

![image][10]

Alors apparaîtra la fenêtre suivante où l'utilisateur peut:

* Sélectionner les arguments à afficher
* Changer pour chaque champ le nom qui sera affiché dans le formulaire

![image][11]

#### 3.2.2. Description du studio

Le studio est divisé en quatre zones principales permettant la gestion des formulaires:

##### 3.2.2.1. La zone d'administration du fichier

Concrètement la zone la plus importante car elle permet la sauvegarde et l'affichage des fichiers, il y a trois types de formulaires: le **formulaire par défaut** qui est le formulaire généré automatiquement, le **formulaire publié** qui est le formulaire en cours d'utilisation dans l'application, et enfin le **formulaire personnalisé** qui est le formulaire en cours d'édition.

![image][12]

Pour modifier un formulaire ,l'administrateur devra se placer sur **Perso**, sélectionner le type de formulaire sur lequel il veut travailler (display, search, update, insert), éditer ce qu'il veut modifier et enfin **publier le formulaire personnalisé** car sans cela les modifications ne seront pas visibles par les utilisateurs.

Le menu déroulant **Fichier** donnera la possibilité de gérer les versions des formulaires (publier le formulaire personnalisé, régénérer le formulaire par défaut etc..)

Le menu déroulant **Édition** permettra quand à lui de faire des actions d'administration sur le formulaire comme par exemple la **gestion des onglets**

##### 3.2.2.2. La zone de prévisualisation

La zone de prévisualisation permet à l'administrateur de visualiser en direct le formulaire en cours.

![image][13]

Il y a également un menu déroulant **Prévisualisation** qui permet l'affichage et la modification de la définition du formulaire au format JSON ainsi que l'ajout de JavaScript au formulaire.

**Attention en cas d'utilisation d'onglets: les onglets ne sont volontairement pas affichés dans cette zone, ils seront affichés lors de l'utilisation réelle du formulaire**.


##### 3.2.2.3. La zone de gestion de mise en page

Sur cette zone l'administrateur peut modifier l'ordre d'affichage des attributs, et en cochant la case "Voir / modifier les lignes" il peut même regrouper plusieurs éléments sur une même ligne.

![image][14]

Vous remarquerez que en bas de la zone il y a un bouton **Sources de données** qui permet d'administrer celles-ci. Ces dernières permettront de remplir les éléments de type liste en allant chercher les données en base par exemple.


##### 3.2.2.4. La zone de définition de l'attribut sélectionné

Dans cette zone, l'administrateur pourra gérer le type de saisie qui sera faite, le libellé à afficher sur le formulaire, le nom de la colonne auquel il est lié et bien d'autres paramètres en fonction du type d'attribut.

![image][15]

#### 3.2.3. Utilisation du studio

Pour comprendre comment utiliser le studio vous pouvez aller voir le document [Cas concrets d'utilisation du studio dans vMap]

  
  [Titre de l'objet tel qu'il apparaît dans le requêteur]: ../../lampe_requeteur.png
  [Titre de l'objet tel qu'il apparaît dans le formulaire de création d'objet]: ../../lampe_creation.png
  [1]: ../../liste_objets_metier.png
  [2]: ../../creation_objet_metier.png
  [3]: ../../infobulle.png
  [4]: ../../liste_requeteur.png
  [5]: ../../images/formulaire_search.png
  [6]: ../../images/formulaire_display.png
  [7]: ../../images/formulaire_update.png
  [8]: ../../images/formulaire_insert.png
  [9]: ../../images/formulaire_message_creation.png
  [10]: ../../images/formulaire_reset_default_button.png
  [11]: ../../images/formulaire_selection_colonnes.png
  [12]: ../../images/formulaire_zone_fichier.png
  [13]: ../../images/formulaire_zone_previsualisation.png
  [14]: ../../images/formulaire_zone_attributs.png
  [15]: ../../images/formulaire_zone_definition.png
  [16]: ../../images/formulaire_studio.png
  [Cas concrets d'utilisation du studio dans vMap]: cas_utilisation_studio.html