---
title: Usare Docker Compose in una VM Linux in Azure | Documentazione Microsoft
description: Come installare e usare Docker e Compose in macchine virtuali Linux con l'interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 03501ea774cf58a4be88ed9155e5cfdfb99f0379
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007197"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Introduzione a Docker e Compose per la definizione e l'esecuzione di un'applicazione multi-contenitore in Azure
Con [Compose](https://github.com/docker/compose) si usa un file di testo semplice per definire un'applicazione costituita da più contenitori Docker. Si avvia quindi l'applicazione mediante un unico comando che effettua le operazioni necessarie per distribuire l'ambiente definito. Come esempio, questo articolo illustra come configurare rapidamente un blog WordPress con un database SQL MariaDB back-end in una macchina virtuale di Ubuntu. È possibile usare Compose anche per configurare applicazioni più complesse.

Questo articolo è stato testato l'ultima volta il 14/02/2019 tramite [Azure Cloud Shell](https://shell.azure.com/bash) e l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) versione 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Creare un host Docker con l'interfaccia della riga di comando di Azure
Installare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e accedere all'account di Azure con il comando [az login](/cli/azure/reference-index).

Innanzitutto, creare un gruppo di risorse per l'ambiente di Docker con il comando [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Creare un file denominato *cloud-init.txt* e incollare la configurazione seguente. Immettere `sensible-editor cloud-init.txt` per creare il file e visualizzare un elenco degli editor disponibili. 

```yaml
#include https://get.docker.com
```

Creare quindi una macchina virtuale con il comando [az vm create](/cli/azure/vm#az-vm-create). Usare il parametro `--custom-data` per specificare il file di configurazione di cloud-init. Se il file è stato salvato all'esterno della directory di lavoro corrente, specificare il percorso completo della configurazione *cloud-init.txt* . L'esempio seguente crea una macchina virtuale denominata *myDockerVM* e apre la porta 80 al traffico Web.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

Per creare la macchina virtuale, installare i pacchetti e avviare l'applicazione sono necessari alcuni minuti. Sono presenti attività in background la cui esecuzione continua dopo che l'interfaccia della riga di comando di Azure è tornata al prompt. Dopo aver creato la macchina virtuale, prendere nota del `publicIpAddress` visualizzato dall'interfaccia della riga di comando di Azure. 

                 

## <a name="install-compose"></a>Installare Compose


Stabilire una connessione SSH alla nuova macchina virtuale host di Docker. Fornire il proprio indirizzo IP.

```bash
ssh azureuser@10.10.111.11
```

Installare Compose nella macchina virtuale.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Creare un file di configurazione docker-compose.yml
Creare un file di configurazione `docker-compose.yml` per definire i contenitori Docker ed eseguirli nella macchina virtuale. Il file specifica l'immagine da eseguire in ogni contenitore, le variabili d'ambiente e le dipendenze necessarie, le porte e i collegamenti tra i contenitori. Per informazioni dettagliate sulla sintassi dei file YML, vedere [Compose file reference](https://docs.docker.com/compose/compose-file/)(Informazioni di riferimento sui file di Compose).

Creare un file *docker-compose.yml*. Usare un editor di testo per aggiungere dati al file. L'esempio seguente crea il file con un prompt per `sensible-editor` per la selezione dell'editor da usare.

```bash
sensible-editor docker-compose.yml
```

Incollare l'esempio seguente nel file di Docker Compose. Questa configurazione utilizza le immagini del [Registro di sistema DockerHub](https://registry.hub.docker.com/_/wordpress/) per installare WordPress (il sistema di creazione blog e gestione del contenuto open source) e un database SQL MariaDB back-end collegato. Immettere il valore *MYSQL_ROOT_PASSWORD* personalizzato.

```yml
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
sudo docker-compose up -d
```

Il comando avvia i contenitori Docker specificati in *docker-compose.yml*. L'esecuzione di questo passaggio richiede un paio di minuti. Verrà visualizzato un output simile al seguente:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Per verificare che i contenitori siano attivi, digitare `sudo docker-compose ps`. Dovrebbe essere visualizzata una schermata analoga alla seguente:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Ora è possibile connettersi a WordPress direttamente nella VM dalla porta 80. Aprire un Web browser e immettere l'indirizzo IP della macchina virtuale. Viene visualizzata la schermata di avvio di WordPress, in cui è possibile completare l'installazione e iniziare a utilizzare l’applicazione.

![Schermata iniziale di WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Passaggi successivi
* Per altri esempi di compilazione e distribuzione di app multi-contenitore, vedere [Compose command-line reference](https://docs.docker.com/compose/reference/) (Informazioni di riferimento sulla riga di comando di Compose) e la [guida dell'utente](https://docs.docker.com/compose/).
* Utilizzare un modello di Gestione risorse di Azure, quello proprio o uno fornito dalla [community](https://azure.microsoft.com/documentation/templates/), per distribuire una macchina virtuale di Azure con Docker e un'applicazione configurata con Compose. Ad esempio, il modello [Distribuire un blog WordPress con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) utilizza Docker e Compose per distribuire rapidamente WordPress con un back-end MySQL in una VM Ubuntu.
* Provare a integrare Docker Compose con un cluster Docker Swarm. Per gli scenari, vedere [Using Compose with Swarm](https://docs.docker.com/compose/swarm/) (Uso di Swarm con Compose).

