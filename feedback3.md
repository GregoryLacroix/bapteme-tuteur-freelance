# Premièrement, le fichier .htaccess n'est pas définit, de ce fait le mapping/routage ne fonctionne pas, aucune page ne peut s'afficher sur le naviagteur.
# Ce fichier va permettre de réecrire les URL dynamiquement, il est possible d'effectuer quasiment tous les types de réécriture d'URLs dont vous avez besoin. Pour plus d'information, vous pouvez consulter la documentation : https://httpd.apache.org/docs/trunk/fr/rewrite/intro.html.

# Concernant les différentes vues que vous avez déclarées, vous avez copier/coller le header/footer dans chaque fichier, cela engendre de la redondance et peut générer des erreurs, par exemple la navigation n'est pas déclarées dans tous les fichiers et ne s'affiche donc pas sur certaines pages. Afin d'éviter ces erreurs, il faut décomposer les différentes parties du site (header.tpl.php/nav.tpl.php/footer.tpl.php) et de l'inclure sur chaque page, le code sera beaucoup plus clair et organisé, si on chercher le navigation, on sait toute de suite dans quel fichier elle se trouve.

# Concernant la navigation, certains liens sont cassés, cela est du fait que les attributs href n'ont pas la bonne valeur, ex :
# <a class="nav-link" href="../index.html">Accueil</a>
# ici la valeur n'est pas la bonne, il faut utiliser l'objet $router afin de générer la route en focntion de son nom, et ce ne sont pas les fichiers .html qui sont appelés mais les fichiers .tpl.php du dossier '/views'

# Toute la partie concernant l'ajout/modification/suppression/list d'un user/admin reste à développer ainsi que la partie authentification/déconnexion.
# Si vous rencontrez des difficultés sur ces différentes parties, n'hésitez pas à revenir vers moi si vous avez besoin de plus d'explications.

# Pour la déclaration des routes, attention à bien respecter les convention de nommage :
# student_add ===> student-add
# Vous avez déclarer les routes direcetement dans le fichier index.php, pour que le fichier ne soit pas trop lourd, il aurait été préférable de déclarer les routes dans un fichier externe et de l'inclure dans le fichier index.php

# Vous avez déclaré un controler MainController qui gère uniquement la page d'accueil, dans ce controller la méthode show() est déclarée, or elle ne devrait pas être déclarée dans ce controller, cette méthode permet d'afficher le rendu HTML, elle doit être disponble dans chaque controller, il aurait été préférable de la déclarer dans un autre controller, plus global (ex: CoreController), ce controller contient des méthodes qui sont globales pour tout le site, elles seront nécessaires pour chaque partie du site, c'est cette classe qui sera héritée par TeacherController/StudentsControler etc...

# Partie Teacher
# Pour la partie listing des profs, le rendu est correct, mais la navigation n'apprait pas puisqu'elle n'est pas décalrée dans la vue.
# Dans le controller, vous avez déclaré une méthode studentAdd() le nommage est érroné, de ce fait la route n'appelle pas la bonne méthode dans le controller ( Fatal error: Uncaught Error: Call to undefined method App\Controllers\TeacherController::teacherAdd() )
# il y'a une autre erreur : Warning: Undefined variable $newStudent, effectivement vous avez nommé la variable $newTeacher.
# attention toutefois au nommage des variables, il faut que ce soit le plus cohérents possible, ex : $newTeacher ====> $teacher
# Dans la méthode permettant d'ajouter un teacher, les arguments de la méthode show() sont érronés, de ce fait ce n'est pas le bon template qui est affiché sur le navigateur.
# Vous avez filtrer les données du formulaire à l'aide de le fonction filter_input, mais le type de filtre utilisé n'est pas le bon, nous souhaitons un filtre de validation.
# if(isset($_POST) && !empty($_POST)) : cette condition est inutile, il est préférable de controller chaque champ du formulaire, cela ne sécurise pas assez le formulaire, de plus cette condition if(isset($_POST)) sera toujours vrai, en effet $_POST est une superglobale prédéfinie, isset() permet de tester l'existence d'une variable, donc $_POST existera et sera définit dans tous les cas.
# Pour le contrôle des champs du formulaire, vous avez déclaré des conditions 
# Il faut appeler les setters de l'objet Student une fois les controles efféctués sur le formulaire et le setter setTitle() n'est pas executé, le titre saisi dans le formulaire ne peut pas être sauvegardé en base de donnée.
# Cette condition est mal formulée : 
if(empty($errorList)) {
    // aucune erreur détectée
    // j'ai soumis mon formulaire et je n'ai pas d'erreur
    $retour = $newTeacher->insert(); ---> cette étape pourrait être évitée, et en plus cette méthode n'existe pas, elle n'est pas déclarée dans le modèle Teacher

    if ($retour == false) { ----> la condition devrait être testé dans l'autre sens, on va d'abord tester si l'insertion a été executé, toujours privilégié la priorité la plus forte
        // gestion d'erreur
        var_dump("erreur, problème avec la requête SQL");
    } else {
        // redirection, car ma requête a fonctionnée
        $route = $router->generate("students");
        //dump($route);
        header('Location: '.$route);  ---> executer une fonction déclarer (ex: redirectToRoute) plutôt que d'appeler la fonction header
    }
}

# les messages d'erreurs n'apparaissent pas, car ils ne sont pas exploités dans le template/view. Vous auriez pu créer un template spécifique uniquement pour afficher les erreurs et l'inclure sur chaque page.
# L'insertion ne fonctionnent pas étant donnée la requête SQL n'est pas déclarée.
# Dans le template teacher_add.tpl.php, le lien permettant de revenir au listing des teachers est cassé. En effet la valeur de l'attribut href est erroné ;
# <a href="../teacher/list.html" class="btn btn-success float-right">Retour</a>
# Comme dit précédemment, ici la valeur n'est pas la bonne, il faut utiliser l'objet $router afin de générer la route en focntion de son nom, et ce ne sont pas les fichiers .html qui sont appelés mais les fichiers .tpl.php du dossier '/views'
# les parties modification/suppression restent à développer.

# Partie Student
# Dans le template/view student_add.tpl.php, l'attribut action dans la balise <form> du formulaire HTML ne doit pas être renseigné, le mapping (routes) déclaré fait en sorte, en fonction de la route donné dans l'URL, d'executer la bonne méthode dans le controller.
# Dans le formulaire d'ajout, les professeurs pouvant être ratachés à un étudiant, doivent être affichés dynamiquement et non écrit en dur, si l'on ajoute un professeur, il n'appraitra pas dans cette liste :
<select name="teacher" id="teacher" class="form-control">
    <option value="0">-</option>
    <option value="1">Prénom Prof - Formateur PHP/MySQL</option> ------> appel dynamque via méthode dans le controller
    <option value="2">Prénom2 Prof2 - Formateur PHP/MySQL</option>
    <option value="5">sgsg fsgfsg - sg</option>
</select>

# Dans chaque model pour les requêtes d'insertion/modification, pour parer aux injections SQL, il faut préparer la requête via la méthode prepare(), ensuite nous executons la méthode bindValue() qui permet d'enfermer chaque valeur saisie dans le formulaire dans des marqueurs nominatifs, de ce fait la requête SQL ne peut être hacker par une injection SQL.
# La validation du formulaire d'ajout ne fonctionne pas puisque vous n'avez pas déclaré de mapping en méthode POST pour la route '/student/add', à la validation du formulaire, nous avons une erreur 404 not found.
# les messages d'erreurs n'apparaissent pas, car ils ne sont pas exploités dans le template/view. Vous auriez pu créer un template spécifique uniquement pour afficher les erreurs et l'inclure sur chaque page.
# Pour le listing des étudiants, vous avez déclaré une colonne 'titre' qui n'est pas déclaré dans le model Student mais Teacher, l'affichage du professeur lié à l'étudiant est manquant. Et la navigation n'apprait pas puisqu'elle n'est pas décalrée dans la vue.

# Toutes les routes ne sont pas protégées, il manque un contrôle d'autorisation, quel utilisateur peut avoir accès à quelle page admin/user

# Commentaire général : 
# C'est un bon début mais certaines parties du projet restent inachevée. Pensez à bien débugger, à contrôler chaque route, beaucoup de liens sont cassés. 
# Ne vous découragez pas ! continuez à pratiquer au maximum, persevérez ;)











