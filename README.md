# biodiversity-project
This project interpret data from the National Parks Service about endangered species in different parks.
import pandas as pd
import numpy as np

from matplotlib import pyplot as plt
import seaborn as sns

%matplotlib inline

#loading tha species data
species = pd.read_csv('species_info.csv',encoding='utf-8')
species.head()

#category - The category of taxonomy for each species
#scientific_name - The scientific name of each species
#common_names - The common names of each species
#conservation_status - The species conservation status

#loading tha observations data
observations = pd.read_csv('observations.csv', encoding='utf-8')
observations.head()

# cientific_name- The scientific name of each species
# park_name - The name of the national park
# observations - The number of observations in the past 7 days

# investigate the data
print(f"species shape:{species.shape}")
print(f"species obs.:{observations.shape}")

print(f"number of species:{species.scientific_name.nunique()}")

print(f"nnumber of categories:{species.category.nunique()}")
print(f"categories:{species.category.unique()}")

print(f"nnumber of categories:{species.category.nunique()}")
print(f"categories:{species.category.unique()}")

species.groupby("category").size()

print(f"number of conservation statuses:{species.conservation_status.nunique()}")
print(f"unique conservation statuses:{species.conservation_status.unique()}")

print(f"na values:{species.conservation_status.isna().sum()}")

print(species.groupby("conservation_status").size())

print(f"number of parks:{observations.park_name.nunique()}")
print(f"unique parks:{observations.park_name.unique()}")

print(f"number of observations:{observations.observations.sum()}")

#analysis
species.fillna('No Intervention', inplace=True)
species.groupby("conservation_status").size()

conservationCategory = species[species.conservation_status != "No Intervention"]\
    .groupby(["conservation_status", "category"])['scientific_name']\
    .count()\
    .unstack()

conservationCategory

ax = conservationCategory.plot(kind = 'bar', figsize=(8,6), 
                               stacked=True)
ax.set_xlabel("Conservation Status")
ax.set_ylabel("Number of Species");

species['is_protected'] = species.conservation_status != 'No Intervention'

category_counts = species.groupby(['category', 'is_protected'])\
                        .scientific_name.nunique()\
                        .reset_index()\
                        .pivot(columns='is_protected',
                                      index='category',
                                      values='scientific_name')\
                        .reset_index()
category_counts.columns = ['category', 'not_protected', 'protected']

category_counts

category_counts['percent_protected'] = category_counts.protected / \
                                      (category_counts.protected + category_counts.not_protected) * 100

category_counts

#statistic significant
from scipy.stats import chi2_contingency

contingency1 = [[30, 146],
              [75, 413]]
chi2_contingency(contingency1)

contingency2 = [[30, 146],
               [5, 73]]
chi2_contingency(contingency2)

#species in park
from itertools import chain
import string

def remove_punctuations(text):
    for punctuation in string.punctuation:
        text = text.replace(punctuation, '')
    return text

common_Names = species[species.category == "Mammal"]\
    .common_names\
    .apply(remove_punctuations)\
    .str.split().tolist()

common_Names[:6]

cleanRows = []

for item in common_Names:
    item = list(dict.fromkeys(item))
    cleanRows.append(item)
    
cleanRows[:6]

res = list(chain.from_iterable(i if isinstance(i, list) else [i] for i in cleanRows))
res[:6]

words_counted = []

for i in res:
    x = res.count(i)
    words_counted.append((i,x))

pd.DataFrame(set(words_counted), columns =['Word', 'Count']).sort_values("Count", ascending = False).head(10)

species['is_bat'] = species.common_names.str.contains(r"\bBat\b", regex = True)

species.head(10)

species[species.is_bat]

bat_observations = observations.merge(species[species.is_bat])
bat_observations

bat_observations.groupby('park_name').observations.sum().reset_index()

obs_by_park = bat_observations.groupby(['park_name', 'is_protected']).observations.sum().reset_index()
obs_by_park

plt.figure(figsize=(16, 4))
sns.barplot(x=obs_by_park.park_name, y= obs_by_park.observations, hue=obs_by_park.is_protected)
plt.xlabel('National Parks')
plt.ylabel('Number of Observations')
plt.title('Observations of Bats per Week')
plt.show()
