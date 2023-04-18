
Toute la documentation est disponible [ici](https://docs.mindsdb.com/what-is-mindsdb), je ne détaillerai pas les installations cloud et venv.



# Sous Docker

Principale avantage |Principale Inconvénient
-|-
Facile et rapide à déployer, pollue peu l'envirronement de travail | Cuda plus compliqué à installer, un peu plus demandant en ressources




## Pour un déploiement basique : 


> <span style="color: red; font-weight: bold">⚠️ Attention :</span> Si vous souhaitez utiliser cuda avec une installation docker, lisez [le paragraphe suivant](#cuda-avec-docker) afin d'installer cuda avant de lancer l'image de MindsDB


```bash
docker run -p 47334:47334 -p 47335:47335 -p 47336:47336 mindsdb/mindsdb
```


docker télécharge automatiquement l'image depuis docker hub et ouvre les ports :
- `47334` permettant d'accéder à l'interface du client web de MindsDB
- `47335` le port d'accès à l'api MySQL (pour se connecter à notre instance MindsDB depuis MySQL CLI ou Workbench)
- `47336` le port d'accès à l'api MongoDB (pour se connecter à notre instance MindsDB depuis mongocli ou Mongo Compass)



Il est également possible de monter un dossier afin de partager les données stockées par MindsDB. Le système de stockage sera précisé dans la partie [Fonctionnement simplifié]()


```bash
mkdir mdb_data
docker run -p 47334:47334 -p 47335:47335 -v $(pwd)/mdb_data:/root/mdb_storage mindsdb/mindsdb
```



## Cuda avec Docker


><span style="color: Turquoise; font-weight: bold">ℹ️Information :</span> La partie suivante <span style="color: Turquoise">concerne principalement les distributions Debian et Ubuntu</span>, il est également possible de faire [l'installation sur wsl2](https://docs.nvidia.com/cuda/wsl-user-guide/index.html) sur un ordinateur Windows. 


Il faut d'abord regarder si nvidia-smi est bien installé. Pour cela on peut utiliser la commande suivant :

```bash
nvidia-smi
```


Si elle ne fonctionne pas, vous pouvez lire le paragraphe [donnant des précisions sur cuda](#précisions-sur-cuda).


Ensuite, il faut installer [nvidia-runtime-container](https://github.com/NVIDIA/nvidia-container-runtime/), la documentation est disponible sur le [site de documentation de nvidia](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#setting-up-nvidia-container-toolkit).


> <span style="color: red; font-weight: bold">⚠️ Attention :</span> si on veut utiliser Nvidia runtime container avec le sécure boot d'activé, il est nécessaire de [signer ](https://download.nvidia.com/XFree86/Linux-x86_64/440.44/README/installdriver.html#modulesigning)  ! 


Il faut tout d'abord ajouter le dépot github à apt :

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/experimental/$distribution/libnvidia-container.list | \
         sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
         sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```


Pour pouvoir installer nvidia-container-runtime :

```bash
sudo apt-get update
sudo apt-get install nvidia-container-runtime
```


On configure et on redémarre ensuite docker :

```bash
sudo nvidia-ctk runtime configure --runtime=docker && \
	sudo systemctl restart docker
```


Et on peut utiliser Cuda sous docker :

```bash
docker run --runtime=nvidia --gpus all -p 47334:47334 -p 47335:47335 -p 47336:47336 mindsdb/mindsdb
```


><span style="color: Turquoise; font-weight: bold">ℹ️Information :</span> l'option `--gpus` permet de spécifier quels GPU on veut attribuer au docker, ce qui peut être pratique dans un cluster. Par exemple : `--gpus '"device=0,2"'` authorise docker à utiliser les cartes graphiques 0 et 2



# Sous Conda

Principale avantage |Principale Inconvénient
-|-
Installation de pyTorche et de Cuda simplifiée avec python | plus compliqué à configurer notamment sur la restriction de la puissance de calcul, moins sécurisé dans le cas d'une installation sur un réseau



><span style="color: Turquoise; font-weight: bold">ℹ️Information :</span> Toutes les commandes suivante sont pour des environnements Linux, dans le cas d'une installation Windows, il faudra changer les `python3` en `py`


> <span style="color: red; font-weight: bold">⚠️ Attention :</span> Pour le moment (17/04/2023), MindsDB ne supporte que <span style="color: red;">python 3.7 et 3.8 </span>, il faut donc faire attention à votre version de l'environnement conda !


## Installation de conda :


Il est recommendé d'installer [Miniconda](https://docs.conda.io/en/latest/miniconda.html#) et non [Anaconda](), on n'aura pas besion de la plus part des fonctionnalités de Anaconda.
- [Installeur linux](https://docs.conda.io/en/latest/miniconda.html#linux-installers) et [Instructions](https://conda.io/projects/conda/en/latest/user-guide/install/linux.html)
- [Installeur Windows](https://docs.conda.io/en/latest/miniconda.html#linux-installers) et [Instructions](https://conda.io/projects/conda/en/latest/user-guide/install/windows.html)


Ensuite On commence par créer un environnement Conda et on l'active :

```bash
conda create -n mindsdb
conda activate mindsdb
```


Dans cet environnement, on met à jour setuptools, wheel puis on installe MindsDB

```bash
pip install --upgrade pip setuptools wheel
pip install mindsdb
```


Pour vérifier l'installation, on peut exécuter la commande suivante

```bash
conda list

	...
    lightgbm==3.3.0
    lightwood==22.4.1.0
    MindsDB==22.4.5.0
    mindsdb-datasources==1.8.2
    mindsdb-sql==0.3.3
    mindsdb-streams==0.0.5
    ...
```


Pour lancer MindsDB avec toutes les api, il faut ajouter le paramètre `--api` :

```bash
python -m mindsdb --api=http,mongodb,mysql
```


## Cuda avec Conda



# Précisions sur Cuda

installation des non-free



# Deux pythons sur 
