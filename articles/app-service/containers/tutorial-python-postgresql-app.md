---
title: Python (Django) con PostgreSQL in Linux - Servizio app di Azure | Microsoft Docs
description: Informazioni su come eseguire un'app Python basata sui dati con connessione a un database PostgreSQL. Django verrà usato nell'esercitazione.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: beverst;cephalin
ms.custom: seodec18
ms.openlocfilehash: 4b2304e170f9ddc14a5c1fa71a8822d083955106
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341535"
---
# <a name="build-a-python-and-postgresql-app-in-azure-app-service"></a>Creare un'app Python e PostgreSQL in Servizio app di Azure

Il [Servizio app in Linux](app-service-linux-intro.md) fornisce un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione. Questa esercitazione illustra come creare un'app Python basata sui dati usando PostgreSQL come back-end del database. Al termine, si avrà un'applicazione Django in esecuzione nel servizio app in Linux.

![App Python Django nel servizio app in Linux](./media/tutorial-python-postgresql-app/django-admin-azure.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database PostgreSQL in Azure
> * Connettere un'app Python a PostgreSQL
> * Distribuire l'app in Azure
> * Visualizzare i log di diagnostica
> * Gestire l'app nel portale di Azure

> [!NOTE]
> Prima di creare un database di Azure per PostgreSQL, vedere [quale generazione di calcolo è disponibile nella propria area](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

I passaggi illustrati in questo articolo possono essere eseguiti in macOS. Sebbene le istruzioni per Linux e Windows siano le stesse nella maggior parte dei casi, le differenze non sono descritte in questa esercitazione.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

1. [Installare Git](https://git-scm.com/)
2. [Installare Python](https://www.python.org/downloads/)
3. [Installare ed eseguire PostgreSQL](https://www.postgresql.org/download/)

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

Creare un database denominato *pollsdb* e configurare un utente di database separato denominato *manager* con password *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Digitare `\q` per uscire dal client PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Creare un'app Python locale

In questo passaggio si configura il progetto Python Django locale.

### <a name="clone-the-sample-app"></a>Clonare l'app di esempio

Aprire la finestra del terminale e usare il comando `CD` per passare a una directory di lavoro.

Eseguire i comandi seguenti per clonare il repository di esempio.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Questo repository di esempio contiene un'applicazione [Django](https://www.djangoproject.com/). È la stessa app basata sui dati che si otterrebbe seguendo l'[esercitazione introduttiva nella documentazione di Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). Questa esercitazione non spiega come usare Django, ma mostra come prendere, distribuire ed eseguire un'app Django (o un'altra app Python basata sui dati) nel servizio app.

### <a name="configure-environment"></a>Configurare l'ambiente

Creare un ambiente virtuale Python e usare uno script per definire le impostazioni di connessione al database.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

Per definire le impostazioni del database, in _azuresite/settings.py_ vengono usate le variabili di ambiente definite in *env.sh* e *env.ps1*.

### <a name="run-app-locally"></a>Eseguire l'app in locale

Installare i pacchetti necessari, [eseguire le migrazioni di Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) e [creare un utente amministratore](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

Dopo aver creato l'utente amministratore, eseguire il server di Django.

```bash
python manage.py runserver
```

Al termine del caricamento dell'app viene visualizzato un messaggio simile al seguente:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Andare a `http://localhost:8000` in un browser. Dovrebbe essere visualizzato il messaggio `No polls are available.`. 

Passare a `http://localhost:8000/admin` e accedere con l'account di utente amministratore creato nel passaggio precedente. Fare clic su **Add** (Aggiungi) accanto a **Questions** (Domande) e creare una domanda di sondaggio con alcune scelte.

![Applicazione Python Django in esecuzione in locale](./media/tutorial-python-postgresql-app/django-admin-local.png)

Passare nuovamente a `http://localhost:8000` e vedere la domanda di sondaggio visualizzata.

L'applicazione Django di esempio archivia i dati utente nel database. Se la domanda di sondaggio è stata aggiunta correttamente, l'app scriverà i dati nel database PostgreSQL locale.

Per arrestare il server di Django in qualsiasi momento, premere CTRL+C nel terminale.

## <a name="create-a-production-postgresql-database"></a>Creare un database di produzione PostgreSQL

In questo passaggio si crea un database PostgreSQL in Azure. Quando viene distribuita in Azure, l'app usa questo database basato sul cloud.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un server PostgreSQL con il comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) in Cloud Shell.

Nel comando di esempio seguente sostituire *\<postgresql-name>* con un nome server univoco e sostituire *\<admin-username>* e *\<admin-password>* con le credenziali utente desiderate. Le credenziali utente sono per l'account utente amministratore del database. Poiché il nome del server viene usato come parte dell'endpoint di PostgreSQL (`https://<postgresql-name>.postgres.database.azure.com`), è necessario che il nome sia univoco in tutti i server di Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql-name> --location "West Europe" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

Quando il database di Azure termina la creazione del server PostgreSQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "<admin-username>",
  "fullyQualifiedDomainName": "<postgresql-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql-name>",
  "location": "westus",
  "name": "<postgresql-name>",
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
> Ricordare i valori di \<admin-username> e \<admin-password> per i passaggi successivi. Saranno necessari per accedere al server Postgres e ai relativi database.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>Creare regole del firewall per il server PostgreSQL

In Cloud Shell eseguire il comando dell'interfaccia della riga di comando di Azure seguente per consentire l'accesso al database dalle risorse di Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Questa impostazione consente le connessioni di rete da tutti gli indirizzi IP all'interno della rete di Azure. Per l'uso nell'ambiente di produzione, provare a configurare le regole del firewall più restrittive possibile [usando solo gli indirizzi IP in uscita usati dall'app](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

In Cloud Shell eseguire di nuovo il comando per consentire l'accesso dal computer locale sostituendo *\<your-ip-address>* con l'[indirizzo IPv4 locale](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Connettere l'app Python al database di produzione

In questo passaggio si connette l'app Django di esempio al server di Database di Azure per PostgreSQL creato.

### <a name="create-empty-database-and-user-access"></a>Creare un database vuoto e l'accesso utente

In Cloud Shell connettersi al database usando il comando seguente. Quando viene richiesta la password dell'amministratore, usare la stessa password specificata in [Creare un database di Azure per il server PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Proprio come nel server Postgres locale, creare il database e l'utente nel server Postgres Azure.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Digitare `\q` per uscire dal client PostgreSQL.

> [!NOTE]
> È consigliabile creare gli utenti del database con autorizzazioni limitate per applicazioni specifiche invece di usare l'utente amministratore. In questo esempio l'utente `manager` ha privilegi completi _solo_ per il database `pollsdb`.

### <a name="test-app-connectivity-to-production-database"></a>Testare la connettività dell'app al database di produzione

Nella finestra del terminale locale modificare le variabili di ambiente del database, configurate in precedenza eseguendo *env.sh* o *env.ps1*:

```bash
# Bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Eseguire la migrazione di Django al database di Azure e creare un utente amministratore.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Dopo aver creato l'utente amministratore, eseguire il server di Django.

```bash
python manage.py runserver
```

Passare di nuovo a `http://localhost:8000`. Dovrebbe essere nuovamente visualizzato il messaggio `No polls are available.`. 

Passare a `http://localhost:8000/admin` e accedere con l'account di utente amministratore creato, quindi creare una domanda di sondaggio come in precedenza.

![Applicazione Python Django in esecuzione in locale](./media/tutorial-python-postgresql-app/django-admin-local.png)

Passare nuovamente a `http://localhost:8000` e vedere la domanda di sondaggio visualizzata. L'app scrive ora i dati nel database in Azure.

## <a name="deploy-to-azure"></a>Distribuisci in Azure

In questo passaggio si distribuirà l'applicazione Python connessa a Postgres in Servizio app di Azure.

### <a name="configure-repository"></a>Configurare il repository

Django convalida l'intestazione `HTTP_HOST` nelle richieste in ingresso. Per il funzionamento dell'app Django nel servizio app, è necessario aggiungere il nome di dominio completo dell'app agli host consentiti. Aprire _azuresite/settings.py_ e trovare l'importazione `ALLOWED_HOSTS`. Modificare la riga in:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net',
                 '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Django non supporta la [gestione di file statici nell'ambiente di produzione](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/) ed è quindi necessario abilitare questa funzionalità manualmente. Ai fini di questa esercitazione, si usa [WhiteNoise](https://whitenoise.evans.io/en/stable/). Il pacchetto di WhiteNoise è già incluso in _requirements.txt_. È sufficiente configurare Django per usarlo. 

In _azuresite/settings.py_ trovare l'impostazione `MIDDLEWARE` e aggiungere il middleware `whitenoise.middleware.WhiteNoiseMiddleware` all'elenco, appena sotto il middleware `django.middleware.security.SecurityMiddleware`. L'impostazione `MIDDLEWARE` dovrebbe avere un aspetto simile al seguente:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

Alla fine di _azuresite/settings.py_ aggiungere le righe seguenti.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Per altre informazioni sulla configurazione di WhiteNoise, vedere la [documentazione di WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> La sezione delle impostazioni del database è già conforme alla procedura consigliata per la sicurezza relativa all'uso di variabili di ambiente. Per indicazioni complete sulla distribuzione, vedere l'[elenco di controllo per la distribuzione nella documentazione di Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Eseguire il commit delle modifiche nel repository.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>Configurare l'utente della distribuzione

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creare un'app Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

In precedenza in questa esercitazione sono state definite variabili di ambiente per la connessione al database PostgreSQL.

Nel servizio app le variabili di ambiente vengono configurate come _impostazioni dell'app_ usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell.

L'esempio seguente specifica i dettagli di connessione del database come impostazioni dell'app. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Per informazioni su come accedere a queste impostazioni dell'app nel codice, vedere [Accedere alle variabili di ambiente](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
   06b6df4..6520eea  master -> master
```  

Il server di distribuzione del servizio app vede il file _requirements.txt_ nella radice del repository ed esegue automaticamente la gestione dei pacchetti Python dopo `git push`.

### <a name="browse-to-the-azure-app"></a>Passare all'app Azure

Passare all'app distribuita. L'avvio richiede tempo perché il contenitore deve essere scaricato ed eseguito quando l'app viene richiesta per la prima volta. Se si verifica il timeout della pagina o se viene visualizzato un messaggio di errore, attendere alcuni minuti e aggiornare la pagina.

```bash
http://<app-name>.azurewebsites.net
```

Dovrebbe essere visualizzata la domanda di sondaggio creata in precedenza. 

Il servizio app rileva un progetto Django nel repository cercando in ogni sottodirectory un file _wsgi.py_, che viene creato da `manage.py startproject` per impostazione predefinita. Quando trova il file, il servizio carica l'app Django. Per altre informazioni sul modo in cui il servizio app carica le app Python, vedere [Configurare l'immagine Python predefinita](how-to-configure-python.md).

Passare a `<app-name>.azurewebsites.net` e accedere con l'account di utente amministratore creato. Se si vuole, provare a creare altre domande di sondaggio.

![Applicazione Python Django in esecuzione in locale](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Congratulazioni** Un'app Python è ora in esecuzione nel servizio app per Linux.

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Gestire l'app nel portale di Azure

Passare al [portale di Azure](https://portal.azure.com) per visualizzare l'app creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Azure.

![Passaggio all'app di Azure nel portale](./media/tutorial-python-postgresql-app/app-resource.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Pagina del servizio app nel portale di Azure](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un database PostgreSQL in Azure
> * Connettere un'app Python a PostgreSQL
> * Distribuire l'app in Azure
> * Visualizzare i log di diagnostica
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](../app-service-web-tutorial-custom-domain.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare un'app Python](how-to-configure-python.md)
