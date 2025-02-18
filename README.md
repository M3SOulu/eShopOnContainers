# Temporal Community Detection in Developer Collaboration Networks of Microservice Projects

This is a fork of eShopOnContainers repository for the ECSA2024 paper Temporal Community Detection in Developer Collaboration
Networks of Microservice Projects.

This branch contains the data replication instructions for the developer coupling network construction and analysis.
All the other branches are original branches and are used for commit mining.

All commands should be run in the root of this repository.

## Installing necessary packages

This replication package uses libraries `mison==1.0.1` and `tenetan==0.2.0` available on PyPI.
For preservation reasons, the source of those version of the libraries is available in this replication package.
It is thus only necessary to install requirements packages from [requirements.txt](requirements.txt):

```shell
python -m pip install -r requirements.txt
```

## Obtaining the developer collaboration networks

For developer collaboration network mining, use the `mison` python package.

### Mining the commits

Mine the commits corresponding to the studied releases with the `mison` commands

```shell
python -m mison commit --backend pydriller --import_mapping_file mison.mappings.eshoponcontainers --repo . --to 2017-10-27 --commit_table data_commits/eshop_commits_2.0.0.csv
python -m mison commit --backend pydriller --import_mapping_file mison.mappings.eshoponcontainers --repo . --to 2018-04-05 --since 2017-10-27 --commit_table data_commits/eshop_commits_2.0.5.csv
python -m mison commit --backend pydriller --import_mapping_file mison.mappings.eshoponcontainers --repo . --since 2018-04-05 --to 2018-11-12 --commit_table data_commits/eshop_commits_2.0.8.csv
python -m mison commit --backend pydriller --import_mapping_file mison.mappings.eshoponcontainers --repo . --since 2018-11-12 --to 2019-03-21 --commit_table data_commits/eshop_commits_2.2.0.csv
python -m mison commit --backend pydriller --import_mapping_file mison.mappings.eshoponcontainers --repo . --to 2019-11-26 --since 2019-03-21 --commit_table data_commits/eshop_commits_3.0.0.csv
python -m mison commit --backend pydriller --import_mapping_file mison.mappings.eshoponcontainers --repo . --since 2019-11-26 --to 2021-10-27 --commit_table data_commits/eshop_commits_5.0.0.csv
```

### Constructing the networks

Process the commits information into file and service developer collaboration networks for each release:

NOTE: order of lines will not be preserved relative to example files that are already in this repository, but the content is the same.

```shell
# File networks
python -m mison network --commit_table data_commits/eshop_commits_2.0.0.csv --field file --skip_zero --network_output data_file_network_raw/eshop_network_files_2.0.0.csv
python -m mison network --commit_table data_commits/eshop_commits_2.0.5.csv --field file --skip_zero --network_output data_file_network_raw/eshop_network_files_2.0.5.csv
python -m mison network --commit_table data_commits/eshop_commits_2.0.8.csv --field file --skip_zero --network_output data_file_network_raw/eshop_network_files_2.0.8.csv
python -m mison network --commit_table data_commits/eshop_commits_2.2.0.csv --field file --skip_zero --network_output data_file_network_raw/eshop_network_files_2.2.0.csv
python -m mison network --commit_table data_commits/eshop_commits_3.0.0.csv --field file --skip_zero --network_output data_file_network_raw/eshop_network_files_3.0.0.csv
python -m mison network --commit_table data_commits/eshop_commits_5.0.0.csv --field file --skip_zero --network_output data_file_network_raw/eshop_network_files_5.0.0.csv

# Service networks
python -m mison network --commit_table data_commits/eshop_commits_2.0.0.csv --field service --skip_zero --network_output data_service_network_raw/eshop_network_services_2.0.0.csv
python -m mison network --commit_table data_commits/eshop_commits_2.0.5.csv --field service --skip_zero --network_output data_service_network_raw/eshop_network_services_2.0.5.csv
python -m mison network --commit_table data_commits/eshop_commits_2.0.8.csv --field service --skip_zero --network_output data_service_network_raw/eshop_network_services_2.0.8.csv
python -m mison network --commit_table data_commits/eshop_commits_2.2.0.csv --field service --skip_zero --network_output data_service_network_raw/eshop_network_services_2.2.0.csv
python -m mison network --commit_table data_commits/eshop_commits_3.0.0.csv --field service --skip_zero --network_output data_service_network_raw/eshop_network_services_3.0.0.csv
python -m mison network --commit_table data_commits/eshop_commits_5.0.0.csv --field service --skip_zero --network_output data_service_network_raw/eshop_network_services_5.0.0.csv
```

### Cleaning the data

The script [`clear_data.py`](clear_data.py) removes `(none)` developer and maps duplicate emails of the same people
from [email_map](email_map.json) to the same email (release `5.0.0` ommited due to lack of collaboration as described in the paper):

```shell
# File networks
python clear_data.py data_file_network_raw/eshop_network_files_2.0.0.csv data_file_network_clean/eshop_network_files_2.0.0.csv email_map.json
python clear_data.py data_file_network_raw/eshop_network_files_2.0.5.csv data_file_network_clean/eshop_network_files_2.0.5.csv email_map.json
python clear_data.py data_file_network_raw/eshop_network_files_2.0.8.csv data_file_network_clean/eshop_network_files_2.0.8.csv email_map.json
python clear_data.py data_file_network_raw/eshop_network_files_2.2.0.csv data_file_network_clean/eshop_network_files_2.2.0.csv email_map.json
python clear_data.py data_file_network_raw/eshop_network_files_3.0.0.csv data_file_network_clean/eshop_network_files_3.0.0.csv email_map.json

# Service networks
python clear_data.py data_service_network_raw/eshop_network_services_2.0.0.csv data_service_network_clean/eshop_network_services_2.0.0.csv email_map.json
python clear_data.py data_service_network_raw/eshop_network_services_2.0.5.csv data_service_network_clean/eshop_network_services_2.0.5.csv email_map.json
python clear_data.py data_service_network_raw/eshop_network_services_2.0.8.csv data_service_network_clean/eshop_network_services_2.0.8.csv email_map.json
python clear_data.py data_service_network_raw/eshop_network_services_2.2.0.csv data_service_network_clean/eshop_network_services_2.2.0.csv email_map.json
python clear_data.py data_service_network_raw/eshop_network_services_3.0.0.csv data_service_network_clean/eshop_network_services_3.0.0.csv email_map.json
```

## Temporal community detection

For developer collaboration network temporal community detection, use the `tenetan` python package:

### Choosing the amount of communities R to detect based on Core Consistency

The script [`choice_of_R.py`](choice_of_R.py) repeats the PARAFAC decomposition of the network tensors for different choices of R 
 and creates the 'elbow' figure to allow selection of the optimal parameter value [cc_elbow](cc_elbow.png).
It also saves the calculated core consistencies for [file](core_consistency_file_network_ncomm_max_6.json)
and [service](core_consistency_service_network_ncomm_max_6.json) networks in json files.

We choose `R=2` for file network and `R=5` for service networks.


### Fitting and saving community models

The script [`save_models.py`](save_models.py) fits the selected amount of communities to the file and service network
and saves them as pickled data to [file_communities.pkl](file_communities.pkl) and [service_communities.pkl](service_communities.pkl)
files for further steps.


### Plotting community structures

The script [`community_structure.py`](community_structure.py) makes the plot of developers strengths of belonging to different communities,
and saves it to figure [`communities.png`](communities.png).


### Plotting temporal activities

The script [`temporal_activity.py`](temporal_activity.py) makes the plot of communities temporal activities and saves it
to figure [`temporal_activity.png`](temporal_activity.png).