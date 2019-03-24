# clean-code-javascript

## Table des matières

1.  [Introduction](#introduction)
2.  [Variables](#variables)
3.  [Functions](#functions)
4.  [Objects and Data Structures](#objects-and-data-structures)
5.  [Classes](#classes)
6.  [SOLID](#solid)
7.  [Testing](#testing)
8.  [Concurrency](#concurrency)
9.  [Error Handling](#error-handling)
10. [Formatting](#formatting)
11. [Comments](#comments)
12. [Translation](#translation)

## Introduction

![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](http://www.osnews.com/images/comics/wtfm.jpg)

Principes d'ingénierie logicielle, de l'ouvrage de Robert C. Martin
[_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adapté pour JavaScript. Ce n'est pas un guide de style. C'est un guide pour la production de logiciels
[lisibles, réutilisables et refactorables](https://github.com/ryanmcdermott/3rs-of-software-architecture) en JavaScript.

Tous les principes énoncés ici ne doivent pas être strictement suivis, et encore moins seront universellement acceptés. Ce sont des lignes directrices et rien de plus, mais ce sont des lignes codifiées au fil de nombreuses années d'expérience collective par les auteurs de _Clean Code_.

Notre métier de génie logiciel a un peu plus de 50 ans et nous apprenons encore beaucoup. Lorsque l'architecture logicielle est aussi ancienne que l'architecture elle-même, nous aurons peut-être des règles plus strictes à suivre. Pour l’instant, laissez ces instructions servir de point de repère pour évaluer la qualité du code JavaScript que vous et votre équipe produisez.

Encore une chose: savoir que cela ne fera pas de vous un meilleur développeur de logiciels et travailler avec eux pendant de nombreuses années ne signifie pas que vous ne ferez pas d'erreurs. Chaque morceau de code commence par un premier brouillon, comme l'argile humide qui prend sa forme finale. Enfin, nous corrigeons les imperfections lorsque nous les examinons avec nos pairs. Ne vous battez pas pour les premières ébauches à améliorer. Battez le code à la place!

## **Variables**

### Utilisez des noms de variables significatifs et prononçables

**Mal:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Bien:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ retour au sommet](#table-des-matières)**

### Utilisez le même vocabulaire pour le même type de variable

**Mal:**

```javascript
obtenirInformationDeUtilisateur();
obtenirDonéesDuClient();
obtenirFicheDuClient();
```

**Bien:**

```javascript
obtenirUtiilisateur();
```

**[⬆ retour au sommet](#table-des-matières)**

### Utilisez des noms interrogeables

Nous lirons plus de code que nous n'en écrirons jamais. Il est important que le code nous
écrire est lisible et consultable. Par _pas_ nommer les variables qui finissent par
avoir un sens pour comprendre notre programme, nous avons blessé nos lecteurs.
Rendez vos noms consultables. Des outils comme
[buddy.js](https://github.com/danielstjules/buddy.js) et
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
peuvent aider à identifier des constantes non nommées.

**Mal:**

```javascript
// Qu'est-ce que c'est que 86400000?
setTimeout(misÀFeu, 86400000);
```

**Bien:**

```javascript
// Déclarez-les comme des constantes nommées capitalisées.
const MILLISECONDES_PAR_JOUR = 86400000;

setTimeout(misÀFeu, MILLISECONDES_PAR_JOUR);
```

**[⬆ retour au sommet](#table-des-matières)**

### Utiliser des variables explicatives

**Mal:**

```javascript
const adresse = "One Infinite Loop, Cupertino 95014";
const codePostalVilleRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
enregistrerCodePostalVille(
  adresse.match(codePostalVilleRegex)[1],
  adresse.match(codePostalVilleRegex)[2]
);
```

**Bien:**

```javascript
const adresse = "One Infinite Loop, Cupertino 95014";
const codePostalVilleRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, ville, codePostal] = adresse.match(codePostalVilleRegex) || [];
enregistrerCodePostalVille(ville, codePostal);
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter la cartographie mentale

Explicite est meilleur qu'implicite.

**Mal:**

```javascript
const endroits = ["Austin", "New York", "San Francisco"];
endroits.forEach(l => {
  faireDesTrucs();
  faireAutreChoses();
  // ...
  // ...
  // ...
  // Attendez, à quoi sert "l`"?
  dispatch(l);
});
```

**Bien:**

```javascript
const endroits = ["Austin", "New York", "San Francisco"];
endroits.forEach(endroit => {
  faireDesTrucs();
  faireAutreChoses();
  // ...
  // ...
  // ...
  dispatch(endroit);
});
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne pas ajouter de contexte inutile

Si votre nom de classe / d’objet vous dit quelque chose, ne répétez pas cela dans votre
Nom de variable.

**Mal:**

```javascript
const Voiture = {
  marqueDeVoiture: "Honda",
  modèleDeVoiture: "Accord",
  couleurDeVoiture: "Bleue"
};

function peindreLaVoiture(voiture) {
  voiture.couleurDeVoiture = "Rouge";
}
```

**Bien:**

```javascript
const Voiture = {
  marque: "Honda",
  modèle: "Accord",
  couleur: "Bleue"
};

function peindreLaVoiture(voiture) {
  voiture.couleur = "Rouge";
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Utiliser des arguments par défaut au lieu de court-circuiter ou conditionnels

Les arguments par défaut sont souvent plus propres que les courts-circuits. Sachez que si vous
utilisez-les, votre fonction ne fournira que les valeurs par défaut pour `undefined`
arguments. Autres valeurs "fausses" telles que `''`, `" ",` `false`, `null`, `0` et
`NaN`, ne sera pas remplacé par une valeur par défaut.

**Mal:**

```javascript
function créerUneMicroBrasserie(nom) {
  const nomDeBrasserie = nom || "Hipster Brew Co.";
  // ...
}
```

**Good:**

```javascript
function créerUneMicroBrasserie(nom = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ retour au sommet](#table-des-matières)**

## **Fonctions**

### Arguments de fonction (idéalement 2 ou moins)

Limiter le nombre de paramètres de fonction est extrêmement important car
facilite le test de votre fonction. Avoir plus de trois prospects à un
explosion combinatoire où vous devez tester des tonnes de cas différents avec
chaque argument séparé.

Un ou deux arguments est le cas idéal, et trois devraient être évités si possible.
Quelque chose de plus que cela devrait être consolidé. Habituellement, si vous avez
plus de deux arguments, votre fonction essaie de faire trop. Dans les cas
où il n'est pas, la plupart du temps un objet de niveau supérieur suffira en tant que
argument.

Puisque JavaScript vous permet de créer des objets à la volée, sans trop de classe
passe-partout, vous pouvez utiliser un objet si vous vous trouvez nécessitant un
beaucoup d'arguments.

Pour rendre évidentes les propriétés attendues par la fonction, vous pouvez utiliser le logiciel ES2015 / ES6.
syntaxe de déstructuration. Cela présente quelques avantages:

1.  Quand quelqu'un regarde la signature de la fonction, on voit immédiatement ce qui
         les propriétés sont utilisées.
2.  La destruction clone également les valeurs primitives spécifiées de l'argument
         objet passé dans la fonction. Cela peut aider à prévenir les effets secondaires. Remarque:
         les objets et les tableaux qui sont déstructurés à partir de l'objet argument ne sont PAS
         cloné.
3.  Les linters peuvent vous avertir des propriétés inutilisées, ce qui serait impossible
         sans déstructuration.

**Mal:**

```javascript
function créerUnMenu(titre, corps, boutonTexte, annulable) {
  // ...
}
```

**Bien:**

```javascript
function créerUnMenu({ titre, corps, boutonTexte, annulable }) {
  // ...
}

créerUnMenu({
  titre: "Foo",
  corps: "Bar",
  boutonTexte: "Baz",
  annulable: true
});
```

**[⬆ retour au sommet](#table-des-matières)**

### Les fonctions doivent faire une chose

C’est de loin la règle la plus importante en génie logiciel. Quand fonctionne
faire plus d’une chose, il est plus difficile de composer, de tester et de raisonner.
Lorsque vous pouvez isoler une fonction à une seule action, elles peuvent être refactorisées.
facilement et votre code lira beaucoup plus propre. Si vous ne prenez rien d'autre de
ce guide autre que celui-ci, vous serez en avance sur de nombreux développeurs.

**Mal:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const enregistrementDuClient = database.lookup(client);
    if (enregistrementDuClient.estActif()) {
      email(client);
    }
  });
}
```

**Good:**

```javascript
function emailClientsActifs(clients) {
  clients.filter(estClientActif).forEach(email);
}

function estClientActif(client) {
  const enregistrementDuClient = database.lookup(client);
  return enregistrementDuClient.estActif();
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Les noms de fonction doivent dire ce qu'ils font

**Mal:**

```javascript
function ajouterÀLaDate(date, mois) {
  // ...
}

const date = new Date();

// Il est difficile de dire à partir du nom de la fonction ce qui est ajouté
ajouterÀLaDate(date, 1);
```

**Bien:**

```javascript
function ajouterMoisÀDate(mois, date) {
  // ...
}

const date = new Date();
ajouterMoisÀDate(1, date);
```

**[⬆ retour au sommet](#table-des-matières)**

### Les fonctions ne devraient être qu'un seul niveau d'abstraction

Lorsque vous avez plus d'un niveau d'abstraction, votre fonction est généralement
faire trop. La scission des fonctions conduit à la réutilisation et à la facilité
essai.

**Mal:**

```javascript
function mieuxAnalyserAlternatifJS(code) {
  const REGEXES = [
    // ...
  ];

  const déclarations = code.split(" ");
  const jetons = [];
  REGEXES.forEach(REGEX => {
    déclarations.forEach(déclaration => {
      // ...
    });
  });

  const ast = [];
  jetons.forEach(jeton => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**Bien:**

```javascript
function mieuxAnalyserAlternatifJS(code) {
  const jetons = tokenize(code);
  const arbreDeSyntaxe = analyser(jetons);
  arbreDeSyntaxe.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const déclarations = code.split(" ");
  const jetons = [];
  REGEXES.forEach(REGEX => {
    déclarations.forEach(déclaration => {
      jetons.push(/* ... */);
    });
  });

  return jetons;
}

function analyser(jetons) {
  const arbreDeSyntaxe = [];
  jetons.forEach(jeton => {
    arbreDeSyntaxe.push(/* ... */);
  });

  return arbreDeSyntaxe;
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Supprimez le code en double

Faites de votre mieux pour éviter le code en double. Dupliquer le code est mauvais parce que
signifie qu'il y a plus d'un endroit pour changer quelque chose si vous avez besoin de changer
un peu de logique.

Imaginez que vous dirigiez un restaurant et que vous gardiez une trace de votre inventaire: tous vos
tomates, oignons, ail, épices, etc. Si vous avez plusieurs listes qui
vous gardez ce sur, alors tous doivent être mis à jour lorsque vous servez un plat avec
tomates en eux. Si vous n'avez qu'une seule liste, il n'y a qu'un seul endroit pour mettre à jour!

Vous avez souvent un code en double parce que vous en avez deux ou plus légèrement
choses différentes, qui partagent beaucoup en commun, mais leurs différences vous forcent
d'avoir deux ou plusieurs fonctions distinctes qui font beaucoup de choses identiques. Enlever
dupliquer le code signifie créer une abstraction capable de gérer cet ensemble de
choses différentes avec une seule fonction / module / classe.

Il est essentiel de bien comprendre l’abstraction, c’est pourquoi vous devriez suivre
Principes SOLID énoncés dans la section _Classes_. Les mauvaises abstractions peuvent être
pire que le code en double, alors soyez prudent! Cela dit, si vous pouvez faire
une bonne abstraction, faites-le! Ne te répète pas, sinon tu te retrouveras
mettre à jour plusieurs endroits chaque fois que vous voulez changer une chose.

**Mal:**

```javascript
function afficherLaListeDesDéveloppeurs(développeurs) {
  développeurs.forEach(développeur => {
    const salairePrévu = développeur.calculerSalairePrévu();
    const expérience = développeur.obtenirExpérience();
    const lienGithub = développeur.obtenirLienGithub();
    const données = {
      salairePrévu,
      expérience,
      lienGithub
    };

    rendre(données);
  });
}

function afficherLaListeDesGestionnaires(gestionnaires) {
  gestionnaires.forEach(gestionnaire => {
    const salairePrévu = gestionnaire.calculerSalairePrévu();
    const expérience = gestionnaire.obtenirExpérience();
    const portfolio = gestionnaire.obtenireProjetsMBA();
    const données = {
      salairePrévu,
      expérience,
      portfolio
    };

    rendre(données);
  });
}
```

**Bien:**

```javascript
function afficherLaListeDesEmployés(employés) {
  employés.forEach(employé => {
    const salairePrévu = employés.calculerSalairePrévu();
    const expérience = employés.obtenirExpérience();

    const données = {
      salairePrévu,
      expérience
    };

    switch (employés.type) {
      case "gestionnaire":
        data.portfolio = employés.obtenireProjetsMBA();
        break;
      case "développeur":
        data.lienGithub = employés.obtenirLienGithub();
        break;
    }

    rendre(données);
  });
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Définir des objets par défaut avec Object.assign

**Mal:**

```javascript
const configMenu = {
  titre: null,
  corps: "Bar",
  boutonTexte: null,
  annulable: true
};

function créerUnMenu(config) {
  config.titre = config.titre || "Foo";
  config.corps = config.corps || "Bar";
  config.boutonTexte = config.boutonTexte || "Baz";
  config.annulable = config.annulable !== undefined ? config.annulable : true;
}

créerUnMenu(configMenu);
```

**Good:**

```javascript
const configMenu = {
  titre: "Commande",
  // L'utilisateur n'a pas inclus la clé 'corps'
  boutonTexte: "Envoyer",
  annulable: true
};

function créerUnMenu(config) {
  config = Object.assign(
    {
      titre: "Foo",
      corps: "Bar",
      boutonTexte: "Baz",
      annulable: true
    },
    config
  );

  // config est maintenant égal à: {titre: "Commande", corps: "Bar", boutonTexte - "Envoyer", annulable: true}
  // ...
}

créerUnMenu(configMenu);
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne pas utiliser les drapeaux comme paramètres de fonction

Les drapeaux indiquent à l'utilisateur que cette fonction fait plus d'une chose. Les fonctions devraient faire une chose. Répartissez vos fonctions si elles suivent différents chemins de code basés sur un booléen.

**Mal:**

```javascript
function créerUnFichier(nom, temp) {
  if (temp) {
    fs.create(`./temp/${nom}`);
  } else {
    fs.create(nom);
  }
}
```

**Bien:**

```javascript
function créerUnFichier(nom) {
  fs.create(nom);
}

function créerUnFichierTemp(nom) {
  créerUnFichier(`./temp/${nom}`);
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter les effets secondaires (partie 1)

Une fonction produit un effet secondaire si elle fait autre chose que prendre une valeur dans
et renvoyer une ou plusieurs valeurs. Un effet secondaire pourrait être l'écriture dans un fichier,
modifier une variable globale ou transférer accidentellement tout votre argent à un
étranger.

Maintenant, vous devez avoir des effets secondaires dans un programme à l'occasion. Comme le précédent
Par exemple, vous devrez peut-être écrire dans un fichier. Ce que tu veux faire c'est
centraliser où vous faites cela. Ne pas avoir plusieurs fonctions et classes
qui écrivent dans un fichier particulier. Avoir un service qui le fait. Seul et l'unique.

Le principal est d'éviter les pièges courants tels que le partage d'état entre objets
sans aucune structure, en utilisant des types de données mutables qui peuvent être écrits par n'importe quoi,
et ne pas centraliser où vos effets secondaires se produisent. Si vous pouvez faire cela, vous voudrez
être plus heureux que la grande majorité des autres programmeurs.

**Mal:**

```javascript
// Variable globale référencée par la fonction suivante.
// Si nous avions une autre fonction qui utilisait ce nom, maintenant ce serait un tableau et cela pourrait le casser.
let nom = "Gavish Barosee";

function diviséEnPrénomEtNom() {
  nom = nom.split(" ");
}

diviséEnPrénomEtNom();

console.log(nom); // ['Gavish', 'Barosee'];
```

**Bien:**

```javascript
function diviséEnPrénomEtNom(nom) {
  return nom.split(" ");
}

const nom = "Gavish Barosee";
const nouveauNom = splitIntoFirstAndLastName(nom);

console.log(nom); // 'Gavish Barosee';
console.log(nouveauNom); // ['Gavish', 'Barosee'];
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter les effets secondaires (partie 2)

En JavaScript, les primitives sont passées par valeur et les objets / tableaux par
référence. Dans le cas d'objets et de tableaux, si votre fonction effectue un changement
dans un tableau de panier, par exemple, en ajoutant un article à acheter,
alors toute autre fonction utilisant ce tableau `cart` sera affectée par cette
une addition. Cela peut être formidable, mais cela peut aussi être mauvais. Imaginons un mauvais
situation:

L'utilisateur clique sur le bouton "Acheter", qui appelle une fonction "achat" qui
génère une requête réseau et envoie le tableau `cart` au serveur. Parce que
d’une mauvaise connexion réseau, la fonction `purchase` doit continuer à réessayer la
demande. Maintenant, qu'en est-il si, dans l'intervalle, l'utilisateur clique accidentellement sur "Ajouter au panier"
bouton sur un élément dont ils ne veulent pas avant le début de la demande réseau?
Si cela se produit et que la demande de réseau commence, alors cette fonction d'achat
enverra l'élément ajouté accidentellement car il a une référence à un achat
cart array que la fonction `addItemToCart` a modifiée en ajoutant un élément indésirable
article.

Une bonne solution serait pour le `addItemToCart` de toujours cloner le`cart`,
éditez-le et retournez le clone. Cela garantit qu'aucune autre fonction ne soit
conserver une référence du panier d'achat sera affecté par tout changement.

Deux mises en garde à mentionner à cette approche:

1.  Il peut arriver que vous souhaitiez réellement modifier l’objet d’entrée,
         mais lorsque vous adoptez cette pratique de programmation, vous constaterez que ces cas
         sont assez rares. La plupart des choses peuvent être refactorisées sans effets secondaires!

2.  Le clonage de gros objets peut être très coûteux en termes de performances. Heureusement,
         ce n'est pas un gros problème dans la pratique car il y a
    [grandes libraries](https://facebook.github.io/immutable-js/) qui permettent
         ce type d’approche de programmation doit être rapide et ne nécessite pas autant de mémoire que
         ce serait à vous de cloner manuellement des objets et des tableaux.

**Mal:**

```javascript
const AjouterUnArticleAuPanier = (panier, article) => {
  cart.push({ article, date: Date.now() });
};
```

**Bien:**

```javascript
const AjouterUnArticleAuPanier = (panier, article) => {
  return [...panier, { article, date: Date.now() }];
};
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne pas écrire dans les fonctions globales

Les globaux polluants sont une mauvaise pratique en JavaScript car vous pourriez vous heurter à un autre
bibliothèque et l'utilisateur de votre API serait absolument inutile jusqu'à ce qu'ils obtiennent un
exception en production. Pensons à un exemple: et si vous vouliez
étendre la méthode Array native de JavaScript pour avoir une méthode `diff` qui pourrait
montrer la différence entre deux tableaux? Vous pouvez écrire votre nouvelle fonction
`Array.prototype`, mais il pourrait entrer en conflit avec une autre bibliothèque qui a essayé
faire la même chose. Et si cette autre bibliothèque utilisait simplement `diff` pour trouver
la différence entre le premier et le dernier élément d'un tableau? C'est pourquoi ça
Il serait bien mieux d’utiliser simplement les classes ES2015 / ES6 et d’étendre simplement le `Array` global.

**Mal:**

```javascript
Array.prototype.diff = function diff(tableauDeComparaison) {
  const hash = new Set(tableauDeComparaison);
  return this.filter(elem => !hash.has(elem));
};
```

**Bien:**

```javascript
class SuperArray extends Array {
  diff(tableauDeComparaison) {
    const hash = new Set(tableauDeComparaison);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Privilégier la programmation fonctionnelle à la programmation impérative

JavaScript n'est pas un langage fonctionnel comme Haskell, mais il a
une saveur fonctionnelle à elle. Les langages fonctionnels peuvent être plus propres et plus faciles à tester.
Privilégiez ce style de programmation quand vous le pouvez.

**Mal:**

```javascript
const sortieDuProgrammeur = [
  {
    nom: "Oncle Bobby",
    linesDeCode: 500
  },
  {
    nom: "Suzie Q",
    linesDeCode: 1500
  },
  {
    nom: "Jimmy Gosling",
    linesDeCode: 150
  },
  {
    nom: "Gracie Hopper",
    linesDeCode: 1000
  }
];

let sortieTotale = 0;

for (let i = 0; i < sortieDuProgrammeur.length; i++) {
  sortieTotale += sortieDuProgrammeur[i].linesDeCode;
}
```

**Bien:**

```javascript
const sortieDuProgrammeur = [
  {
    nom: "Oncle Bobby",
    linesDeCode: 500
  },
  {
    nom: "Suzie Q",
    linesDeCode: 1500
  },
  {
    nom: "Jimmy Gosling",
    linesDeCode: 150
  },
  {
    nom: "Gracie Hopper",
    linesDeCode: 1000
  }
];

const sortieTotale = sortieDuProgrammeur.reduce(
  (linesTotal, sortie) => linesTotal + sortie.linesDeCode,
  0
);
```

**[⬆ retour au sommet](#table-des-matières)**

### Encapsuler des conditions

**Mal:**

```javascript
if (fsm.state === "chercher" && estVide(listNode)) {
  // ...
}
```

**Bien:**

```javascript
function devraitMontrerSpinner(fsm, listNode) {
  return fsm.state === "chercher" && estVide(listNode);
}

if (devraitMontrerSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter les conditionnels négatifs

**Mal:**

```javascript
function DOMNodeNestPasPrésent(node) {
  // ...
}

if (!DOMNodeNestPasPrésent(node)) {
  // ...
}
```

**Bien:**

```javascript
function estDOMNodePresent(node) {
  // ...
}

if (estDOMNodePresent(node)) {
  // ...
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter les conditionnels

Cela semble être une tâche impossible. En entendant cela, la plupart des gens disent:
"comment suis-je censé faire quoi que ce soit sans une déclaration`if`? " La réponse est que
vous pouvez utiliser le polymorphisme pour accomplir la même tâche dans de nombreux cas. La deuxième
la question est généralement, "bien c'est génial, mais pourquoi voudrais-je faire cela?" le
answer est un précédent concept de code propre que nous avons appris: une fonction ne devrait faire que
une chose. Quand vous avez des classes et des fonctions qui ont des instructions `if`, vous
dites à votre utilisateur que votre fonction fait plus d’une chose. Rappelles toi,
faites juste une chose.

**Mal:**

```javascript
class Avion {
  // ...
  obtenirAltitudeDeCroisière() {
    switch (this.type) {
      case "777":
        return this.obtenirAltitudeMax() - this.obtenirLeNombreDePassagers();
      case "Air Force One":
        return this.obtenirAltitudeMax();
      case "Cessna":
        return this.obtenirAltitudeMax() - this.obtenirDépensesDeCarburant();
    }
  }
}
```

**Good:**

```javascript
class Avion {
  // ...
}

class Boeing777 extends Avion {
  // ...
  obtenirAltitudeDeCroisière() {
    return this.obtenirAltitudeMax() - this.obtenirLeNombreDePassagers();
  }
}

class AirForceOne extends Avion {
  // ...
  obtenirAltitudeDeCroisière() {
    return this.obtenirAltitudeMax();
  }
}

class Cessna extends Avion {
  // ...
  obtenirAltitudeDeCroisière() {
    return this.obtenirAltitudeMax() - this.obtenirDépensesDeCarburant();
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter la vérification de type (partie 1)

JavaScript n'est pas typé, ce qui signifie que vos fonctions peuvent accepter n'importe quel type d'argument.
Parfois, vous êtes mordu par cette liberté et cela devient tentant de le faire
vérification de type dans vos fonctions. Il y a plusieurs façons d'éviter d'avoir à le faire.
La première chose à considérer est des API cohérentes.

**Mal:**

```javascript
function voyagerAuTexas(véhicule) {
  if (véhicule instanceof vélo) {
    véhicule.pédale(this.localisationActuelle, new Location("texas"));
  } else if (véhicule instanceof voiture) {
    véhicule.conduire(this.localisationActuelle, new Location("texas"));
  }
}
```

**Bien:**

```javascript
function voyagerAuTexas(véhicule) {
  véhicule.déplacer(this.localisationActuelle, new Location("texas"));
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Éviter la vérification de type (partie 2)

Si vous travaillez avec des valeurs primitives de base telles que des chaînes et des entiers,
et vous ne pouvez pas utiliser le polymorphisme mais vous ressentez toujours le besoin de vérifier le type,
vous devriez envisager d'utiliser TypeScript. C'est une excellente alternative à la normale
JavaScript, car il fournit du typage statique en plus du JavaScript standard
syntaxe. Le problème avec la vérification manuelle du code JavaScript normal est que
bien le faire nécessite tellement de verbiage supplémentaire que le faux "type-safety" que vous obtenez
ne compense pas la lisibilité perdue. Gardez votre JavaScript propre, écrivez
de bons tests et de bonnes critiques de code. Sinon, faites tout cela mais avec
TypeScript (qui, comme je l'ai dit, est une excellente alternative!).

**Mal:**

```javascript
function combiner(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Doit être de type String ou Number");
}
```

**Bien:**

```javascript
function combiner(val1, val2) {
  return val1 + val2;
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne pas trop optimiser

Les navigateurs modernes effectuent beaucoup d’optimisation au moment de l’exécution. Beaucoup de
Parfois, si vous optimisez, vous perdez simplement votre temps. [Il y a de bons
Ressources](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
pour voir où l'optimisation fait défaut. Ciblez-les entre-temps, jusqu'à ce que
ils sont fixes s'ils peuvent l'être.

**Mal:**

```javascript
// Sur les anciens navigateurs, chaque itération avec `list.length` non mis en cache serait coûteuse
// à cause du recalcul de `list.length`. Dans les navigateurs modernes, cela est optimisé.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Bien:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Supprimer le code mort

Le code mort est aussi grave qu'un code en double. Il n'y a aucune raison de le garder dans
votre base de code. Si ce n'est pas appelé, éliminez-le! Il sera toujours en sécurité
dans votre historique de version si vous en avez toujours besoin.

**Mal:**

```javascript
function ancienModuleDeDemande(url) {
  // ...
}

function nouveauModuleDeDemande(url) {
  // ...
}

const req = nouveauModuleDeDemande;
traqueurInventaire("pommes", req, "www.inventory-awesome.io");
```

**Bien:**

```javascript
function nouveauModuleDeDemande(url) {
  // ...
}

const req = nouveauModuleDeDemande;
traqueurInventaire("pommed", req, "www.inventory-awesome.io");
```

**[⬆ retour au sommet](#table-des-matières)**

## **Objets et Structures De Données**

### Utilisez des getters et des setters

Utiliser des getters et des setters pour accéder aux données sur des objets pourrait être mieux que simplement
chercher une propriété sur un objet. "Pourquoi?" vous pourriez demander. Eh bien, voici un
liste non organisée des raisons pour lesquelles:

- Lorsque vous voulez faire plus que d'obtenir une propriété d'objet, vous n'avez pas
     pour rechercher et changer tous les accesseurs de votre base de code.
- Facilite l'ajout d'une validation lors d'un `set`.
- Encapsule la représentation interne.
- Facile à ajouter la journalisation et la gestion des erreurs lors de l'obtention et la configuration.
- Vous pouvez charger paresseux les propriétés de votre objet, disons l'obtenir d'un
     serveur.

**Mal:**

```javascript
function faireUnCompteBancaire() {
  // ...

  return {
    bilan: 0
    // ...
  };
}

const compte = faireUnCompteBancaire();
compte.bilan = 100;
```

**Bien:**

```javascript
function faireUnCompteBancaire() {
  // celui-ci est privé
  let bilan = 0;

  // un "getter", rendu public via l'objet renvoyé ci-dessous
  function obtenirLeBilan() {
    return bilan;
  }

  // un "setter", rendu public via l'objet retourné ci-dessous
  function fixerLeBilan(montante) {
    // ... valider avant de mettre à jour le solde
    bilan = montante;
  }

  return {
    // ...
    obtenirLeBilan,
    fixerLeBilan
  };
}

const compte = faireUnCompteBancaire();
compte.fixerLeBilan(100);
```

**[⬆ retour au sommet](#table-des-matières)**

### Faire en sorte que les objets aient des membres privés

Ceci peut être accompli par des fermetures (pour ES5 et moins).

**Mal:**

```javascript
const Employé = function(nom) {
  this.nom = nom;
};

Employé.prototype.obtenirNom = function obtenirNom() {
  return this.nom;
};

const employé = new Employé("John Doe");
console.log(`Nom de l'employé: ${employé.obtenirNom()}`); // Nom de l'employé: John Doe
delete employé.nom;
console.log(`Nom de l'employé: ${employé.obtenirNom()}`); // Nom de l'employé: undefined
```

**Bien:**

```javascript
function faireEmployé(nom) {
  return {
    obtenirNom() {
      return nom;
    }
  };
}

const employé = faireEmployé("John Doe");
console.log(`Nom de l'employé: ${employé.obtenirNom()}`); // Nom de l'employé: John Doe
delete employé.nom;
console.log(`Nom de l'employé: ${employé.obtenirNom()}`); // Nom de l'employé: undefined
```

**[⬆ retour au sommet](#table-des-matières)**

## **Classes**

### Préférez les classes ES2015 / ES6 aux fonctions simples de ES5

Il est très difficile d'obtenir un héritage de classe, une construction et une méthode lisibles.
définitions pour les classes ES5 classiques. Si vous avez besoin d'héritage (et soyez conscient
que vous ne pourriez pas), alors préférez les classes ES2015 / ES6. Cependant, préférez les petites fonctions aux
cours jusqu’à ce que vous ayez besoin d’objets plus grands et plus complexes.

**Mal:**

```javascript
const Animale = function(âge) {
  if (!(this instanceof Animale)) {
    throw new Error("Instancier Animal avec `new`");
  }

  this.âge = âge;
};

Animale.prototype.bouger = function bouger() {};

const Mammifère = function(age, couleurDefourrure) {
  if (!(this instanceof Mammifère)) {
    throw new Error("Instancier Mammifère avec `new`");
  }

  Animale.appel(this, âge);
  this.couleurDefourrure = couleurDefourrure;
};

Mammifère.prototype = Object.create(Animale.prototype);
Mammifère.prototype.constructor = Mammifère;
Mammifère.prototype.naissanceVivante = function naissanceVivante() {};

const Humain = function(âge, couleurDefourrure, langueParlée) {
  if (!(this instanceof Humain)) {
    throw new Error("Instancier Humain avec `new`");
  }

  Mammifère.appel(this, âge, couleurDefourrure);
  this.langueParlée = langueParlée;
};

Humain.prototype = Object.create(Mammifère.prototype);
Humain.prototype.constructor = Humain;
Humain.prototype.parler = function parler() {};
```

**Bien:**

```javascript
class Animale {
  constructor(âge) {
    this.âge = âge;
  }

  bouger() {
    /* ... */
  }
}

class Mammifère extends Animale {
  constructor(âge, couleurDefourrure) {
    super(âge);
    this.couleurDefourrure = couleurDefourrure;
  }

  naissanceVivante() {
    /* ... */
  }
}

class Humain extends Mammal {
  constructor(âge, couleurDefourrure, languageSpoken) {
    super(âge, couleurDefourrure);
    this.languageSpoken = languageSpoken;
  }

  parler() {
    /* ... */
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Utiliser la méthode de chaînage

Ce modèle est très utile en JavaScript et vous le voyez dans de nombreuses bibliothèques telles que
comme jQuery et Lodash. Cela permet à votre code d’être expressif et moins bavard.
Pour cette raison, dis-je, utilisez la méthode de chaînage et jetez un coup d'œil à la propreté de votre code
sera. Dans vos fonctions de classe, retournez simplement `this` à la fin de chaque fonction,
et vous pouvez y chaîner d’autres méthodes de classe.

**Mal:**

```javascript
class Voiture {
  constructor(marque, modèle, couleur) {
    this.marque = marque;
    this.modèle = modèle;
    this.couleur = couleur;
  }

  fixerLaMarque(marque) {
    this.marque = marque;
  }

  fixerLaModèle(modèle) {
    this.modèle = modèle;
  }

  fixerLaCouleur(couleur) {
    this.couleur = couleur;
  }

  enregistrer() {
    console.log(this.marque, this.modèle, this.couleur);
  }
}

const voiture = new Voiture("Ford", "F-150", "rouge");
voiture.fixerLaCouleur("rose");
voiture.enregistrer();
```

**Bien:**

```javascript
class Voiture {
  constructor(marque, modèle, couleur) {
    this.marque = marque;
    this.modèle = modèle;
    this.couleur = couleur;
  }

  fixerLaMarque(marque) {
    this.marque = marque;
    // NOTE: Renvoyer ceci pour chaîner
    return this;
  }

  fixerLaModèle(modèle) {
    this.modèle = modèle;
    // NOTE: Renvoyer ceci pour chaîner
    return this;
  }

  fixerLaCouleur(couleur) {
    this.couleur = couleur;
    // NOTE: Renvoyer ceci pour chaîner
    return this;
  }

  enregistrer() {
    console.log(this.marque, this.modèle, this.couleur);
    // NOTE: Returning this for chaining
    return this;
  }
}

const voiture = new Voiture("Ford", "F-150", "rouge")
  .fixerLaCouleur("rose")
  .enregistrer();
```

**[⬆ retour au sommet](#table-des-matières)**

### Préfère la composition à l'héritage

Comme l'a déclaré célèbre dans [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns) par la Gang of Four, vous devriez préférer la composition à l'héritage lorsque vous le pouvez. Il y a beaucoup de
bonnes raisons d'utiliser l'héritage et beaucoup de bonnes raisons d'utiliser la composition.
Le point principal de cette maxime est que si votre esprit opte instinctivement pour
héritage, essayez de penser si la composition pourrait mieux modéliser votre problème. Dans certaines
cas il peut.

Vous vous demandez peut-être alors "quand devrais-je utiliser l'héritage?" Il
dépend de votre problème actuel, mais ceci est une liste décente des cas où l'héritage
est plus logique que la composition:

1.  Votre héritage représente une relation "est-une" et non une relation "a-une"
         relation (Humain-> Animal contre Utilisateur-> UserDetails).
2.  Vous pouvez réutiliser le code des classes de base (les humains peuvent se déplacer comme tous les animaux).
3.  Vous souhaitez apporter des modifications globales aux classes dérivées en modifiant une classe de base.
         (Changer la dépense calorique de tous les animaux quand ils bougent).

**Mal:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ back to top](#table-des-matières)**

## **SOLID**

### Single Responsibility Principle (SRP)

As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the amount of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify
a piece of it, it can be difficult to understand how that will affect other
dependent modules in your codebase.

**Bad:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Good:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ back to top](#table-des-matières)**

### Open/Closed Principle (OCP)

As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**Bad:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Good:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ back to top](#table-des-matières)**

### Liskov Substitution Principle (LSP)

This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class and child class can be used interchangeably without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**Bad:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ back to top](#table-des-matières)**

### Interface Segregation Principle (ISP)

JavaScript doesn't have interfaces so this principle doesn't apply as strictly
as others. However, it's important and relevant even with JavaScript's lack of
type system.

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." Interfaces are implicit contracts in JavaScript because of
duck typing.

A good example to look at that demonstrates this principle in JavaScript is for
classes that require large settings objects. Not requiring clients to setup
huge amounts of options is beneficial, because most of the time they won't need
all of the settings. Making them optional helps prevent having a
"fat interface".

**Bad:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**Good:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ back to top](#table-des-matières)**

### Dependency Inversion Principle (DIP)

This principle states two essential things:

1.  High-level modules should not depend on low-level modules. Both should
    depend on abstractions.
2.  Abstractions should not depend upon details. Details should depend on
    abstractions.

This can be hard to understand at first, but if you've worked with AngularJS,
you've seen an implementation of this principle in the form of Dependency
Injection (DI). While they are not identical concepts, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

As stated previously, JavaScript doesn't have interfaces so the abstractions
that are depended upon are implicit contracts. That is to say, the methods
and properties that an object/class exposes to another object/class. In the
example below, the implicit contract is that any Request module for an
`InventoryTracker` will have a `requestItems` method.

**Bad:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Good:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ back to top](#table-des-matières)**

## **Testing**

Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a great testing framework, you also need to use a
[good coverage tool](http://gotwarlost.github.io/istanbul/).

There's no excuse to not write tests. There are [plenty of good JS test frameworks](http://jstherightway.org/#testing-tools), so find one that your team prefers.
When you find one that works for your team, then aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single concept per test

**Bad:**

```javascript
import assert from "assert";

describe("MakeMomentJSGreatAgain", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MakeMomentJSGreatAgain("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MakeMomentJSGreatAgain("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MakeMomentJSGreatAgain("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Good:**

```javascript
import assert from "assert";

describe("MakeMomentJSGreatAgain", () => {
  it("handles 30-day months", () => {
    const date = new MakeMomentJSGreatAgain("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MakeMomentJSGreatAgain("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MakeMomentJSGreatAgain("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ back to top](#table-des-matières)**

## **Concurrency**

### Use Promises, not callbacks

Callbacks aren't clean, and they cause excessive amounts of nesting. With ES2015/ES6,
Promises are a built-in global type. Use them!

**Bad:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", response.body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Good:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(response => {
    return writeFile("article.html", response);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ back to top](#table-des-matières)**

### Async/Await are even cleaner than Promises

Promises are a very clean alternative to callbacks, but ES2017/ES8 brings async and await
which offer an even cleaner solution. All you need is a function that is prefixed
in an `async` keyword, and then you can write your logic imperatively without
a `then` chain of functions. Use this if you can take advantage of ES2017/ES8 features
today!

**Bad:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-promise";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(response => {
    return writeFile("article.html", response);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-promise";

async function getCleanCodeArticle() {
  try {
    const response = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", response);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}
```

**[⬆ back to top](#table-des-matières)**

## **Error Handling**

Thrown errors are a good thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping function execution on the current stack, killing the
process (in Node), and notifying you in the console with a stack trace.

### Don't ignore caught errors

Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error to the console (`console.log`)
isn't much better as often times it can get lost in a sea of things printed
to the console. If you wrap any bit of code in a `try/catch` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**Bad:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Don't ignore rejected promises

For the same reason you shouldn't ignore caught errors
from `try/catch`.

**Bad:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Good:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ back to top](#table-des-matières)**

## **Formatting**

Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are [tons of tools](http://standardjs.com/rules.html) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

For things that don't fall under the purview of automatic formatting
(indentation, tabs vs. spaces, double vs. single quotes, etc.) look here
for some guidance.

### Use consistent capitalization

JavaScript is untyped, so capitalization tells you a lot about your variables,
functions, etc. These rules are subjective, so your team can choose whatever
they want. The point is, no matter what you all choose, just be consistent.

**Bad:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ back to top](#table-des-matières)**

### Function callers and callees should be close

If a function calls another, keep those functions vertically close in the source
file. Ideally, keep the caller right above the callee. We tend to read code from
top-to-bottom, like a newspaper. Because of this, make your code read that way.

**Bad:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Good:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ back to top](#table-des-matières)**

## **Comments**

### Only comment things that have business logic complexity.

Comments are an apology, not a requirement. Good code _mostly_ documents itself.

**Bad:**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ back to top](#table-des-matières)**

### Don't leave commented out code in your codebase

Version control exists for a reason. Leave old code in your history.

**Bad:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**

```javascript
doStuff();
```

**[⬆ back to top](#table-des-matières)**

### Don't have journal comments

Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**Bad:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ back to top](#table-des-matières)**

### Avoid positional markers

They usually just add noise. Let the functions and variable names along with the
proper indentation and formatting give the visual structure to your code.

**Bad:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ back to top](#table-des-matières)**

## Translation

This is also available in other languages:

- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Spanish**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanish**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamese**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonesia**:
  [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italian**:
  [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)

**[⬆ back to top](#table-des-matières)**
