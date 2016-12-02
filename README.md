# EasyMiner - Easy association rule mining on the web

EasyMiner is an open source web-based project for data mining based on association rules.
Key features:
- complete Prediction API (PAPI) - RESTful
- interactive web UI working in all modern web browsers
- association rule mining (based on Apriori algorithm/[arules](https://github.com/mhahsler/arules/) package in R)
- classification model building and rule pruning (based on Classification based on Associations/CBA/ algorithm)

## Project web
http://easyminer.eu

## Repository content (where do I see the issues and commits?)
EasyMiner project is composed of three independently developed components:
- **[EasyMinerCenter](https://github.com/KIZI/EasyMiner-EasyMinerCenter)**
  - user interaction (web UI, RESTful API)
  - written in PHP/JavaScript 
- **[EasyMiner-Backend](https://github.com/KIZI/EasyMiner-Backend)**
  - data manipulation and handling of mining tasks
  - three services: data service, preprocessing service and miner service
  - written in Scala
- **[rCBA](https://github.com/jaroslav-kuchar/rCBA)**
  - implementation of the CBA algorithm used for rule pruning and building of classification models
  - compiled version is available also in [CRAN repository](https://cran.r-project.org/package=rCBA)
  - written in Java 8
- **[Evaluation](https://github.com/KIZI/EasyMiner-Evaluation)**
  - evaluation framework for EasyMiner
  - covers 40 UCI datasets
  - written in Python

When cloning project content do not forget to clone also all the linked submodules (recursively) with:
```git
 git clone --recursive https://github.com/KIZI/EasyMiner.git 
```

## Installation instructions

This is an installation package of the Easyminer bundle for the docker environment. This installation contains backend, frontend and database.  We offer two versions of the software. Completely free version with R backend and an on-request  version with Hadoop/Spark backend. An overview of available REST endpoints is provided in the end of the document.

### EasyMiner with R backend

Note: The HTTP_SERVER_ADDR has to contain an address to a **docker host** that is reachable from both any docker container and your internet browser.

Requirements: Docker 1.12+

```bash
#!/bin/bash
#user inputs
HTTP_SERVER_ADDR=<docker-server>

#commands
docker network create easyminer
docker pull mysql:5.7
docker run --name easyminer-mysql -e MYSQL_ROOT_PASSWORD=root --network easyminer -d mysql:5.7
docker build -t easyminer-frontend https://github.com/KIZI/EasyMiner-EasyMinerCenter.git#:docker
docker run -d -p 8894:80 --name easyminer-frontend -e HTTP_SERVER_NAME="$HTTP_SERVER_ADDR:8894" --network easyminer easyminer-frontend
docker build -t easyminer-backend https://github.com/KIZI/EasyMiner-Backend.git#:docker
docker run -d -p 8893:8893 -p 8891:8891 -p 8892:8892 --name easyminer-backend -e EM_USER_ENDPOINT=http://easyminer-frontend/easyminercenter --network easyminer easyminer-backend
```

* Web GUI: http://\<docker-server\>:8894/easyminercenter
* Frontend re-install page: http://\<docker-server\>:8894/easyminercenter/install (password: 12345)

### EasyMiner with Hadoop/Spark backend

In order to build this version of EasyMiner you will need bitbucket private key file and the kerberos keytab file. These files are provided upon request (see license notice below).

Requirements: Docker 1.12+

Please note that in the script below, you should use absolute paths to the bitbucket private key file and to the kerberos keytab file.

```bash
#!/bin/bash
#user inputs
HTTP_SERVER_ADDR=<docker-server>
BITBUCKET_PRIVATE_KEY=<bitbucket-private-key-file>
EASYMINER_KERBEROS_KEYTAB=<easyminer-kerberos-keytab-file>

#commands
docker network create easyminer
docker pull mysql:5.7
docker run --name easyminer-mysql -e MYSQL_ROOT_PASSWORD=root --network easyminer -d mysql:5.7
docker build -t easyminer-frontend https://github.com/KIZI/EasyMiner-EasyMinerCenter.git#v2.4:docker
docker run -d -p 8894:80 --name easyminer-frontend -e HTTP_SERVER_NAME="$HTTP_SERVER_ADDR:8894" --network easyminer easyminer-frontend
git clone -b v2.0 https://bitbucket.org/easyminer/easyminer-docker.git
cd easyminer-docker
cp $BITBUCKET_PRIVATE_KEY ./bitbucket-private-key
cp $EASYMINER_KERBEROS_KEYTAB ./easyminer.keytab
docker build -t easyminer-backend .
docker run -d -p 8893:8893 -p 8891:8891 -p 8892:8892 --name easyminer-backend -e EM_USER_ENDPOINT=http://easyminer-frontend/easyminercenter --network easyminer easyminer-backend
```

* Web GUI: http://\<docker-server\>:8894/easyminercenter
* Frontend re-install page: http://\<docker-server\>:8894/easyminercenter/install (password: 12345)

#### License

The Hadoop version is provided for free to (i) members of CESNET for non-commercial academic use. In cases not covered by (i) it is mandatory to contact the copyright holder for commercial license for components, which were not released independently under a free license. 

The software comes with absolutely no warranty.

### Additional information

REST API endpoints are accessible on:

* http://\<docker-server\>:8891/easyminer-data/index.html - data service
* http://\<docker-server\>:8892/easyminer-preprocessing/index.html - preprocessing service
* http://\<docker-server\>:8893/easyminer-miner/index.html - miner service
* http://\<docker-server\>:8894/easyminercenter/api - easyminer central service 

## Issue tracking
EasyMiner is composed of three components, which are maintained separately. Before posting an issue, please select the right issue tracker. 
- Issues related to the user interface: [https://github.com/KIZI/EasyMiner-EasyMinerCenter/issues](https://github.com/KIZI/EasyMiner-EasyMinerCenter/issues)
- Issues related to data handling and processing:  [https://github.com/KIZI/EasyMiner-Backend/issues](https://github.com/KIZI/EasyMiner-Backend/issues)
- Issues related to the rule learning and pruning algorithm: [https://github.com/jaroslav-kuchar/rCBA/issues](https://github.com/jaroslav-kuchar/rCBA/issues)

If you are not sure which one to pick, don't worry, your issue will be migrated to the right repository.
## License
EasyMiner components are licensed under [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0). 

Copyright 2016 Department of Information and Knowledge Engineering, University of Economics, Prague

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
