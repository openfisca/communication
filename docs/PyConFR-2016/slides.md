layout: true

<footer style="position: absolute; bottom: 0em; left: 0em; right: 0em;">
  <img src="images/logo-marianne.gif" style="height: 80px; margin: 0.5em 1em; float: left;">
  <img src="images/logo-openfisca.svg" style="height: 80px; margin: 0.5em 1em; float: right;">
</footer>

---

class: center, middle

# Écrire la loi en Python

PyCon France 2016 à Rennes

[Christophe Benz](mailto:christophe.benz@data.gouv.fr)

# openfisca.fr

???

Je m'appelle ... je vais vous parler de comment on écrit la loi en Python depuis quelques années.

---

# Quelles lois

- les impôts
- les aides
- l'entreprise
- autres

???

Quelles lois ? Tout ce qui se calcule en euros. On parle de lois fiscales pour les impôts et sociales pour les aides.

---

TODO Dessin des simulateurs fermés

certains ouvert mais pas libres (surtout web)

.center[<img title="Refus" src="images/refus.jpg" width="300">]

???

Problèmes :

- la connaissance est enfermée
- les citoyens perdent du temps
- les économistes sont coincés

---

# Solution

<img src="images/logo-openfisca.svg" style="height: 7em; margin-right: 2em; float: left;">

- unique modèle ouvert
- calculateur
- logiciel libre
- indépendance

???

Pour y remédier la solution est soit de se battre pour l'ouverture, soit de créer une alternative en dehors de l'état.

---

# Complexité de la loi

<div style="float: left; margin-top: 1em; margin-right: 1em;">
  .center[<img title="Livres des codes de la loi" src="images/livres-codes.jpg" width="450">]
</div>

Qui comprend ?

Ça grossit !

<br><br>Traduction en code source ?

???
Au boulot ! On part des textes de loi. Économistes et développeurs font bon ménage. Les geeks écrivent la loi en Python.

---

# La loi en code source

```python
class iai(Variable):
    column = FloatCol
    entity_class = FoyersFiscaux
    label = u"Impôt avant imputations de l'impôt sur le revenu"

    def function(self, simulation, period):
        period = period.this_year
        iaidrdi = simulation.calculate('iaidrdi', period)
        plus_values = simulation.calculate('plus_values', period)
        cont_rev_loc = simulation.calculate('cont_rev_loc', period)
        teicaa = simulation.calculate('teicaa', period)

        return period, iaidrdi + plus_values + cont_rev_loc + teicaa
```

???
Juste pour donner un exemple voici une formule d'OpenFisca.

---

# Explorateur de la loi

.center[<img title="Explorateur de la législation" src="images/legislation-explorer-irpp.png" width="700">]

???
On a d'ailleurs des outils web pour naviguer dans les formules.

---

# Visualisation des formules

<img title="Graphe de la législation dans OpenFisca" src="images/graphe-legislation.jpg" width="600">

???
On peut dessiner un graphe de dépendances des formules.

---

# La loi est complexe
# La réalité est complexe
# Pas de simplification hâtive

???
La philosophie du projet OpenFisca est que si on simplifie trop tôt on risque de léser des tas de gens.
Il vaut mieux d'abord appréhender cette complexité avec les bons outils.

---

class: center, middle

<img title="Démonstrateur" src="images/démonstrateur.png" width="700">

Démonstrateur – https://ui.openfisca.fr/

???
Par exemple on a ici en une seule vue tout un tas d'indicateurs qui auparavant étaient éparpillés dans de nombreux calculateurs.

---

# Nouveaux problèmes

- fiablité
- non-officiel
- contributions

???
Une fois qu'on a ces outils libres de nouveaux problèmes se posent.

---

# Exemple simplifié

```python
def impot(salaire):
    return salaire * 0.3
```

???
Prenons un peu de recul pour mieux comprendre : codons l'impôt sur le revenu.

---

# Exemple simplifié

```python
def impot(salaire):
    return salaire * 0.3

def allocations(salaire):
  return 1000 if salaire < 10000 else 0
```

---

# Exemple simplifié

```python
def impot(salaire):
    return salaire * 0.3

def allocations(salaire):
  return 1000 if salaire < 10000 else 0

def revenu_disponible(salaire):
    return salaire - impot(salaire) + allocations(salaire)
```

## Et voilà !

---

exclude: true

# Historique d'OpenFisca

- 2011 : accès impossible aux codes sources
- besoin de simuler des réformes
- 2 économistes
  - apprennent le Python pour l'occasion
  - petits scripts Python / NumPy

---

exclude: true

# Historique d'OpenFisca

- version initiale en Python Qt
- 2014 : des développeurs rejoignent le projet
- version Python avec API web et UI JavaScript
- entièrement sous licence libre

.center[<img src="images/logo-etalab-fullres.png" style="height: 80px;">]

---

# Technos

- Moteur de calcul et formules : Python NumPy
- API web : Python WSGI
- UI : JavaScript, React

---

# Usages d'OpenFisca

## Simuler des cas individuels

- produits dédiés à un domaine
  - https://mes-aides.gouv.fr/
  - https://embauche.beta.gouv.fr/

???
L'API Web permet de voir émerger des usages.

l'IPP
Communauté du revenu de base

---

# Usages d'OpenFisca

## Simuler avec des données

- données réelles secrètes
- données d'enquête accès limité
- données générées (en travaux)
- réformes : qui gagne, qui perd ?

---

# Usages d'OpenFisca

## Base de documentation

- https://legislation.openfisca.fr/

---

# Usages d'OpenFisca

## Études d'impact, projets de lois, de réformes

- Jupyter notebooks

TODO illustration suppression de la trance d'impôts

---

# Composants logiciels

- moteur de calcul : [OpenFisca-Core](https://github.com/openfisca/openfisca-core)
- législation Française : [OpenFisca-France](https://github.com/openfisca/openfisca-france)
- il existe aussi [OpenFisca-Tunisia](https://github.com/openfisca/openfisca-tunisia)
- API Web HTTP+JSON
- possibilité d'héberger sur son serveur

???
début Novembre hackathon à Dakar (Sénégal)

---

# Appréhender la complexité

- outils de trace
- explication des résultats des calculs
- tests

TODO développer, montrer la trace, ludwig

???
Au final la technique permet de mieux s'y retrouver
mais toujours non-officiel






---

class: center, middle

# « la Calculette Impôts »
## libérée en avril 2016

---

# Un nouvel univers

- ce n'est pas OpenFisca
- périmètres différents
- implication des devs d'OpenFisca

???
Le code M couvre les impôts sur les revenus, OpenFisca couvre en plus le social, l'entreprise...

---

# Étapes de la libération

TODO Dates

- étudiant-chercheur en économie
- dépose d'une demande CADA => OK
- décision du tribunal administratif
- février 2016 : livraison du code source
- avril 2016 : [hackathon](https://forum.openfisca.fr/t/guide-pratique-du-hackathon-codeimpot/42) à la fondation Mozilla

---

# Exécution... impossible

- langage M
- il manque le compilateur :-/
- un chantier :
  - transpilation de M vers Python
  - moteur de calcul en Python en standalone
  - API web en Python

???
adapté aux non informaticiens
Le langage M propose des constructions pas aussi simples qu'elles pourraient l'être.

---

# Exemple langage M

TODO

---

# M vers Python

TODO Illustrer

- grammaire « PEG » avec [Arpeggio](http://igordejanovic.net/Arpeggio/getting_started/)
- génération d'un arbre syntaxique en JSON
- interpréter l'AST ou le compiler en Python
- fournir des outils (CLI, API Web)

---

# Command-Line Interface

```
$ calculette-impots info TSHALLOV
{
  "TSHALLOV": {
    "variable_definition": {
      "alias": "1AJ",
      "description": "Salaires - Declarant 1",
      "tgvh_linecol": [ [ 13637, 1 ], [ 13637, 200 ] ],
      "type": "variable_saisie"
    },
    "variable_reverse_dependencies": [
      "PERP_INDV",
      "PPENEXOV",
      "PPE_BOOL_ACT_COND",
      "PPE_BOOL_NADAV",
      [...]
    ]
  }
}
```

---

# Command-Line Interface

Un célibataire sans enfants gagnant 30000€ par an :
```
$ calculette-impots calculate V_ANREV=2014 \
  TSHALLOV=30000 IRN --no-verifs
{
  "calculate_results": {
    "IRN": 2461
  }
}
```

---

# Command-Line Interface

Un couple marié (date du mariage 05/05/1980) sans enfants dont le déclarant 1 gagne 10000€ par an
et le déclarant 2 gagne 20000€ par an :
```
$ calculette-impots calculate V_ANREV=2014 \
  TSHALLOV=10000 TSHALLOC=20000 V_0AM=1 V_0AX=05051980 IRN --no-verifs
{
  "calculate_results": {
    "IRN": 264
  }
}
```

---

# Hackathon #CodeImpot

.center[<img title="Ateliers du hackathon #CodeImpot" src="images/hackathon-salle.jpg" width="500">]

---

# Ateliers du hackathon

- M vers JavaScript : simulateur web offline !
- code législatif vers code source
- optimisation du temps de calcul
- correspondances M / OpenFisca
- gain à la déclaration commune
- réformes : revenu de base

---

exclude: true

# Démocratie ++

TODO Redites...

- transparence
- compréhension du système socio-fiscal
- possibilité de reproduire les études d'experts
- un outil neutre pour un débat informé
- simulateur offline en JavaScript
- opportunités de business
- tester des réformes politiques

???
L'API web peut être remplacée par un simulateur offline en JavaScript !
Phrases démagogiques... les pauvres qui tirent sur la corde

---

## Que peut apporter la libération de la Calculette Impôts à OpenFisca ?

- fiabilisation des résultats
- faire tourner les tests
- extraction de données : taux, barèmes

???
S'assurer que les calculs sont justes

---

# Comparaison des résultats

- génération de population aléatoire
- exécution des calculs dans les différents simulateurs
- référence : le simulateur en ligne des impôts
- comparaison [graphique](https://github.com/openfisca/combine-calculators/blob/master/scripts/visual_comparisons.ipynb) des résultats

???
TODO comment est générée la population ?

---

exclude: true

# Impôt par calculateur

.center[<img title="Impôt sur le revenu par calculateur" src="images/irpp_by_calculator.png" width="600">]

---

exclude: true

## Différences avec le simulateur impots.gouv.fr

.center[<img title="Différences avec le simulateur en ligne" src="images/differences_with_online_simulator.png" width="600">]

---

# Synthèse des différences

- 2000 cas aléatoires : déclaration simplifiée
- 2 erreurs pour OpenFisca \o/
- plus d'erreurs pour la Calculette Impôts
- mais travail à poursuivre
- il faut itérer avec l'équipe M

???

- le code M libéré n'est pas appelé correctement (vérifs)
- transformation en Python à améliorer
- cas générés simples, cases de la déclaration simplifiée
- si on ajoute des cases d'autres erreurs vont apparaître dans OpenFisca

---

# Tendance à l'ouverture

- ouverture du [simulateur INES](https://git.framasoft.org/openfisca/ines-libre) par l'INSEE
- TODO

---

# Comment aider ?

TODO

- envie de simuler un domaine particulier de la loi
- envie d'écrire un outil
- envie d'améliorer les choses

---

# Améliorations possibles

- refactorings multiples (redbaron)
- faciliter l'édition des formules
- abstraction dans les calculs (helpers)
- vers un arbre de calcul déclaratif ?

---

class: center, middle

## L'État tend à plus d'ouverture.
## La société civile en bénéficie.

.center[[<img title="Démocratie mise à jour" src="images/democratie-mise-a-jour.png" height="200">](http://www.renaissancenumerique.org/publications/rn/792-2016-04-18-08-25-24)]

# Un outil neutre pour un débat informé

<br>

---

class: center, middle

# Source des slides

Sur [GitHub](https://github.com/openfisca/communication/tree/master/docs/PyConFR-2016)

---

class: center, middle

# Questions
