---
title: Creare un'app multi-contenitore (anteprima) in App Web per contenitori
description: Informazioni su come usare più contenitori in Azure, con file di configurazione Docker Compose e Kubernetes, con un'app WordPress e MySQL.
keywords: servizio app di Azure, app Web, linux, docker, compose, multicontenitore, multi-contenitore, app Web per contenitori, più contenitori, contenitore, kubernetes, wordpress, db azure per mysql, database di produzione con contenitori
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: c8002b10e2c7c0823cd59bf300283d04fca5ce2e
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49390211"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Esercitazione: Creare un'app multi-contenitore (anteprima) in App Web per contenitori

[App Web per contenitori](app-service-linux-intro.md) offre un modo flessibile per usare le immagini Docker. In questa esercitazione si apprenderà come creare un'app multi-contenitore usando WordPress e MySQL. Questa esercitazione verrà completata in Cloud Shell, ma gli stessi comandi possono essere eseguiti anche in locale con l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.32 o versioni successive).

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Convertire una configurazione Docker Compose per l'uso con App Web per contenitori
> * Convertire una configurazione Kubernetes per l'uso con App Web per contenitori
> * Distribuire un'app multi-contenitore in Azure
> * Aggiungere le impostazioni applicazione
> * Usare l'archiviazione permanente per i contenitori
> * Connettersi a Database di Azure per MySQL
> * Risolvere gli errori

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario avere una buona conoscenza di [Docker Compose](https://docs.docker.com/compose/) o [Kubernetes](https://kubernetes.io/).

## <a name="download-the-sample"></a>Scaricare l'esempio

Per questa esercitazione si userà il file Compose di [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), ma modificato in modo da includere Database di Azure per MySQL, l'archiviazione permanente e Redis. Il file di configurazione è disponibile negli [Esempi di Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

In Cloud Shell creare una directory di esercitazione e passare a questa directory.

```bash
mkdir tutorial

cd tutorial
```

Eseguire quindi il comando seguente per clonare il repository dell'app di esempio nella directory di esercitazione. Quindi, passare alla directory `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

In Cloud Shell creare un gruppo di risorse con il comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *South Central US*. Per visualizzare tutte le località supportate per il servizio app in Linux nel livello **Standard**, eseguire il comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

In genere, il gruppo di risorse e le risorse vengono create in un'area vicina alla località dell'utente.

Al termine del comando, un output JSON visualizza le proprietà del gruppo di risorse.

## <a name="create-an-azure-app-service-plan"></a>Creare un piano di servizio app di Azure

In Cloud Shell creare un piano di servizio app nel gruppo di risorse con il comando [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` nel piano tariffario **Standard** (`--sku S1`) e in un contenitore Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Al termine della creazione del piano di servizio app, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="docker-compose-configuration-options"></a>Opzioni di configurazione di Docker Compose

Per questa esercitazione si userà il file Compose di [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), ma modificato in modo da includere Database di Azure per MySQL, l'archiviazione permanente e Redis. In alternativa, è possibile usare una [configurazione Kubernetes](#use-a-kubernetes-configuration-optional). I file di configurazione sono disponibili negli [Esempi di Azure](https://github.com/Azure-Samples/multicontainerwordpress).

Gli elenchi seguenti illustrano le opzioni di configurazione di Docker Compose supportate e non supportate in App Web per contenitori:

### <a name="supported-options"></a>Opzioni supportate

* command
* entrypoint
* environment
* image
* ports
* restart
* services
* volumes

### <a name="unsupported-options"></a>Opzioni non supportate

* build (non consentita)
* depends_on (ignorata)
* networks (ignorata)
* secrets (ignorata)

> [!NOTE]
> Tutte le altre opzioni non indicate in modo esplicito vengono ignorate anche nell'anteprima pubblica.

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>Docker Compose con contenitori WordPress e MySQL

## <a name="create-a-docker-compose-app"></a>Creare un'app Docker Compose

In Cloud Shell creare un'[app Web](app-service-linux-intro.md) multi-contenitore nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Non dimenticare di sostituire _\<app_name>_ con un nome univoco per l'app.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Dopo la creazione dell'app Web, Cloud Shell visualizza un output simile all'esempio seguente:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app_name>.azurewebsites.net`. Il caricamento dell'app può richiedere alcuni minuti. Se si riceve un errore, attendere qualche minuto e quindi aggiornare il browser. Se si verificano problemi, per la risoluzione esaminare i [log dei contenitori](#find-docker-container-logs).

![App multi-contenitore di esempio in App Web per contenitori][1]

La creazione di un'app multi-contenitore in App Web per contenitori è stata **completata**. Ora si configurerà l'app per l'uso di Database di Azure per MySQL. Non installare WordPress in questo momento.

## <a name="connect-to-production-database"></a>Connettersi al database di produzione

Non è consigliabile usare contenitori di database in un ambiente di produzione. I contenitori locali non sono scalabili. In questo caso si userà Database di Azure per MySQL, che è scalabile.

### <a name="create-an-azure-database-for-mysql-server"></a>Creare un server Database di Azure per MySQL

Creare un server Database di Azure per MySQL con il comando [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create).

Nel comando seguente sostituire il segnaposto _&lt;mysql_server_name>_ con il nome del server MySQL (i caratteri validi sono `a-z`, `0-9` e `-`). Poiché questo nome fa parte del nome host del server MySQL (`<mysql_server_name>.database.windows.net`) è necessario che sia univoco a livello globale.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

La creazione del server può richiedere alcuni minuti. Al termine della creazione del server MySQL, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configurare il firewall del server

Creare una regola del firewall per il server MySQL per consentire le connessioni client usando il comando [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create). Quando sia l'IP iniziale che l'IP finale sono impostati su 0.0.0.0, il firewall viene aperto solo per le altre risorse di Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> È possibile rendere ancora più restrittiva la regola del firewall [usando solo gli indirizzi IP in uscita usati dall'app](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Creare il database di WordPress

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

Al termine della creazione del database, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>Configurare le variabili di database in WordPress

Per connettere l'app WordPress a questo nuovo server MySQL occorre configurare alcune variabili di ambiente specifiche di WordPress, incluso il percorso della CA SSL definito da `MYSQL_SSL_CA`. Nell'[immagine personalizzata](https://docs.microsoft.com/azure/app-service/containers/tutorial-multi-container-app#use-a-custom-image-for-mysql-ssl-and-other-configurations) più avanti viene fornito il certificato [Baltimore CyberTrust Root](https://www.digicert.com/digicert-root-certificates.htm) di [DigiCert](http://www.digicert.com/).

Per apportare queste modifiche, usare il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. Le impostazioni delle app applicano la distinzione tra maiuscole e minuscole e sono separate da spazi.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Al termine della creazione delle impostazioni app, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
```

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Usare un'immagine personalizzata per SSL in MySQL e altre configurazioni

Per impostazione predefinita, Database di Azure per MySQL usa SSL. WordPress richiede una configurazione aggiuntiva per l'uso di SSL con MySQL. L'immagine ufficiale di WordPress non contiene la configurazione aggiuntiva, ma per comodità è stata preparata un'[immagine personalizzata](https://hub.docker.com/r/microsoft/multicontainerwordpress/builds/). Nella pratica, si aggiungeranno le modifiche desiderate alla propria immagine.

L'immagine personalizzata si basa sull'immagine ufficiale di [WordPress dall'hub Docker](https://hub.docker.com/_/wordpress/). In questa immagine personalizzata sono state apportate le modifiche seguenti per Database di Azure per MySQL:

* [Aggiunge il file di certificato Baltimore Cyber Trust Root per SSL a MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [Usa l'impostazione app per il certificato dell'autorità di certificazione SSL per MySQL nel file wp-config.php di WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [Aggiunge la definizione WordPress per MYSQL_CLIENT_FLAGS necessaria per SSL in MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

Per Redis (da usare in una sezione successiva) sono state apportate le modifiche seguenti:

* [Aggiunge l'estensione PHP per Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Aggiunge l'utilità di decompressione necessaria per l'estrazione dei file.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Aggiunge il plug-in WordPress Redis Object Cache 1.3.8.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Usa l'impostazione app per il nome host Redis nel file wp-config.php di WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Per usare l'immagine personalizzata è necessario aggiornare il file docker-compose-wordpress.yml. In Cloud Shell digitare `nano docker-compose-wordpress.yml` per aprire l'editor di testo Nano. Modificare `image: wordpress` in modo da usare `image: microsoft/multicontainerwordpress`. Il contenitore del database non è più necessario. Rimuovere le sezioni `db`, `environment`, `depends_on` e `volumes` dal file di configurazione. Il file deve essere simile al codice seguente:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Salvare le modifiche e uscire da Nano. Usare il comando `^O` per salvare e `^X` per uscire.

### <a name="update-app-with-new-configuration"></a>Aggiornare l'app con la nuova configurazione

In Cloud Shell riconfigurare l'[app Web](app-service-linux-intro.md) multi-contenitore con il comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Non dimenticare di sostituire _\<app_name>_ con il nome dell'app Web creata in precedenza.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Al termine della riconfigurazione dell'app, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
```

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app_name>.azurewebsites.net`. Ora l'app usa Database di Azure per MySQL.

![App multi-contenitore di esempio in App Web per contenitori][1]

## <a name="add-persistent-storage"></a>Aggiungere l'archiviazione permanente

L'app multi-contenitore è ora in esecuzione in App Web per contenitori. Tuttavia, se si installa WordPress adesso e si riavvia l'app in un secondo momento, si scoprirà che l'installazione di WordPress non è più presente. Questo accade perché la configurazione Docker Compose attualmente punta a un percorso di archiviazione all'interno del contenitore. I file installati nel contenitore non vengono mantenuti dopo il riavvio dell'app. In questa sezione si aggiungerà l'archiviazione permanente al contenitore WordPress.

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Per usare l'archiviazione permanente, è necessario abilitare questa impostazione all'interno del servizio app. Per apportare questa modifica, usare il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. Le impostazioni delle app applicano la distinzione tra maiuscole e minuscole e sono separate da spazi.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Al termine della creazione delle impostazioni app, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="modify-configuration-file"></a>Modificare il file di configurazione

In Cloud Shell digitare `nano docker-compose-wordpress.yml` per aprire l'editor di testo Nano.

L'opzione `volumes` esegue il mapping del file system a una directory all'interno del contenitore. `${WEBAPP_STORAGE_HOME}` è una variabile di ambiente nel servizio app mappata all'archiviazione permanente per l'app. Si userà questa variabile di ambiente nell'opzione volumes, in modo che i file di WordPress vengano installati nell'archiviazione permanente anziché nel contenitore. Apportare al file le modifiche seguenti:

Nella sezione `wordpress` aggiungere un'opzione `volumes` in modo che rispecchi il codice seguente:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Aggiornare l'app con la nuova configurazione

In Cloud Shell riconfigurare l'[app Web](app-service-linux-intro.md) multi-contenitore con il comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Non dimenticare di sostituire _\<app_name>_ con un nome univoco per l'app.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Dopo l'esecuzione del comando, l'output restituito è simile all'esempio seguente:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app_name>.azurewebsites.net`.

Ora il contenitore WordPress usa Database di Azure per MySQL e l'archiviazione permanente.

## <a name="add-redis-container"></a>Aggiungere il contenitore Redis

 L'immagine ufficiale di WordPress non include le dipendenze per Redis. Queste dipendenze e la configurazione aggiuntiva necessaria per l'uso di Redis con WordPress sono state preparate per comodità degli utenti in questa [immagine personalizzata](https://github.com/Azure-Samples/multicontainerwordpress). Nella pratica, si aggiungeranno le modifiche desiderate alla propria immagine.

L'immagine personalizzata si basa sull'immagine ufficiale di [WordPress dall'hub Docker](https://hub.docker.com/_/wordpress/). In questa immagine personalizzata sono state apportate le modifiche seguenti per Redis:

* [Aggiunge l'estensione PHP per Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Aggiunge l'utilità di decompressione necessaria per l'estrazione dei file.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Aggiunge il plug-in WordPress Redis Object Cache 1.3.8.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Usa l'impostazione app per il nome host Redis nel file wp-config.php di WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Aggiungere il contenitore Redis alla fine del file di configurazione in modo che risulti simile all'esempio seguente:

[!code-yml[Main](../../../azure-app-service-multi-container/compose-wordpress.yml)]

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Per usare Redis è necessario abilitare l'impostazione `WP_REDIS_HOST` all'interno del servizio app. Si tratta di un'*impostazione necessaria* perché WordPress possa comunicare con l'host Redis. Per apportare questa modifica, usare il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. Le impostazioni delle app applicano la distinzione tra maiuscole e minuscole e sono separate da spazi.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WP_REDIS_HOST="redis"
```

Al termine della creazione delle impostazioni app, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
```

### <a name="update-app-with-new-configuration"></a>Aggiornare l'app con la nuova configurazione

In Cloud Shell riconfigurare l'[app Web](app-service-linux-intro.md) multi-contenitore con il comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Non dimenticare di sostituire _\<app_name>_ con un nome univoco per l'app.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Dopo l'esecuzione del comando, l'output restituito è simile all'esempio seguente:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app_name>.azurewebsites.net`.

Completare i passaggi e installare WordPress.

### <a name="connect-wordpress-to-redis"></a>Connettere WordPress a Redis

Accedere all'interfaccia di amministrazione di WordPress. Nel riquadro di spostamento a sinistra, selezionare **Plugin** (Plug-in), quindi selezionare **Installed Plugins** (Plug-in installati).

![Selezionare i plug-in di WordPress][2]

Tutti i plug-in vengono visualizzati qui

Nella pagina dei plug-in individuare **Redis Object Cache** e fare clic su **Activate** (Attiva).

![Attivare Redis][3]

Fare clic su **Settings** (Impostazioni).

![Fare clic su Settings (Impostazioni)][4]

Fare clic sul pulsante **Enable Object Cache** (Abilita cache oggetti).

![Fare clic sul pulsante Enable Object Cache (Abilita cache oggetti)][5]

WordPress si connette al server Redis. Lo **stato** della connessione viene visualizzato nella stessa pagina.

![WordPress si connette al server Redis. Lo **stato** della connessione viene visualizzato nella stessa pagina.][6]

WordPress è ora **connesso** a Redis. A questo punto, l'app pronta per la produzione usa **Database di Azure per MySQL, l'archiviazione permanente e Redis**. È ora possibile scalare orizzontalmente il piano di servizio app per più istanze.

## <a name="use-a-kubernetes-configuration-optional"></a>Usare una configurazione Kubernetes (facoltativo)

In questa sezione si apprenderà come usare una configurazione Kubernetes per distribuire più contenitori. Assicurarsi di eseguire i passaggi precedenti per creare un [gruppo di risorse](#create-a-resource-group) e un [piano di servizio app](#create-an-azure-app-service-plan). Poiché la maggior parte dei passaggi sono simili a quello della sezione Compose, il file di configurazione è già stato combinato.

### <a name="supported-kubernetes-options-for-multi-container"></a>Opzioni Kubernetes supportate per l'uso di più contenitori

* args
* command
* containers
* image
* name
* ports
* spec

> [!NOTE]
>Tutte le altre opzioni di Kubernetes non indicate in modo esplicito non sono supportate nell'anteprima pubblica.
>

### <a name="kubernetes-configuration-file"></a>File di configurazione di Kubernetes

Per questa parte dell'esercitazione si userà *kubernetes-wordpress.yml*. Per riferimento, viene visualizzato di seguito:

[!code-yml[Main](../../../azure-app-service-multi-container/kubernetes-wordpress.yml)]

### <a name="create-an-azure-database-for-mysql-server"></a>Creare un server Database di Azure per MySQL

Creare un server in Database di Azure per MySQL con il comando [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create).

Nel comando seguente sostituire il segnaposto _&lt;mysql_server_name>_ con il nome del server MySQL (i caratteri validi sono `a-z`, `0-9` e `-`). Poiché questo nome fa parte del nome host del server MySQL (`<mysql_server_name>.database.windows.net`) è necessario che sia univoco a livello globale.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

Al termine della creazione del server MySQL, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configurare il firewall del server

Creare una regola del firewall per il server MySQL per consentire le connessioni client usando il comando [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create). Quando sia l'IP iniziale che l'IP finale sono impostati su 0.0.0.0, il firewall viene aperto solo per le altre risorse di Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> È possibile rendere ancora più restrittiva la regola del firewall [usando solo gli indirizzi IP in uscita usati dall'app](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Creare il database di WordPress

Se non è ancora stato creato un [server Database di Azure per MySQL](#create-an-azure-database-for-mysql-server), crearlo ora.

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

Al termine della creazione del database, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="create-a-multi-container-app-kubernetes"></a>Creare un'app multi-contenitore (Kubernetes)

In Cloud Shell creare un'[app Web](app-service-linux-intro.md) multi-contenitore nel gruppo di risorse `myResourceGroup` e il piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Non dimenticare di sostituire _\<app_name>_ con un nome univoco per l'app.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type kube --multicontainer-config-file kubernetes-wordpress.yml
```

Dopo la creazione dell'app Web, Cloud Shell visualizza un output simile all'esempio seguente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-database-variables-in-wordpress"></a>Configurare le variabili di database in WordPress

Per connettere l'app WordPress a questo nuovo server MySQL occorre configurare alcune variabili di ambiente specifiche di WordPress. Per apportare questa modifica, usare il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. Le impostazioni delle app applicano la distinzione tra maiuscole e minuscole e sono separate da spazi.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Al termine della creazione delle impostazioni app, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  }
]
```

### <a name="add-persistent-storage"></a>Aggiungere l'archiviazione permanente

L'app multi-contenitore è ora in esecuzione in App Web per contenitori. I dati verranno cancellati al riavvio perché i file non sono persistenti. In questa sezione si aggiungerà l'archiviazione permanente al contenitore WordPress.

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Per usare l'archiviazione permanente, è necessario abilitare questa impostazione all'interno del servizio app. Per apportare questa modifica, usare il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. Le impostazioni delle app applicano la distinzione tra maiuscole e minuscole e sono separate da spazi.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Al termine della creazione delle impostazioni app, Cloud Shell visualizza informazioni simili all'esempio seguente:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app_name>.azurewebsites.net`.

L'app ora esegue più contenitori in App Web per contenitori.

![App multi-contenitore di esempio in App Web per contenitori][1]

La creazione di un'app multi-contenitore in App Web per contenitori è stata **completata**.

Per usare Redis, seguire i passaggi descritti in [Connettere WordPress a Redis](#connect-wordpress-to-redis).

## <a name="find-docker-container-logs"></a>Trovare i log dei contenitori Docker

Se si verificano problemi relativi all'uso di più contenitori, è possibile accedere ai log dei contenitori passando a: `https://<app_name>.scm.azurewebsites.net/api/logs/docker`.

L'output sarà simile all'esempio seguente:

```json
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
```

Saranno disponibili un log per ogni contenitore e un log aggiuntivo per il processo padre. Copiare il valore `href` corrispondente nel browser per visualizzare il log.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

In questa esercitazione si è appreso come:
> [!div class="checklist"]
> * Convertire una configurazione Docker Compose per l'uso con App Web per contenitori
> * Convertire una configurazione Kubernetes per l'uso con App Web per contenitori
> * Distribuire un'app multi-contenitore in Azure
> * Aggiungere le impostazioni applicazione
> * Usare l'archiviazione permanente per i contenitori
> * Connettersi a Database di Azure per MySQL
> * Risolvere gli errori

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare un'immagine Docker personalizzata per App Web per contenitori](tutorial-custom-docker-image.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
