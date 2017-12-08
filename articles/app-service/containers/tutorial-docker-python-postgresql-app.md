---
title: Compilare un'app Web Python Docker e PostgreSQL in Azure | Microsoft Docs
description: "Informazioni su come ottenere un'app Docker Python che è possibile usare in Azure con connessione a un database PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2017
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 161d9fda75caa7836e012e6e1ff79df576281137
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Compilare un'app Web Python Docker e PostgreSQL in Azure

Le app Web per contenitori forniscono un servizio di hosting Web con scalabilità elevata e con funzioni di auto-correzione. Questa esercitazione illustra come creare un'app Web Python Docker di base in Azure. Questa app verrà connessa a un database PostgreSQL. Al termine, si avrà un'applicazione Python Flask in esecuzione in un contenitore Docker nel [Servizio app in Linux](app-service-linux-intro.md).

![App Python Flask di Docker nel Servizio app in Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

In MacOS è possibile seguire la procedura seguente. Sebbene le istruzioni per Linux e Windows siano le stesse nella maggior parte dei casi, le differenze non sono descritte in questa esercitazione.
 
## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

1. [Installare Git](https://git-scm.com/)
1. [Installare Python](https://www.python.org/downloads/)
1. [Installare ed eseguire PostgreSQL](https://www.postgresql.org/download/)
1. [Installare Docker Community Edition](https://www.docker.com/community-edition)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testare l'installazione di PostgreSQL locale e creare un database

Aprire la finestra terminale ed eseguire `psql postgres` per connettersi al server PostgreSQL locale.

```bash
psql postgres
```

Se la connessione ha esito positivo, il database PostgreSQL è in esecuzione. In caso contrario, assicurarsi che il database PostgreSQL locale venga avviato seguendo la procedura descritta in [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Download - Distribuzione di base di PostgreSQL).

Creare un database denominato *eventregistration* e configurare un utente di database separato denominato *manager* con password *supersecretpass*.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Digitare *\q* per uscire dal client PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Creare l'applicazione locale Python Flask

In questo passaggio si configura il progetto Python Flask locale.

### <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Aprire la finestra del terminale e usare il comando `CD` per passare a una directory di lavoro.

Eseguire i comandi seguenti per clonare il repository di esempio e passare alla versione *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Questo repository di esempio contiene un'applicazione [Flask](http://flask.pocoo.org/). 

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
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Al termine del caricamento dell'app viene visualizzato un messaggio simile al seguente:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Andare a `http://127.0.0.1:5000` in un browser. Fare clic su **Registra!** e creare un utente test.

![Applicazione Python Flask in esecuzione in locale](./media/tutorial-docker-python-postgresql-app/local-app.png)

L'applicazione di esempio Flask archivia i dati utente nel database. Dopo aver completato la registrazione di un utente, l'app scriverà i dati nel database PostgreSQL locale.

Per arrestare il server Flask in qualsiasi momento, digitare Ctrl+C nel terminale. 

## <a name="create-a-production-postgresql-database"></a>Creare un database di produzione PostgreSQL

In questo passaggio si crea un database PostgreSQL in Azure. Quando viene distribuita in Azure, l'app usa questo database basato sul cloud.

### <a name="log-in-to-azure"></a>Accedere ad Azure

Verrà ora usata l'interfaccia della riga di comando di Azure 2.0 per creare le risorse necessarie per ospitare l'applicazione Python in app Web per contenitori.  Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#az_login) e seguire le istruzioni visualizzate.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md) con il comando [az group create](/cli/azure/group#az_group_create).

[!INCLUDE [Resource group intro](../../../includes/resource-group.md)]

L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti occidentali:

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

Usare il comando dell'interfaccia della riga di comando di Azure [az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations) per visualizzare un elenco dei percorsi disponibili.

### <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un server PostgreSQL con il comando [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create).

Nel comando seguente sostituire con un nome di server univoco il segnaposto *\<postgresql_name>* e con un nome utente il segnaposto *\<admin_username>*. Poiché il nome del server viene usato come parte dell'endpoint di PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), è necessario che il nome sia univoco in tutti i server di Azure. Il nome utente viene usato per l'account dell'utente amministratore del database iniziale. Viene richiesto di scegliere una password per questo utente.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>
```

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

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Creare una regola del firewall per il database di Azure per il server PostgreSQL

Eseguire il comando dell'interfaccia della riga di comando di Azure seguente per consentire l'accesso al database da tutti gli indirizzi IP.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

L'interfaccia della riga di comando di Azure conferma la creazione della regola del firewall con un output simile all'esempio seguente:

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

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>Creare un database vuoto e impostare un nuovo utente dell'applicazione del database

Creare un utente del database con accesso solo a un singolo database. Verranno usate queste credenziali per evitare di concedere al server accesso completo all'applicazione.

Connettersi al database. Viene richiesta la password dell'amministratore.

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Creare il database e l'utente dall'interfaccia della riga di comando di PostgreSQL.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Digitare *\q* per uscire dal client PostgreSQL.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Testare l'applicazione in locale rispetto al database PostgreSQL di Azure

Se si torna alla cartella *app* del repository Github clonato è possibile eseguire l'applicazione Python Flask aggiornando le variabili di ambiente del database.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Al termine del caricamento dell'app viene visualizzato un messaggio simile al seguente:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Passare a http://127.0.0.1:5000 in un browser. Fare clic su **Registra!** e creare una registrazione test. I dati vengono ora scritti nel database in Azure.

![Applicazione Python Flask in esecuzione in locale](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Esecuzione dell'applicazione da un contenitore Docker

Creare l'immagine del contenitore Docker.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker mostra un messaggio di conferma relativo alla corretta creazione del contenitore.

```bash
Successfully built 7548f983a36b
```

Aggiungere le variabili di ambiente del database in un file delle variabili di ambiente *db.env*. L'app si connetterà al database di produzione di Azure per PostgreSQL.

```text
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

Eseguire l'app dall'interno del contenitore Docker. Il comando seguente specifica il file delle variabili di ambiente ed esegue il mapping della porta Flask predefinita 5000 alla porta locale 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

L'output è simile a quello illustrato in precedenza. Tuttavia, la migrazione del database iniziale non deve essere più eseguita e pertanto verrà ignorata.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Il database contiene già la registrazione creata in precedenza.

![Applicazione Python Flask basata sul contenitore Docker in esecuzione in locale](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Caricare il contenitore Docker in un registro contenitori

In questo passaggio il contenitore Docker viene caricato in un registro contenitori. Verrà usato il registro contenitori di Azure. È possibile tuttavia usare anche altri registri comuni, ad esempio l'hub Docker.

### <a name="create-an-azure-container-registry"></a>Creare un Registro contenitori di Azure

Per creare un registro contenitori, nel comando seguente sostituire *\<registry_name>* con un nome di registro contenitori di Azure univoco di propria scelta.

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

Per mostrare le credenziali del registro, abilitare innanzitutto la modalità di amministrazione.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Vengono visualizzate due password. Prendere nota del nome utente e della prima password.

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

Creare un piano di servizio app con il comando [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create).

[!INCLUDE [app-service-plan](../../../includes/app-service-plan-linux.md)]

L'esempio seguente crea un piano di servizio app basato su Linux denominato *myAppServicePlan* che usa il piano tariffario S1:

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

Creare un'app Web nel piano di servizio app *myAppServicePlan* con il comando [az webapp create](/cli/azure/webapp#az_webapp_create).

L'app Web fornisce uno spazio host per distribuire il codice e un URL in cui visualizzare l'applicazione distribuita. Usarlo per creare l'app Web.

Nel comando seguente sostituire il segnaposto *\<app_name>* con un nome di app univoco. Poiché questo nome è incluso nell'URL predefinito dell'app Web, è necessario che sia univoco in tutte le app del servizio app di Azure.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Al termine della creazione dell'app Web, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

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
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>Configurare le variabili dell'ambiente del database

In precedenza in questa esercitazione sono state definite variabili di ambiente per la connessione al database PostgreSQL.

Nel servizio app le variabili di ambiente vengono impostate come _impostazioni dell'app_ usando il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set).

L'esempio seguente specifica i dettagli di connessione del database come impostazioni dell'app. Viene usata anche la variabile *PORT* per eseguire il mapping della porta 5000 dal contenitore Docker per ricevere il traffico HTTP nella porta 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Configurare la distribuzione del contenitore Docker

AppService può automaticamente di scaricare ed eseguire un contenitore Docker.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Ogni volta che si aggiorna il contenitore Docker o si modificano le impostazioni, riavviare l'app. Il riavvio garantisce che tutte le impostazioni vengano applicate e che venga estratto dal registro il contenitore più recente.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure 

Passare all'app Web distribuita usando il Web browser. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> Il caricamento dell'app Web richiede più tempo poiché dopo la modifica della configurazione il contenitore deve essere scaricato e avviato.

Vengono visualizzati gli utenti guest registrati in precedenza salvati nel database di produzione di Azure nel passaggio precedente.

![Applicazione Python Flask basata sul contenitore Docker in esecuzione in locale](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Congratulazioni.** L'app Python Flask basata sul contenitore Docker è in esecuzione nel Servizio app di Azure.

## <a name="update-data-model-and-redeploy"></a>Aggiornare il modello di dati e ridistribuire

In questo passaggio viene aggiunto il numero di partecipanti a ogni registrazione eventi tramite l'aggiornamento del modello Guest.

Verificare la versione *0.2-migration* con il comando Git seguente:

```bash
git checkout tags/0.2-migration
```

Questa versione ha già apportato le modifiche necessarie a visualizzazioni, ai controller e al modello. Include anche una migrazione del database generata tramite *alembic* (`flask db migrate`). È possibile visualizzare tutte le modifiche apportate tramite il comando Git seguente:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Testare le modifiche in locale

Eseguire i comandi seguenti per testare le modifiche in locale eseguendo il server Flask.

Mac/Linux:
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Passare a http://127.0.0.1:5000 nel browser per visualizzare le modifiche. Creare una registrazione test.

![Applicazione Python Flask basata sul contenitore Docker in esecuzione in locale](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

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

![App Docker Python Flask nel Servizio app di Azure](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al [portale di Azure](https://portal.azure.com) per visualizzare l'app Web creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app Web. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Pagina del servizio app nel portale di Azure](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](../app-service-web-tutorial-custom-domain.md)
