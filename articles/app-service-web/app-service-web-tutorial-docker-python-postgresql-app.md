---
title: Compilare un&quot;app Web Python Docker e PostgreSQL in Azure | Microsoft Docs
description: "Informazioni su come ottenere un&quot;app Docker Python che è possibile usare in Azure con connessione a un database PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
editor: 
ms.assetid: 2bada123-ef18-44e5-be71-e16323b20466
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/03/2017
ms.author: beverst
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: ca086f76e50cb27f012bb2e7f05595be5fdcb241
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Compilare un'app Web Python Docker e PostgreSQL in Azure
Questa esercitazione illustra come creare un'app Web Python Docker di base in Azure. Questa app verrà poi connessa al database PostgreSQL. Al termine, si avrà un'applicazione Python Flask in esecuzione in un contenitore Docker nell'[app Web del servizio app di Azure](app-service-web-overview.md).

![App Docker Python Flask nel Servizio app di Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="before-you-begin"></a>Prima di iniziare

Prima di eseguire l'esempio, installare in locale questi prerequisiti:

1. [Scaricare e installare Git](https://git-scm.com/)
1. [Scaricare e installare Python](https://www.python.org/downloads/)
1. [Scaricare, installare ed eseguire PostgreSQL](https://www.postgresql.org/download/)
1. [Scaricare e installare Docker Community Edition](https://www.docker.com/community-edition)
1. [Scaricare e installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testare l'installazione di PostgreSQL locale e creare un database
In questo passaggio ci si assicura che il database PostgreSQL locale sia in esecuzione.

Aprire la finestra terminale ed eseguire `psql postgres` per connettersi al server PostgreSQL locale.

```bash
psql postgres
```

Se la connessione ha esito positivo significa che il database PostgreSQL è già in esecuzione. In caso contrario, assicurarsi che il database PostgreSQL locale venga avviato seguendo i passaggi descritti in [Scaricare, installare ed eseguire PostgreSQL](https://www.postgresql.org/download/).

Creare un database denominato `eventregistration` e configurare un utente del database separato denominato `manager` con password `supersecretpass`.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Digitare `\q` per uscire dal client PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Creare l'applicazione locale Python Flask
In questo passaggio si configura il progetto Python Flask locale.

### <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Aprire la finestra del terminale e usare il comando `CD` per passare a una directory di lavoro.  

Eseguire i comandi seguenti per clonare il repository di esempio e passare alla versione `0.1-initialapp`.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.gi
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Questo repository di esempio contiene un'applicazione [Flask](http://http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Eseguire l'applicazione

> [!NOTE] 
> In un passaggio successivo questo processo verrà semplificato creando un contenitore Docker da usare con il database di produzione.

Installare i pacchetti necessari e avviare l'applicazione.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Al termine del caricamento dell'applicazione viene visualizzato un messaggio simile al seguente:

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Andare a `http://127.0.0.1:5000` in un browser. Fare clic su **Registra!** e provare a creare una registrazione fittizia.

![Applicazione Python Flask in esecuzione in locale](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

L'applicazione di esempio Flask archivia i dati utente nel database. Se l'operazione ha esito positivo e l'utente può visualizzare la registrazione nell'app significa che l'app sta scrivendo i dati nel database PostgreSQL locale.

Per arrestare il server Flask in qualsiasi momento, digitare `Ctrl`+`C` nel terminale. 

## <a name="create-a-production-postgresql-database"></a>Creare un database di produzione PostgreSQL

In questo passaggio si crea un database PostgreSQL in Azure. Quando l'app viene distribuita in Azure, viene specificato questo database per il carico di lavoro di produzione.

### <a name="log-in-to-azure"></a>Accedere ad Azure

Verrà ora usata l'interfaccia della riga di comando di Azure 2.0 in una finestra terminale per creare le risorse necessarie per ospitare l'applicazione Python nel Servizio app di Azure.  Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate. 

```azurecli-interactive 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app Web, database e account di archiviazione. 

L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti occidentali:

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

Per visualizzare i possibili valori utilizzabili per `--location`, usare il comando `az appservice list-locations` dell'interfaccia della riga di comando di Azure.

### <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un server PostgreSQL con il comando [az postgres server create](/cli/azure/documentdb#create).

Nel comando seguente sostituire il segnaposto `<postgresql_name>` con il nome univoco dell'account PostgreSQL. Questo nome univoco viene usato come parte dell'endpoint di PostgreSQL, `https://<postgresql_name>.postgres.database.azure.com`, pertanto deve essere univoco per tutti i server di Azure. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <my_admin_username>
```

`--admin-user` è necessario per creare l'account dell'utente amministratore del database iniziale. Viene richiesto di scegliere una password per questo utente.

Quando il database di Azure termina la creazione del server PostgreSQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="creating-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Creazione di una regola del firewall per il database di Azure per il server PostgreSQL

Prima che sia possibile accedere al database, è necessario fare in modo che questo sia raggiungibile da tutti gli indirizzi IP. Questa operazione può essere eseguita tramite il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

In seguito alla creazione del firewall, l'interfaccia della riga di comando di Azure conferma la presenza di regole come indicato di seguito:

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>Connettere l'applicazione Python Flask al database

In questo passaggio si connette l'applicazione di esempio Python Flask al database di Azure per il server PostgreSQL creato.

### <a name="creating-an-empty-database-and-setting-up-a-new-database-application-user"></a>Creazione di un database vuoto e configurazione di un nuovo utente del database dell'applicazione

Viene creato un nuovo utente del database con accesso solo a un singolo database. Questo passaggio consente di evitare che l'applicazione abbia accesso completo al server tramite le credenziali dell'amministratore.

Connettersi al database per cui viene richiesta la password dell'amministratore.
```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Creare quindi il database e l'utente dall'interfaccia della riga di comando PostgreSQL.
```
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Digitare `\q` per uscire dal client PostgreSQL.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Testare l'applicazione in locale rispetto al database PostgreSQL di Azure 

Tornando indietro alla cartella `app` del repository Github clonato, è possibile eseguire l'applicazione Python Flask semplicemente aggiornando le variabili dell'ambiente del database.

```bash
FLASK_APP=app.py;DBHOST="<postgresql_name>.postgres.database.azure.com";DBUSER="manager@<postgresql_name>";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Al termine del caricamento dell'app viene visualizzato di nuovo un messaggio simile al seguente:

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Andare a `http://127.0.0.1:5000` in un browser. Fare clic su **Registra!** e provare a creare una registrazione fittizia. In questo modo vengono scritti i dati nel database di produzione in Azure.

![Applicazione Python Flask in esecuzione in locale](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Esecuzione dell'applicazione da un contenitore Docker

Verrà ora compilata l'immagine del contenitore Docker ed eseguita in locale l'applicazione dal contenitore Docker durante la connessione al database di produzione PostgreSQL in Azure.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker mostra un messaggio di conferma relativo alla corretta creazione del contenitore.

```
Successfully built 7548f983a36b
```

Aggiungere le variabili dell'ambiente del database in un file delle variabili dell'ambiente `db.env`.

```
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

È ora possibile eseguire l'app all'interno del contenitore Docker. Viene specificato il file delle variabili dell'ambiente e viene eseguito il mapping della porta Flask predefinita 5000 nella porta locale 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

Ovviamente, l'output è simile a quello precedente. Tuttavia, la migrazione del database iniziale non deve essere più eseguita e pertanto verrà ignorata.
```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 ```

 Il database contiene già la registrazione creata in precedenza.

 ![Applicazione Python Flask basata sul contenitore Docker in esecuzione in locale](./media/app-service-web-tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Caricare il contenitore Docker in un registro contenitori
In questo passaggio, verrà caricato il contenitore Docker creato in un registro contenitori. Si userà il Registro contenitori di Azure, tuttavia, è anche possibile usare altri registri comuni, ad esempio Hub Docker.

### <a name="create-an-azure-container-registry"></a>Creare un Registro contenitori di Azure

Per creare un registro contenitori, nel comando seguente sostituire `<registry_name>` con un nome del Registro contenitori di Azure univoco di propria scelta.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Output
```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Recuperare le credenziali del registro per eseguire il push e il pull delle immagini Docker

È innanzitutto necessario attivare la modalità di amministrazione prima di poter accedere alle credenziali.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Verranno visualizzate due password. Annotare il nome utente e la prima password man mano che si prosegue.
```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Caricare il contenitore Docker in un Registro contenitori di Azure

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Distribuire l'applicazione di Docker Python Flask in Azure
In questo passaggio, si distribuisce l'applicazione Python Flask basata sul contenitore Docker nel Servizio app di Azure.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Creare un piano di servizio app con il comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

L'esempio seguente crea un piano di servizio app basato su Linux denominato `myAppServicePlan` tramite il piano tariffario S1:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Al termine della creazione del piano di servizio app, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json 
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
``` 

### <a name="create-a-web-app"></a>Creare un'app Web

È stato creato un piano di servizio app`myAppServicePlan` e ora occorre creare un'app Web al suo interno. L'app Web fornisce uno spazio host per distribuire il codice e un URL in cui visualizzare l'applicazione distribuita. Usare il comando [az appservice web create](/cli/azure/appservice/web#create) per creare l'app Web. 

Nel comando seguente sostituire il segnaposto `<app_name>` con il nome univoco dell'app. Questo nome verrà usato come parte del nome di dominio predefinito per l'app Web, pertanto è necessario che sia univoco rispetto a tutte le app presenti in Azure. In un secondo momento è possibile eseguire il mapping di qualsiasi voce DNS personalizzata all'app Web prima di esporla agli utenti. 

```azurecli-interactive
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Al termine della creazione dell'app Web, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente: 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-the-database-environment-variables"></a>Configurare le variabili dell'ambiente del database

In precedenza nell'esercitazione, sono state definite manualmente le variabili dell'ambiente per la connessione al database PostgreSQL.

Nel servizio app si impostano le variabili di ambiente come _impostazioni dell'app_ usando il comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

L'operazione seguente consente di specificare i dettagli di connessione del database come impostazioni app. Viene anche usata la variabile `PORT` per specificare che si desidera eseguire il mapping della porta 5000 dal contenitore Docker per ricevere il traffico HTTP sulla porta 80.

```azurecli-interactive
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Configurare la distribuzione del contenitore Docker 

AppService può automaticamente di scaricare ed eseguire un contenitore Docker.

Usare il comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) per creare le credenziali a livello di account. 

```azurecli
az appservice web config container update --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Quando si aggiorna il contenitore Docker o si modificano le impostazioni sopra riportate, riavviare l'app per verificare che tutte le impostazioni vengono applicate e che il contenitore più recente viene estratto dal registro.

```azurecli-interactive
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure 
Passare all'app Web distribuita usando il Web browser. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> La prima volta che si accede all'app Web dopo aver apportato una modifica alla configurazione del contenitore, attendere che il contenitore venga scaricato e avviato.

Verranno visualizzati i guest registrati in precedenza salvati nel database di produzione di Azure nel passaggio precedente.

 ![Applicazione Python Flask basata sul contenitore Docker in esecuzione in locale](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Congratulazioni.** L'app Python Flask basata sul contenitore Docker è in esecuzione nel Servizio app di Azure.


## <a name="update-data-model-and-redeploy"></a>Aggiornare il modello di dati e ridistribuire

In questo passaggio, viene aggiunto il numero di partecipanti a ogni registrazione di eventi tramite l'aggiornamento del modello Guest.

Verificare la versione `0.2-migration` con il comando Git seguente.
```bash
git checkout tags/0.2-migration
```

Questa versione ha già apportato le modifiche necessarie a visualizzazioni, controller e al modello. Include anche una migrazione del database generata tramite *alembic* (`flask db migrate`). È possibile visualizzare tutte le modifiche apportate tramite il comando Git seguente.

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Testare le modifiche in locale

Eseguire i comandi seguenti per testare le modifiche in locale eseguendo il server Flask.

Mac/Linux:
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

Passare a `http://127.0.0.1:5000` nel browser per visualizzare le modifiche. Creare una nuova registrazione fittizia.

![Applicazione Python Flask basata sul contenitore Docker in esecuzione in locale](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

Creare la nuova immagine Docker, eseguirne il push nel registro contenitori e riavviare l'app.

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Passare all'app Web di Azure e provare ancora la nuova funzionalità. Creare un'altra registrazione dell'evento.

```bash 
http://<app_name>.azurewebsites.net 
```

![App Docker Python Flask nel Servizio app di Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al portale di Azure per visualizzare l'app Web creata.

A tale scopo, accedere a [https://portal.azure.com](https://portal.azure.com).

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/app-service-web-tutorial-docker-python-postgresql-app/app-resource.png)

Si accede così al _pannello_, ovvero una pagina del portale visualizzata in orizzontale, dell'app Web.

Per impostazione predefinita, nel pannello dell'app Web viene aperta la pagina **Panoramica**, che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro del pannello mostrano le diverse pagine di configurazione che è possibile aprire.

![Pannello del servizio app nel portale di Azure](./media/app-service-web-tutorial-docker-python-postgresql-app/app-mgmt.png)

Queste schede del pannello mostrano le numerose utili funzionalità che è possibile aggiungere all'app Web. Nell'elenco seguente sono riportate solo alcune delle possibilità:

* Eseguire il mapping di un nome DNS personalizzato
* Associare un certificato SSL personalizzato
* Configurare la distribuzione continua
* Aumentare le prestazioni e il numero di istanze
* Aggiungere l'autenticazione utente


