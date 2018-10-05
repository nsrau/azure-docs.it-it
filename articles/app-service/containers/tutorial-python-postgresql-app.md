---
title: Creare un'app Web Python e PostgreSQL in Servizio app di Azure | Microsoft Docs
description: Informazioni su come eseguire un'app Python basata sui dati con connessione a un database PostgreSQL.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435787"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Compilare un'app Web Python Docker e PostgreSQL in Azure

Il [Servizio app in Linux](app-service-linux-intro.md) fornisce un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione. Questa esercitazione illustra come creare un'app Web Python basata sui dati usando PostgreSQL come back-end del database. Al termine, sarà stata creata un'applicazione Python Flask in esecuzione in un contenitore Docker nel servizio app in Linux.

![App Python Flask di Docker nel Servizio app in Linux](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database PostgreSQL in Azure
> * Connettere un'app Python a PostgreSQL
> * Distribuire l'app in Azure
> * Visualizzare i log di diagnostica
> * Aggiornare il modello di dati e ridistribuire l'app
> * Gestire l'app nel portale di Azure

I passaggi illustrati in questo articolo possono essere eseguiti in macOS. Sebbene le istruzioni per Linux e Windows siano le stesse nella maggior parte dei casi, le differenze non sono descritte in questa esercitazione.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

1. [Installare Git](https://git-scm.com/)
1. [Installare Python](https://www.python.org/downloads/)
1. [Installare ed eseguire PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testare l'installazione di PostgreSQL locale e creare un database

In una finestra del terminale locale eseguire `psql` per connettersi al server PostgreSQL locale.

```bash
sudo -u postgres psql postgres
```

Se viene visualizzato un messaggio di errore simile a `unknown user: postgres`, l'installazione di PostgreSQL può essere configurata con il nome utente connesso. Provare invece il comando seguente.

```bash
psql postgres
```

Se la connessione ha esito positivo, il database PostgreSQL è in esecuzione. In caso contrario, verificare che il database PostgreSQL locale venga avviato seguendo la procedura descritta in [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Download - Distribuzione di base di PostgreSQL).

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

Eseguire i comandi seguenti per clonare il repository di esempio.

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

Questo repository di esempio contiene un'applicazione [Flask](http://flask.pocoo.org/).

### <a name="run-the-app-locally"></a>Eseguire l'app in locale

Installare i pacchetti necessari e avviare l'applicazione.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
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

![Applicazione Python Flask in esecuzione in locale](./media/tutorial-python-postgresql-app/local-app.png)

L'applicazione di esempio Flask archivia i dati utente nel database. Dopo aver completato la registrazione di un utente, l'app scriverà i dati nel database PostgreSQL locale.

Per arrestare il server Flask in qualsiasi momento, digitare Ctrl+C nel terminale.

## <a name="create-a-production-postgresql-database"></a>Creare un database di produzione PostgreSQL

In questo passaggio si crea un database PostgreSQL in Azure. Quando viene distribuita in Azure, l'app usa questo database basato sul cloud.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un server PostgreSQL con il comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) in Cloud Shell.

Nel comando di esempio seguente sostituire *\<postgresql_name>* con un nome server univoco e sostituire *\<admin_username>* e *\<admin_password>* con le credenziali utente desiderate. Le credenziali utente sono per l'account utente amministratore del database. Poiché il nome del server viene usato come parte dell'endpoint di PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), è necessario che il nome sia univoco in tutti i server di Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
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
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Ricordare i valori di \<admin_username> e \<admin_password > per i passaggi successivi. Saranno necessari per accedere al server Postgres e ai relativi database.


### <a name="create-firewall-rules-for-the-postgresql-server"></a>Creare regole del firewall per il server PostgreSQL

In Cloud Shell eseguire il comando dell'interfaccia della riga di comando di Azure seguente per consentire l'accesso al database dalle risorse di Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Questa impostazione consente le connessioni di rete da tutti gli indirizzi IP all'interno della rete di Azure. Per l'uso nell'ambiente di produzione, provare a configurare le regole del firewall più restrittive possibile [usando solo gli indirizzi IP in uscita usati dall'app](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

In Cloud Shell eseguire di nuovo il comando per consentire l'accesso dal computer locale sostituendo *\<your_ip_address>* con l'[indirizzo IPv4 locale](http://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Connettere l'app Python al database di produzione

In questo passaggio si connette l'app di esempio Flask al database di Azure per il server PostgreSQL creato.

### <a name="create-empty-database-and-user-access"></a>Creare un database vuoto e l'accesso utente

Nella finestra del terminale locale connettersi al database eseguendo il comando seguente. Quando viene richiesta la password dell'amministratore, usare la stessa password specificata in [Creare un database di Azure per il server PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Proprio come nel server Postgres locale, creare il database e l'utente nel server Postgres Azure.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Digitare `\q` per uscire dal client PostgreSQL.

> [!NOTE]
> È consigliabile creare gli utenti del database con autorizzazioni limitate per applicazioni specifiche invece di usare l'utente amministratore. In questo esempio l'utente `manager` ha privilegi completi _solo_ per il database `eventregistration`.

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

![Applicazione Python Flask in esecuzione in locale](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>Distribuisci in Azure

In questo passaggio si distribuirà l'applicazione Python connessa a Postgres in Servizio app di Azure.

### <a name="configure-repository"></a>Configurare il repository

Il motore di distribuzione Git nel servizio app richiama l'automazione `pip` quando è presente un file _application.py_ nella radice del repository. In questa esercitazione si lascerà al motore di distribuzione l'esecuzione dell'automazione. Nella finestra del terminale locale passare alla radice del repository, creare un file _application.py_ fittizio ed eseguire il commit delle modifiche.

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
```

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creare un'app Web 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

In precedenza in questa esercitazione sono state definite variabili di ambiente per la connessione al database PostgreSQL.

Nel servizio app le variabili di ambiente vengono configurate come _impostazioni dell'app_ usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell.

L'esempio seguente specifica i dettagli di connessione del database come impostazioni dell'app. 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

### <a name="configure-entry-point"></a>Configurare il punto di ingresso

Per impostazione predefinita, l'immagine predefinita cerca un file _wsgi.py_ o _application.py_ nella directory radice come punto di ingresso, ma il punto di ingresso è _app/app.py_. Il file _application.py_ aggiunto in precedenza è vuoto e non esegue alcuna operazione.

In Cloud Shell eseguire il comando [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set) per impostare uno script di avvio personalizzato.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

Il parametro `--startup-file` accetta un comando personalizzato o il percorso del file che contiene il comando personalizzato. Il comando personalizzato deve avere il formato seguente:

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

Nel comando personalizzato `--chdir` è obbligatorio se il punto di ingresso non si trova nella directory radice e `<subdirectory>` è la sottodirectory. `<module>` è il nome del file _.py_ e `<variable>` è la variabile nel modulo che rappresenta l'app Web.

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure

Passare all'app Web distribuita. L'avvio richiede tempo perché il contenitore deve essere scaricato ed eseguito quando l'app viene richiesta per la prima volta. Se si verifica il timeout della pagina o se viene visualizzato un messaggio di errore, attendere alcuni minuti e aggiornare la pagina.

```bash
http://<app_name>.azurewebsites.net
```

Vengono visualizzati gli utenti guest registrati in precedenza salvati nel database di produzione di Azure nel passaggio precedente.

![Applicazione Python Flask in esecuzione in Azure](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**Congratulazioni** Un'app Python è ora in esecuzione nel servizio app per Linux.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

Poiché l'app Python è in esecuzione in un contenitore, il servizio app in Linux permette di accedere ai log della console generati all'interno del contenitore. Per trovare i log, passare a questo URL:

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

Verranno visualizzati due oggetti JSON, ognuno con una proprietà `href`. Una proprietà `href` punta ai log della console Docker (termina con `_docker.log`), mentre l'altra proprietà `href` punta ai log della console generati all'interno del contenitore Python. 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

Copiare il valore di `href` desiderato in una finestra del browser per passare ai log. Poiché non viene eseguito il flusso dei log, potrebbero verificarsi ritardi. Per visualizzare i nuovi log, aggiornare la pagina del browser.

## <a name="update-data-model-and-redeploy"></a>Aggiornare il modello di dati e ridistribuire

In questo passaggio si aggiungerà il numero di partecipanti a ogni registrazione dell'evento aggiornando il modello `Guest`, quindi si ridistribuirà l'aggiornamento in Azure.

Nella finestra del terminale locale estrarre i file dal ramo `modelChange` usando il comando Git seguente:

```bash
git checkout origin/modelChange -- .
```

Questa estrazione apporta già le modifiche necessarie al modello, alle visualizzazioni e ai controller. Include anche una migrazione del database generata tramite *alembic* (`flask db migrate`). È possibile visualizzare tutte le modifiche tramite il comando Git seguente:

```bash
git diff master origin/modelChange
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

![Applicazione Python Flask basata sul contenitore Docker in esecuzione in locale](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

Nella finestra del terminale locale eseguire il commit di tutte le modifiche in Git e quindi eseguire il push delle modifiche al codice in Azure.

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

Passare all'app Web di Azure e provare ancora la nuova funzionalità. Assicurarsi di aggiornare la pagina.

```bash
http://<app_name>.azurewebsites.net
```

![App Docker Python Flask nel Servizio app di Azure](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>Gestire l'app Web nel portale di Azure

Accedere al [portale di Azure](https://portal.azure.com) per visualizzare l'app Web creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/tutorial-python-postgresql-app/app-resource.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app Web. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Pagina del servizio app nel portale di Azure](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un database PostgreSQL in Azure
> * Connettere un'app Python a PostgreSQL
> * Distribuire l'app in Azure
> * Visualizzare i log di diagnostica
> * Aggiornare il modello di dati e ridistribuire l'app
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app Web.

> [!div class="nextstepaction"]
> [Configurare l'immagine Python predefinita](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](../app-service-web-tutorial-custom-domain.md)

