
> Ceci est une traduction française de ce lien: https://github.com/ryanmcdermott/clean-code-javascript

## Table Des Matières

1.  [Introduction](#introduction)
2.  [Variables](#variables)
3.  [Fonctions](#fonctions)
4.  [Objets Et Structures De Données](#objets-et-structures-de-données)
5.  [Classes](#classes)
6.  [SOLID](#solid)
7.  [Testage](#testage)
8.  [Simultanéité](#simultanéité)
9.  [La Gestion Des Erreurs](#la-gestion-des-erreurs)
10. [Mise En Forme](#mise-en-forme)
11. [Commentaires](#commentaires)
12. [Traduction](#traduction)

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
const dateActuelle = moment().format("YYYY/MM/DD");
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
class Employé {
  constructor(nom, email) {
    this.nom = nom;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class DonnéesFiscalesDuEmployé extends Employé {
  constructor(ssn, salaire) {
    super();
    this.ssn = ssn;
    this.salaire = salaire;
  }

  // ...
}
```

**Bien:**

```javascript
class DonnéesFiscalesDuEmployé {
  constructor(ssn, salaire) {
    this.ssn = ssn;
    this.salaire = salaire;
  }

  // ...
}

class Employé {
  constructor(nom, email) {
    this.nom = nom;
    this.email = email;
  }

  définirDonnéesFiscales(ssn, salaire) {
    this.donnéesFiscales = new DonnéesFiscalesDuEmployé(ssn, salaire);
  }
  // ...
}
```

**[⬆ retour au sommet](#table-des-matières)**

## **SOLID**

### Principe de responsabilité unique (PRU)

Comme indiqué dans Clean Code, "Il ne devrait jamais y avoir plus d'une raison pour une classe
pour changer ". Il est tentant de confier une classe avec beaucoup de fonctionnalités, comme
lorsque vous ne pouvez emporter qu'une valise par vol. Le problème avec ceci est
que votre classe ne sera pas conceptuellement cohérente et cela lui donnera de nombreuses raisons
changer. Il est important de minimiser le nombre de fois que vous avez besoin de changer de classe.
C'est important car si trop de fonctionnalités sont dans une classe et que vous modifiez
un morceau de celui-ci, il peut être difficile de comprendre comment cela affectera les autres
modules dépendants dans votre base de code.

**Mal:**

```javascript
class ParamètresUtilisateur {
  constructor(utilisateur) {
    this.utilisateur = utilisateur;
  }

  changerParamètres(paramètres) {
    if (this.vérifierIdentifications()) {
      // ...
    }
  }

  vérifierIdentifications() {
    // ...
  }
}
```

**Bien:**

```javascript
class AuthentificationUtilisateur {
  constructor(utilisateur) {
    this.utilisateur = utilisateur;
  }

  vérifierIdentifications() {
    // ...
  }
}

class ParamètresUtilisateur {
  constructor(utilisateur) {
    this.utilisateur = utilisateur;
    this.auth = new AuthentificationUtilisateur(utilisateur);
  }

  changerParamètres(paramètres) {
    if (this.auth.vérifierIdentifications()) {
      // ...
    }
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Principe Ouvert / Fermé (POF)

Selon Bertrand Meyer, "les entités logicielles (classes, modules, fonctions,
etc.) devrait être ouvert pour extension, mais fermé pour modification. "Qu'est-ce que cela
signifie cependant? Ce principe stipule essentiellement que vous devez permettre aux utilisateurs de
ajouter de nouvelles fonctionnalités sans changer le code existant.

**Mal:**

```javascript
class AdaptateurAjax extends Adaptateur {
  constructor() {
    super();
    this.nom = "adaptateurAjax";
  }
}

class AdaptateurNode extends Adaptateur {
  constructor() {
    super();
    this.nom = "adaptateurNode";
  }
}

class RequêtementHttp {
  constructor(adaptateur) {
    this.adaptateur = adaptateur;
  }

  fetch(url) {
    if (this.adaptateur.nom === "adaptateurAjax") {
      return faireAppelAjax(url).then(réponse => {
        // transformer la réponse et le retour
      });
    } else if (this.adaptateur.nom === "adaptateurNode") {
      return faireAppelHttp(url).then(réponse => {
        //transformer la réponse et le retour
      });
    }
  }
}

function faireAppelAjax(url) {
  // demande et retour promesse
}

function faireAppelHttp(url) {
  // demande et retour promesse
}
```

**Bien:**

```javascript
class AdaptateurAjax extends Adaptateur {
  constructor() {
    super();
    this.nom = "adaptateurAjax";
  }

  demande(url) {
    // demande et retour promesse
  }
}

class AdaptateurNode extends Adaptateur {
  constructor() {
    super();
    this.nom = "adaptateurNode";
  }

  demande(url) {
    // demande et retour promesse
  }
}

class RequêtementHttp {
  constructor(adaptateur) {
    this.adaptateur = adaptateur;
  }

  rapporter(url) {
    return this.adaptateur.demande(url).then(réponse => {
      //transformer la réponse et le retour
    });
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Principe de substitution de Liskov (PSL)

C'est un terme effrayant pour un concept très simple. Il est formellement défini comme "Si S
est un sous-type de T, les objets de type T peuvent être remplacés par des objets de type S
(c’est-à-dire que les objets de type S peuvent se substituer aux objets de type T) sans modifier
des propriétés souhaitables de ce programme (exactitude, tâche effectuée,
etc.). "C’est une définition encore plus effrayante.

La meilleure explication à cela est si vous avez une classe parent et une classe enfant,
alors la classe de base et la classe enfant peuvent être utilisées de manière interchangeable sans obtenir
résultats incorrects. Cela peut encore être déroutant, alors jetons un coup d'oeil à la
exemple classique de Square-Rectangle. Mathématiquement, un carré est un rectangle, mais
si vous modélisez en utilisant la relation "est-une" via l'héritage, vous rapidement
avoir des problèmes.

**Mal:**

```javascript
class Rectangle {
  constructor() {
    this.largeur = 0;
    this.taille = 0;
  }

  définirLaCouleur(couleur) {
    // ...
  }

  rendre(surface) {
    // ...
  }

  définirLaLargeur(largeur) {
    this.largeur = largeur;
  }

  définirLaTaille(taille) {
    this.taille = taille;
  }

  avoireLaSurface() {
    return this.largeur * this.taille;
  }
}

class Carré extends Rectangle {
  définirLaLargeur(largeur) {
    this.largeur = largeur;
    this.taille = largeur;
  }

  définirLaTaille(taille) {
    this.largeur = taille;
    this.taille = taille;
  }
}

function rendreLesGrandsRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.définirLaLargeur(4);
    rectangle.définirLaTaille(5);
    const surface = rectangle.avoireLaSurface(); // BAD: renvoie 25 pour Square. Devrait être 20.
    rectangle.rendre(surface);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Carré()];
rendreLesGrandsRectangles(rectangles);
```

**Bien:**

```javascript
class Forme {
  définirLaCouleur(couleur) {
    // ...
  }

  rendre(surface) {
    // ...
  }
}

class Rectangle extends Forme {
  constructor(largeur, taille) {
    super();
    this.largeur = largeur;
    this.taille = taille;
  }

  avoireLaSurface() {
    return this.largeur * this.taille;
  }
}

class Carré extends Forme {
  constructor(longeur) {
    super();
    this.longeur = longeur;
  }

  getArea() {
    return this.longeur * this.longeur;
  }
}

function rendreLesGrandesFormes(formes) {
  formes.forEach(forme => {
    const surface = forme.avoireLaSurface();
    forme.rendre(surface);
  });
}

const formes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
rendreLesGrandesFormes(formes);
```

**[⬆ retour au sommet](#table-des-matières)**

### Principe de séparation des interfaces (ISP)

JavaScript n'a pas d'interface donc ce principe ne s'applique pas aussi strictement
comme d'autres. Cependant, il est important et pertinent même avec le manque de JavaScript de JavaScript.
système de type.

ISP déclare que "les clients ne doivent pas être obligés de dépendre d’interfaces qui
ils n'utilisent pas. "Les interfaces sont des contrats implicites en JavaScript en raison de
canard en tapant.

Un bon exemple à regarder qui démontre que ce principe en JavaScript est pour
les classes qui nécessitent des objets de paramètres volumineux. Ne nécessite pas de clients à configurer
d'énormes quantités d'options est bénéfique, car la plupart du temps, ils n'auront pas besoin
tous les paramètres. Les rendre facultatifs aide à éviter d'avoir un
"interface grasse".

**Mal:**

```javascript
class DOMTraverser {
  constructor(réglages) {
    this.réglages = réglages;
    this.installer();
  }

  installer() {
    this.rootNode = this.réglages.rootNode;
    this.moduleDAnimation.installer();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  moduleDAnimation() {} // La plupart du temps, nous n'avons pas besoin d'animer lorsque vous traversez.
  // ...
});
```

**Bien:**

```javascript
class DOMTraverser {
  constructor(réglages) {
    this.réglages = réglages;
    this.options = réglages.options;
    this.installer();
  }

  installer() {
    this.rootNode = this.réglages.rootNode;
    this.optionsInstallation();
  }

  setupOptions() {
    if (this.options.moduleDAnimation) {
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
    moduleDAnimation() {}
  }
});
```

**[⬆ retour au sommet](#table-des-matières)**

### Principe d'inversion de dépendance (DIP)

Ce principe énonce deux choses essentielles:

1.  Les modules de haut niveau ne doivent pas dépendre de modules de bas niveau. Les deux devraient
        dépend des abstractions.
2.  Les abstractions ne doivent pas dépendre des détails. Les détails devraient dépendre de
        abstractions.

Cela peut être difficile à comprendre au début, mais si vous avez travaillé avec AngularJS,
vous avez vu une mise en œuvre de ce principe sous la forme de dépendance
Injection (DI). Bien qu’ils ne soient pas des concepts identiques, DIP conserve un haut niveau de qualité.
modules de connaître les détails de ses modules de bas niveau et de les configurer.
Cela peut être accompli grâce à DI. Un énorme avantage de cela est qu'il réduit
le couplage entre les modules. Le couplage est un très mauvais schéma de développement car
cela rend votre code difficile à refactoriser.

Comme indiqué précédemment, JavaScript n'a pas d'interface, donc les abstractions
qui sont dépendantes sont des contrats implicites. C'est-à-dire les méthodes
et les propriétés qu'un objet / classe expose à un autre objet / classe. dans le
exemple ci-dessous, le contrat implicite est que tout module de demande pour un
`InventoryTracker` aura une méthode`requestItems`.

**Mal:**

```javascript
class DemandeurInventaire {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  demanderUnArticle(article) {
    // ...
  }
}

class SuiviInventaire {
  constructor(articles) {
    this.articles = articles;

    // BAD: Nous avons créé une dépendance sur une implémentation de requête spécifique.
    // On devrait juste avoir demanderUnArticles dépendent d'une méthode de requête: `request`
    this.demandeur = new DemandeurInventaire();
  }

  demanderDesArticles() {
    this.articles.forEach(article => {
      this.demandeur.demanderUnArticle(article);
    });
  }
}

const traqueurInventaire = new SuiviInventaire(["pommes", "bananes"]);
traqueurInventaire.demanderDesArticles();
```

**Bien:**

```javascript
class SuiviInventaire {
  constructor(articles, demandeur) {
    this.articles = articles;
    this.demandeur = demandeur;
  }

  demanderDesArticles() {
    this.articles.forEach(article => {
      this.demandeur.demanderUnArticle(article);
    });
  }
}

class DemandeurInventaireV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  demanderUnArticle(article) {
    // ...
  }
}

class DemandeurInventaireV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  demanderUnArticle(article) {
    // ...
  }
}

// En construisant nos dépendances à l'extérieur et en les injectant, on peut facilement
// substitue notre module de requête à un nouveau module utilisant WebSockets.
const traqueurInventaire = new SuiviInventaire(
  ["pommes", "bananes"],
  new DemandeurInventaireV2()
);
traqueurInventaire.demanderDesArticles();
```

**[⬆ retour au sommet](#table-des-matières)**

## **Testage**

Le test est plus important que l'expédition. Si vous n'avez pas de test ou un
quantité insuffisante, alors chaque fois que vous expédiez du code, vous ne serez pas sûr de
n'a rien casser. Décider de ce qui constitue un montant adéquat est en place
à votre équipe, mais avoir une couverture de 100% (tous les états et branches) est la
vous obtenez une très grande confiance et une tranquillité d'esprit développeur. Cela signifie que
en plus d'avoir un excellent framework de test, vous devez également utiliser un
[bon outil de couverture](http://gotwarlost.github.io/istanbul/).

Il n'y a aucune excuse pour ne pas écrire de tests. Il y a [beaucoup de bons frameworks de test JS](http://jstherightway.org/#testing-tools), Alors, trouvez-en un que votre équipe préfère.
Lorsque vous en trouvez un qui convient à votre équipe, essayez de toujours écrire des tests
pour chaque nouvelle fonctionnalité / module que vous introduisez. Si votre méthode préférée est
Test Driven Development (TDD), c’est génial, mais l’essentiel est de simplement
assurez-vous d'atteindre vos objectifs de couverture avant de lancer une fonctionnalité,
ou refactoriser un existant.

### Concept unique par test

**Mal:**

```javascript
import assert from "assert";

describe("Rendre MomentJS encore une fois génial", () => {
  it("gère les limites de la date", () => {
    let date;

    date = new RendreMomentJSEncoreUneFoisGénial("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new RendreMomentJSEncoreUneFoisGénial("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new RendreMomentJSEncoreUneFoisGénial("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Bien:**

```javascript
import assert from "assert";

describe("Rendre MomentJS encore une fois génial", () => {
  it("gère les mois de 30 jours", () => {
    const date = new RendreMomentJSEncoreUneFoisGénial("1/1/2015");
    date.ajouterLesJours(30);
    assert.equal("1/31/2015", date);
  });

  it("gère l'année bissextile", () => {
    const date = new RendreMomentJSEncoreUneFoisGénial("2/1/2016");
    date.ajouterLesJours(28);
    assert.equal("02/29/2016", date);
  });

  it("gère l'année non bissextile", () => {
    const date = new RendreMomentJSEncoreUneFoisGénial("2/1/2015");
    date.ajouterLesJours(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ retour au sommet](#table-des-matières)**

## **Simultanéité**

### Utilisez des promesses, pas des rappels

Les rappels ne sont pas propres et provoquent des quantités excessives d'imbrication. Avec ES2015 / ES6,
Les promesses sont un type global intégré. Utilise les!

**Mal:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (errDemande, réponse) => {
    if (errDemande) {
      console.error(errDemande);
    } else {
      writeFile("article.html", réponse.body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("Fichier écrit");
        }
      });
    }
  }
);
```

**Bien:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(réponse => {
    return writeFile("article.html", réponse);
  })
  .then(() => {
    console.log("Fichier écrit");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ retour au sommet](#table-des-matières)**

### Async / Await sont encore plus propres que Promises

Les promesses sont une alternative très propre aux callbacks, mais ES2017 / ES8 apporte async et attend
qui offrent une solution encore plus propre. Tout ce dont vous avez besoin est une fonction préfixée
dans un mot clé `async`, et vous pourrez alors écrire votre logique impérativement sans
une chaîne de fonctions "alors". Utilisez-le si vous pouvez tirer parti des fonctionnalités de ES2017 / ES8
aujourd'hui!

**Mal:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-promise";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(réponse => {
    return writeFile("article.html", réponse);
  })
  .then(() => {
    console.log("Fichier écrit");
  })
  .catch(err => {
    console.error(err);
  });
```

**Bien:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-promise";

async function obtenirUnArticleDeCodePropre() {
  try {
    const response = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", réponse);
    console.log("Fichier écrit");
  } catch (err) {
    console.error(err);
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

## **La Gestion des Erreurs**

Les erreurs jetées sont une bonne chose! Ils veulent dire que le runtime a réussi
identifié quand quelque chose dans votre programme a mal tourné et il laisse
vous savez en arrêtant l'exécution de la fonction sur la pile en cours, tuant le
processus (en nœud), et vous notifiant dans la console avec une trace de pile.

### Ne pas ignorer les erreurs interceptées

Ne rien faire avec une erreur interceptée ne vous donne pas la possibilité de réparer
ou réagir à cette erreur. Consigner l'erreur dans la console (`console.log`)
n'est pas beaucoup mieux que souvent, il peut se perdre dans une mer de choses imprimées
à la console. Si vous intégrez un morceau de code dans un `try / catch`, cela signifie que vous
pense qu'une erreur peut se produire là-bas et que vous devriez donc avoir un plan,
ou créer un chemin de code, pour quand il se produit.

**Mal:**

```javascript
try {
  fonctionQuiPourraitJeter();
} catch (error) {
  console.log(error);
}
```

**Bien:**

```javascript
try {
  fonctionQuiPourraitJeter();
} catch (error) {
  // Une option (plus bruyante que console.log):
  console.error(error);
  // Une autre option:
  notifierUtilisateurDuErreur(error);
  // Une autre option:
  signalerAuServiceDuErreur(error);
  // OU faire tous les trois!
}
```

### Ne pas ignorer les promesses rejetées

Pour la même raison, vous ne devez pas ignorer les erreurs interceptées
de `try / catch`.

**Mal:**

```javascript
getdata()
  .then(données => {
    fonctionQuiPourraitJeter(données);
  })
  .catch(error => {
    console.log(error);
  });
```

**Good:**

```javascript
getdata()
  .then(données => {
    fonctionQuiPourraitJeter(données);
  })
  .catch(error => {
    // Une option (plus bruyante que console.log):
    console.error(error);
    // Une autre option:
    notifierUtilisateurDuErreur(error);
    // Une autre option:
    signalerAuServiceDuErreur(error);
    // OU faire tous les trois!
  });
```

**[⬆ retour au sommet](#table-des-matières)**

## **Mise En Forme**

Le formatage est subjectif. Comme beaucoup de règles, il n’existe pas de solution simple et rapide.
règle que vous devez suivre. L'essentiel est de NE PAS ARGUER sur le formatage.
Il y a [des tonnes d'outils](http://standardjs.com/rules.html) pour automatiser cela.
Utilisez-en un! Discuter de la mise en forme est une perte de temps et d'argent pour les ingénieurs.

Pour les choses qui ne relèvent pas du formatage automatique
(indentation, tabulations vs espaces, guillemets doubles vs simples, etc.) regardez ici
pour des conseils.

### Utilisez une capitalisation cohérente

JavaScript n'est pas typé, la capitalisation en dit beaucoup sur vos variables,
fonctions, etc. Ces règles sont subjectives, votre équipe peut donc choisir ce que
Ils veulent. Le fait est que, peu importe ce que vous choisissez tous, soyez juste cohérent.

**Mal:**

```javascript
const JOURS_PAR_SEMAINE = 7;
const joursParMois = 30;

const chansons = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function effacerLeDatabase() {}
function restaurer_Ledatabase() {}

class animale {}
class Alpaga {}
```

**Bien:**

```javascript
const JOURS_PAR_SEMAINE = 7;
const JOURS_PAR_MOIS = 30;

const CHANSONS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function effacerLeDatabase() {}
function restaurerLeDatabase() {}

class Animale {}
class Alpaga {}
```

**[⬆ retour au sommet](#table-des-matières)**

### Les appelants et les correspondants doivent être rapprochés

Si une fonction en appelle une autre, gardez ces fonctions verticalement proches dans la source
fichier. Idéalement, laissez l'appelant juste au-dessus de l'appelé. Nous avons tendance à lire le code de
de haut en bas, comme un journal. Pour cette raison, faites votre code lu de cette façon.

**Mal:**

```javascript
class ExamenDuRendement {
  constructor(employé) {
    this.employé = employé;
  }

  lookupCollègues() {
    return db.lookup(this.employé, "collègues");
  }

  lookupDirecteur() {
    return db.lookup(this.employé, "directeur");
  }

  obtenirEvaluationsDesCollègues() {
    const collègues = this.lookupCollègues();
    // ...
  }

  examenDuRendement() {
    this.obtenirEvaluationsDesCollègues();
    this.obtenirEvaluationsDuDirecteur();
    this.obtenirEvaluationDeSoi();
  }

  obtenirEvaluationsDuDirecteur() {
    const manager = this.lookupDirecteur();
  }

  obtenirEvaluationDeSoi() {
    // ...
  }
}

const evaluation = new ExamenDuRendement(employé);
evaluation.examenDuRendement();
```

**Bien:**

```javascript
class ExamenDuRendement {
  constructor(employé) {
    this.employé = employé;
  }

  examenDuRendement() {
    this.obtenirEvaluationsDesCollègues();
    this.obtenirEvaluationsDuDirecteur();
    this.obtenirEvaluationDeSoi();
  }

  obtenirEvaluationsDesCollègues() {
    const collègues = this.lookupCollègues();
    // ...
  }

  lookupCollègues() {
    return db.lookup(this.employé, "collègues");
  }

  obtenirEvaluationsDuDirecteur() {
    const manager = this.lookupDirecteur();
  }

  lookupDirecteur() {
    return db.lookup(this.employé, "directeur");
  }

  obtenirEvaluationDeSoi() {
    // ...
  }
}

const evaluation = new ExamenDuRendement(employé);
evaluation.examenDuRendement();
```

**[⬆ retour au sommet](#table-des-matières)**

## **Commentaires**

### Ne commentez que les choses qui ont une complexité de logique métier.

Les commentaires sont des excuses, pas une exigence. Bon code _mostly_ se documente.

**Mal:**

```javascript
function hasher(données) {
  // La hash
  let hash = 0;

  // Longeur du string
  const longeur = données.longeur;

  // Boucle à travers chaque caractère dans les données
  for (let i = 0; i < length; i++) {
    // Obtenez le code de caractère.
    const char = données.charCodeAt(i);
    // Faire le hash
    hash = (hash << 5) - hash + char;
    // Convertir en entier 32 bits
    hash &= hash;
  }
}
```

**Bien:**

```javascript
function hasher(données) {
  let hash = 0;
  const longeur = données.longeur;

  for (let i = 0; i < length; i++) {
    const char = données.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convertir en entier 32 bits
    hash &= hash;
  }
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Ne laissez pas de code commenté dans votre base de code

Le contrôle de version existe pour une raison. Laissez l'ancien code dans votre historique.

**Mal:**

```javascript
faireUneChose();
// faireAutreChoses();
// faireEncoreAutreChoses();
// faireTropDeChoses();
```

**Bien:**

```javascript
faireUneChose();
```

**[⬆ retour au sommet](#table-des-matières)**

### Je n'ai pas de commentaires dans le journal

Rappelez-vous, utilisez le contrôle de version! Il n'y a pas besoin de code mort, de code commenté,
et surtout les commentaires de journaux. Utilisez `git log` pour obtenir l'historique!

**Mal:**

```javascript
/**
 * 2016-12-20: Monades supprimées, je ne les ai pas comprises (RM)
 * 2016-10-01: Amélioration de l'utilisation de monades spéciales (JP)
 * 2016-02-03: Suppression de la vérification de type (LI)
 * 2015-03-14: Ajout d'une combinaison avec la vérification de type (JR)
 */
function combiner(a, b) {
  return a + b;
}
```

**Bien:**

```javascript
function combiner(a, b) {
  return a + b;
}
```

**[⬆ retour au sommet](#table-des-matières)**

### Évitez les marqueurs de position

Ils ne font généralement qu'ajouter du bruit. Laissez les fonctions et les noms de variables avec le
L’indentation et le formatage appropriés donnent la structure visuelle à votre code.

**Mal:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Instanciation du modèle de portée
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Configuration de l'action
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Bien:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ retour au sommet](#table-des-matières)**

## Traduction

Ceci est également disponible dans d'autres langues:

- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **Français**:
  [GavBaros/clean-code-javascript-fr](https://github.com/GavBaros/clean-code-javascript-fr)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Portugais Brésilien**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Espanol**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Espanol**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinois**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **Allemand**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Coréen**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polonais**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russe**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamien**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japonais**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonésie**:
  [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italien**:
  [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)

**[⬆ retour au sommet](#table-des-matières)**
