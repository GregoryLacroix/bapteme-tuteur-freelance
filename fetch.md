<?php 
################################# EXPLICATION METHODE FETCH ###########################
/* 
    Je fais suite au message que vous m'avez envoyé concernant le méthode fetch().
    Tout d'abord, nous allons nous connecter à la base de données, une notion que vous connaissez déjà, mais une petite révision ne fait pas de mal ;)
    Pour cela créons une instance de la classe PDO, on crée un objet issue de la classe PDO, c'est cet objet ($db) qui va nous permettre d'exploiter la classe, l'objet PDO nous permet d'utiliser, d'exploiter les méthodes déclarées dans la classe PDO, on pioche dans l'objet.
*/
$db = new PDO(
    "mysql:host=localhost;dbname=skoule;charset=utf8",'root','', array(PDO::ATTR_ERRMODE => PDO::ERRMODE_WARNING) 
);

// L'outil de débbugage var_dump() nous permet d'observer que la variable $db est bien un objet issu de la classe PDO, c'est un enfant de la classe PDO :https://www.php.net/manual/fr/book.pdo.php
echo '<pre>'; var_dump($db); echo '</pre>';

/*
    Résultat : 
    object(PDO)#1 (0) {
    }
*/

// La classe PDO possède plusieurs méthodes (fonctions), dont query() qui permet d'executer des requêtes SQL en base de données, en effet la méthode query() reçoit en argument la requête SQL.
// get_class_methods() est une fonction prédéfinie permettant de lister toutes les méthodes (fonctions) déclarées dans une classe.
$class_methods = get_class_methods($db);
echo '<pre>'; print_r($class_methods); echo '</pre>';
/*
    Méthodes issues de la classe PDO ($db) :
    Array
    (
        [0] => __construct
        [1] => beginTransaction
        [2] => commit
        [3] => errorCode
        [4] => errorInfo
        [5] => exec
        [6] => getAttribute
        [7] => getAvailableDrivers
        [8] => inTransaction
        [9] => lastInsertId
        [10] => prepare
        [11] => query
        [12] => quote
        [13] => rollBack
        [14] => setAttribute
    )

    Nous allons prendre comme exemple cette route du projet : /students qui permet de lister tous les étudiants enregistrés dans la base de données.
    En argument de la méthode query(), nous fournissons la requête SQL permettant de selectionner l'ensemble des étudiants enregistrés en base de données.
*/
$objectPDOStatement = $db->query('SELECT * FROM student');
$class_methods = get_class_methods($objectPDOStatement);
echo '<pre>'; print_r($class_methods); echo '</pre>';
/*
Résultat : 
object(PDOStatement)#2 (1) {
    ["queryString"]=>
    string(21) "SELECT * FROM student"
}
On observe que le méthode query() retourne un autre objet issu d'une autre classe : PDOStatement !
Cette classe possède aussi ses propres méthodes : https://www.php.net/manual/fr/class.pdostatement.php

Méthodes issues de la classe PDOStatement :
Array
(
    [0] => bindColumn
    [1] => bindParam
    [2] => bindValue
    [3] => closeCursor
    [4] => columnCount
    [5] => debugDumpParams
    [6] => errorCode
    [7] => errorInfo
    [8] => execute
    [9] => fetch
    [10] => fetchAll
    [11] => fetchColumn
    [12] => fetchObject
    [13] => getAttribute
    [14] => getColumnMeta
    [15] => nextRowset
    [16] => rowCount
    [17] => setAttribute
    [18] => setFetchMode
    [19] => getIterator
)
*/

echo '<pre>'; var_dump($objectPDOStatement); echo '</pre>';
/*
    Jusqu'à cette étape, voici le résultat que nous obtenons :
    object(PDOStatement)#2 (1) {
        ["queryString"]=>
        string(21) "SELECT * FROM student"
    }

    Ce n'est pas le résultat que nous attendons :( 
    Nous aimerions afficher les noms, prénoms etc... des étudiants enregistrés en base de données.

    Pour cela, il existe plusieurs méthodes issues de la classe PDOStatement permettant de retourner ce résultat.
*/

echo '<hr>';
######################## 1ÈRE METHODE : FETCH() #############################
/*
    La méthode fetch() issue de la classe PDOStatement ($objectPDOStatement) retourne chaque ligne de résultat issue de la requête SQL executée par query(), en général sous forme de tableau ARRAY ou sous forme d'objet, c'est ce que nous allons voir tout de suite :).

    Voici la liste de tous les étudiants enregistrés en base de données : 
    id	firstname	lastname	status	created_at	updated_at	teacher_id
    1	Sacha	Touille	1	24/08/2023 09:41	NULL	2
    2	Grégory	LACROIX	1	26/08/2023 14:47	NULL	0

    Sur la ligne suivante, nous executons la méthode fetch() issue de la classe PDOStatement ($objectPDOStatement), je rappel que le résultat de la requête SQL est contenu dans cet objet.
    En argument de la méthode fetch(), nous avons tranmis la constante FETCH_ASSOC issu de la classe PDO (les '::' permettent d'atteindre, ici, la constante FETCH_ASSOC en passant directement par la classe).
    La constante FETCH_ASSOC permet de récupérer chaque de résultats sous forme de tableau associatif.
*/
$teacher = $objectPDOStatement->fetch(PDO::FETCH_ASSOC);
echo '<pre>'; print_r($teacher); echo '</pre>';

/*
    Observons le résultat : 
    Array
    (
        [id] => 1
        [firstname] => Sacha
        [lastname] => Touille
        [status] => 1
        [created_at] => 2023-08-24 09:41:41
        [updated_at] => 
        [teacher_id] => 2
    )

    C'est déjà mieux ! ;) On obtient un tableau ARRAY dont les indices (id, firstname, lastname etc...) correspondent aux noms des colonnes de la table SQL 'student'. Pour chaque indice, on retrouve les valeurs associées.
    Mais récupère t-on toute les étudiants ? Non... :(
    Pourquoi n'as t-on pas l'ensemble des résultats ? Tout simplement parce que nous n'avons pas bouclé le résultat ! Sans boucle, la méthode fetch() ne retourne que la première de résultat de la base de données.
*/

echo '<hr>';

#############################################################################
/*
    Nous allons maintenant détaillé ce 2ème exemple : 
    On peut déjà observer qu'une nouvelle requête SQL a été exécutée, mais Pourquoi ? 
    Tout simplement parce qu'il n'est pas possible d'excuter plusieurs fois la méthode fetch() sur la même objet PDOStatement ($objectPDOStatement) ! 
*/

$objectPDOStatement = $db->query('SELECT * FROM student');

// $teacher receptionne un tableau ARRAY par tour de boucle WHILE
while($teacher = $objectPDOStatement->fetch(PDO::FETCH_ASSOC)){
    echo '<pre>'; print_r($teacher); echo '</pre>';
}

/*
    Résultat :

    1er tour de boucle WHILE
    Array
    (
        [id] => 1
        [firstname] => Sacha
        [lastname] => Touille
        [status] => 1
        [created_at] => 2023-08-24 09:41:41
        [updated_at] => 
        [teacher_id] => 2
    )

    2ème tour de boucle WHILE
    Array
    (
        [id] => 2
        [firstname] => Grégory
        [lastname] => LACROIX
        [status] => 1
        [created_at] => 2023-08-26 14:47:20
        [updated_at] => 
        [teacher_id] => 0
    )

    Génial ! Nous avons enfin le résultat que nous souhaitions ! :)
    détaillons maintenant cette boucle while : 
    
    while($teacher = $objectPDOStatement->fetch(PDO::FETCH_ASSOC)){
        echo '<pre>'; print_r($teacher); echo '</pre>';
    }

    Pour chaque tour de boucle while, la méthode fetch() retourne une ligne de résultat de la base de données sous forme de tableau ARRAY

    id	firstname	lastname	status	created_at	updated_at	teacher_id
    1	Sacha	Touille	1	24/08/2023 09:41	NULL	2 ----> 1er tour de boucle WHILE
    2	Grégory	LACROIX	1	26/08/2023 14:47	NULL	0 ----> 2ème tour de boucle WHILE

    Une fois que chaque ligne de résultat a été retourné, la boucle WHILE s'arrête automatiquement
    La variable $teacher est une variable qui receptionne chaque tableau ARRAY retourné par la méthode fetch 

    Conclusion : 
    Si la requête SQL ne retourne qu'un seul résultat de la base de données : pas de boucle ! 
    Si la requête SQL retourne plusieurs lignes de résultats de la base de données : une boucle !
*/

echo '<hr>';

#############################################################################
/*
    Dans cette exmple, nous allons répéter le même procédé, à la seule différence que nous allons utiliser une autre constante : FETCH_OBJ
    FETCH_OBJ : constante issue de la classe PDO permettant de retourner chaque ligne de résultat de la base de données sous forme d'objet (stdClass), dont les indices sont des propriétés public de l'objet.
*/

$objectPDOStatement = $db->query('SELECT * FROM student');
while($teacher = $objectPDOStatement->fetch(PDO::FETCH_OBJ)){
    echo '<pre>'; print_r($teacher); echo '</pre>';
}

/*
    Résultat : stdClass Object
    (
        [id] => 1
        [firstname] => Sacha
        [lastname] => Touille
        [status] => 1
        [created_at] => 2023-08-24 09:41:41
        [updated_at] => 
        [teacher_id] => 2
    )
    stdClass Object
    (
        [id] => 2
        [firstname] => Grégory
        [lastname] => LACROIX
        [status] => 1
        [created_at] => 2023-08-26 14:47:20
        [updated_at] => 
        [teacher_id] => 0
    )
*/

echo '<hr>';

$objectPDOStatement = $db->query('SELECT * FROM student');
while($teacher = $objectPDOStatement->fetch(PDO::FETCH_OBJ)){
    // echo '<pre>'; print_r($teacher); echo '</pre>';
    echo $teacher->firstname . '<br>'; // on affiche successivement les prénoms des étudiants.
}

echo '<hr>';

#############################################################################
/*
    Dans cette exmple, nous allons répéter le même procédé, à la seule différence que nous allons utiliser une autre constante : FETCH_NUM
    FETCH_NUM : constante issue de la classe PDO permettant de retourner chaque ligne de résultat de la base de données sous forme de tableau ARRAY avec pouir indices, des indices numériques.
*/

$objectPDOStatement = $db->query('SELECT * FROM student');
while($teacher = $objectPDOStatement->fetch(PDO::FETCH_NUM)){
    echo '<pre>'; print_r($teacher); echo '</pre>';
}

/*
    Résultat :
    Array
    (
        [0] => 1
        [1] => Sacha
        [2] => Touille
        [3] => 1
        [4] => 2023-08-24 09:41:41
        [5] => 
        [6] => 2
    )
    Array
    (
        [0] => 2
        [1] => Grégory
        [2] => LACROIX
        [3] => 1
        [4] => 2023-08-26 14:47:20
        [5] => 
        [6] => 0
    )
*/

echo '<hr>';

/*
    Il existe d'autres constantes : https://www.php.net/manual/fr/pdostatement.fetch.php.
    Notamment FECTH_CLASS qui retourne une nouvelle instance de la classe demandée, liant les colonnes du jeu de résultats aux noms des propriétés de la classe.
*/

######################## 2ÈME METHODE : FETCHALL() ###########################
/*
    Dans ce dernier exemple, nous allons executer la méthode fetchAll() issue de la classe PDOStatement.
    nous allons répéter le même procédé, à la seule différence, que nous allons executer la méthode fetchAll() sur l'objet PDOStatement.
    La méthode fetchAll() reçoit en argument les mêmes constantes que le méthode fetch().
*/

$objectPDOStatement = $db->query('SELECT * FROM student');
$teacherList = $objectPDOStatement->fetchAll(PDO::FETCH_ASSOC);
echo '<pre>'; print_r($teacherList); echo '</pre>';

/*
    Résultat : 
    Array
    (
        [0] => Array  --------> 1ère ligne de résultat de la BDD
        (
            [id] => 1
            [firstname] => Sacha
            [lastname] => Touille
            [status] => 1
            [created_at] => 2023-08-24 09:41:41
            [updated_at] => 
            [teacher_id] => 2
        )

        [1] => Array --------> 2ème ligne de résultat de la BDD
        (
            [id] => 2
            [firstname] => Grégory
            [lastname] => LACROIX
            [status] => 1
            [created_at] => 2023-08-26 14:47:20
            [updated_at] => 
            [teacher_id] => 0
        )
    )

    Observons le résultat : 
    Ici, sans executer (pour le moment) de boucle, nous retrouvons automatiquement l'ensemble des résutats de la base de données.
    La méthode fetchAll() retourne un tableau ARRAY multidimensionnel contenant chaque ligne de résultat de la base de données sous forme de tableau ARRAY indexé numériquement.
    Toutefois, pour expoiter et afficher le résultat, nous aurons besoin de... BOUCLES !!!
    Plusieurs possibilités : 
    - 2 boucle foreach()
    - 1 boucle while() / 1 boucle foreach()

    Allez petit défit ! Je vous laisse le soin d'afficher successivement l'ensemble des résultats de la méthode fetchAll() :)

    J'éspère que mes exmplications sont claire et vous aurons aidé, dans le cas contraire, n'hésitez pas à revenir vers moi ;)

    Bon courage !
*/
