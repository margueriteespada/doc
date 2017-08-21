Services web
============

.. figure:: ../images/api_rest.png
   :alt: API REST vMap

1. Définition
-------------

Les web services sont la partie back-end de l'application, ils se
composent de plusieurs ressources qui permettent au client d'interroger
la base de données, de lire/modifier des fichiers et d'effectuer des
opérations sur la machine physique du serveur.

Dans vMap et autres produits Veremes ils sont mis en place par une
API-REST cela signifie qu'on accède aux données selon des règles bien
spécifiques.

Exemple de requête permettant de lister les cartes vMap

::

    https://corbieres/vmap_rest/vmap/maps

Exemple de requête permettant de voir les informations de la carte ayant
pour identifiant le nombre 15

::

    https://corbieres/vmap_rest/vmap/maps/{15}

Ainsi l'API-REST retournera au client un résultat JSON où XML contenant
les informations demandées:

.. code:: json

    {
      "maps": [
        {
          "theme_name": "Thème Géobretagne",
          "theme_description": "Cartes Géobretagne avec fond OSM",
          "crs_name": "[EPSG:2154]-RGF93.Lambert-93",
          "map_id": 15,
          "crs_id": "EPSG:2154",
          "name": "Carte OSM Géobretagne",
          "description": "Carte Geobretagne avec un fond osm",
          "extent": "140807|6725441|303007|6799494",
          "catalog_index": 3,
          "thumbnail": "https://corbieres/vmap_ws_data/vitis/vmap_admin_map_vmap_admin_map/documents/15/thumbnail/geobret.png?d=1499068782",
          "maptheme_id": 1,
          "groups": "",
          "groups_label": ""
        }
      ],
      "status": 1
    }

2. Utilisation
--------------

2.1. Entêtes
~~~~~~~~~~~~

Il y a diverses entêtes essentielles à l'utilisation des ressources

2.1.1. Accept
^^^^^^^^^^^^^

Valeurs possibles:
''''''''''''''''''

-  application/json
-  application/xml
-  application/x-vm-json

Définition
''''''''''

Cette entête détermine le format de réponse demandé par le client. Les
formats application/json et application/xml retourneront un objet
possédant un tableau qui porte le nom de la ressource (dans le cas de
l'exemple ci-dessus il s'agit de "maps"). Le format
application/x-vm-json diffère en donnant comme nom du tableau "data",
cela permettant de faire des requêtes génériques par le client

2.1.2. Token
^^^^^^^^^^^^

Le token de connexion identifie l'utilisateur de l'application, c'est
grâce à lui que la ressource saura si le demandeur possède les droits
suffisants pour avoir un résultat, et c'est par son intermédiaire que se
feront les connexions à la base de données.

**Pour des raisons de sécurité il est strictement interdit de passer le
token en tant que paramètre dans l'URL** et il faut donc le passer dans
l'entête: si une personne malveillante a accès au réseau (man in the
middle) elle pourra alors voir ce token et donc usurper l'identité d'un
autre utilisateur.

2.1.3. X-HTTP-Method-Override
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Lorsqu'on utilise régulièrement l'API-REST il est possible que l'on soit
confronté à des problèmes des longueur des URL: au bout d un certain
nombre de caractères, les navigateurs refuseront d’exécuter la requête
et afficheront l'erreur suivante:

::

    414 URI Too Long

Pour palier à cela, nous avons mis en place l'entête
X-HTTP-Method-Override qui sert à envoyer une requête de type POST avec
des paramètres figurant dans le body (sans limite de taille) et de les
faire interpréter comme des requêtes GET:

::

    General
        Request Method:POST

    Request Headers
        X-HTTP-Method-Override: GET

2.2. Paramètres génériques
~~~~~~~~~~~~~~~~~~~~~~~~~~

2.2.1. order\_by
^^^^^^^^^^^^^^^^

Permet de définir l'ordre d'affichage en cas ou il y ait plusieurs
données, par défaut il vaudra l'identifiant de la ressource

2.2.2. sort\_order
^^^^^^^^^^^^^^^^^^

Couplé au paramètre "order\_by" il permet de définir l'ordre avec les
valeurs suivantes:

-  asc: ordre ascendant
-  desc: ordre descendant

2.2.3. limit
^^^^^^^^^^^^

Si le paramètre limit est fourni, alors le tableau retourné se limitera
à "n" éléments

2.2.4. offset
^^^^^^^^^^^^^

Souvent couplé avec les paramètres "limit" et "order\_by", il peut
permettre par exemple d'effectuer une pagination sur une liste

2.2.5. attributs
^^^^^^^^^^^^^^^^

Définit les attributs qui seront retournés par le client, pour les
renseigner il faudra écrire ces attributs en les séparant par le
caractère "\|"

2.2.6. distinct
^^^^^^^^^^^^^^^

True/false permet de distinguer les valeurs résultantes

2.2.7. filter
^^^^^^^^^^^^^

Donne la possibilité à l’utilisateur de filtrer les données, pour cela
il faudra écrire un objet JSON composé de **relations** et
d'\ **opérateurs**.

2.2.7.1. Relations
''''''''''''''''''

Les relations définissent le type de condition à utiliser selon la
structure JSON suivante:

.. code:: json

    {
        "relation": "AND",
        "operators":[{
            "..."
        }, {
            "..."
        }]
    }

Ici on demande d'ajouter les filtres définis par les opérateurs selon
la relation "AND", on aurait pu également utiliser une relation "OR".

Il est également possible de faire dans une même requête du AND et du OR
en incorporant une relation comme ci c'était un opérateur:

.. code:: json

    {
        "relation": "AND",
        "operators":[{
            "..."
        }, {
            "relation": "OR",
            "operators": [{
                "..."
            }, {
                "..."
            }]
        }]
    }

Ainsi on obtiendra une requête constituée de AND et de OR (voir
l'exemple ci-après).

2.2.7.2. Opérateurs
'''''''''''''''''''

Les opérateurs sont plus simples à comprendre, ils se composent de trois
ou quatre arguments:

-  **column**: nom de la colonne sur laquelle appliquer le filtre
-  **value**: valeur sur laquelle il faudra filtrer
-  **compare\_operator**: type de comparaison ("=", "!=", "<>", ">=",
   "<=", ">", "<", "IN", "NOT IN", "IS NULL", "IS NOT NULL", "LIKE",
   "INTERSECT")
-  **compare\_operator\_options (optionnel)**: ajoute des options
   suivant le type de compare\_operator.

La structure est la suivante:

.. code:: json

    {
        "column": "...",
        "compare_operator": "...",
        "value": "...",
        "compare_operator_options": {
            "..." : "..."
        }
    }

2.2.7.3. Exemples
'''''''''''''''''

Pour être plus parlant, voici quelques exemples avec leur équivalent
sous forme SQL.

En utilisant une relation AND on peut filtrer sur plusieurs opérateurs:

.. code:: json

    {
        "relation": "AND",
        "operators":[{
            "column": "auteur",
            "compare_operator": "=",
            "value": "Laurent"
        }, {
            "column": "allume",
            "compare_operator": "=",
            "value": "true"
        }, {
            "column": "route_id",
            "compare_operator": "=",
            "value": 10
        }]
    }

Équivalent SQL

.. code:: sql

    auteur='laurent' AND allume='true' AND route_id=10

--------------

Si un seul opérateur est utilisé, alors pas la peine de renseigner de
relation:

.. code:: json

    {
        "column":"auteur",
        "compare_operator":"=",
        "value":"laurent"
    }

Équivalent SQL

.. code:: sql

    auteur='laurent'

--------------

En utilisant des relations imbriquées on peut effectuer des filtres
complexes:

.. code:: json

    {
        "relation": "AND",
        "operators":[{
            "column":"auteur",
            "compare_operator":"=",
            "value":"laurent"
        }, {
            "relation": "OR",
            "operators": [{
                "column":"allume",
                "compare_operator":"=",
                "value":"true"
            }, {
                "column":"route_id",
                "compare_operator":"=",
                "value":10
            }]
        }]
    }

Équivalent SQL

.. code:: sql

    auteur='laurent' AND (allume='true' OR route_id=10)

--------------

On peut utiliser "compare\_operator" = "IN" en utilisant des valeurs
situées dans un tableau:

.. code:: json

    {
        "relation": "AND",
        "operators":[{
            "column":"auteur",
            "compare_operator":"=",
            "value":"laurent"
        }, {
            "relation": "OR",
            "operators": [{
                "column":"allume",
                "compare_operator":"=",
                "value":"true"
            }, {
                "column":"route_id",
                "compare_operator":"IN",
                "value":[5,10]
            }]
        }]
    }

Équivalent SQL

.. code:: sql

    auteur='laurent' AND (allume='true' OR route_id IN (5, 10))

--------------

Il est possible d'utiliser "compare\_operator" = "LIKE" avec des valeurs
suivies ou précédées du caractère "%":

.. code:: json

    {
        "column":"auteur",
        "compare_operator":"LIKE",
        "value":"laur%"
    }

Équivalent SQL

.. code:: sql

    auteur LIKE 'laur'%

--------------

En utilisant "compare\_operator\_options.case\_insensitive" sur un type
"LIKE" on peut rendre le filtre insensible à la casse:

.. code:: json

    {
        "column":"auteur",
        "compare_operator":"LIKE",
        "compare_operator_options":{
            "case_insensitive": true
        },
        "value":"%laur%"
    }

Équivalent SQL

.. code:: sql

    LOWER(auteur) LIKE LOWER('%lAur%')

--------------

Utilisation de "IS NOT NULL"

.. code:: json

    {    
        "column": "nom",    
        "compare_operator": "NOT NULL"
    }

Équivalent SQL

.. code:: sql

    nom IS NOT NULL

--------------

On peut effectuer des intersections géométriques utilisant PostGIS:

.. code:: json

    {
        "column":"geom",
        "compare_operator":"intersect",
        "value":"SRID=3857;POINT(349627.744690664 5237367.243157785)"
    }

--------------

L'option "source\_proj" utilisée ici n'est pas obligatoire mais
conseillée si on connaît le système de projection de la table:

.. code:: json

    {
        "column":"geom",
        "compare_operator":"intersect",
        "compare_operator_options":{
            "source_proj": 2154
        },
        "value":"SRID=3857;POINT(349627.744690664 5237367.243157785)"
    }

--------------

On peut utiliser un buffer lors de l'intersection, et même spécifier sur
quelle type de géométrie s'appliquera le buffer:

.. code:: json

    {  
        "column":"geom",
        "compare_operator":"intersect",
        "compare_operator_options":{  
            "source_proj":"2154",
            "intersect_buffer":8.31909066896183,
            "intersect_buffer_geom_type":"point|line"
        },
        "value":"SRID=3857;POINT(349643.2709620344 5237383.963757724)"
    }

3. Exemple de création d'un web service et de ses ressources
------------------------------------------------------------

Dans une installation classique, les web services se trouvent sous forme
de dossiers dans le répertoire vmap/vas/rest/ws. Dans ces dossiers se
trouvent les fichiers indispensables ainsi que les ressources des web
services.

Dans cet exemple nous allons créer un web service "customWS" dans lequel
nous allons créer une ressource "villes"

3.1. Création du dossier et des fichiers indispensables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Parmi les fichiers indispensables, nous retrouvons les fichiers
suivants:

-  **overview.phtml**: Permet d'afficher la ressource dans la page
   d'aide au développement
-  **CustomWS.class.inc**: Classe mère du projet
-  **CustomWS.class.sql.inc**: Fichier contenant les requêtes SQL du
   projet, doit contenir au moins les requêtes "Définition des requêtes
   de l'api Vitis"

3.2. Création de la première ressource
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Dans cet exemple nous cherchons à créer la ressource "villes" qui
permettra de lister les villes contenues dans la table "f\_villes\_l93"
installée par défaut avec vMap.

Chaque ressource est définie par deux fichiers PHP: l'un pour la
définition unitaire d'un objet (ici Ville.class.inc) et l'autre pour
agir sur une liste complète d'objets (ici Villes.class.inc). Vous
remarquerez le "s" (obligatoire) qui permet de faire la différencie
entre la liste et l'unitaire.

3.2.1 La ressource unitaire (Ville.class.inc)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Il s'agit d'une classe PHP qui devra au moins contenir les éléments
suivants:

3.2.1.1 Inclusions des fichiers
'''''''''''''''''''''''''''''''

.. code:: php

    require_once 'CustomWS.class.inc';
    require_once __DIR__ . '/../../class/vitis_lib/Connection.class.inc';

Inclusion de la classe mère du web service ainsi que la classe
permettant d'effectuer des connexions à la base de données.

3.2.1.2 Classe
''''''''''''''

.. code:: php

    class Ville extends CustomWS {
        ...
    }

Définition de la classe Ville

3.2.1.3 Constructeur
''''''''''''''''''''

.. code:: php

    /**
     * construct
     * @param type $aPath url of the request
     * @param type $aValues parameters of the request
     * @param type $properties properties
     * @param type $oConnection connection object
     */
    function __construct($aPath, $aValues, $properties, $oConnection) {
        $this->aValues = $aValues;
        $this->aPath = $aPath;
        $this->aProperties = $properties;
        $this->oConnection = $oConnection;
        $this->aSelectedFields = Array(...);
    }

Constructeur de la classe, vous remarquerez la variable
**$this->aSelectedFields** qui définit attributs à afficher lors des
requêtes.

3.2.1.4 Fontion GET
'''''''''''''''''''

.. code:: php

    /**
     * @SWG\Get(path="/villes/{code}", 
     *   tags={"villes"},
     *   summary="Get Ville",
     *   description="Request to get Ville by id",
     *   operationId="GET",
     *   produces={"application/xml", "application/json", "application/x-vm-json"},
     *   @SWG\Parameter(
     *     name="token",
     *     in="query",
     *     description="user token",
     *     required=true,
     *     type="string"
     *   ),
     *   @SWG\Parameter(
     *     name="code",
     *     in="path",
     *     description="",
     *     required=true,
     *     type="integer"
     *   ),
     *   @SWG\Response(
     *         response=200,
     *         description="Poprerties Response",
     *         @SWG\Schema(ref="#/definitions/villes")
     *     )
     *  )
     */

    /**
     * get informations about villes
     */
    function GET() {
        require $this->sRessourcesFile;
        $this->aFields = $this->getFields('sig', 'f_villes_l93', 'code');
    }

Vous remarquerez qu'il y a deux commentaires au dessus de cette
fonction, le premier sera utilisé par `swagger <https://swagger.io/>`__
pour générer la documentation en ligne interactive et le second est le
commentaire de la fonction utilisée pour décrire aux développeurs ce que
fait la fonction.

Les paramètres décrits dans les commentaires swagger passés dans le
chemin l'URL par la relation in="path"(comme ici "*code*") seront
disponibles via la variable **$this->aPath**.

Les paramètres décrits dans les commentaires swagger passés dans l'URL
par la relation in="query" (comme ici "*token*") seront disponibles via
la variable **$this->aValues**.

La ligne **require $this->sRessourcesFile** permet de récupérer le
contenu du fichier *CustomWS.class.sql.inc*.

La fonction **$this->getFields** permet de récupérer en base de données
les informations la ville en question en utilisant le paramètre "*code*"
passé dans l'URL.

Le résultat stocké dans **$this->aFields** sera retourné lors de la
requête http.

3.2.2 La ressource multiple (Villes.class.inc)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

3.2.2.1 Inclusions des fichiers
'''''''''''''''''''''''''''''''

.. code:: php

    require_once 'Vmap.class.inc';
    require_once 'Ville.class.inc';
    require_once __DIR__ . '/../../class/vitis_lib/Connection.class.inc';
    require_once __DIR__ . '/../../class/vmlib/BdDataAccess.inc';

Require de la classe mère du web service ainsi que la classe unitaire et
les fichiers permettant l'utilisation de la base de données.

3.2.1.2 Classe
''''''''''''''

.. code:: php

    class Villes extends CustomWS {
        ...
    }

Définition de la classe Villes

3.2.2.3 Constructeur
''''''''''''''''''''

.. code:: php

    /**
     * construct
     * @param type $aPath url of the request
     * @param type $aValues parameters of the request
     * @param type $properties properties
     */
    function __construct($aPath, $aValues, $properties) {
        $this->aValues = $aValues;
        $this->aPath = $aPath;
        $this->aProperties = $properties;
        $this->oConnection = new Connection($this->aValues, $this->aProperties);
        $this->aSelectedFields = Array(...);
    }

Contrairement à la ressource unitaire, la connexion est cette fois
instanciée.

3.2.1.4 Fontion GET
'''''''''''''''''''

.. code:: php

    /**
     * @SWG\Get(path="/villes",
     *   tags={"Villes"},
     *   summary="Get Villes",
     *   description="Request to get Villes",
     *   operationId="GET",
     *   produces={"application/xml", "application/json", "application/x-vm-json"},
     *   @SWG\Parameter(
     *     name="token",
     *     in="query",
     *     description="user token",
     *     required=true,
     *     type="string"
     *   ),
     * @SWG\Parameter(
     *     name="order_by",
     *     in="query",
     *     description="list of ordering fields",
     *     required=false,
     *     type="string"
     *   ),
     * @SWG\Parameter(
     *     name="sort_order",
     *     in="query",
     *     description="sort order",
     *     required=false,
     *     type="string"
     *   ),
     * @SWG\Parameter(
     *     name="limit",
     *     in="query",
     *     description="number of element",
     *     required=false,
     *     type="integer",
     *     default="4"
     *   ),
     * @SWG\Parameter(
     *     name="offset",
     *     in="query",
     *     description="index of first element",
     *     required=false,
     *     type="string"
     *   ),
     * @SWG\Parameter(
     *     name="attributs",
     *     in="query",
     *     description="list of attributs",
     *     required=false,
     *     type="string"
     *   ),
     * @SWG\Parameter(
     *     name="filter",
     *     in="query",
     *     description="filter results",
     *     required=false,
     *     type="string"
     *   ),
     * @SWG\Parameter(
     *     name="distinct",
     *     in="query",
     *     description="delete duplicates",
     *     required=false,
     *     type="boolean"
     *   ),
     *   @SWG\Response(
     *         response=200,
     *         description="Poprerties Response",
     *         @SWG\Schema(ref="#/definitions/villes")
     *     )
     *  )
     */

    /**
     * get Villes
     * @return the array of objects
     */
    function GET() {
        $aReturn = $this->genericGet('sig', 'f_villes_l93', 'code');
        return $aReturn['sMessage'];
    }

Tous les paramètres génériques sont listés dans les commentaires
swagger, et sont disponibles sur les variables \*\* $this->aPath \*\* et
\*\* $this->aValues \*\*.

Ici c'est la fonction **genericGet()** qui est utilisée et la fonction
retourne du texte.

3.3. Ressource complexe avec executeWithParams()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Nous avons vu ci-dessus comment créer une ressource standard qui permet
d'aller chercher en base de données les informations d'une table et de
les renvoyer.

Imaginons que l'on veuille dans la classe Ville, faire une deuxième
requête en base de données (cette fois définie dans
*CustomWS.class.sql.inc*) pour aller chercher les monuments associés à
la ville.

*CustomWS.class.sql.inc*:

.. code:: php

    $aSql['getVilleMonuments'] = "SELECT * FROM sig.f_monuments WHERE \"code\"=[sCode]";

*Ville.class.inc*:

.. code:: php

    function GET() {
        require $this->sRessourcesFile;
        $this->aFields = $this->getFields('sig', 'f_villes_l93', 'code');

        $aSQLParams = array(
            'sCode' => array('value' => $this->aFields['code'], 'type' => 'string')
        );
        $oResult = $this->oConnection->oBd->executeWithParams($aSql['getVilleMonuments'], $aSQLParams);
        if (gettype($oResult) == 'object') {
            $this->aFields['monuments'] = Array();
            while ($aLigne = $this->oConnection->oBd->ligneSuivante($oResult)) {
                array_push($this->aFields['monuments'], $aLigne);
            }
        }
    }

Ci dessus la fonction **executeWithParams()** permet d’exécuter une
requête SQL, le résultat est alors rajouté dans
$this->aFields['monuments'].

4. Fonction executeWithParams()
-------------------------------

Pour effectuer des requêtes SQL en PHP est est impératif d'utiliser la
fonction executeWithParams() qui va exécuter une requête avec un tableau
de paramètres passé en option.

**Il ne faut surtout pas concaténer des variables à une requête SQL au
risque d'exposer l'application à une faille de type**
`SQLi <https://fr.wikipedia.org/wiki/Injection_SQL>`__

Il faut écrire dans la requête une balise contenant le nom de la
variable, et fournir un tableau de variables à executeWithParams().

Les différents formats sont:

-  **string**, **number**, **integer**: pour les valeurs de variables à passer entre
   simple quotes.
-  **group**: pour les valeurs à passer entre simple quotes et séparées par des virgules.
-  **geometry**: pour les géométries à passer entre simple quotes
-  **quoted\_string**: comme string mais pour intégrer des caractères
   spéciaux ex: 'ma lampe%'.
-  **column\_name**, **schema\_name**, **table\_name**: pour les noms de
   colonnes, tables, schémas. Attention car pour ces types de paramètre
   executeWithParams() ne s'occupera pas des quotes, il faut donc les
   mettre à l'avance ex: SELECT "[column\_name]" FROM
   [schema\_name].[table\_name] WHERE table='[table\_name]'.

Exemples
~~~~~~~~

.. code:: php

    $aSQLParams = array(
        'sSchema' => array('value' => $this->aProperties['schema_vmap'], 'type' => 'column_name'),
        'sGroups' => array('value' => $sGroups, 'type' => 'group')
    );
    $sSql = "SELECT map_id, group_id FROM [sSchema].map_group WHERE \"group_id\" in ([sGroups])";
    $oResult = $this->oConnection->oBd->executeWithParams($sSql, $aSQLParams);

.. code:: php

    $aSQLParams = array(
        'sSchema' => array('value' => $this->aProperties['schema_vmap'], 'type' => 'column_name'),
        'sMapId' => array('value' => $map_id, 'type' => 'number')
    );
    $sSql = "SELECT * FROM [sSchema].map_layer WHERE \"map_id\" = [sMapId]";
    $oResult = $this->oConnection->oBd->executeWithParams($sSql, $aSQLParams);

