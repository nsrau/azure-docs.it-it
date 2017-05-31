---
title: Usare Docker Compose in una VM Linux in Azure | Documentazione Microsoft
description: Come usare Docker e Compose in macchine virtuali Linux con l&quot;interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: ad9759f20135a87356819d5b819eab357b688cdc
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Introduzione a Docker e Compose per la definizione e l'esecuzione di un'applicazione multi-contenitore in Azure
Con [Compose](http://github.com/docker/compose) si usa un file di testo semplice per definire un'applicazione costituita da più contenitori Docker. Si avvia quindi l'applicazione mediante un unico comando che effettua le operazioni necessarie per distribuire l'ambiente definito. Come esempio, questo articolo illustra come configurare rapidamente un blog WordPress con un database SQL MariaDB back-end in una macchina virtuale di Ubuntu. È possibile usare Compose anche per configurare applicazioni più complesse.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Configurare una macchina virtuale Linux come host Docker
È possibile usare diverse procedure di Azure e le immagini o i modelli di Resource Manager disponibili in Azure Markeplace per creare una macchina virtuale di Linux da configurare come host Docker. Ad esempio, vedere [Using the Docker VM Extension to deploy your environment](dockerextension.md) (Uso dell'estensione di VM Docker per distribuire l'ambiente) per creare rapidamente una VM Ubuntu con l'estensione VM Docker di Azure tramite un [modello di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Quando si usa l'estensione di VM Docker, la macchina virtuale viene automaticamente configurata come host Docker e Compose è già installato.


### <a name="create-docker-host-with-azure-cli-20"></a>Creare un host Docker con l'interfaccia della riga di comando di Azure 2.0
Installare la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e accedere a un account Azure tramite il comando [az login](/cli/azure/#login).

Innanzitutto, creare un gruppo di risorse per l'ambiente di Docker con il comando [az group create](/cli/azure/group#create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *westus*:

```azurecli
az group create --name myResourceGroup --location westus
```

Successivamente, distribuire una macchina virtuale con il comando [az group deployment create](/cli/azure/group/deployment#create) che include l'estensione di VM Docker di Azure da [questo modello di Azure Resource Manager su GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Specificare i propri valori per *newStorageAccountName*, *adminUsername*, *adminPassword* e *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

L'operazione di distribuzione richiede alcuni minuti. Al termine della distribuzione, [procedere al passaggio successivo](#verify-that-compose-is-installed) per configurare SSH sulla macchina virtuale. 

Facoltativamente, per restituire il controllo al prompt e per consentire la distribuzione continua in background, aggiungere il flag `--no-wait` al comando precedente. Questo processo consente di eseguire altre operazioni nell'interfaccia della riga di comando mentre la distribuzione continua per alcuni minuti. È possibile visualizzare i dettagli sullo stato dell'host Docker con il comando [az vm show](/cli/azure/vm#show). L'esempio seguente controlla lo stato della macchina virtuale denominata *myDockerVM* (il nome predefinito del modello, non modificare questo nome) che appartiene al gruppo di risorse *myResourceGroup*:

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --query [provisioningState] \
    --output tsv
```

Quando questo comando restituisce *Succeeded*, la distribuzione è stata completata ed è possibile configurare SSH sulla macchina virtuale nel passaggio seguente.


## <a name="verify-that-compose-is-installed"></a>Verificare che Compose sia installato
Al termine della distribuzione, stabilire una connessione SSH al nuovo host Docker con il nome DNS specificato durante la distribuzione. È possibile usare `az vm show -g myResourceGroup -n myDockerVM -d --query [fqdns] -o tsv` per visualizzare i dettagli della VM, incluso il nome DNS.

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Per verificare l'installazione di Compose nella macchina virtuale, eseguire il comando seguente:

```bash
docker-compose --version
```

Si ottiene un output simile a *docker-compose 1.6.2, build 4d72027*.

> [!TIP]
> Se è stato usato un altro metodo per creare un host Docker ed è necessario installare Compose manualmente, vedere la [Compose documentation](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)(Documentazione di Compose).


## <a name="create-a-docker-composeyml-configuration-file"></a>Creare un file di configurazione docker-compose.yml
In seguito viene creato un file `docker-compose.yml` , che è semplicemente un file di configurazione di testo, per definire i contenitori Docker ed eseguirli nella macchina virtuale. Il file specifica l'immagine da eseguire su ciascun contenitore (o può essere una compilazione da un file Docker), le variabili d'ambiente, le dipendenze necessarie, le porte e i collegamenti tra i contenitori. Per informazioni dettagliate sulla sintassi dei file YML, vedere [Compose file reference](http://docs.docker.com/compose/yml/)(Informazioni di riferimento sui file di Compose).

Creare il file *docker-compose.yml* come segue:

```bash
touch docker-compose.yml
```

Usare un editor di testo per aggiungere dati al file. L'esempio seguente usa l'editor *vi*:

```bash
vi docker-compose.yml
```

Incollare l'esempio seguente nel file di testo. Questa configurazione utilizza le immagini del [Registro di sistema DockerHub](https://registry.hub.docker.com/_/wordpress/) per installare WordPress (il sistema di creazione blog e gestione del contenuto open source) e un database SQL MariaDB back-end collegato. Immettere il proprio valore *MYSQL_ROOT_PASSWORD* come segue:

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Avviare i contenitori con Compose
Nella stessa directory del file *docker-compose.yml* eseguire il comando seguente (a seconda dell'ambiente, può essere necessario eseguire `docker-compose` usando `sudo`):

```bash
docker-compose up -d
```

Il comando avvia i contenitori Docker specificati in *docker-compose.yml*. L'esecuzione di questo passaggio richiede un paio di minuti. L'output sarà simile all'esempio seguente:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> Assicurarsi di utilizzare l’opzione **-d** all'avvio in modo che i contenitori vengano eseguiti in background continuamente.


Per verificare che i contenitori siano attivi, digitare `docker-compose ps`. Dovrebbe essere visualizzata una schermata analoga alla seguente:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Ora è possibile connettersi a WordPress direttamente nella VM dalla porta 80. Aprire un Web browser e immettere il nome DNS della VM (ad esempio `http://mypublicdns.westus.cloudapp.azure.com`). Viene visualizzata la schermata di avvio di WordPress, in cui è possibile completare l'installazione e iniziare a utilizzare l’applicazione.

![Schermata iniziale di WordPress][wordpress_start]

## <a name="next-steps"></a>Passaggi successivi
* Vedere la [Docker VM extension user guide](https://github.com/Azure/azure-docker-extension/blob/master/README.md) (Guida dell'utente dell'estensione di VM Docker) per altre opzioni di configurazione di Docker e Compose nella VM Docker. Ad esempio, un'opzione consiste nell'inserire il file YML di Compose (convertito in JSON) direttamente nella configurazione dell'estensione della VM Docker.
* Per altri esempi di compilazione e distribuzione di app multi-contenitore, vedere [Compose command-line reference](http://docs.docker.com/compose/reference/) (Informazioni di riferimento sulla riga di comando di Compose) e la [guida dell'utente](http://docs.docker.com/compose/).
* Utilizzare un modello di Gestione risorse di Azure, quello proprio o uno fornito dalla [community](https://azure.microsoft.com/documentation/templates/), per distribuire una macchina virtuale di Azure con Docker e un'applicazione configurata con Compose. Ad esempio, il modello [Distribuire un blog WordPress con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) utilizza Docker e Compose per distribuire rapidamente WordPress con un back-end MySQL in una VM Ubuntu.
* Provare a integrare Docker Compose con un cluster Docker Swarm. Per gli scenari, vedere [Using Compose with Swarm](https://docs.docker.com/compose/swarm/) (Uso di Swarm con Compose).

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png

