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

Je m'appelle ... je travaille à Etalab, service du premier ministre.

Je vais vous parler de :

- comment on écrit la loi en Python depuis quelques années
- quels sont les enjeux démocratiques
- ce qui se passe lorsque l'état libère son logiciel de calcul de l'impôt
- en quoi la situation s'est améliorée depuis 2011

Je suis développeur, au départ je n'y connaissais rien en fiscalité mais ce n'est pas un problème pour participer au projet !

---

# Quelles lois ?

- les impôts
- les aides
- l'entreprise
- autres

???

Quelles lois sont écrites en Python ? Tout ce qui se calcule en euros. On parle de lois fiscales pour les impôts et sociales pour les aides.

---

# Les polémiques

Exemple : [le RSA rapporterait plus que le SMIC](http://rue89.nouvelobs.com/rue89-eco/2013/03/12/la-fable-bidon-de-la-famille-rsa-qui-gagne-plus-que-la-famille-salariee-240493)

<img title="Tableau RSA" src="images/tableau-rsa.png" width="350">

???

- politiques et journalises affirment des choses, créant des polémiques
- Pourquoi écrire la loi en Python ?
  - pour effectuer des calculs fiables
  - fonder la décision sur des résultats reproductibles.

---

count: false

# Les polémiques

Exemple : [le RSA rapporterait plus que le SMIC](http://rue89.nouvelobs.com/rue89-eco/2013/03/12/la-fable-bidon-de-la-famille-rsa-qui-gagne-plus-que-la-famille-salariee-240493)

<img title="Tableau RSA" src="images/tableau-rsa.png" width="350">
<img title="Tableau RSA corrigé" src="images/tableau-rsa-corrige.png" width="350">

---

# Un calculateur

- une situation en entrée
- évaluation de formules de calcul
- un résultat en sortie
- simulateur – calculateur

???

Pour cela il nous faut un calculateur et des formules de calcul :

- on entre une situation : des individus, familles, salaires, loyer, etc.
- on calcule par exemple l'impôt sur le revenu, les allocations familiales, etc.
- le terme calculateur est employé lorsque le résultat est officiel, sinon on dit un simulateur

---

# Les problèmes en 2011

- les calculateurs sont éparpillés
- accessibles ou pas – mais propriétaires
- les démarches sont morcelées
- les économistes sont coincés

???

- la situation n'est pas idéale
- les domaines de la loi sont inter-dépendants

---

# Une solution

<img src="images/logo-openfisca.svg" style="height: 7em; margin-right: 2em; float: left;">

- modèle unifié en Python
- logiciel libre
- performance
- pédagogie

???

Pour y remédier on peut soit se battre pour l'ouverture, soit créer une alternative en dehors de l'état.

---

# Un pari ambitieux

<div style="float: left; margin-right: 1em;">
  .center[<img title="Livres des codes de la loi" src="images/livres-codes.jpg" width="450">]
</div>

Qui maîtrise le sujet ?

<br><br>Traduction en code source !

???
On part des textes de loi, complexes vous en conviendrez.
Économistes et développeurs font bon ménage, les geeks veulent écrire la loi en Python.
C'est un pari ambitieux mais on finit par y arriver !

---

<h1 style="margin: 0">Visualisation des formules</h1>

<img title="Graphe de la législation dans OpenFisca" src="images/graphe-legislation.jpg" width="700">

???
On peut dessiner un graphe de dépendances des formules.

---

class: center, middle

# La réalité est complexe
# La loi est complexe
# Pas de simplification hâtive

???
La philosophie du projet OpenFisca est que si on simplifie trop tôt on risque de léser des tas de gens.
Il vaut mieux d'abord appréhender cette complexité avec les bons outils.

---

<h1 style="margin: 0">Impôts et aides</h1>

.center[<img title="Démonstrateur" src="images/démonstrateur.png" width="750">]

???
- on a ici réunis au même endroit un ensemble d'indicateurs qui auparavant étaient éparpillés dans de nombreux calculateurs.
- outil pour connaisseurs

---

# Exemple simplifié

```python
def impot(salaire):
    return salaire * 0.3
```

???
Prenons un peu de recul pour mieux comprendre : codons l'impôt sur le revenu.

---

count: false

# Exemple simplifié

```python
def impot(salaire):
    return salaire * 0.3

def allocations(salaire):
  return 1000 if salaire < 10000 else 0
```

---

count: false

# Exemple simplifié

```python
def impot(salaire):
    return salaire * 0.3

def allocations(salaire):
  return 1000 if salaire < 10000 else 0

def revenu_disponible(salaire):
    return salaire \
        - impot(salaire) \
        + allocations(salaire)
```

???
Voyons un exemple réel

---

<h1 style="margin: 0">Exemple réel</h1>

.small[```python
class revdisp(Variable):
    column = FloatCol(default = 0)
    entity_class = Menages
    label = u"Revenu disponible du ménage"

    def function(self, simulation, period):
        period = period.start.period('year').offset('first-of')
        rev_trav_holder = simulation.compute('rev_trav', period)
        pen_holder = simulation.compute('pen', period)
        rev_cap_holder = simulation.compute('rev_cap', period)
        psoc_holder = simulation.compute('psoc', period)
        ppe_holder = simulation.compute('ppe', period)
        impo = simulation.calculate('impo', period)

        pen = self.sum_by_entity(pen_holder)
        ppe = self.cast_from_entity_to_role(ppe_holder, role = VOUS)
        ppe = self.sum_by_entity(ppe)
        psoc = self.cast_from_entity_to_role(psoc_holder, role = CHEF)
        psoc = self.sum_by_entity(psoc)
        rev_cap = self.sum_by_entity(rev_cap_holder)
        rev_trav = self.sum_by_entity(rev_trav_holder)

        return period, rev_trav + pen + rev_cap + psoc + ppe + impo
```]

???
- plus complexe : périodes, vectoriel, entités
- Ces formules sont-elles exactes, càd reflètent bien la loi ?


---

# Fiabilisation par les tests

- tests écrits en même temps que les formules
- tests consolidés suite à la détection d'erreurs
- non-régression

???
- Comme pour les logiciels avec les tests unitaires.
- Les tests sont écrits en même temps que les formules.
- Lorsqu'on s'aperçoit qu'il existe une erreur de calcul, on crée un test et on le fait passer.
- Il existe un outil web pour déclarer des tests

---

# Composants logiciels

- moteur de calcul : [OpenFisca-Core](https://github.com/openfisca/openfisca-core)
- législation Franç<a href=""></a>ise : [OpenFisca-France](https://github.com/openfisca/openfisca-france)
- API Web HTTP+JSON
- api.openfisca.fr ou auto-hébergement

???
- le moteur est détaché du pays

---

# Utile pour les particuliers

- calculer des cas individuels
- connaître les aides
- estimer le coût d'embauche

???
Ces produits appellent en bout de chaîne l'API web d'OpenFisca.

---

<h1 style="margin: 0">mes-aides.gouv.fr</h1>

.center[<img title="Mes-aides" src="images/mes-aides.png" width="700">]

---

<h1 style="margin: 0">embauche.beta.gouv.fr</h1>

<img title="Embauche" src="images/simulateur-embauche.gif" width="800">

---

# Utile pour les économistes

- calcul sur population entière
- données d'enquête accessibles aux seuls chercheurs / données générées
- études d'impact, réformes, gagnants, perdants

???

- Plus intéressant : on peut calculer sur une population
- exemple de réforme : suppression de la tranche d'impôts

---

<h1 style="margin: 0">Jupyter notebook</h1>

.center[<img title="Notebook" src="images/notebook.png" width="600">]

---

<h1 style="margin: 0">Comprendre la loi</h1>

.center[<img title="Heatmap" src="images/tax-income.png" width="550">]

???
- Une carte de chaleur qui a été faite en Python mais en dehors d'OpenFisca.
- taxation en fonction du revenu et des parts en capital

---

<h1 style="margin: 0">Les défis</h1>

- fiablité des calculs
- acceptation par l'administration
- attirer les contributeurs
- maintenance des formules
- intégrer les calculateurs officiels
- utilisation aisée par les chercheurs sur les centres d'accès aux données sécurisés

???

- non-officiel

---

# Performances Python

- Python - NumPy
- calcul vectoriel
- 10 secondes pour 120 000 individus
- ~1500 formules, + de 10 000 appels par calcul

???
- calcul vectoriel : 1 seule exécution pour une population

---

# Fiabilisation par comparaison

- génération aléatoire de ~2000 familles
- exécution des calculs dans chaque calculateur
- les écarts apparaissent
- des cas de plus en plus complexes

???

- Désormais OpenFisca peut être comparé à un autre calculateur, officiel.
- Une population est générée en utilisant certaines cases de la déclaration simplifiée.
- l'exécution du simulateur en ligne est automatisée
- si on ajoute des cases d'autres erreurs vont apparaître dans OpenFisca
- Comparaison [graphique](https://github.com/openfisca/combine-calculators/blob/master/scripts/visual_comparisons.ipynb) des résultats

---

# Les contribteurs extérieurs

- utilisent OpenFisca pour un article, une étude
- implémentent des formules de la loi
- créent des applications web ou mobile
- apportent leur savoir sur la loi

???

- logiciel libre géré par la communauté
- le gros de notre travail consiste à simplifier l'écriture des formules

---

class: center, middle

## L'État tend à plus d'ouverture

## La société civile en bénéficie

<h1 style="margin: 0">Un outil neutre pour un débat informé</h1>

???

- la situation est bien meilleure qu'en 2011, le cadre est posé
- d'autres pays sont intéressés (début Novembre hackathon à Dakar (Sénégal))
- ouverture du [calculateur INES](https://git.framasoft.org/openfisca/ines-libre) par l'INSEE
- questions

---

count: false
class: center, middle

# Source des slides

Sur [GitHub](https://github.com/openfisca/communication/tree/master/docs/PyConFR-2016)
