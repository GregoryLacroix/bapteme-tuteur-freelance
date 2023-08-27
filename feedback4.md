# Premièrement le système de routage ne fonctionne pas, pour une raison simple, vous n'avez pas créer de fichier .htaccess dans le dossier /public. Ce fichier va permettre de réecrire les URL dynamiquement, il est possible d'effectuer quasiment tous les types de réécriture d'URLs dont vous avez besoin. Pour plus d'information, vous pouvez consulter la documentation : https://httpd.apache.org/docs/trunk/fr/rewrite/intro.html.

# les paramètres de connexion de la base de données sont erronés, username = 'root' | password = '' (ou root pour MAC)

# Dans le fichier index.php, vous faites appel au controler \App\Controllers\ErrorController mais il n'est pas déclaré.

# Vous faites un héritage de la classe CoreController dans les classes StudentsController/TeachersController/UserController mais encore une fois ce controller n'est pas déclaré. vous faites appel à des méthodes de CoreController sans l'avoir déclaré, comment est-ce possible ?

# Vous avez créer une route en appelant le controller MainController et encore une fois, ce controller n'est pas déclaré.

# Vous avez déclaré l'ensemble des routes dans le fichier public/index.php, il serait judiceux de déclarer les routes dans un fichier externe (ex : app/routes.php) et de l'inclure directement dans le fichier public/index.php, ce qui permet d'avoir des fichiers moins lourd, plus clair et mieux organisé.

# Les parties header/nav/footer ne sont inclus dans aucun des templates/views. Il aurait juducieux de séparer toute les parties du site dans des fichiers externe et de les inclure dans chaque page. Il n'y a donc aucune mise en forme.




