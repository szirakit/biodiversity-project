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
