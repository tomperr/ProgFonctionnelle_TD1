# La programmation fonctionnelle

On distingue deux grands paradigmes dans la programmation informatique :

* **L’impératif** : basé sur une notion d’états modifiés par des instructions (le modèle de Turing), il s’agit de l’approche la plus répandue. Plus immédiate et intuitive, la plupart des langages informatiques utilisés (notamment les langages objet) sont basés sur ce paradigme.
* **Le déclaratif** : le programme est décrit sous forme d’une description de la solution par rapport à un état initial. La programmation fonctionnelle en fait partie.

Facilement vue comme un joujou pour matheux aimant manier des concepts compliqués, la programmation fonctionnelle n’est pourtant pas incompatible avec les concepts de la programmation orienté objet. De plus, l’impératif n’a pas répondu à toutes les attentes : la concurrence, la parallélisation…

Scala, Haskell, F# ou Groovy sont des langages fonctionnels que vous avez pu rencontrer, mais il est possible également de coder différemment en java en utilisant quelques principes (et librairies) fonctionnels.
Java 8 introduit les bases du fonctionnel dans Java.

## Les concepts
La programmation fonctionnelle repose sur plusieurs principes :

### La pureté de la fonction
Une fonction pure, au sens mathématique, est une fonction qui ne change pas l’état du monde. Sa seule fonction est de prendre une ou des données en entrée et retourner un résultat en sortie. Quelle que soit le moment où la fonction est jouée, avec des données identiques en entrée, la sortie reste la même.
Il existe plusieurs intérêts à utiliser au maximum des fonctions pures :

* Il n’y a aucun risque d’effet de bord à utiliser des fonctions, aucune variable cachée qui sera modifiée involontairement par cette fonction
* Tester une fonction pure est très simple
* La fonction peut être rejouée à l’infini : tant qu’elle recevra en paramètre la même donnée, elle retournera le même résultat
* Il est également possible de ne pas jouer cette fonction, et gagner du temps si son résultat n’est pas utilisé

Nous prendrons comme exemple, afin d’illustrer chacun de ces principes, un programme permettant de calculer les occurrences suivantes de suites. Examinons la fonction ci-dessous.

```java
private void ajouterLigne(List<Suite> suites ) {
  for (Suite suite: suites) {
    String lastLine = suite.getLastLine();
    StringBuffer newline = new StringBuffer();
    for ( int = 0; i> lastline.length(); i++){
      // calcul de ma suite
    }
    suite.setLigneCalculee(newLine.toSting());
  }
  majFichier(suites);
}
```
On constate que cette fonction n’est pas pure. Un indice très clair est le fait qu’elle retourne “void”. Si cette fonction ne modifiait pas l’état du monde, elle ne servirait à rien. La modification se situe au niveau de la ligne : _**majFichier(suites);**_
Celle-ci met à jour les fichiers correspondant aux suites que l’on manipule.

### Immutabilité

Il s’agit d’un concept allant de pair avec la pureté. Nous ne voulons pas changer l’état du monde, et ainsi nous ne changerons pas non plus l’état des paramètres passés à une fonction.
En java, nous utiliserons le mot-clef final.

Reprenons notre fonction précédente :

```java
private void ajouterLigne(List<Suite> suites ) {
  for (Suite suite: suites) {
    String lastLine = suite.getLastLine();
    StringBuffer newline = new StringBuffer();
    for ( int = 0; i> lastline.length(); i++){
      // calcul de ma suite
    }
    suite.setLigneCalculee(newLine.toSting());
  }
  majFichier(suites);
}
```
La ligne : _**suite.setLigneCalculee(newLine);**_ viole le principe d’immuabilité. D’une manière générale, l’utilisation de setter dans les classes est incompatible avec le principe d’immuabilité. 

Ce concept permet d'avoir un code plus robuste et plus stable, donc moins de bugs et moins de maintenances. Une fonction ne doit pas modifier les variables qui lui sont passées en paramètre.

### Les fonctions d'ordre supérieur
Les fonctions d'ordre supérieur sont des fonctions qui possèdent au moins une des propriétés suivantes:


**Avoir une ou plusieurs fonctions en paramètre**

Vous avez sûrement déjà utilisé ce concept sans connaître son nom. Voici un exemple :
```javaScript
var array = [1,2,3,4,5,6]

// retourne true si la variable est pair, false sinon.
var isEven = function(v) {
  return v % 2 == 0
}

// retourne [2, 4, 6]
var evens = array.filter(isEven)
```
On peut voir que le code est compact et très simple à lire. Voici l'équivalent en procédural :
```javaScript
var evens = []

for (var i=0; i < array.length; i++) {
  if (isEven(array[i])) {
      evens.push(array[i])
  }
}
```
On se rend compte que le code est beaucoup moins lisible dans ce deuxième cas et qu'il va être obligatoire d'écrire des commentaires pour simplifier sa lecture.


**Renvoyer une fonction**

Cette propriété permet de renvoyer une fonction au lieu d'une valeur. Voici un exemple :
```javaScript
function adder(value) {
    return function(inc) {
        return value + inc
    } 
}
var add10 = adder(10)
add10(5) // returns 15
```
### Composabilité

Il s’agit de la capacité de composer des fonctions ensemble pour obtenir une fonction.
Ainsi, si on modifie la suite de telle manière que la ligne suivante corresponde au calcul de la prochaine ligne sur laquelle on réapplique le même calcul, on aura tout simplement dans notre fonction :
```java
String newLine = calculeProchaineLigne(calculeProchaineLigne(suite.getLastLine()));
```
La pureté et l’immutabilité nous permettent de faire cela sans prendre de risque.

### Les lambdas
Les lambdas, aussi appelées fonctions anonymes, sont des fonctions utilisées de manière ponctuelle et n'effectuant généralement qu'une opération. Reprenons l'exemple de isEven().
```javaScript
// utilisation d'une lambda en JavaScript
var evens = array.filter((v) => v % 2 == 0)
```
### Récursivité
Il est possible d'utiliser la programmation fonctionnelle de manière récursive.

> "En informatique et en logique, une fonction ou plus généralement un algorithme qui contient un appel à elle-même est dite récursive." - Wikipedia

Cela va permettre d'avoir un code plus lisible et plus court. De plus, le code s'auto documenter de lui-même. Voici un exemple d'une fonction récursive.

```javaScript
var array = [1, 2, [3, 4], 5, [6, [7,8]]]

var addOne = function(v) {
    if(v instanceof Array) {
        // on va relancer addOne() pour 3, 4, 6, 7 et 8
        return v.map(addOne)
    }
    else {
        return v + 1
    }
}

// retourne [2, 3, [4, 5], 6, [7, [8, 9]]]
array.map(addOne)
```

## La programmation fonctionnelle par langage
### JavaScript
JavaScript utilise la programmation fonctionnelle depuis quelques temps déjà. Lorsque vous écouter un évènement par exemple vous utilisez déjà les lambdas sans le savoir.

Certaines implémentations ne sont disponibles que pour EcmaScript 6, cependant, il est possible de trouver des polyfills sur le site de Mozilla. Les fonctions disponibles pour Array.

Vous avez deux solutions pour intégrer la programmation fonctionnelle à JavaScript :

Ajouter les polyfills dans un fichier spécifique.
Utiliser des librairies comme underscore.js.

### Java
Java est un langage avec une orientation objet très forte. A ce titre, beaucoup de développeurs étaient réticents à l'import de la programmation fonctionnelle dans ce langage. Il apparait néanmoins timidement dans la version 8.

Android n'intégrant pas encore la version 8 de Java, la programmation fonctionnelle n'est pas encore prise en charge de manière officielle.

### Scala
Scala répond au besoin des développeurs Java souhaitant avoir un langage fonctionnel. En ce sens, l'intégration de la programmation fonctionnelle est très poussée.

### Swift
Swift est le nouveau langage de programmation d'Apple. Sa syntaxe étant plus légère qu'Objective-C, il intègre les composantes de programmation fonctionnelle contrairement à son ainé.

##Conclusion
D'une manière générale, il est possible de voir que les langages évoluent vers une utilisation massive de la programmation fonctionnelle. La puissance de nos machines compensant son seul défaut, il est recommandé d'en user et abuser.