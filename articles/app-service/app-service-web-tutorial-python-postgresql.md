---
title: Creare un'app Web Python e PostgreSQL in Azure | Microsoft Docs
description: Informazioni su come ottenere un'app Python da usare in Azure con connessione a un database PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: de20dae10ae6b43adcbc5040a8a71ba5650bafec
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="build-a-python-and-postgresql-web-app-in-azure"></a>Creare un'app Web Python e PostgreSQL in Azure

> [!NOTE]
> Questo articolo consente di distribuire un'app nel servizio app in Windows. Per la distribuzione nel servizio app in _Linux_, vedere [Compilare un'app Web Python Docker e PostgreSQL in Azure](./containers/tutorial-docker-python-postgresql-app.md).
>

[Servizio app di Azure](app-service-web-overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questa esercitazione illustra come creare un'app Web Python di base in Azure. Questa app verrà connessa a un database PostgreSQL. Al termine, si avrà un'applicazione Python Flask in esecuzione nel servizio app.

![App Python Flask nel servizio app in Linux](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database PostgreSQL in Azure
> * Connettere un'app Python a MySQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Gestire l'app nel portale di Azure

I passaggi illustrati in questa esercitazione possono essere eseguiti in macOS. Sebbene le istruzioni per Linux e Windows siano le stesse nella maggior parte dei casi, le differenze non sono descritte in questa esercitazione.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione:

1. [Installare Git](https://git-scm.com/)
1. [Installare Python](https://www.python.org/downloads/)
1. [Installare ed eseguire PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testare l'installazione di PostgreSQL locale e creare un database

Aprire la finestra terminale ed eseguire `psql` per connettersi al server PostgreSQL locale.

```bash
sudo -u postgres psql
```

Se la connessione ha esito positivo, il database PostgreSQL è in esecuzione. In caso contrario, assicurarsi che il database PostgreSQL locale venga avviato seguendo la procedura descritta in [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Download - Distribuzione di base di PostgreSQL).

Creare un database denominato *eventregistration* e configurare un utente di database separato denominato *manager* con password *supersecretpass*.

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

Eseguire questi comandi per clonare il repository di esempio, quindi ripristinare il commit per l'app iniziale (prima di `modelChange`).

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

Questo repository di esempio contiene un'applicazione [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Eseguire l'applicazione

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

![Applicazione Python Flask in esecuzione in locale](./media/app-service-web-tutorial-python-postgresql/local-app.png)

L'applicazione di esempio Flask archivia i dati utente nel database. Dopo aver completato la registrazione di un utente, l'app scriverà i dati nel database PostgreSQL locale.

Per arrestare il server Flask in qualsiasi momento, digitare Ctrl+C nel terminale. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>Creare PostgreSQL in Azure

In questo passaggio si crea un database PostgreSQL in Azure. Quando viene distribuita in Azure, l'app usa questo database basato sul cloud.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>Creare un server PostgreSQL

Creare un server PostgreSQL con il comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

Nel comando seguente sostituire con un nome di server univoco il segnaposto *\<postgresql_name>* e con un nome utente il segnaposto *\<admin_username>*. Poiché il nome del server viene usato come parte dell'endpoint di PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), è necessario che il nome sia univoco in tutti i server di Azure. Il nome utente viene usato per l'account dell'utente amministratore del database iniziale. Viene richiesto di scegliere una password per questo utente.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
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

### <a name="configure-server-firewall"></a>Configurare il firewall del server

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

### <a name="create-a-production-database-and-user"></a>Creare un database di produzione e un utente

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

Digitare `\q` per uscire dal client PostgreSQL.

### <a name="test-app-locally-with-azure-postgresql"></a>Testare l'app in locale con Azure PostgreSQL

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

Passare a http://localhost:5000 in un browser. Fare clic su **Registra!** e creare una registrazione test. I dati vengono ora scritti nel database in Azure.

![Applicazione Python Flask in esecuzione in locale](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>Distribuisci in Azure

In questo passaggio si distribuisce l'applicazione Python connessa a PostgreSQL nel servizio app di Azure.

Il repository Git contiene già i file seguenti, necessari per eseguire l'app Web Flask nel servizio app.

- `.deployment`: specifica lo script di distribuzione personalizzato da eseguire.
- `deploy.cmd`: script di distribuzione, in cui viene eseguito `pip install`.
- `web.config`: specifica lo script del punto di ingresso da eseguire in `httpPlatformHandler` in IIS.
- `run_waitress_server.py`: script del punto di ingresso, che avvia l'app Web Flask in un server [`waitress`](https://docs.pylonsproject.org/projects/waitress).

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Creare un'app Web

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Installare Python

In questo passaggio si installa Python 3.6.2 con le [estensioni del sito](https://www.siteextensions.net/packages?q=Tags%3A%22python%22) nel servizio app. Per l'autenticazione per l'endpoint REST si useranno le credenziali configurate in [Configurare un utente della distribuzione](#configure-a-deployment-user).

In Cloud Shell eseguire il comando successivo per recuperare le informazioni relative al pacchetto Python 3.6.2. Sostituire *\<deployment_user>* con il nome utente della distribuzione configurato e *\<app_name>* con il nome dell'app. Quando richiesto, usare la password della distribuzione che è stata configurata.

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

In Cloud Shell eseguire il comando successivo per installare il pacchetto Python. Sostituire *\<deployment_user>* con il nome utente della distribuzione configurato e *\<app_name>* con il nome dell'app. Quando richiesto, usare la password della distribuzione che è stata configurata.

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

Nell'output del comando è possibile rilevare che Python è stato installato nel percorso `D:\home\python362x86\python.exe`.

### <a name="configure-database-settings"></a>Configurare le impostazioni del database

In precedenza in questa esercitazione sono state definite variabili di ambiente per la connessione al database PostgreSQL.

Nel servizio app le variabili di ambiente vengono impostate come _impostazioni dell'app_ usando il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

L'esempio seguente specifica i dettagli di connessione del database come impostazioni dell'app. Sostituire *\<app_name>* con il nome dell'app e *\<postgresql_name>* con il nome del server PostgreSQL.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure 

Passare all'app Web distribuita usando il Web browser. 

```bash 
http://<app_name>.azurewebsites.net 
```

Vengono visualizzati gli utenti guest registrati in precedenza salvati nel database di produzione di Azure nel passaggio precedente.

![Applicazione Python Flask in esecuzione in locale](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**Congratulazioni** L'app Python Flask è in esecuzione nel servizio app di Azure.

## <a name="update-data-model-and-redeploy"></a>Aggiornare il modello di dati e ridistribuire

In questo passaggio si aggiunge il numero di partecipanti a ogni registrazione per l'evento aggiornando il modello `Guest`.

Estrarre i file contrassegnati dal commit `modelChange`:

```bash
git checkout modelChange -- *
```

Questa versione ha già apportato le modifiche necessarie a visualizzazioni, ai controller e al modello. Include anche una migrazione del database generata tramite *alembic* (`flask db migrate`). È possibile verificare le modifiche apportate a tutti i file nella [visualizzazione del commit in GitHub](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e).

### <a name="test-your-changes-locally"></a>Testare le modifiche in locale

Eseguire questi comandi per testare le modifiche in locale eseguendo il server Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Passare a http://localhost:5000 nel browser per visualizzare le modifiche. Creare una registrazione test.

![Applicazione Python Flask in esecuzione in locale](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

Nella finestra del terminale locale eseguire il commit di tutte le modifiche in Git e quindi eseguire il push delle modifiche al codice in Azure.

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Passare all'app Web di Azure e provare ancora la nuova funzionalità. Creare un'altra registrazione dell'evento.

```bash 
http://<app_name>.azurewebsites.net 
```

![App Python Flask nel servizio app di Azure](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al [portale di Azure](https://portal.azure.com) per visualizzare l'app Web creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app Web. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Pagina del servizio app nel portale di Azure](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](app-service-web-tutorial-custom-domain.md)
