---
title: 'Esercitazione: App Python Linux con PostgreSQL'
description: Informazioni su come usare un'app Python Linux in Servizio app di Azure, con connessione a un database PostgreSQL in Azure. In questa esercitazione si usa Django.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523935"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Esercitazione: Eseguire un'app Web Python (Django) con PostgreSQL nel Servizio app di Azure

[Servizio app di Azure](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questa esercitazione illustra come connettere un'app Web Python Django basata sui dati a un database di Azure per PostgreSQL e come distribuire ed eseguire l'app nel Servizio app di Azure.

![App Web Python Django nel Servizio app di Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un database di Azure per PostgreSQL e connettervi un'app Web
> * Distribuire l'app Web nel Servizio app di Azure
> * Visualizzare i log di diagnostica
> * Gestire l'app Web nel portale di Azure

I passaggi illustrati in questo articolo possono essere eseguiti in macOS, Linux o Windows. Le procedure sono simili nella maggior parte dei casi, anche se le differenze non sono descritte in questa esercitazione. La maggior parte degli esempi seguenti usa una finestra del terminale `bash` in Linux. 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installare [Git](https://git-scm.com/).
- Installare [Python 3](https://www.python.org/downloads/).
- Installare ed eseguire [PostgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Testare l'installazione di PostgreSQL e creare un database

Prima di tutto, connettersi al server PostgreSQL locale e creare un database: 

In una finestra del terminale locale eseguire `psql` per connettersi al server PostgreSQL locale come utente `postgres` predefinito.

```bash
sudo su - postgres
psql
```
o
```PowerShell
psql -U postgres
```

Se la connessione ha esito positivo, il database PostgreSQL è in esecuzione. In caso contrario, verificare che il database PostgreSQL locale venga avviato seguendo la procedura descritta in [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Download - Distribuzione di base di PostgreSQL).

Creare un nuovo database denominato *pollsdb* e configurare un utente di database denominato *manager* con password *supersecretpass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Digitare `\q` per uscire dal client PostgreSQL.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Creare ed eseguire l'app Python locale

Successivamente, configurare ed eseguire l'app Web Python Django di esempio.

Il repository di esempio [djangoapp](https://github.com/Azure-Samples/djangoapp) contiene l'app di sondaggi [Django](https://www.djangoproject.com/) basata sui dati che si ottiene seguendo l'esercitazione [Scrivere la prima app Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) nella documentazione di Django.

### <a name="clone-the-sample-app"></a>Clonare l'app di esempio

In una finestra del terminale eseguire i comandi seguenti per clonare il repository dell'app di esempio e passare alla nuova directory di lavoro:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Configurare l'ambiente virtuale Python

Creare e attivare un ambiente virtuale Python per eseguire l'app.

```bash
python3 -m venv venv
source venv/bin/activate
```
o
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

Nell'ambiente `venv` eseguire *env.sh* o *ENV. ps1* per impostare le variabili di ambiente che *azuresite/settings.py* userà per le impostazioni di connessione del database.

```bash
source ./env.sh
```
o
```PowerShell
.\env.ps1
```

Installare i pacchetti necessari da *requirements.txt*, eseguire le [migrazioni di Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) e [creare un utente amministratore](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user):

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Eseguire l'app Web

Dopo aver creato l'utente amministratore, eseguire il server Django.

```bash
python manage.py runserver
```

Al termine del caricamento dell'app web Django viene visualizzato un messaggio simile al seguente:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Passare a *http:\//localhost:8000* in un browser. Verrà visualizzato un messaggio che indica che **non sono disponibili sondaggi**. 

Passare a *http:\//localhost:8000/admin* e accedere con l'account utente amministratore creato nel passaggio precedente. Selezionare **Add** (Aggiungi) accanto a **Questions** (Domande) e creare una domanda del sondaggio con alcune scelte.

![Eseguire l'app Python Django in Servizi app in locale](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Passare di nuovo a *http:\//localhost: 8000* per visualizzare la domanda del sondaggio e rispondere alla domanda. L'applicazione di esempio Django locale scrive e archivia i dati utente nel database PostgreSQL locale.

Per arrestare il server Django, premere CTRL+C nel terminale.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

La maggior parte dei passaggi rimanenti di questo articolo usa i comandi dell'interfaccia della riga di comando di Azure in Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Creare e connettersi a un database di Azure per PostgreSQL

In questa sezione verranno creati un database e un server di database di Azure per PostgreSQL e si connetterà l'app Web. Quando si distribuisce l'app Web nel Servizio app di Azure, l'app usa questo database cloud. 

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per il server di database di Azure per PostgreSQL è possibile selezionare un gruppo di risorse esistente o crearne uno nuovo. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Creare un server PostgreSQL con il comando [az postgres server create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) in Cloud Shell.

> [!NOTE]
> Prima di creare un server di database di Azure per PostgreSQL, verificare quale [generazione di calcolo](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) è disponibile nella propria area. Se l'area non supporta l'hardware di quarta generazione, modificare *--sku-name* nella riga di comando seguente impostandolo su un valore supportato nell'area, ad esempio quinta generazione. 

Nel comando seguente sostituire *\<postgresql-name>* con un nome di server univoco. Poiché il nome del server viene usato come parte dell'endpoint PostgreSQL *https://\<postgresql-name>.postgres.database.azure.com*, è necessario che il nome sia univoco tra tutti i server in Azure. 

Sostituire *\<resourcegroup-name>* e *\<region>* con il nome e l'area del gruppo di risorse che si vuole usare. Per *\<admin-username>* e *\<admin-password>* , creare le credenziali utente per l'account amministratore del database. Prendere nota di *\<admin-username>* e *\<admin-password>* perché sarà necessario usarli in seguito per l'accesso al server e ai database PostgreSQL.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Una volta creato il server di database di Azure per PostgreSQL, l'interfaccia della riga di comando di Azure restituisce codice JSON simile all'esempio seguente:

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Creare le regole del firewall per il server di database di Azure per PostgreSQL

Eseguire il comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) per consentire l'accesso al database dalle risorse di Azure. Sostituire i segnaposto di *\<postgresql-name>* e *\<resourcegroup-name>* con i valori dell'ambiente corrente.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> L'impostazione precedente consente le connessioni di rete da tutti gli indirizzi IP all'interno della rete di Azure. Per l'uso nell'ambiente di produzione, provare a configurare le regole del firewall più restrittive possibile [consentendo solo gli indirizzi IP in uscita usati dall'app](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Eseguire di nuovo il comando `firewall-rule create` per consentire l'accesso dal computer locale. Sostituire *\<your-ip-address>* con l'[indirizzo IP IPv4 locale](https://www.whatsmyip.org/). Sostituire i segnaposto di *\<postgresql-name>* e *\<resourcegroup-name>* con i valori dell'ambiente corrente.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Creare e connettersi a un database di Azure per PostgreSQL

Eseguire il comando seguente per connettersi a un server di database di Azure per PostgreSQL. Usare i propri valori per *\<postgresql-name>* e *\<admin-username>* e accedere con la password creata.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Come nel server PostgreSQL locale, creare un database e un utente nel server di database di Azure per PostgreSQL:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> È consigliabile creare gli utenti di database con autorizzazioni limitate per app specifiche anziché usare l'utente amministratore. L'utente `manager` dispone di privilegi completi *solo* per il database `pollsdb`.

Digitare `\q` per uscire dal client PostgreSQL.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Testare la connettività dell'app al database PostgreSQL di Azure

Modificare il file *env.sh* o *env.ps1* locale affinché punti al database PostgreSQL cloud, sostituendo *\<postgresql-name>* con il nome del server di database di Azure per PostgreSQL.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
o
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Nell'ambiente `venv` nella finestra del terminale locale eseguire il file *env.sh* o *env.ps1* modificato. 
```bash
source ./env.sh
```
o
```PowerShell
.\env.ps1
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

In un browser passare a *http:\//localhost: 8000*. Verrà visualizzato di nuovo il messaggio che indica che **non sono disponibili sondaggi**. 

Passare a *http:\//localhost:8000/admin*, accedere con l'account utente amministratore creato e quindi creare una domanda del sondaggio come in precedenza.

![Eseguire l'app Python Django in Servizi app in locale](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Passare nuovamente a *http:\//localhost:8000*. Verrà visualizzata la domanda del sondaggio. A questo punto, l'app scrive i dati nel database di Azure per PostgreSQL.

Per arrestare il server Django, premere CTRL+C nel terminale.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Distribuire l'app Web nel Servizio app di Azure

In questo passaggio l'app Python connessa al database di Azure per PostgreSQL verrà distribuita nel Servizio app di Azure.

### <a name="configure-repository"></a>Configurare il repository

Poiché in questa esercitazione viene usato un esempio di Django, è necessario modificare e aggiungere alcune impostazioni nel file *djangoapp/azuresite/settings.py* per usarlo con il Servizio app di Azure. 

1. Django convalida l'intestazione `HTTP_HOST` nelle richieste in ingresso. Per il corretto funzionamento dell'app Web Django nel servizio app, è necessario aggiungere il nome di dominio completo dell'app agli host consentiti. 
   
   Modificare *azuresite/settings.py* per modificare la riga `ALLOWED_HOSTS` come segue:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django non supporta la [gestione dei file statici nell'ambiente di produzione](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). Per questa esercitazione, si userà [WhiteNoise](https://whitenoise.evans.io/en/stable/) per abilitare la gestione dei file. Il pacchetto WhiteNoise è già stato installato con *requirements.txt*. 
   
   Per configurare Django per l'uso di WhiteNoise, in *azuresite/settings.py* trovare l'impostazione `MIDDLEWARE` e aggiungere `whitenoise.middleware.WhiteNoiseMiddleware` all'elenco, subito dopo la riga `django.middleware.security.SecurityMiddleware`. L'impostazione `MIDDLEWARE` dovrebbe avere un aspetto simile al seguente:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Alla fine di *azuresite/settings.py* aggiungere le righe seguenti:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Per altre informazioni sulla configurazione di WhiteNoise, vedere la [documentazione di WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> La sezione delle impostazioni del database è già conforme alla procedura consigliata per la sicurezza relativa all'uso di variabili di ambiente. Per indicazioni complete sulla distribuzione, vedere l'[elenco di controllo per la distribuzione nella documentazione di Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Eseguire il commit delle modifiche nel fork del repository *djangoapp*:

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creare un'app Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

In precedenza in questa esercitazione sono state definite variabili di ambiente per la connessione al database PostgreSQL.

Nel Servizio app di Azure le variabili di ambiente vengono impostate come *impostazioni dell'app* usando il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

In Azure Cloud Shell eseguire il comando seguente per specificare i dettagli della connessione di database come impostazioni dell'app. Sostituire *\<app-name>* , *\<resourcegroup-name>* e *\<postgresql-name>* con i valori dell'ambiente corrente.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Per informazioni su come il codice accede a queste impostazioni dell'app, vedere [Accedere alle variabili di ambiente](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

Il server di distribuzione del servizio app vede il file *requirements.txt* nella radice del repository ed esegue automaticamente la gestione dei pacchetti Python dopo `git push`.

### <a name="browse-to-the-azure-app"></a>Passare all'app Azure

Passare all'app distribuita con l'URL *http:\//\<app-name>.azurewebsites.net*. L'avvio richiede tempo perché il contenitore deve essere scaricato ed eseguito quando l'app viene richiesta per la prima volta. Se si verifica il timeout della pagina o se viene visualizzato un messaggio di errore, attendere alcuni minuti e aggiornare la pagina.

Dovrebbero essere visualizzate le domande del sondaggio create in precedenza. 

Il servizio app rileva un progetto Django nel repository cercando in ogni sottodirectory un file *wsgi.py*, che viene creato da `manage.py startproject` per impostazione predefinita. Quando trova il file, il servizio app carica l'app Web Django. Per altre informazioni sul modo in cui il servizio app carica le app Python, vedere [Configurare l'immagine Python predefinita](how-to-configure-python.md).

Passare a *http:\//\<app-name>.azurewebsites.net/admin* e accedere con l'account utente amministratore creato in precedenza. Se si vuole, provare a creare altre domande del sondaggio.

![Eseguire l'app Python Django in Servizi app in Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Congratulazioni** Un'app Web Python (Django) è ora in esecuzione nel Servizio app di Azure per Linux.

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Gestire l'app nel portale di Azure

Nel [portale di Azure](https://portal.azure.com) cercare e selezionare l'app creata.

![Passare all'app Python Django nel portale di Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app. che offre una visualizzazione dello stato dell'app. Qui è possibile eseguire attività di gestione di base come esplorazione, arresto, riavvio ed eliminazione. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Gestire l'app Python Django nella pagina Panoramica del portale di Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione successiva per informazioni su come eseguire il mapping di un nome DNS personalizzato all'app:

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](../app-service-web-tutorial-custom-domain.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare un'app Python](how-to-configure-python.md)
