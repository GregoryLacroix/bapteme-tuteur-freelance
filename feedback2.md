# Premièrement, toute la partie concernant l'ajout/modification/suppression/list d'un user/admin reste à développer ainsi que la partie authentification/déconnexion.
# Si vous rencontrez des difficultés sur ces différentes parties, n'hésitez pas à revenir vers moi si vous avez besoin de plus d'explications.

# Pour la déclaration des routes, attention à bien respecter les convention de nommage :
# teachers_list ===> teachers-list
# Vous avez déclarer les routes direcetement dans le fichier index.php, pour que le fichier ne soit pas trop lourd, il aurait été préférable de déclarer les routes dans un fichier externe et de l'inclure dans le fichier index.php

# Vous avez déclarer la navigation dans le fichier header.tpl.php, il aurait été préférable de la déclarer dans un fichier externe afin de bien séparer les différentes parties du site.
# Vous avez déclarer des conditions pour affecter la classe 'active' en fonction de l'url donnée, il y a une erreur sur le lien des étudiants, un guillemets " en trop, je vous laisse vérfier ;)

# Partie Teacher
# Vous avez nommé le controller TeachersController, le 's' n'est pas nécessaire, simplement TeacherController.
# Pour la partie listing des profs, le rendu est correct, atention toutefois au nommage des variables, il faut que ce soit le plus cohérents possible, ex : $teachersList ====> $items / $data.
# Et mieux indenter votre code, afin que ce soit le plus lisible possible.
# Les parties insertion/modification/suppression restent à développer.

# Partie Student
# Vous avez nommé le controller StudentsController, le 's' n'est pas nécessaire, simplement StudentController.
# Pour la partie listing des profs, le rendu est correct, attention toutefois au nommage des variables, il faut que ce soit le plus cohérents possible, ex : $studentsList ====> $items / $data.
# Pour le listing des étudiants, vous avez déclaré une colonne 'titre' qui n'est pas déclaré dans le model Student mais Teacher, l'affichage du professeur lié à l'étudiant est manquant.
# Les parties insertion/modification/suppression restent à développer.

# Toutes les routes ne sont pas protégées, il manque un contrôle d'autorisation, quel utilisateur peut avoir accès à quelle page admin/user

# Commentaire général : 
# C'est un bon début mais le projet reste inachevé en grande partie.
# Ne vous découragez pas ! continuez à pratiquer au maximum, persevérez ;)

