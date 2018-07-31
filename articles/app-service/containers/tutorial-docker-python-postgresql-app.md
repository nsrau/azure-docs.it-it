---
title: Creare un'app Web Python e PostgreSQL in Servizio app di Azure | Microsoft Docs
description: Informazioni su come eseguire un'app Python basata sui dati con connessione a un database PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 20b549914daf71c0d23235b5c20ebb6f14367471
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172035"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Compilare un'app Web Python Docker e PostgreSQL in Azure

Le app Web per contenitori forniscono un servizio di hosting Web con scalabilità elevata e con funzioni di auto-correzione. Questa esercitazione illustra come creare un'app Web Python basata sui dati usando PostgreSQL come back-end del database. Al termine, si avrà un'applicazione Python Flask in esecuzione in un contenitore Docker nel [Servizio app in Linux](app-service-linux-intro.md).

![App Python Flask di Docker nel Servizio app in Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database PostgreSQL in Azure
> * Connettere un'app Python a PostgreSQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Gestire l'app nel portale di Azure

I passaggi illustrati in questo articolo possono essere eseguiti in macOS. Sebbene le istruzioni per Linux e Windows siano le stesse nella maggior parte dei casi, le differenze non sono descritte in questa esercitazione.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

1. [Installare Git](https://git-scm.com/)
1. [Installare Python](https://www.python.org/downloads/)
1. [Installare ed eseguire PostgreSQL](https://www.postgresql.org/download/)
1. [Installare Docker Community Edition](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testare l'installazione di PostgreSQL locale e creare un database

In una finestra del terminale locale eseguire `psql` per connettersi al server PostgreSQL locale.

```bash
sudo -u postgres psql
```

Se la connessione ha esito positivo, il database PostgreSQL è in esecuzione. In caso contrario, assicurarsi che il database PostgreSQL locale venga avviato seguendo la procedura descritta in [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Download - Distribuzione di base di PostgreSQL).

Creare un database denominato *eventregistration* e configurare un utente di database separato denominato *manager* con password *supersecretpass*.

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Digitare `\q` per uscire dal client PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-app"></a>Creare un'app Python locale

In questo passaggio si configura il progetto Python Flask locale.

### <a name="clone-the-sample-app"></a>Clonare l'app di esempio

Aprire la finestra del terminale e usare il comando `CD` per passare a una directory di lavoro.

Eseguire i comandi seguenti per clonare il repository di esempio e passare alla versione *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Questo repository di esempio contiene un'applicazione [Flask](http://flask.pocoo.org/). 

### <a name="run-the-app-locally"></a>Eseguire l'app in locale

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

Andare a `http://localhost:5000` in un browser. Fare clic su **Registra!** e creare un utente test.

![Applicazione Python Flask in esecuzione in locale](./media/tutorial-docker-python-postgresql-app/local-app.png)

L'applicazione di esempio Flask archivia i dati utente nel database. Dopo aver completato la registrazione di un utente, l'app scriverà i dati nel database PostgreSQL locale.

Per arrestare il server Flask in qualsiasi momento, digitare Ctrl+C nel terminale. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>Creare un database di produzione PostgreSQL

In questo passaggio si crea un database PostgreSQL in Azure. Quando viene distribuita in Azure, l'app usa questo database basato sul cloud.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un server PostgreSQL con il comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) in Cloud Shell.

Nel comando di esempio seguente sostituire *\<postgresql_name>* con un nome server univoco e sostituire *\<admin_username>* e *\<admin_password>* con le credenziali utente desiderate. Poiché il nome del server viene usato come parte dell'endpoint di PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), è necessario che il nome sia univoco in tutti i server di Azure. Le credenziali utente sono per l'account utente amministratore del database. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name GP_Gen4_2
```

Quando il database di Azure termina la creazione del server PostgreSQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "<admin_username>",
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

### <a name="create-a-firewall-rule-for-the-postgresql-server"></a>Creare una regola del firewall per il server PostgreSQL

In Cloud Shell eseguire il comando dell'interfaccia della riga di comando di Azure seguente per consentire l'accesso al database da tutti gli indirizzi IP. Quando sia l'IP iniziale che l'IP finale sono impostati su `0.0.0.0`, il firewall viene aperto solo per le altre risorse di Azure. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

> [!TIP] 
> È possibile rendere ancora più restrittiva la regola del firewall [usando solo gli indirizzi IP in uscita usati dall'app](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

In Cloud Shell eseguire di nuovo il comando per consentire l'accesso al database dal computer locale sostituendo *\<you_ip_address>* con l'[indirizzo IPv4 locale](https://whatismyipaddress.com/). 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<you_ip_address> --end-ip-address=<you_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Connettere l'app Python al database di produzione

In questo passaggio si connette l'app di esempio Flask al database di Azure per il server PostgreSQL creato.

### <a name="create-empty-database-and-user-access"></a>Creare un database vuoto e l'accesso utente

In Cloud Shell connettersi al database eseguendo `psql`. Quando viene richiesta la password dell'amministratore, usare la stessa password specificata in [Creare un database di Azure per il server PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Creare il database e l'utente dall'interfaccia della riga di comando di PostgreSQL.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Digitare `\q` per uscire dal client PostgreSQL.

### <a name="test-app-connectivity-to-production-database"></a>Testare la connettività dell'app al database di produzione

Tornare nella finestra del terminale locale, eseguire i comandi seguenti per eseguire la migrazione del database Flask e del server Flask.

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

Andare a http://localhost:5000 in un browser. Fare clic su **Registra!** e creare una registrazione test. I dati vengono ora scritti nel database in Azure.

![Applicazione Python Flask in esecuzione in locale](./media/tutorial-docker-python-postgresql-app/local-app.png)

## <a name="upload-app-to-a-container-registry"></a>Caricare l'app in un registro contenitori

In questo passaggio si crea un'immagine Docker e la si carica in Registro contenitori di Azure. È anche possibile usare registri diffusi, come Docker Hub.

### <a name="build-the-docker-image-and-test-it"></a>Compilare l'immagine Docker e testarla

Nella finestra del terminale locale creare l'immagine Docker.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker mostra un messaggio di conferma relativo alla corretta creazione dell'immagine.

```bash
Successfully built 7548f983a36b
```

Nella radice del repository aggiungere un file di variabili di ambiente denominato _db.env_ e quindi aggiungervi le variabili di ambiente del database seguenti. L'app si connetterà al database di produzione di Azure per PostgreSQL.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Eseguire l'immagine in locale in un contenitore Docker. Il comando seguente specifica il file delle variabili di ambiente ed esegue il mapping della porta Flask predefinita 5000 alla porta locale 5000.

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

Dopo avere verificato che il contenitore funzioni in locale, eliminare _db.env_. In Servizio app di Azure si useranno le impostazioni dell'app per definire le variabili di ambiente.  

### <a name="create-an-azure-container-registry"></a>Creare un Registro contenitori di Azure

In Cloud Shell creare un registro in Registro contenitori di Azure con il comando seguente. Sostituire *\<registry_name >* con un nome di registro univoco.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

### <a name="retrieve-registry-credentials"></a>Recuperare le credenziali del registro

In Cloud Shell eseguire i comandi seguenti per recuperare le credenziali del registro. Saranno necessarie per effettuare push e pull di immagini.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Nell'output vengono visualizzate due password. Prendere nota del nome utente (ovvero il nome del registro per impostazione predefinita) e della prima password.

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

### <a name="upload-docker-image-to-registry"></a>Caricare l'immagine Docker nel registro

Dalla finestra del terminale locale, accedere al nuovo registro con `docker`. Quando richiesto, specificare la password recuperata.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Eseguire il push dell'immagine Docker nel registro.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="create-web-app-with-uploaded-image"></a>Creare un'app Web con l'immagine caricata

In questo passaggio si crea un'app in Servizio app di Azure e la si configura per l'uso dell'immagine Docker caricata in Registro contenitori di Azure.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creare un'app Web

In Cloud Shell creare un'app Web nel piano di servizio app *myAppServicePlan* con il comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create).

Nel comando seguente sostituire il segnaposto *\<app_name>* con un nome di app univoco. Poiché questo nome è incluso nell'URL predefinito dell'app Web, è necessario che sia univoco in tutte le app del servizio app di Azure.

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
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

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

In precedenza in questa esercitazione sono state definite variabili di ambiente per la connessione al database PostgreSQL.

Nel servizio app, le variabili di ambiente vengono configurate come _impostazioni dell'app_ usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

L'esempio seguente specifica i dettagli di connessione del database come impostazioni dell'app. Usa inoltre la variabile *WEBSITES_PORT* per la porta del contenitore 5000, che consente al contenitore di ricevere il traffico HTTP sulla porta 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" WEBSITES_PORT=5000
```

### <a name="configure-custom-container-deployment"></a>Configurare la distribuzione del contenitore personalizzato

Anche se è già stato specificato il nome dell'immagine del contenitore, è comunque necessario specificare l'URL del registro personalizzato e le credenziali utente. In Cloud Shell eseguire il comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

In Cloud Shell riavviare l'app. Il riavvio garantisce che tutte le impostazioni vengano applicate e che venga estratto dal registro il contenitore più recente.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure 

Passare all'app Web distribuita. 

```bash 
http://<app_name>.azurewebsites.net 
```

> [!NOTE]
> L'avvio dell'app Web richiede tempo perché il contenitore deve essere scaricato ed eseguito quando l'app viene richiesta per la prima volta. Se inizialmente viene visualizzato un errore dopo un lungo periodo, è sufficiente aggiornare la pagina.

Vengono visualizzati gli utenti guest registrati in precedenza salvati nel database di produzione di Azure nel passaggio precedente.

![Applicazione Python Flask basata sul contenitore Docker in esecuzione in locale](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Congratulazioni** Si sta eseguendo un'app Python in App Web per contenitori.

## <a name="update-data-model-and-redeploy"></a>Aggiornare il modello di dati e ridistribuire

In questo passaggio si aggiunge il numero di partecipanti a ogni registrazione per l'evento aggiornando il modello `Guest`.

Nella finestra del terminale locale estrarre la versione *0.2-migration* con il comando Git seguente:

```bash
git checkout tags/0.2-migration
```

Questa versione ha già apportato le modifiche necessarie al modello, alle visualizzazioni e ai controller. Include anche una migrazione del database generata tramite *alembic* (`flask db migrate`). È possibile visualizzare tutte le modifiche apportate tramite il comando Git seguente:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Testare le modifiche in locale

Nella finestra del terminale locale eseguire questi comandi per testare le modifiche in locale eseguendo il server Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Passare a http://localhost:5000 nel browser per visualizzare le modifiche. Creare una registrazione test.

![Applicazione Python Flask basata sul contenitore Docker in esecuzione in locale](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

Nella finestra del terminale locale compilare la nuova immagine Docker ed eseguirne il push nel registro.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

In Cloud Shell riavviare l'app per assicurarsi che venga eseguito il pull del contenitore più recente dal registro.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
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
