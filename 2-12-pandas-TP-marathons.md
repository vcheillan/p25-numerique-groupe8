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

# TP sur les marathons

```{code-cell} ipython3
import pandas as pd
```

## les données

+++

On va étudier un jeu de données trouvés sur Internet  
des données des marathons dans différents pays

+++

mettez le fichier `marathon.txt` dans votre répertoire data

```{code-cell} ipython3
# regardons les 5 premières lignes des deux fichier de données
# notez l'utilisation de with/as

with open('data/marathon.txt') as f:
    for _ in range(10):
        print(f.readline(), end='') # on remplace le '\n' par rien
```

vous remarquez que le fichiers ont l'extension `.txt` mais qu'ils contiennent des valeurs séparées par quelque chose  
et peuvent donc être considérés comme des `csv`

+++

## chargement des données

+++ {"tags": ["level_basic"]}

Chargez les deux fichiers avec `read_csv` de `pandas`  
naturellement vous n'allez pas pouvoir utiliser les valeurs par défaut de tous les paramètres...

```{code-cell} ipython3
# les noms des colonnes sont:
names = ["city", "year", "duration", "seconds"]
```

```{code-cell} ipython3
df1 = pd.read_csv('data/marathon.txt', sep ='\t', names = names)
df1
```

## sauvegarde dans un fichier csv

+++

dans l'autre sens, quand on a produit une dataframe et qu'on veut sauver le résultat dans un fichier texte  
sauvez le fichier des marathon dans différentes villes en `csv`

```{code-cell} ipython3
df1.to_csv('Fichier Enregistré')
```

+++ {"tags": ["raises-exception"]}

vérifiez que le fichier a bien été créé et contient de bonnes valeurs

```{code-cell} ipython3
#enregistré dans notebooks
```

# des analyses des données des marathons dans différentes villes

+++ {"tags": ["level_basic"]}

### les éditions de 1971

```{code-cell} ipython3
# à vous de calculer les éditions de 1971
df_1971 = df1[(df1['year'] == 1971)]
df_1971
# FUKUOKA
# NY
# BOSTON
```

```{code-cell} ipython3
:tags: [raises-exception]

# ceci doit retourner True
df_1971.shape == (3, 4) and df_1971.seconds.max() == 8574
# np.True_
```

+++ {"tags": ["level_basic"]}

### l'édition de 1981 à Londres

```{code-cell} ipython3
# à vous
df_london_1981 = df1[(df1['year']==1981) & (df1['city'] == 'LONDON')]
df_london_1981
```

```{code-cell} ipython3
:tags: [raises-exception]

# ceci doit retourner True

df_london_1981.shape == (1, 4) and df_london_1981.iloc[0].seconds == 7908
```

### les villes

+++

listez les villes qui apparaissent au moins une fois

```{code-cell} ipython3
df1['city'].unique()
```

+++ {"tags": ["level_basic"]}

construisez l'ensemble (un `set` `Python`) des villes

```{code-cell} ipython3
set(df1['city'].unique())
```

### des extraits

attention ici dans les consignes, les numéros de ligne **commencent à 1**

+++ {"tags": ["level_basic"]}

donner les entrées correspondant aux lignes 10 à 12 inclusivement

```{code-cell} ipython3
# à vous
df_10_to_12 = df1.iloc[[9,10,11]]
df_10_to_12
```

```{code-cell} ipython3
:tags: [raises-exception]

# ceci doit retourner True

df_10_to_12.shape == (3, 4) and df_10_to_12.iloc[0].year == 2002 and df_10_to_12.iloc[-1].year == 2000
```

+++ {"tags": ["level_basic"]}

donnez la Series correspondant aux événements à Paris après 2000 (inclus), 
dans laquelle on n'a gardé que l'année

```{code-cell} ipython3
# à vous
s1_paris_2000 = df1[(df1['year']>=2000) & (df1['city'] == 'PARIS')]['year']
s1_paris_2000
```

```{code-cell} ipython3
:tags: [raises-exception]

# ceci doit retourner True

isinstance(s1_paris_2000, pd.Series) and len(s1_paris_2000) == 12 and s1_paris_2000.iloc[-1] == 2000
```

+++ {"tags": ["level_basic"]}

donnez la DataFrame correspondant aux événements à Paris après 2000, 
dans laquelle on n'a gardé que les deux colonnes `year` et `seconds`

```{code-cell} ipython3
# à vous
df_paris_2000_ys = df1[(df1['year']>=2000) & (df1['city'] == 'PARIS')][['year','seconds']]
df_paris_2000_ys
```

```{code-cell} ipython3
:tags: [raises-exception]

# ceci doit retourner True

(isinstance(df_paris_2000_ys, pd.DataFrame)
 and df_paris_2000_ys.shape == (12, 2) 
 and df_paris_2000_ys.iloc[-2].seconds == 7780)
```

## aggrégats

+++ {"tags": ["level_basic"]}

calculez la moyenne de la colonne `seconds` ?

```{code-cell} ipython3
# votre code
seconds_average = df1['seconds'].mean()
seconds_average
#7933.660167130919
```

```{code-cell} ipython3
:tags: [raises-exception]

# pour vérifier

import math
math.isclose(seconds_average, 7933.660167130919)
```

### combien de marathons par an

+++ {"tags": ["level_basic"]}

si maintenant je veux produire une série qui compte par année combien il y a eu de marathons

il y a plein de façons de faire, si vous en voyez plusieurs n'hésitez pas...

```{code-cell} ipython3
# à vous

count_by_year = df1.groupby(by = 'year').size()
count_by_year
```

```{code-cell} ipython3
:tags: [raises-exception]

# pour vérifier

(isinstance(count_by_year, pd.Series)
 and len(count_by_year) == 65
 and count_by_year.loc[1947] == 1
 and count_by_year.loc[2007] == 9
 and count_by_year.loc[2011] == 5)
```

### combien y-a-t-il eu de marathon par ville ?

```{code-cell} ipython3
nb_marathons = df1.groupby(by = 'city').size()
nb_marathons
```

quelles est la moyenne des durées des marathons par ville

```{code-cell} ipython3
duree_moyenne_marathons = df1.groupby(by = 'city')['seconds'].mean()
duree_moyenne_marathons
```

affichez la moyenne des durées des marathons par ville triée en ordre croissant

```{code-cell} ipython3

duree_moyenne_marathons_triée = duree_moyenne_marathons.sort_values()
duree_moyenne_marathons_triée
```

## les durées

+++ {"tags": ["level_intermediate"]}

dans cette partie, notre but est de vérifier que la colonne `seconds` contient bien le nombre de secondes correspondant à la colonne `duration`

+++ {"tags": ["level_basic"]}

pour cela on va commencer par convertir la colonne `duration` en quelque chose d'un peu plus utilisable

`numpy` expose deux types particulièrement bien adaptés à la gestion du temps

* `datetime64` pour modéliser un instant particulier
* `timedelta64` pour modéliser une durée entre deux instants

voir plus de détails si nécessaire ici: <https://numpy.org/doc/stable/reference/arrays.datetime.html>

pour convertir la colonne en `datetime64` on va utiliser `pd.to_timedelta()`

voyez la documentation de cette fonction, et modifiez la dataframe `df` pour que la colonne `duration` soit maintenant du type `timedelta64`

```{code-cell} ipython3
# à vous
df1['duration'] = pd.to_timedelta(df1['duration'])
df1
```

```{code-cell} ipython3
:tags: [raises-exception]

# pour vérifier - doit retourner True

df1.duration.dtype == 'timedelta64[ns]'
```

### duration == seconds ?

+++

à présent qu'on a converti `duration` dans le bon type, on peut utiliser toutes les fonctions disponibles sur ce type.  
en pratique ça se fait en deux temps

* sur l'objet `Series` on applique l'attribut `dt` pour, en quelque sorte, se projeter dans l'espace des 'date-time'  
  c'est exactement comme on l'a vu déjà avec le `.str` lorsqu'on a eu besoin d'appliquer des méthodes comme `.lower()` ou `replace()` sur les chaines et non pas sur la série  
  plus de détails ici <https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.html>

* de là on peut appeler toutes les méthodes disponibles sur les objets `timedelta` - on pourra en particulier s'intéresser à `total_seconds`

- notez aussi l'existance de `df['duration'].dt.components`

+++ {"tags": ["level_basic"]}

vérifiez que la colonne `seconds` correspond bien à `duration`

```{code-cell} ipython3
df1['duration'].dt.total_seconds() == df1['seconds']
```

### colonnes `hour` `minute` et `second`

+++ {"tags": ["level_basic"]}

rajoutez les colonnes `hour` `minute` et `second` de type entier à votre dataframe

deux approches:
- "à la main": on fait les calculs nous-mêmes à partir de la colonne `'seconds'`
- on utilise l'accesseur `dt` de la colonne `'duration'`

```{code-cell} ipython3
# des indices
import numpy as np

# une durée de 1h, 1m, 1s
one_hour = np.timedelta64(1, 'h')
one_minute = np.timedelta64(1, 'm')
one_second = np.timedelta64(1, 's')

# une durée de 2 hours 25 minutes 30 seconds
random_duration = 2*one_hour + 25*one_minute + 30*one_second

print(random_duration)

quotient, reste = random_duration // one_hour, random_duration % one_hour

quotient, reste
```

+++ {"tags": ["level_basic"]}

maintenant qu'on sait faire tout ça, on peut calculer les colonnes `hour`, `minute` et `second`

```{code-cell} ipython3
df1['hour'] = (df1['seconds']//3600)
df1['minute'] = ((df1['seconds']/3600 -df1['hour'])*60).astype(dtype = int)
df1['second'] = ((df1['seconds']/3600 -df1['hour'] - df1['minute']/60)*3600).astype(dtype = int)
df1
```

```{code-cell} ipython3
:tags: [raises-exception]

# pour vérifier, vous décommentez tout ceci et ça doit afficher True
(    np.all(df1.loc[0, ['hour', 'minute', 'second']] == [2, 6, 29])
 and df1.hour.dtype == int
 and df1.minute.dtype == int 
 and df1.second.dtype == int)
```

supprimer les colonnes 'hour', 'minute', 'second' que vous avez ajoutées:

```{code-cell} ipython3
df1.drop(columns = ['hour', 'minute', 'second'])
```

#### version paresseuse avec `dt.components`

il se trouve qu'on peut faire le même travail sans s'embêter autant, une fois qu'on découvre que [l'accesseur `.dt` possède un attribut qui donne accès à ce genre de détails ](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.dt.components.html)

```{code-cell} ipython3
df1['hour'] = df1['duration'].dt.components['hours']
df1['minute'] = df1['duration'].dt.components['minutes']
df1['second'] = df1['duration'].dt.components['seconds']
df1
```

```{code-cell} ipython3
:tags: [raises-exception]

# pour vérifier: même consigne
(    np.all(df1.loc[0, ['hour', 'minute', 'second']] == [2, 6, 29])
 and df1.hour.dtype == int
 and df1.minute.dtype == int 
 and df1.second.dtype == int)
```

```{code-cell} ipython3

```
