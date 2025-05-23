---
title: Ajouter une fonctionnalité de connexion à votre application Django
description: Ce guide explique comment intégrer Auth0 à une application Python Django, à l’aide de la trousse SDK Authlib.
interactive:  true
files:
 - files/webappexample/templates/index
 - files/webappexample/settings
 - files/webappexample/urls
 - files/webappexample/views
github:
  path: 01-Login
locale: fr-CA
---

# Ajouter une fonctionnalité de connexion à votre application Django


<p>Auth0 vous permet d’ajouter l’authentification et de pouvoir accéder aux informations relatives au profil de l’utilisateur dans votre application. Ce guide explique comment intégrer Auth0 à une application Python <a href="https://www.djangoproject.com/" target="_blank" rel="noreferrer noopener">Django</a>, à l’aide de la trousse SDK <a href="https://authlib.org/" target="_blank" rel="noreferrer noopener">Authlib</a>.</p><p></p>

## Configuration d’Auth0


<p>Pour utiliser les services Auth0, vous devez avoir une application installée dans Auth0 Dashboard. L’application Auth0 est l’endroit où vous allez configurer le fonctionnement de l’authentification pour le projet que vous développez.</p><h3>Configurer une application</h3><p>Utilisez le sélecteur interactif pour créer une nouvelle application Auth0 ou sélectionner une application existante qui représente le projet avec lequel vous souhaitez effectuer l’intégration. Dans Auth0, chaque application se voit attribuer un identifiant client unique alphanumérique que votre code d’application utilisera pour appeler les API Auth0 via la trousse SDK.</p><p>Tous les paramètres que vous configurez à l’aide de ce guide de démarrage rapide seront automatiquement mis à jour pour votre application dans le <a href="https://manage.auth0.com/dashboard/us/auth0-dsepaid/" target="_blank" rel="noreferrer noopener">Tableau de bord</a>, qui est l’endroit où vous pourrez gérer vos applications à l’avenir.</p><p>Si vous préférez explorer une configuration complète, consultez plutôt un exemple d’application.</p><h3>Configurer les URL de rappel</h3><p>Une URL de rappel est une URL intégrée dans votre application vers laquelle vous souhaitez qu’Auth0 redirige les utilisateurs après leur authentification. Si elle n’est pas définie, les utilisateurs ne seront pas redirigés vers votre application après s’être connectés.</p><p><div class="alert-container" severity="default"><p>Si vous suivez notre exemple de projet, définissez cette URL comme suit : <code>http://localhost:3000</code><code>/callback</code>.</p></div></p><h3>Configuration des URL de déconnexion</h3><p>Une URL de déconnexion est une URL intégrée dans votre application vers laquelle vous souhaitez qu’Auth0 redirige les utilisateurs après leur déconnexion. Si elle n’est pas définie, les utilisateurs ne pourront pas se déconnecter de votre application et recevront un message d’erreur.</p><p><div class="alert-container" severity="default"><p>Si vous suivez notre exemple de projet, définissez cette URL comme suit : <code>http://localhost:3000</code>.</p></div></p>

## Installer les dépendances


<p>Pour cette intégration, vous ajouterez plusieurs dépendances de bibliothèque, telles qu’Authlib. Créez un fichier <code>requirements.txt</code> dans votre répertoire de projet et incluez ce qui suit :</p><p><pre><code>authlib ~= 1.0

django ~= 4.0

python-dotenv ~= 0.19

requests ~= 2.27

</code></pre>

</p><p>Exécutez la commande suivante depuis votre interface système pour accéder à ces dépendances :</p><p><code>pip install -r requirements.txt</code></p>

## Configurez votre fichier .env.


<p>Ensuite, créez un fichier <code>.env</code> dans votre répertoire de projet. Ce fichier contiendra les clés de vos clients et d’autres détails de configuration.</p><p><pre><code>AUTH0_CLIENT_ID=${account.clientId}

AUTH0_CLIENT_SECRET=${account.clientSecret}

AUTH0_DOMAIN=${account.namespace}

</code></pre>

</p>

## Création d’une application


<p>Si vous avez déjà une application Django installée, passez à l’étape suivante. Pour un nouveau projet d’application, exécutez la commande suivante :</p><p><code>django-admin startproject webappexample</code></p><p>Placez-vous dans le nouveau dossier du projet :</p><p><code>cd webappexample</code></p>

## Mettre à jour settings.py {{{ data-action="code" data-code="webappexample/settings.py" }}}


<p>Ouvrez le fichier <code>webappexample/settings.py</code> pour examiner les valeurs <code>.env</code>.</p><p>En haut du fichier, ajoutez les importations <code>os</code> et <code>dotenv</code>.</p><p>Ensuite, sous la définition <code>BASE_DIR</code>, ajoutez la variable <code>TEMPLATE_DIR</code>.</p><p>Ensuite, trouvez la variable <code>TEMPLATES</code> et mettez à jour la valeur <code>DIRS</code> pour y ajouter notre chaîne <code>TEMPLATE_DIR</code>. Cette action détermine le chemin des fichiers modèles, que vous créerez à une étape future. Ne modifiez aucun autre élément de ce tableau.</p><p>À la fin de ce fichier, ajoutez le code pour charger la configuration d’Auth0.</p>

## Configurez votre application {{{ data-action="code" data-code="webappexample/views.py#1:18" }}}


<p>Pour commencer à créer votre application, ouvrez le fichier <code>webappexample/views.py</code> dans votre IDE.</p><p>Importez toutes les bibliothèques nécessaires à votre application.</p><p>Vous pouvez maintenant configurer Authlib pour gérer l’authentification de votre application avec Auth0.</p><p>Découvrez les options de configuration possibles pour la méthode Authlib’s OAuth <code>register()</code> à partir de <a href="https://docs.authlib.org/en/latest/client/frameworks.html#using-oauth-2-0-to-log-in" target="_blank" rel="noreferrer noopener">leur documentation.</a></p>

## Configurer vos gestionnaires de route {{{ data-action="code" data-code="webappexample/views.py#20:52" }}}


<p>Dans cet exemple, vous allez ajouter quatre routes pour votre application : connexion, callback, déconnexion et index.</p><ul><li><p><code>login</code> – Lorsque les visiteurs de votre application se rendent sur la route <code>/login</code> ils seront redirigés vers Auth0 pour commencer le processus d’authentification.</p></li><li><p><code>callback</code> – Après la connexion de vos utilisateurs à Auth0, ceux-ci reviendront à votre application à la route <code>/callback</code>. Cette route enregistre la session de l’utilisateur et permet de ne plus avoir à se reconnecter lorsqu’il revient.</p></li><li><p><code>logout</code> – La route <code>/logout</code> permet aux utilisateurs de se déconnecter de votre application. Cette route efface la session de l’utilisateur dans votre application et redirige vers le point de terminaison de déconnexion d’Auth0 pour s’assurer que la session n’est plus enregistrée. Ensuite, l’application redirige l’utilisateur vers votre route d’accueil.</p></li><li><p><code>index</code> – La route d’accueil affichera les détails de l’utilisateur authentifié ou permettra aux visiteurs de se connecter.</p></li></ul><p></p>

## Enregistrer vos routes {{{ data-action="code" data-code="webappexample/urls.py" }}}


<p>Remplacez le contenu de votre fichier <code>webappexample/urls.py</code> par le code à droite pour vous connecter à ces nouvelles routes.</p><p>Cela dirigera les routes <code>/login</code>, <code>/callback</code>, <code>/logout</code> et <code>/</code> vers les gestionnaires appropriés.</p>

## Ajouter des modèles {{{ data-action="code" data-code="webappexample/templates/index.html" }}}


<p>Vous allez ensuite créer un fichier modèle utilisé dans la route de la page d’accueil.</p><p>Créez un nouveau sous-répertoire dans le dossier <code>webappexample</code> nommé <code>templates</code>, et créez un fichier <code>index.html</code>.</p><p>Le fichier <code>index.html</code> contiendra un code modèle pour afficher les informations de l’utilisateur s’il est connecté, ou pour lui présenter un bouton de connexion s’il ne l’est pas.</p>

## Exécuter votre application


<p>Vous êtes prêts à exécuter votre application! À partir de votre Directory de projet, ouvrez un interface et utilisez :</p><p><pre><code>python3 manage.py migrate

python3 manage.py runserver 3000

</code></pre>

</p><p>Votre application devrait maintenant être prête à s’ouvrir à partir de votre navigateur à l’adresse <a href="http://localhost:3000" target="_blank" rel="noreferrer noopener">http://localhost:3000</a>.</p><p><div class="checkpoint">Django - Étape 10 - Exécuter votre application - Point de contrôle <div class="checkpoint-default"><p>Visitez <a href="http://localhost:3000/" target="_blank" rel="noreferrer noopener">http://localhost:3000</a> pour des raisons de vérification. Un bouton de connexion devrait vous permettre de vous connecter à Auth0, puis de revenir à votre application pour consulter les informations relatives à votre profil.</p></div>

  <div class="checkpoint-success"></div>

  <div class="checkpoint-failure"><p>Si votre application n’a pas démarré avec succès :</p><ul><li><p>Vérifiez les erreurs éventuelles dans la console.</p></li><li><p>Vérifiez que le domaine et l’identifiant client ont été importés correctement.</p></li><li><p>Vérifiez la configuration de votre locataire.</p></li></ul><p>Vous rencontrez toujours des problèmes? Consultez notre <a href="https://auth0.com/docs" target="_blank" >documentation</a> ou la <a href="https://community.auth0.com/" target="_blank" rel="noreferrer noopener">page de notre communauté</a> pour obtenir de l’aide.</p></div>

  </div></p>
