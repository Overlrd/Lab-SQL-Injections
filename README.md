# Lab-SQL-Injections

## C'est quoi les injetions SQL ?

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





La requete derriere est telle:



```sql
SELECT id FROM Users WHERE name = ' azerty ' AND password = ' Hashed(1234)';

```

<br/>

Si le ```UserName``` existe et que le ```Password``` lui correspond  , un ```id``` est renvoyé, le compte existe , sinon les entrées sont érronnés.


<br/>

Un individu malveillant 
Pourrai entré dans les champs du ```UserName```  un [**or payload**](https://github.com/payloadbox/sql-injection-payload-list#sql-injection-auth-bypass-payloads)



![hecker_or_payload](https://user-images.githubusercontent.com/90383672/194573864-a36c285e-193c-44d8-9f56-e88b8c12d094.png)

Et la requete envoyée a la base de donnée sera donc :
```sql
SELECT id FROM Users WHERE name = ' admin' or '1' = '1 ' AND password = ' Hashed(Password)';
```

L'utilisateur sera connecté sur le compte de l'```admin``` s'il existe et si *'1' = '1'* , meme si le ```Password``` est erroné.



![Drawing](https://user-images.githubusercontent.com/90383672/194575336-9e9a7eed-8177-46f3-8b34-9a10f229ae5e.png)



## Demo
