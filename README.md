# Lab-SQL-Injections

## C'est quoi les injections SQL ?

Les injections SQL sont un groupe de méthodes d'exploitation de faille de sécurité d'une application communiquant avec
une base de données SQL.


Ils  permettent entre autres à un utilisateur malveillant d'interagir avec les requêtes envoyées par cette application.

Cette interaction survient la plupart du temps par  l'entrée de morceaus de requêtes dans les divers champs de l'application.
Une application vulnérable exécutera la requête compomise  causant des dommages irréversibles.

L'individu malveillant pourra entre autre : 
- **Extraire des données utilisateur (noms, adresses, numeros de cartes de crédit)**
- **Contourner l'authentification (Se connecter sur les comptes d'autres utilisateurs voir de l'administrateur)**
- **Modifier ou Supprimer des tables , voir l'entiereté de la base de donnée.**



## Exemple

Considérons ce  [site](https://demo.testfire.net/) vulnérable fourni par [IBM](https://fr.wikipedia.org/wiki/IBM) comme un exemple.


![altaro_login_page](https://user-images.githubusercontent.com/90383672/194561607-fe6cfa95-8a26-4e85-8f25-9612fcc3bb8c.png)


<sub>
les variables Username et Password contienent les entrés de lutilisateur:
</sub>


```
UserName = getRequestString("azerty");
Password = getRequestString("1234");
```

<sub>
Le mot de passe est généralement hashé par mesure de sécurité.
</sub>





La requête derriere est telle:



```sql
SELECT id FROM Users WHERE name = ' azerty ' AND password = ' Hashed(1234)';

```

<br/>

Si le ```UserName``` existe et que le ```Password``` lui correspond  , un ```id``` est renvoyé, le compte existe , sinon les entrées sont érronnés.


<br/>

Un individu malveillant 
Pourrai entré dans les champs du ```UserName```  un [**or payload**](https://github.com/payloadbox/sql-injection-payload-list#sql-injection-auth-bypass-payloads)



![hecker_or_payload](https://user-images.githubusercontent.com/90383672/194573864-a36c285e-193c-44d8-9f56-e88b8c12d094.png)

Et la requête envoyée a la base de donnée sera donc :
```sql
SELECT id FROM Users WHERE name = ' admin' or '1' = '1 ' AND password = ' Hashed(Password)';
```

L'utilisateur sera connecté sur le compte de l'```admin``` s'il existe et si *'1' = '1'* , meme si le ```Password``` est erroné.



![Drawing](https://user-images.githubusercontent.com/90383672/194575336-9e9a7eed-8177-46f3-8b34-9a10f229ae5e.png)



## Demo


Voici une démo d'une injection SQL avec différents types de payloads

[![Watch the video](https://i.imgur.com/9XL00Cyl.png)](https://youtu.be/vt5fpE0bzSY)




## Comment s'en premunir ?


![imageedit_0_4430024721](https://user-images.githubusercontent.com/90383672/194895183-f760f745-a6b9-43d4-a007-d600c0e2ef1e.png)



Les attaques du type Injection quoiqu'étant l'une des failles de sécurité web les plus connues ont été  classée troisième en 2021 par 
L' [**Open Web Application Security Project**](https://owasp.org/Top10/fr/A03_2021-Injection/) .


***94 % des applications ont été testées pour une forme d'injection avec un taux d'incidence maximal de 19 %, un taux d'incidence moyen de 3 % et 274 000 occurrences***.


**L'une des meilleurs options permettant d'éviter les attaques par injection SQL,
quelle que soit la base de données que vous utilisez, est de séparer
les les variables entrées par l'utilisteur de la syntaxe de notre requête  SQL, afin que les variables restent des variables et ne soient jamais interprétées comme des commandes par l'analyseur SQL.**

Faire transiter les requêtes par des API saines comme :
 - [PDO](https://fr.wikipedia.org/wiki/PHP_Data_Objects)
 - [MySQLi](https://fr.wikipedia.org/wiki/MySQLi)


### Exemple de requête avec PDO


![PDO_shot_avoid_sqli](https://user-images.githubusercontent.com/90383672/194904896-9304811a-64fe-4233-8c06-aead8424b35e.png)

### Expliquation

L'instruction SQL que vous transmettez à ```prepare``` est analysée et compilée par le serveur de base de données. En spécifiant des paramètres (comme le ```?``` ci dessus), vous indiquez au moteur de base de données où vous souhaitez filtrer.

Ensuite, lorsque vous appelez ```execute```, l'instruction préparée est combinée avec les valeurs de paramètre que vous spécifiez.

L'avantage avec cette technique est que les valeurs des paramètres sont combinées avec la requête compilée( Ce qui se fait au moment ou vous appellez ```prepare```), et non avec les chaines de charactere la constituant.

L'injection SQL fonctionnant  en incitant le script à inclure des chaînes  malveillantes lorsqu'il crée la requête SQL à envoyer à la base de données, en envoyant le SQL réel séparément des paramètres, tous les paramètres que vous envoyez lors de l'utilisation d'une instruction préparée seront simplement traités comme des chaînes de charactere (bien que le moteur de base de données puisse effectuer une certaine optimisation afin que les paramètres puissent également se retrouver sous forme de nombres).


### **or payload sur une requêtes avec PDO


![login_page_forum](https://user-images.githubusercontent.com/90383672/194907445-5ac8562f-e82b-48cf-8515-e3367450b564.png)


Un **or payload** , un **comment payload** ou n'importe quelle autre **syntaxe du type Injection SQL** entrée dans les champs d'une page de connexion utilisant des requêtes via PDO sera infructueuse.

![test_sqli_inputs_shot](https://user-images.githubusercontent.com/90383672/194907858-720f0e3d-116f-4f82-a8ce-55dabb7ba8e2.png)


![response_sqli_test](https://user-images.githubusercontent.com/90383672/194908082-75fbf903-9526-4bec-b4cb-dcb6db6ff447.png)


L' OWASP fournit plusieurs techniques permettant d'éviter les [injections SQL](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html) et toutes sortes  d'[attaques du type Injection](https://owasp.org/Top10/fr/A03_2021-Injection/) et de sécuriser vos applications.



### Notes et références:
 - [payloadbox/sql-injection-payload-list](https://github.com/payloadbox/sql-injection-payload-list)
 - [OWASP Top 10:2021](https://owasp.org/Top10/fr/A03_2021-Injection/)
 - [SQL Injection Tutorial: Learn with Example](https://www.guru99.com/learn-sql-injection-with-practical-example.html)
 - [Wikipedia :Injection SQL](https://fr.wikipedia.org/wiki/Injection_SQL)
 - [w3schools :SQL Injection](https://www.w3schools.com/sql/sql_injection.asp)
 - [SQL Injections are scary!! (hacking tutorial for beginners)](https://youtu.be/2OPVViV-GQk)
