---
title: 'Esercitazione: Distribuire Python (Django) con PostgreSQL'
description: Informazioni su come creare un'app Python con un database PostgreSQL e distribuirla in Servizio app di Azure in Linux. In questa esercitazione si usa un'app Django di esempio a fini dimostrativi.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 504e2f7c07d8d29e4fe4dad52dc008c895517a3d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609783"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Esercitazione: Distribuire un'app Web Python (Django) con PostgreSQL nel Servizio app di Azure

Questa esercitazione illustra come distribuire un'app Web Python (Django) basata sui dati nel [Servizio app di Azure](app-service-linux-intro.md) e connetterla a un Database di Azure per PostgreSQL. Il Servizio app offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione.

![Distribuire un'app Web Python Django nel Servizio app di Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un Database di Azure per PostgreSQL
> * Distribuire il codice nel Servizio app di Azure e connettersi a Postgres
> * Aggiornare il codice e ridistribuire
> * Visualizzare i log di diagnostica
> * Gestire l'app Web nel portale di Azure

I passaggi illustrati in questo articolo possono essere eseguiti in macOS, Linux o Windows.

## <a name="install-dependencies"></a>Installare le dipendenze

Prima di iniziare questa esercitazione, è necessario:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
- Installare [Git](https://git-scm.com/).
- Installare [Python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Clonare l'app di esempio

In una finestra del terminale eseguire i comandi seguenti per clonare il repository dell'app di esempio e passare alla radice del repository:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

Il repository di esempio djangoapp contiene l'app di sondaggi [Django](https://www.djangoproject.com/) basata sui dati che si ottiene seguendo l'esercitazione [Scrivere la prima app Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) nella documentazione di Django. Viene fornito qui per praticità.

## <a name="prepare-app-for-app-service"></a>Preparare l'app per il Servizio app

Come molti framework Web Python, Django [richiede alcune modifiche prima di poter essere eseguito in un server di produzione](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/) e non è diverso dal Servizio app. È necessario modificare e aggiungere alcune impostazioni nel file predefinito *azuresite/settings.py* in modo che l'app funzioni dopo la distribuzione nel Servizio app. 

Esaminare il file *azuresite/production.py*, che applica la configurazione necessaria per il Servizio app. In breve, esegue le operazioni seguenti:

- Eredita tutte le impostazioni da *azuresite/settings.py*.
- Aggiunge il nome di dominio completo dell'app del Servizio app agli host consentiti. 
- Usa [WhiteNoise](https://whitenoise.evans.io/en/stable/) per abilitare la gestione dei file statici nell'ambiente di produzione, perché Django per impostazione predefinita non gestisce i file statici nell'ambiente di produzione. Il pacchetto di WhiteNoise è già incluso in *requirements.txt*.
- Aggiunge la configurazione per il database PostgreSQL. Per impostazione predefinita, Django usa Sqlite3 come database, ma non è adatto per le app di produzione. Il pacchetto [psycopg2-binary](https://pypi.org/project/psycopg2-binary/) è già incluso in *requirements.txt*.
- La configurazione Postgres usa le variabili di ambiente. In seguito, verrà illustrato come impostare le variabili di ambiente nel Servizio app.

*azuresite/production.py* è incluso nel repository per praticità, ma non viene ancora usato dall'app. Per assicurarsi che le relative impostazioni vengano usate nel Servizio app, è necessario configurare due file, *manage.py* e *azuresite/wsgi.py*, per accedervi.

- In *manage.py* sostituire la riga seguente:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    Con il codice seguente:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    La variabile di ambiente `DJANGO_ENV` verrà impostata in un secondo momento quando si configura l'app del Servizio app.

- In *azuresite/wsgi.py*apportare la stesse modifica illustrata in precedenza.

    Nel Servizio app si usa *manage.py* per eseguire le migrazioni del database e il Servizio app usa *azuresite/wsgi.py* per eseguire l'app Django nell'ambiente di produzione. Questa modifica in entrambi i file garantisce che le impostazioni di produzione vengano usate in entrambi i casi.

## <a name="sign-in-to-azure-cli"></a>Accedere all'interfaccia della riga di comando di Azure

È necessario che sia installata l'interfaccia della riga di comando di Azure. L'[interfaccia della riga di comando di Azure](/cli/azure/what-is-azure-cli) consente di usare le risorse di Azure dal terminale della riga di comando. 

Per accedere ad Azure, usare il comando [`az login`](/cli/azure/reference-index#az-login):

```azurecli
az login
```

Seguire le istruzioni nel terminale per accedere all'account Azure. Al termine, le sottoscrizioni vengono visualizzate in formato JSON nell'output del terminale.

## <a name="create-postgres-database-in-azure"></a>Creare un database Postgres in Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

In questa sezione verranno creati un database e un server di Database di Azure per PostgreSQL. Per iniziare, installare l'estensione `db-up` con il comando seguente:

```azurecli
az extension add --name db-up
```

Creare il database Postgres in Azure con il comando [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), come illustrato nell'esempio seguente. Sostituire *\<postgresql-name>* con un nome *univoco* (l'endpoint server è *https://\<postgresql-name>.postgres.database.azure.com*). Per *\<admin-username>* and *\<admin-password>* specificare le credenziali per creare un utente amministratore per questo server Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Questo comando può richiedere alcuni minuti perché esegue le operazioni seguenti:

- Crea un [gruppo di risorse](../../azure-resource-manager/management/overview.md#terminology) denominato `myResourceGroup` se non esiste. Ogni risorsa di Azure deve trovarsi in uno di questi gruppi. `--resource-group` è facoltativo.
- Crea un server Postgres con l'utente amministratore.
- Crea un database `pollsdb`.
- Consente l'accesso dall'indirizzo IP locale.
- Consente l'accesso dai servizi di Azure.
- Crea un utente del database con accesso al database `pollsdb`.

È possibile eseguire tutti i passaggi separatamente con altri comandi `az postgres` e `psql`, ma `az postgres up` li esegue tutti in un unico passaggio.

Al termine del comando, trovare le righe di output che iniziano con `Ran Database Query:`. Mostrano l'utente del database creato con il nome utente `root` e la password `Pollsdb1`. Verranno usati in un secondo momento per connettere l'app al database.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`, può essere impostato su una delle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). È possibile ottenere le aree disponibili per la sottoscrizione con il comando [`az account list-locations`](/cli/azure/account#az-account-list-locations). Per le app di produzione, inserire il database e l'app nella stessa posizione.

## <a name="deploy-the-app-service-app"></a>Distribuire l'app del Servizio app

In questa sezione viene creata l'app del Servizio app. Questa app verrà connessa al database Postgres creato e si distribuirà il codice.

### <a name="create-the-app-service-app"></a>Creare l'app del Servizio app

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Assicurarsi di trovarsi nella radice del repository (`djangoapp`), perché l'app verrà distribuita da questa directory.

Creare un'app del Servizio app con il comando [`az webapp up`](/cli/azure/webapp#az-webapp-up), come illustrato nell'esempio seguente. Sostituire *\<app-name>* con un nome *univoco* (l'endpoint server è *https://\<app-name>.azurewebsites.net*). I caratteri consentiti per *\<app-name>* sono `A`-`Z`, `0`-`9` e `-`.

```azurecli
az webapp up --plan myAppServicePlan --location westus2 --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Questo comando può richiedere alcuni minuti perché esegue le operazioni seguenti:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Genera automaticamente un [gruppo di risorse](../../azure-resource-manager/management/overview.md#terminology).
- Crea il [piano di servizio app](../overview-hosting-plans.md) *myAppServicePlan* nel piano tariffario Basic (B1), se non esiste. `--plan` e `--sku` sono facoltativi.
- Crea l'app del Servizio app se non esiste.
- Abilita la registrazione predefinita per l'app, se non è già abilitata.
- Carica il repository usando la distribuzione ZIP con l'automazione della compilazione abilitata.

Al termine della distribuzione, viene visualizzato un output JSON simile al seguente:

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Copiare il valore di *\<app-resource-group>* . È necessario per configurare l'app in un secondo momento. 

> [!TIP]
> Le impostazioni pertinenti vengono salvate in una directory *.azure* nascosta del repository. È possibile usare il comando semplice in un secondo momento per ridistribuire eventuali modifiche e abilitare immediatamente i log di diagnostica con:
> 
> ```azurecli
> az webapp up
> ```

Il codice di esempio è ora distribuito, ma l'app non si connette ancora al database Postgres in Azure. Questa operazione verrà eseguita più avanti.

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Quando si esegue l'app localmente, è possibile impostare le variabili di ambiente nella sessione del terminale. Nel Servizio app questa operazione viene eseguita con *app settings* usando il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

Eseguire il comando seguente per specificare i dettagli della connessione di database come impostazioni dell'app. Sostituire *\<app-name>* , *\<app-resource-group>* e *\<postgresql-name>* con i valori dell'ambiente corrente. Tenere presente che le credenziali utente `root` e `Pollsdb1` sono state create automaticamente da `az postgres up`.

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Per informazioni su come il codice accede a queste impostazioni dell'app, vedere [Accedere alle variabili di ambiente](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Eseguire le migrazioni del database

Per eseguire le migrazioni del database nel Servizio app, aprire una sessione SSH nel browser passando a *https://\<app-name>.scm.azurewebsites.net/webssh/host*:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Nella sessione SSH eseguire questo comando:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Passare all'app Azure

Passare all'app distribuita con l'URL *http:\//\<app-name>.azurewebsites.net* in un browser. Verrà visualizzato un messaggio che indica che **non sono disponibili sondaggi**. 

Passare a *http:\//\<app-name>.azurewebsites.net/admin* e accedere con l'account utente amministratore creato nel passaggio precedente. Selezionare **Add** (Aggiungi) accanto a **Questions** (Domande) e creare una domanda del sondaggio con alcune scelte.

Passare all'app distribuita con l'URL *http:\//\<app-name>.azurewebsites.net/admin* e creare alcune domande del sondaggio. È possibile visualizzare le domande in *http:\//\<app-name>.azurewebsites.net/* . 

![Eseguire l'app Python Django in Servizi app in Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Passare di nuovo all'app distribuita con l'URL *http:\//\<app-name>.azurewebsites.net* per visualizzare e rispondere alla domanda del sondaggio.

Il servizio app rileva un progetto Django nel repository cercando in ogni sottodirectory un file *wsgi.py*, che viene creato da `manage.py startproject` per impostazione predefinita. Quando trova il file, il servizio app carica l'app Web Django. Per altre informazioni sul modo in cui il servizio app carica le app Python, vedere [Configurare l'immagine Python predefinita](how-to-configure-python.md).

**Congratulazioni** Un'app Web Python (Django) è ora in esecuzione nel Servizio app di Azure per Linux.

## <a name="develop-app-locally-and-redeploy"></a>Sviluppare l'app in locale e ridistribuire

In questa sezione si sviluppa l'app nell'ambiente locale e si ridistribuisce il codice nel Servizio app.

### <a name="set-up-locally-and-run"></a>Configurare localmente ed eseguire

Per configurare l'ambiente di sviluppo locale ed eseguire l'app di esempio per la prima volta, eseguire i comandi seguenti:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Al termine del caricamento dell'app web Django viene visualizzato un messaggio simile al seguente:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Passare a *http:\//localhost:8000* in un browser. Verrà visualizzato un messaggio che indica che **non sono disponibili sondaggi**. 

Passare a *http:\//localhost:8000/admin* e accedere con l'account utente amministratore creato nel passaggio precedente. Selezionare **Add** (Aggiungi) accanto a **Questions** (Domande) e creare una domanda del sondaggio con alcune scelte.

![Eseguire l'app Python Django in Servizi app in locale](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Passare di nuovo a *http:\//localhost: 8000* per visualizzare la domanda del sondaggio e rispondere alla domanda. L'applicazione di esempio Django locale scrive e archivia i dati utente in un database Sqlite3 locale, pertanto non è necessario preoccuparsi di rovinare il database di produzione. Per fare in modo che l'ambiente di sviluppo corrisponda all'ambiente di Azure, è consigliabile usare un database Postgres localmente.

Per arrestare il server Django, premere CTRL+C.

### <a name="update-the-app"></a>Aggiornare l'app

Per capire come funzionano gli aggiornamenti delle app, apportare una piccola modifica in `polls/models.py`. Trovare la riga:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

E sostituirla con:

```python
choice_text = models.CharField(max_length=100)
```

Modificando il modello di dati, è necessario creare una nuova migrazione di Django. Eseguire questa operazione con il comando seguente:

```
python manage.py makemigrations
```

È possibile testare le modifiche in locale eseguendo le migrazioni, eseguendo il server di sviluppo e passando a *http:\//localhost:8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Ridistribuire il codice in Azure

Per ridistribuire le modifiche, eseguire il comando seguente dalla radice del repository:

```azurecli
az webapp up
```

Il Servizio app rileva l'esistenza dell'app e distribuisce il codice.

### <a name="rerun-migrations-in-azure"></a>Eseguire di nuovo le migrazioni in Azure

Poiché sono state apportate modifiche al modello di dati, è necessario rieseguire le migrazioni del database nel Servizio app. Aprire una sessione SSH nel browser passando a *https://\<app-name>.scm.azurewebsites.net/webssh/host*. Eseguire i comandi seguenti:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Esaminare l'app nell'ambiente di produzione

Passare a *http:\//\<app-name>.azurewebsites.net* e visualizzare le modifiche in esecuzione nell'ambiente di produzione. 

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

È possibile accedere ai log della console generati dall'interno del contenitore.

> [!TIP]
> `az webapp up` attiva automaticamente la registrazione predefinita. Per motivi di prestazioni, questa registrazione si disattiva dopo un certo periodo di tempo, ma viene riattivata ogni volta che si esegue di nuovo `az webapp up`. Per attivarla manualmente, eseguire il comando seguente:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Eseguire il comando dell'interfaccia della riga di comando di Azure per visualizzare il flusso di log:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Se i log di console non sono immediatamente visibili, controllare nuovamente dopo 30 secondi.

> [!NOTE]
> È anche possibile esaminare i file di log nel browser all'indirizzo `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Per interrompere lo streaming dei log in qualsiasi momento, digitare `Ctrl`+`C`.

## <a name="manage-your-app-in-the-azure-portal"></a>Gestire l'app nel portale di Azure

Nel [portale di Azure](https://portal.azure.com) cercare e selezionare l'app creata.

![Passare all'app Python Django nel portale di Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app. che offre una visualizzazione dello stato dell'app. Qui è possibile eseguire attività di gestione di base come esplorazione, arresto, riavvio ed eliminazione. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Gestire l'app Python Django nella pagina Panoramica del portale di Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si prevede di usare queste risorse in futuro, eliminare i gruppi di risorse eseguendo questi comandi:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]
> * Creare un Database di Azure per PostgreSQL
> * Distribuire il codice nel Servizio app di Azure e connettersi a Postgres
> * Aggiornare il codice e ridistribuire
> * Visualizzare i log di diagnostica
> * Gestire l'app Web nel portale di Azure

Passare all'esercitazione successiva per informazioni su come eseguire il mapping di un nome DNS personalizzato all'app:

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](../app-service-web-tutorial-custom-domain.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare un'app Python](how-to-configure-python.md)
