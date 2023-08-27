# apprenant 1

# 1. Premièrement le système de routage ne fonctionne pas, pour une raison simple, vous n'avez pas créer de fichier .htaccess dans le dossier /public. Ce fichier va permettre de réecrire les URL dynamiquement, il est possible d'effectuer quasiment tous les types de réécriture d'URLs dont vous avez besoin. Pour plus d'information, vous pouvez consulter la documentation : https://httpd.apache.org/docs/trunk/fr/rewrite/intro.html.

# 2. Dans le déclaration des routes dans le fichier public/index.php, vous faites appel aux controller sans préciser le namespace, de ce fait le controller n'est pas reconnu, dans l'idéal, il aurait été judicieux de déclarer une variable contenant le nom du namepace et de l'inclure dans chaque déclaration de route, cela évite la redondance et si pour X raison, le namespace venait à être modifié, une seule variable serait impactée.

# 3. De plus, vous avez déclaré l'ensemble des routes dans le fichier public/index.php, il serait judiceux de déclarer les routes dans un fichier externe (ex : routes.php) et de l'inclure directement dans le fichier public/index.php, ce qui permet d'avoir des fichiers moins lourd, plus clair et mieux organisé.

# 4. Il y a différentes erreurs dans la déclaration des routes, pour rappel, pour réaliser le mapping, la méthode map() issue de la classe AltoRouter() attend 5 arguments : 

# 1. méthode HTTP : GET ou POST (pour résumer)
# 2. La route : la portion d'URL après le basePath
# 3. Target/Cible : informations contenant
#      - le nom de la méthode à utiliser pour répondre à cette route
#      - le nom du controller contenant la méthode
# 4. Le nom de la route : pour identifier la route, on va suivre une convention
#      - "NomDuController-NomDeLaMéthode"
#      - ainsi pour la route /, méthode "home" du MainController => "main-home"

# Dans vos déclarations de routes, je vous donne un exemple des différentes erreurs : 

$router->map(
    'GET',
    '/students/list', ==> route éronnée
    [
        'method' => 'student', ==> il serait préférable de nommé cette fonction list(), ce qui parait logique puisqu'elle permet de lister l'ensemble des étudiants, ce sera plus compréhensible si un autre développeur serait ammené à travailler sur le projet
        'controller' => $controllersNamespace . 'StudentController' 

    ],
    'student_list' ==> la convention de nommage de le route n'est pas respectée
);

# Vous avez déclarer les routes '/404' et '/403', ces routes ne doivent pas être déclarées avec le reste du mapping, ces routes interviennent en cas de route érronés donc page non trouvée (erreur 404) ou pour une route interdite (erreur 403), par exemple une route uniquement accessible par un administrateur du site, ces routes sont déclenchées en fonction de l'action de l'utilisateur sur le site

# Pensez à bien controller et débuger les différentes routes déclarées

# 5. PAGE DE CONNEXION
# Plusieurs erreurs apparraissent : 
# Lors de l'authentification, la navigation ne doit pas apparaitre, si l'utilisateur n'est pas connecté, il ne doit pas avoir accès aux différentes pages de l'application, la méthode signin() est donc à revoir, en ajoutant des arguments à la méthode show(), permettant d'avoir un redu visuel sur le navigateur, cela vous permettra d'accéder à ceux ci dans les différents templates et de pouvoir définir entre autre si la navigation doit être visible ou non.

# Dans le template/view signin.tpl.php, l'attribut action dans la balise <form> du formulaire HTML ne doit pas être renseigné, le mapping (routes) déclaré fait en sorte, en fonction de la route donné dans l'URL, d'executer la bonne méthode dans le controller permettant de s'authentifier.

# Authentification
# Dans la méthode signinPost, premièrement, il est inutile d'importer la variable $router dans la méthode, le service de mapping est déjà en place, la route appelle la bonne méthode dans le controller

# if(isset($_POST) && !empty($_POST)) : cette condition est inutile, il est préférable de controller chaque champ du formulaire, cela ne sécurise pas assez le formulaire, de plus cette condition if(isset($_POST)) sera toujours vrai, en effet $_POST est une superglobale prédéfinie, isset() permet de tester l'existence d'une variable, donc $_POST existera et sera définit dans tous les cas.
# Vous avez filtrer les données du formulaire à l'aide de le fonction filter_input, mais le type de filtre utilisé n'est pas le bon, nous souhaitons un filtre de validation
# Comme je l'ai dit précedemment, les champs du formulaire doivent être absolument controlés, un formulaire est fonctionnel lorsque tous les cas potentiels ont été évalués. Et si et seulement si l'utilisateur à correctement remplit le formulaire sans erreur (champ vide etc..), nous pouvons executer le code permettant de controller l'authentification de l'utilisateur

# Pour le controle des identifiants, vous avez déclaré cette condition if($user), cette condition sera toujours vrai, en cas d'erreur d'authentifaction, la variable $user renvoi un tableau array vide mais la variable user existe et renvoi true dans tout les cas. Il serait préférable de la tester différement, afin de savoir si la méthode findByEmail renvoi bien un utilisateur ou non.
# Les erreurs utilisateurs ne s'affichent pas, dans tous les cas l'utlisateur est redirigé automatiquement, il faut donc transmettre la variables d'erreurs au template prévu à cet effet (errors.tpl.php), afin de pouvoir les afficher.
# Dans le template errors.tpl.php, les erreurs ne peuvent pas s'afficher puisque ce n'est pas la bonne variable qui est testé
# Une fois le mot de passe contrôlé, le statut de l'utilisateur doit être testé (actif/inactif)
# Dans la session vous avez stocké l'identifiant de l'utilisateur et son rôle, c'est un bon début mais il serait peut-être judiceux de stocker l'ensemble des données de l'utilisateur dans la session, elle sera accessible de partout et nous pourrions avoir besoin des données de l'utilisateur sur certaines pages (nom, prénom etc...), cela évitera des requêtes en base de données supplémentaire.
# Et dernier point, plutôt que d'appeler le fonction header() dans les méthodes, il sera préférable de déclarer une méthode (ex: redirectToRoute), ce qui nous perrmettra d'y ajouter plusieurs arguments sans avoir à importer la variable $router dans chaque méthode.

# C'est bon j'ai réussi à me connecter ;), mais dans la navigation, les routes ne sont pas définit correctement, pensez à respecter les conventions de nommages (teacher_list => teacher-list)
# Vous avez déclaré 2 conditions afin d'affecter la classe 'active' en fonction de la page où l'on se trouve : <?php if($viewData["currentPage"] == "student/student_list") echo "active"; ?> <?php if($viewData["currentPage"] == "student/student_add") echo "active"; ?>, il est possible de simplifier le code en une seule condition ;)
# le lien 'se connecter' est inutile puisqu'à l'authentification, la navigation est masquée, donc l'utlisateur n'aura accès à la navigation qu'une fois connecté.
# Pour simplifier le code, il n'est pas nécessaire d'englober toute la navigation dans une condition pour savoir si l'id de l'utilisateur est bien stocker en session, si l'utilisateur arrive à cette étape, son id est forcement stocké dans la session, seulement pour la deconnexion.

# 6. Page Student
# Tout d'abord dans le controller et la méthode Student(), le nommage des variables n'est pas très clair.
# Plutôt que de définir un objet Student et d'executer le méthode findAll(), il est possible d'accéder à la méthode findAll() en passant directement par l'entité, cela permet de simplifier la méthode. Et attention au nommage des variables, plutôt que $student, utiliser $items ou $data par exemple, le but est qu'à la lecture du code, ce soit plus clair et le plus logique possible.
# Dans le template permettant d'afficher les étudiants, il est préférable d'appeler la variable $students définit dans la méthode student() plutôt que la variable $viewData provenant de la méthode show(), cette méthode est comme un plan de construction, il y a une méthode définit pour les étudiants, alors utilisons là !
# Pour le listing des étudiants, l'affichage du professeur lié à l'étudiant est manquant.
# pour ajouter un étudiant, les erreurs utilisateurs n'apparaissent, il faut inclure le template permettant d'afficher les erreurs utilisateurs.
# L'attribut action ne doit pas être renseigné comme dit précedemment
# Dans la méthode permettant d'jouter un étudiant en base de données, plusieurs point sont à revoir : 
# 1. Il faut appeler les setters de l'objet Student une fois les controles efféctués sur le formulaire
# 2.
if(empty($errorList)) {
    // aucune erreur détectée
    // j'ai soumis mon formulaire et je n'ai pas d'erreur
    $retour = $newstudent->insert(); ---> cette étape pourrait être évitée

    if ($retour == false) { ----> la condition devrait être testé dans l'autre sens, on va d'abord tester si l'insertion a été executé, toujours privilégié la priorité la plus forte
        // gestion d'erreur
        dump("erreur, problème avec la requête SQL");
    } else {
        // redirection, car ma requête a fonctionnée
        $route = $router->generate("student-list");
        //dump($route);
        header('Location: '.$route); ---> executer une fonction déclarer (ex: redirectToRoute) plutôt que d'appeler la fonction header
    }
}

# Vous avez crée 2 méthode, une pour l'ajout et une pour la modification d'un étudiant, pour alléger le controller, il aurait été préférable de déclarer une seule et même méthode permettant à la fois d'ajouter et de modifier un utilisateur, celà évite aussi la redondance et permet aussi de n'utiliser qu'un seul et unique template ;)
# if(isset($_POST) && !empty($_POST)) : cette condition n'est pas nécessaire, comme expliqué plus haut
# Pour la suppression d'un utilisatreur, il faut tout d'abord controller que l'id de l'utilisatreur transmit dans l'url correspond bien à un utilisateur en base de données, dans vous cas, vous supprimez directement sans faire ce controle, cela peut générer des erreurs, il faut d'abord s'assurer que l'utilisateur existe bien en BDD avant de le supprimer. Si l'étudiant existe, on le supprime et on redirige l'internaute vers le listing des étudiants sinon dans tous les autres cas on affiche un message d'erreur à l'utilisateur.
# la méthode findAll() peut être appelé directement par la classe, cela permet de supprimer une ligne de code, et oui nous sommes des fainéants les développeurs :)

# 6. Page Teacher
# Pour cette partie, vous avez utilisé le même procédé que pour la partie Student, je vous laisse relir mes commentaires ci-dessus.

# 6. Page Utilisateurs
# L'affichage des utilisateurs est correct mais le code peut-être optimisé comme dans les exemples enoncés ci-dessus pour la partie Student et Teacher.
# En revanche, l'ajout, la modification et la suppression restent à développer, mais vous pouvez facilement vous servir de la même procédure comme pour les autres entités.

# Dans chaque model pour les requêtes d'insertion/modification, pour parer aux injections SQL, il faut préparer la requête via la méthode prepare(), ensuite nous executons la méthode bindValue() qui permet d'enfermer chaque valeur saisie dans le formulaire dans des marqueurs nominatifs, de ce fait la requête SQL ne peut être hacker par une injection SQL.

# Toutes les routes ne sont pas protégées, il manque un contrôle d'autorisation, quel utilisateur peut avoir accès à quelle page admin/user

# Commentaire général :
# Beaucoup d'erreurs qui auraient pû être débugger facilement, pensez à bien contrôler et tester votre code au maximum.
# Le concept php orienté objet (MVC) est dans l'ensemble acquit.
# Vous êtes sur la bonne voie, persévérez ;)  







 
