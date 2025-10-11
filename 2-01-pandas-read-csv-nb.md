---
jupytext:
  encoding: '# -*- coding: utf-8 -*-'
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.17.3
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

# Python-numérique - les tables de données

+++ {"tags": ["framed_cell"]}

## introduction sur les tables de données

````{admonition} →
nous avons vu quelques fonctions de `numpy`, pour manipuler les `numpy.ndarray` qui sont des tableaux

* multidimensionnels
* homogènes
* d'éléments de taille fixe

il existe d'autres **tables de données**, très fréquentes en data-science où on a:

* une observation par ligne (ici les passagers du Titanic)  
* plusieurs informations par observation  
* les différentes informations forment les colonnes de la table
* ces colonnes ne sont pas toutes du même type...

```{image} media/titanic.png
:width: 1000px
```
````

+++

## lecture d'une table de données

+++ {"tags": ["framed_cell"]}

### format CSV

````{admonition} →
format de fichier, le plus simple, pour stocker ces tables ?

* une observation par ligne
* dans chaque ligne, les informations séparées par un caractère choisi au préalable  
(qui sera le même pour tout le fichier)

fichier de format **`CSV`** pour ***Comma-Separated-Values***  
**notez bien**: le caractère séparateur n'est pas obligatoirement une `,`

voici le début du fichier `data/titanic.csv`, notez:

* le séparateur `,` sans espace autour
* dans `"Sharp, Mr. Percival James R"` un *faux* séparateur `,` dans une chaîne
* dans la première ligne, les noms des colonnes (pas obligatoire)
* des `,,` (quand les informations ne sont pas connues)

***

```
PassengerId,Survived,Pclass,Name,Sex,Age,SibSp,Parch,Ticket,Fare,Cabin,Embarked
552,0,2,"Sharp, Mr. Percival James R",male,27.0,0,0,244358,26.0,,S
638,0,2,"Collyer, Mr. Harvey",male,31.0,1,1,C.A. 31921,26.25,,S
499,0,1,"Allison, Mrs. Hudson J C (Bessie Waldo Daniels)",female,25.0,1,2,113781,151.55,C22 C26,S
261,0,3,"Smith, Mr. Thomas",male,,0,0,384461,7.75,,Q.../...
```
````

+++

***

+++ {"tags": ["framed_cell"]}

### la librairie de data-science

````{admonition} →
pour lire, mettre en forme et manipuler des données de data-science  
on utilise **la librairie `pandas`** (2008)

`numpy` ne propose pas directement ces fonctions  

`pandas` expose un type évolué de table de données: les **`pandas.DataFrame`**

`pandas` possède un type pour gérer une ligne et une colonne le **`pandas.Series`**

`pandas` comme `numpy` favorisent l'efficacité  
(parfois au détriment de la lisibilité)

`pandas` repose entièrement sur `numpy` (aujourd'hui en tous cas)  
i.e. les données manipulées par `pandas` sont implémentées comme des tableaux `numpy.ndarray`

il peut vous arriver d'utiliser la librairie `numpy` dans du code `pandas`  
(on importe souvent les deux)

````

+++

***

+++ {"tags": ["framed_cell"]}

### on importe `pandas`

````{admonition} →
on importe la librairie `pandas`  
avec son raccourci conventionnel

```python
import pandas as pd
```

vous n'avez pas `pandas` ? faites
```
# depuis le terminal
pip install pandas
# depuis une cellule du notebook:
%pip install pandas
```

regardez la version de la librairie installée  
<https://pandas.pydata.org/docs/whatsnew/index.html>
```python
pd.__version__
```
````

```{code-cell} ipython3
# le code
import pandas as pd
pd.__version__
```

+++ {"tags": ["framed_cell"]}

### lecture d'un fichier `csv`

````{admonition} →
on lit le fichier de format `csv` de la table de passagers du Titanic
```python
df = pd.read_csv('data/titanic.csv')
```

la table est une instance de `pandas.DataFrame`

```python
type(df)
-> pandas.core.frame.DataFrame
```

(`pandas.core.frame.DataFrame` est le même type que `pandas.DataFrame`)

la méthode `df.head()` affiche les qq premières lignes

```python
df.head(2)
```

```{admonition} note
:class: attention

quand on écrit "la méthode `df.head`", ça se lit comme:  
l'attribut `head` recherché à partir de l'objet `df`  
comme `df` est une dataframe, on trouve la méthode/fonction qui se trouve être aussi `pd.DataFrame.head`
```
````

```{code-cell} ipython3
# le code
df = pd.read_csv('data/titanic.csv')
type(df)
```

```{code-cell} ipython3
# pour vous convaincre que les types sont bien les mêmes

pd.core.frame.DataFrame is pd.DataFrame
```

```{code-cell} ipython3
# pour afficher les premières lignes
# à votre avis, comment on voit les dernières lignes ?
df
```

+++ {"tags": ["framed_cell"]}

### lecture d'un fichier `excel`

````{admonition} →
les fichiers de données en format Excel sont constitués de feuilles contenant des tables en 2 dimensions comme les tables des fichiers csv (nous ne parlons pas ici de macros ou calculs)

la fonction `pandas` `read_excel` permet de charger un fichier Excel et d'obtenir:
- un `dict` de DataFrame (les clés sont les noms des feuilles)
- directement un `csv` si il contient une unique feuille

pour utiliser `read_excel` vous devez préalablement importer la librairie `openpyxl`

```python
%pip install openpyxl
```
si il vous dit *Note: you may need to restart the kernel to use updated packages* faites le

```python
df1 = pd.read_excel('data/titanic.xlsx')
```

attention: les fonctions `read_csv` et `read_excel` sont des fonctions différentes  
(les *mêmes* dataframes peuvent *différer* au chargement sur des types de données)

nous n'irons pas plus loin sur `read_excel`, vous savez maintenant qu'il existe
````

```{code-cell} ipython3
# le code
%pip install openpyxl
```

```{code-cell} ipython3
# le code
df1 = pd.read_excel('data/titanic.xlsx')
df1
```

+++ {"tags": ["framed_cell"]}

## description rapide de la table des données

````{admonition} →
la méthode `describe()` vous donne un premier aperçu rapide de vos données

sur une `DataFrame`, elle vous donne  
pour chaque colonne **de type numérique**

* le nombre de valeurs non-manquantes (voir colonne `Age`)
* la moyenne
* l'écart-type
* le minimum
* les 3 quartiles (les valeurs à 25%, 50% et 75% de données)
* le maximum

```python
df.describe()
->     PassengerId Survived Pclass Age     SibSp   Parch   Fare
count  891.00      891.00   891.00 714.00  891.00  891.00  891.00
mean   446.00      0.38     2.30   29.69   0.52    0.38    32.20
std    257.35      0.48     0.83   14.52   1.10    0.80    49.69
min    1.00        0.00     1.00   0.42    0.00    0.00    0.00
25%    223.50      0.00     2.00   20.12   0.00    0.00    7.91
50%    446.00      0.00     3.00   28.00   0.00    0.00    14.45
75%    668.50      1.00     3.00   38.00   1.00    0.00    31.00
max    891.00      1.00     3.00   80.00   8.00    6.00    512.32
```

on remarque que `pandas.DataFrame.describe`

* a, par défaut, appliqué les calculs sur **les colonnes numériques**  
même quand ça n'a pas forcément beaucoup d'intérêt - voir `Survived` ou `Pclass`  
qui sont plutôt des catégories

* n'a rien fait sur les colonnes non-numériques

on aurait pu n'appliquer la méthode qu'à une sous-dataframe  
```python
df[['Age', 'Fare']].describe()  
```

ou forcer la méthode à s'appliquer à **toutes les colonnes**  
pour les colonnes non-numériques, seront affichés à la place

* le nombre de valeurs
* le nombre de valeurs `unique`s
* la valeur la plus fréquente `top`
* sa fréquence `freq`

```python
df.describe(include='all')
```

la méthode `describe()` marche aussi sur les séries (colonnes)

```python
df['Sex'].describe()
->
count 891
unique 2
top male
freq 577
Name: Sex, dtype: object
```

````

```{code-cell} ipython3
# le code

# pas besoin de 6 chiffres après la virgule
pd.options.display.precision = 2

df.describe()
```

```{code-cell} ipython3
df[['Age', 'Fare']].describe()
```

```{code-cell} ipython3
# le code
df.describe(include='all')
```

```{code-cell} ipython3
# le code
df['Sex'].describe()
```

## les index et indices des tables

+++ {"tags": ["framed_cell"]}

### la notion d'index

````{admonition} →
la clé pour comprendre `pandas`:

* **les lignes et les colonnes ont des index**
* les opérations sur ces index sont **le plus efficace possible**  
  (on verra par la suite que les lignes et les colonnes ont aussi naturellement des indices, numérotés à partir de `0`)

**Pourquoi (et comment) les opérations sur les index sont très efficaces ?**

deux constats  

* rechercher dans une liste est très inefficace  
(en moyenne $n/2$ essais pour localiser un élément dans une liste de $n$ éléments)

* accéder au $i^{ème}$ élément d'un tableau est très efficace  
(on est en *temps constant* - on ne parcourt bien sûr pas les éléments de $0$ à $i-1$)


**l'idée**

* trouver une caractéristique qui identifie une observation de manière unique  
(genre *numéro de sécurité sociale* pour un individu, ici par exemple le `PassengerId`)

* calculer un index à partir de cette caractéristique  
qui soit *le plus unique* possible (pour avoir peu de collisions)

* utiliser cet index comme entrée dans une table
* la recherche peut alors être considérée comme en temps constant  
  comme l'accès à un élément d'un tableau

* c'est la technique des **tables de hachage** (comme les `dict` ou `set` Python)

`pandas` indexe ses lignes et ses colonnes suivant vos indications  
dit autrement, c'est à vous de choisir parmi les colonnes
celle(s) qui peut servir d'identificateur pour servir d'index  
(un index en `pandas` n'est pas obligatoirement unique)
````

+++

***

+++ {"tags": ["framed_cell"]}

### index des colonnes

````{admonition} →
dans notre fichier du Titanic seules les colonnes ont un nom  
(les lignes n'en ont pas), du coup:

* les **colonnes** ont été **indexées par leur nom**
* les **lignes** ont été **indexées par leur indice**  
i.e. une *simple numérotation à partir de 0*  
on y reviendra

l'attribut `columns` permet d'accéder aux colonnes de la table

```python
df.columns
-> Index(['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp',
          'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked'],
   dtype='object')
```

c'est un object de type `Index`; il permet d'accéder facilement aux noms des colonnes

```python
df.columns[0]
-> 'PassengerId'
```

````

```{code-cell} ipython3
# le code
df.columns
```

```{code-cell} ipython3
df.columns[0]
```

+++ {"tags": ["framed_cell"]}

### accès aux colonnes avec `df[]`

````{admonition} →
les colonnes ont un traitement privilégié en `pandas`

une table `pandas` est un "dictionnaire"

* où les clés sont les noms des colonnes
* où les valeurs sont les colonnes (**de type `Series`**)

accès à la colonne `Age` de la data-frame du Titanic

* on remarque les `891` entrées
* on remarque les indices des lignes de `0` à `890`
* on constate que le type des éléments de cette colonne est `float64`
* on constate que l'age du passager d'indice `3` est manquant `NaN` (*Not a Number*)  
(`NaN` est un terme générique, il signifie ici que la valeur n'est simplement pas disponible)

```python
df['Age']
->  0      27.00
    1      31.00
    2      25.00
    3        NaN
    4      24.00
           ...
    886    47.00
    887    30.00
    888    36.00
    889    22.00
    890     0.83
Name: Age, Length: 891, dtype: float64
```

attention `pandas` accepte que plusieurs colonnes portent le même nom  
en cas d'accès, il vous les donne toutes
````

```{code-cell} ipython3
:cell_style: split

df['Age'].head(2)
#df['Fare']
```

```{code-cell} ipython3
:cell_style: split

# type Series
type(df['Age'])
```

```{code-cell} ipython3
# on peut aussi passer une liste de colonnes
# auquel cas on récupère une dataframe
df[['Age', 'Sex']].head()
```

+++ {"tags": ["framed_cell"]}

### accès aux colonnes avec `df.`

````{admonition} →
Lorsque le nom de la colonne ne comporte pas de caractère *bizarre*  
on peut aussi accéder à une colonne au travers d'**un attribut**

Cette notation est **plus lisible** mais aussi **plus limitée**  
par exemple ne fonctionne pas si le nom de la colonne contient un espace

Il faut le voir uniquement comme *une commodité*  
**Pas forcément recommandé aux débutants**  
Mais c'est *très utilisé* - il faut savoir le lire

```python
df.Age is df['Age']
-> True
```
````

```{code-cell} ipython3
:cell_style: split

# le code
# on peut aussi accéder à une colonne par un attribut
# qui est une notation plus lisible
df.Age
```

```{code-cell} ipython3
:cell_style: split

df.Age is df['Age']
```

+++ {"tags": ["framed_cell"]}

### type des colonnes `pandas.Series`

````{admonition} →
```python
    type(df['Age'])
-> pandas.core.series.Series
```

le second type en `pandas` est le type des colonnes, qui sont des `Series`


on peut voir la `DataFrame` comme un dictionnaire qui associe  
{ `nom-de-colonne` $\rightarrow$ `un-objet-series` }
````

```{code-cell} ipython3
# le code
type(df['Age'])
```

+++ {"tags": ["framed_cell"]}

### indexer les lignes

````{admonition} →
c'est une bonne pratique de choisir **une colonne**  
comme **index** (des lignes) de la table  
quand on le peut...

par exemple, dans la table du titanic, on remarque que  
la colonne `PassengerId` contient un **identifiant unique** pour chaque passager

choisissons **cette colonne comme index** (des lignes) de la table  
pour cela, trois options

1. directement à la lecture du fichier

   ```python
   # option 1
   df = pd.read_csv('data/titanic.csv', index_col='PassengerId')
   ```

2. après coup

   ```python
   # option 2
   df = pd.read_csv('data/titanic.csv')
   df = df.set_index('PassengerId')
   ```
    
3. après coup sans ré-affecter df

   ```python
   # option 3
   df = pd.read_csv('data/titanic.csv')
   df.set_index('PassengerId', inplace=True)

observez le changement dans la présentation de la table
````

```{code-cell} ipython3
# le code
# option 1.
df = pd.read_csv('data/titanic.csv', index_col='PassengerId')
```

```{code-cell} ipython3
# le code
# option 2.
df = pd.read_csv('data/titanic.csv')
# la table avant
df.head(1)
```

```{code-cell} ipython3
# la table après
# remarquez que 'PassengerId'
# n'est plus présenté de la même manière
df = df.set_index('PassengerId')
df.head(1)
```

```{code-cell} ipython3
# le code
# option 2-bis
# la méthode set_index avec son paramètre inplace à True modifie directement la table sans renvoyer une nouvelle dataframe
df = pd.read_csv('data/titanic.csv')
df.set_index('PassengerId', inplace=True)
df.head(1)
```

+++ {"tags": ["framed_cell"]}

### une série aussi possède un index

````{admonition} →

* accèdons à la colonne `Name`  
  remarquons qu'ici aussi, l'objet Series possède un index  
  qui en l'occurrence est `PassengerId` (provient de la `df`)

  ```python
  df['Name']
  ->  PassengerId
    552                          Sharp, Mr. Percival James R
    638                                  Collyer, Mr. Harvey
    499      Allison, Mrs. Hudson J C (Bessie Waldo Daniels)
    261                                    Smith, Mr. Thomas
    395    Sandstrom, Mrs. Hjalmar (Agnes Charlotta Bengt...
                                 ...
    463                                    Gee, Mr. Arthur H
    287                              de Mulder, Mr. Theodore
    326                             Young, Miss. Marie Grice
    396                                  Johansson, Mr. Erik
    832                      Richards, Master. George Sibley
   ```

* toutes les colonnes de la dataframe partagent **le même index**  
  c'est de cette façon que les colonnes sont "alignées" entre elles

* accèdons à la colonne `Name` de la première ligne  
  puis à son index `552` (`PassengerId`)

  ```python
  df['Name'][552] # passager d'id 552
  -> 'Sharp, Mr. Percival James R'
  ```
````

```{code-cell} ipython3
# le code
df = pd.read_csv('data/titanic.csv', index_col='PassengerId')
df['Name']
```

```{code-cell} ipython3
# ici on obtient bien la première ligne !

# quand on indexe une Series,
# c'est par l'index (552)
# et non pas par l'indice qui ici serait 0
df['Name'][552]
```

+++ {"tags": ["framed_cell"]}

### différence entre index et indice

````{admonition} →
les **indices** c'est quand on compte nos éléments à partir de `0`  
(c'est valable pour les colonnes comme pour les lignes)

les **index** c'est quand on utilise des valeurs fournies par l'utilisateur, comme

* les **noms** de colonnes
* ou les **identifiants** de lignes  
  par ex. plus haut `552` est l'index de la première ligne, parce que dans la colonne-index `PassengerId`,  
  la première ligne contient `552`

lorsqu'une table n'a **pas d'index** particulier - i.e. avant qu'on fasse un `set_index()`:

* dans ce cas l'**index commence à 0**, 
* et du coup, par accident, les **indices** et les **index coincident**
* pour information, pandas crée automatiquement un index de type `RangeIndex`  
````

+++ {"tags": ["framed_cell"]}

### l'index des lignes

````{admonition} →
il est accessible par l'attribut `pandas.DataFrame.index`

lisons la data-frame du titanic sans fixer l'index de lignes

```python
df = pd.read_csv('data/titanic.csv')
df.index
-> RangeIndex(start=0, stop=891, step=1)
```

* l'index est alors un `RangeIndex` de `0` à `890` inclus

lisons la data-frame du titanic et choisissons la colonne `PassengerId` comme index de ligne

```python
df = pd.read_csv('data/titanic.csv').set_index('PassengerId')
df.index
-> Int64Index([552, 638, 499, 261, 395, 811, 758, 703, 406, 641,
              ...
              556, 236, 224, 598, 258, 463, 287, 326, 396, 832],
             dtype='int64', name='PassengerId', length=891)
```

* les index des lignes sont les valeurs de la colonne `PassengerId`

`pandas.Int64Index` et `pandas.RangeIndex` sont tout deux des `pandas.Index`

```{code-cell} ipython3
# le code
df = pd.read_csv('data/titanic.csv')
df.index
```

```{code-cell} ipython3
# le code
df = pd.read_csv('data/titanic.csv').set_index('PassengerId')
df.index
```

+++ {"tags": ["framed_cell"]}

## **résumé** à propos des types DataFrame et Series

````{admonition} →

* les tables pandas sont représentées par le type `DataFrame`
* une dataframe a
  * un index pour accéder aux colonnes (`df.columns`)  
  * et un index pour accéder aux lignes (`df.index`)  
  * ces deux objets sont de type `Index`
* une colonne, ou une ligne, sont de type `Series`  
  qui correspond si on veut à des données en 1 seule dimension
````

+++

***

+++ {"tags": ["framed_cell"]}

## forme et dimension de la table

`````{admonition} →
`pandas` est fondé sur `numpy` (cela pourrait changer dans le futur)

la **forme** de la table est donnée par l'attribut `pandas.DataFrame.shape`

```python
df.shape
-> (891, 11)
```

`numpy` s'occupe de stocker et manipuler des tableaux de dimension 2  
`pandas` apporte

* l'indexation du tableau
* des fonctions pratiques et de haut-niveau pour manipuler cette table de données

````{admonition} et la dimension ?
:class: admonition-small tip

une `pandas.dataFrame` est donc toujours une table à deux dimensions  
on peut le vérifier par l'attribut `pandas.DataFrame.ndim`

```python
df.ndim
-> 2
```

on retrouve là les attributs classiques de `numpy` `ndim`, `shape`
````
`````

```{code-cell} ipython3
# le code
df = pd.read_csv('data/titanic.csv', index_col='PassengerId')
print(df.shape)
print(df.ndim)
```

## **exercice** basique

+++

Le fichier `data/petit-titanic.csv` contient les 10 premières lignes de passagers  
*Attention* ce n'est **pas exactement** le même format que `data/titanic.csv`

```{code-cell} ipython3
# pour voir le contenu du fichier
import pandas as pd 
%cat data/petit-titanic.csv

# remarquez qu'on peut aussi voir ce contenu en Python pur
#with open("data/petit-titanic.csv") as f:
#    for line in f:
#        print(line, end="")
```

1. lisez le contenu de ce fichier avec les paramètres par défaut de `pandas.read_csv`  
affichez les 2 premières lignes avec `pandas.DataFrame.head`  
voyez-vous les trois problèmes ?  
essayez de les résoudre en lisant le help de la fonction `help(pd.read_csv)` ou `pd.read_csv?`  
ou passez à la question 2 pour être aidé

```{code-cell} ipython3
df1 = pd.read_csv('data/petit-titanic.csv').head(2)
df1.head(2)
# On remarque après execution que les colonnes ne sont pas séparées, que les ; subsistent et que la première ligne a été utilisée en tant qu'index
```

```{code-cell} ipython3
pd.read_csv?
```

2. passez le **bon séparateur** à la méthode `pandas.read_csv`  
   indiquez lui que l'entête **ne contient pas** la liste des noms des colonnes  
   passez-lui la **liste des noms des colonnes** puisqu'elles ne sont pas  mentionnées dans le fichier (à récupérer plus haut)  
  ````{admonition} *spoiler*
  :class: dropdown tip
  
   voyez les paramètres `sep`, `header` et `names`
   ````

```{code-cell} ipython3
df1=pd.read_csv('data/petit-titanic.csv', sep = ';',names = ['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp',
          'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked'], headeref = None ) #Headers permet de ne pas mettre la première ligne en tant que nom de colonne
# pour changer le nom de colonnes, on peut aussi df.columns = names
# df.set_index (On met une colonne en index)
df1
```

3. appliquer la méthode `describe` aux colonnes `Age` et `Fare`  
   (ou plus exactement à une dataframe qui contient ces colonnes)

```{code-cell} ipython3
df1[0][1]=99999
df1[0][1]
```

```{code-cell} ipython3
print(df1['Age'].describe())
print(df1['Fare'].describe())
```

```{code-cell} ipython3
df1.iloc[0,1]
```

4. affichez le nombre de colonnes et de lignes

```{code-cell} ipython3
print(len(df1.columns))
print(len(df1.index))
```

5. affichez les index des colonnes et des lignes

```{code-cell} ipython3
print(df1.index)
print(df1.columns)
```

6. modifiez l'index des colonnes par la liste
```Python
columns = ['Identifiant', 'Survécu', 'Pclass',
           'Nom', 'Genre', 'Age', 'SibSp', 'Parch',
           'Ticket', 'Tarif', 'Cabine', 'Embarquement']
```

```{code-cell} ipython3
df1.columns = ['Identifiant', 'Survécu', 'Pclass',
           'Nom', 'Genre', 'Age', 'SibSp', 'Parch',
           'Ticket', 'Tarif', 'Cabine', 'Embarquement']
df1
```

7. modifiez l'index des lignes par la colonne `'Identifiant'`

```{code-cell} ipython3
df1 = df1.set_index('Identifiant')
df1
```

```{code-cell} ipython3
import numpy as np
tab = np.arange(0,24).reshape(2,3,4)
np.sum(tab, axis = 2)
```

```{code-cell} ipython3
df1
```

```{code-cell} ipython3
df
```

```{code-cell} ipython3

```
