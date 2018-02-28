# TP 4  

## Objectif:

1. Comprendre les mécanismes des Servlet
2. Réaliser une application Web en utilisant Combinant JPA et les Servlet
3. Comprendre les principes d’une architecture Rest
4. Comprendre les bénéfices d’un framework comme Jersey

## Sujet 

L’objectif de ce projet est de continuer le développement d’une application type
réseau social permettant de comparer sa consommation électrique avec ses amis, ses voisins,etc. dans la lignée de opower.

## Etape 1 Chargement des dépendances

Tout d’abord, nous avons modifié notre fichier pom.xml pour ajouter les dépendances nécessaires

```
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
	<version>3.0.1</version>
	<scope>provided</scope>
</dependency>
```
## Insertion et visualisation dés données en utilisant les  Servlets

Nous avons crée 2 formulaires

## Création des Servlets
Précédemment, nous avons mis  «/UserInfo » et « /HomeInfo » comme action des formulaires. Ces deux url référencie  l’url de notre  servlet soit en GET ou bien en POST, les servlets jouent un rôle de contrôleurs dans notre application 

Donc nous avons crée une servlet pour chaque formulaire comme ceci:

### HomeInfo.java - UserInfo.java

Dans la partie doGet,

on crée une variable de type collection pour récupérer et afficher les données qui sont dans notre base
```
Collection<Home> result = manager.createQuery("Select h From Home h", Home.class).getResultList();
    out.println("<HTML>\n<BODY>\n" + "<H1>Recapitulatif des informations sur les maisons</H1>\n" + "<UL>\n");
		for (Home h : result) {
		out.println("<LI> maison : " + h+ "\n");	
		}
		out.println("</UL>\n" + "</BODY></HTML>");
```

Dans la partie doPost,

Ici on crée une maison à partir de données envoyées dans le formulaire home.html
```
		this.ManagerSingleton = ManagerSingleton.getInstance();
		EntityManager manager = this.ManagerSingleton.getManager();
		EntityTransaction tx = manager.getTransaction();
		tx.begin();
		Home home = new Home();
		home.setNombre_de_piece(Long.valueOf(request.getParameter("piece")));
		home.setTaille(Long.valueOf(request.getParameter("taille")));
		manager.persist(home);
		tx.commit();
		out.println("Enregistrement effectué");

```

Pareil pour la servlet UserInfo.

### ManagerSingleton.java

Nous avons créé ce fichier pour eviter de créer la connexion à la base de données plusieurs fois pour chaque action de l'utilisateur.
```
private ManagerSingleton() {
		factory = Persistence.createEntityManagerFactory("example");
		manager = factory.createEntityManager();
		m=this;
	}
	public static ManagerSingleton getInstance() {
		if (m == null) { // Premier appel
	         m = new ManagerSingleton();
			}
		return m;
	}
```

## Etape 4 Les architectures Rest

Pour la partie Rest nous avons créé deux fichiers, HomeService.java et PersonService.java

HomeService.java nous permet de récupérer une maison spécifique,toutes les maisons, modifier, supprimer et ajouter une maisons dans la base de données en format json.

UserInfo.java nous permet de récupérer une personne spécifique,toutes les personnes,ajouter un utilisateur dans la base de données en format en json, pour la suppression d'une personne ça ne fonctionne pas à cause des clés étrangères d’autres classes. 

Nous avons utilisé les methodes POST,GET et DELETE
```
Par défaut quand on met  localhost :8080/rest/home la méthode getAllHomes()est appelé qui est une méthode get ($GET) permettant de récupérer tous les maisons sous format Json « @produces(MediaType.APPLICATION8JSON)».
Quand on rajoute un id après home comme : localhost :8080/rest/home/1 la méthode getHomeById est appelé pour récupérer une maison ayant un id égal 1.
L’annotation @Pathparam("id") précise bien que l’id passé en url sera bien le paramètre de la fonction et aussi ici on récupère du json grâce à  @produces(MediaType.APPLICATION8JSON).
La troisième  est de type delete @DELETE pour supprimer une maison ayant un id précis
La dernière est de type POST @POST pour ajouter une maison, dans ce cas on doit fournir à la méthode addHome  une maison sous format JSON  « @consumes(MediaType.APPLICATION8JSON)».
```

Pareil pour UserInfo.java .
 ## API services' URIs
 
 Resource used : ```application/json```
 Base URI : ```localhost:9002/rest/```


| Method     | URL | Action   |
 | :------- | ----: | :---: |
 | GET    | /person  |  display all the people   |
 | GET    | /person/{id}  |  display one specific person with the given {id} parameter   |
 | POST    | /person  |  create a new person   |
 | DELETE    | /person/{id}  |  delete one specific person with the given {id} parameter   |
 | GET    | /home  |  display all the homes   |
 | GET    | /home/{id}  |  display one specific home with the given {id} parameter   |
 | DELETE    | /home/{id}  |  delete one specific home with the given {id} parameter   |






