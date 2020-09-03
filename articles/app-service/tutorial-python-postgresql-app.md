---
title: "Esercitazione: Distribuire un'app Python Django con Postgres"
description: Creare un'app Web Python con un database PostgreSQL e distribuirla in Azure. L'esercitazione usa il framework Django e l'app è ospitata nel Servizio app di Azure in Linux.
ms.devlang: python
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 368a87d1054e4a5ad12fa1e8c78bcde39f76ee63
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959409"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Esercitazione: Distribuire un'app Web Django con PostgreSQL nel Servizio app di Azure

Questa esercitazione illustra come distribuire un'app Web Python [Django](https://www.djangoproject.com/) basata sui dati nel [Servizio app di Azure](overview.md) e connetterla a un Database di Azure per Postgres. Il Servizio app offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione.

In questa esercitazione verrà usata l'interfaccia della riga di comando di Azure per completare le attività seguenti:

> [!div class="checklist"]
> * Configurare l'ambiente iniziale con Python e l'interfaccia della riga di comando di Azure
> * Creare un Database di Azure per PostgreSQL
> * Distribuire il codice nel Servizio app di Azure e connettersi a Postgres
> * Aggiornare il codice e ridistribuire
> * Visualizzare i log di diagnostica
> * Gestire l'app Web nel portale di Azure

È anche possibile usare la [versione di questa esercitazione per il portale di Azure](/azure/developer/python/tutorial-python-postgresql-app-portal).


## <a name="set-up-your-initial-environment"></a>Configurare l'ambiente iniziale

1. Avere a disposizione un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Installare <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 o versioni successive</a>.
1. Installare l'<a href="/cli/azure/install-azure-cli" target="_blank">interfaccia della riga di comando di Azure</a> 2.0.80 o versione successiva, con cui si eseguono i comandi in qualsiasi shell per il provisioning e la configurazione delle risorse di Azure.

Aprire una finestra del terminale e verificare che la versione di Python sia 3.6 o successiva:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Verificare anche che la versione dell'interfaccia della riga di comando di Azure sia 2.0.80 o successiva:

```azurecli
az --version
```

Quindi accedere ad Azure tramite l'interfaccia della riga di comando:

```azurecli
az login
```

Questo comando apre un browser per raccogliere le credenziali. Al termine dell'esecuzione del comando, viene visualizzato un output JSON contenente informazioni sulle sottoscrizioni.

Dopo aver eseguito l'accesso, è possibile eseguire i comandi di Azure con l'interfaccia della riga di comando di Azure per usare le risorse nella sottoscrizione.

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

## <a name="clone-or-download-the-sample-app"></a>Clonare o scaricare l'app di esempio

# <a name="git-clone"></a>[Clonazione dell'esempio Git](#tab/clone)

Clonare il repository di esempio:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Passare quindi alla cartella:

```terminal
cd djangoapp
```

# <a name="download"></a>[Scaricare](#tab/download)

Visitare la pagina Web all'indirizzo [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp), fare clic su **Code** (Codice) e quindi su **Download ZIP** (Scarica ZIP). 

Decomprimere il file ZIP in una cartella denominata *djangoapp*. 

Aprire quindi una finestra del terminale in tale cartella *djangoapp*.

---

L'esempio djangoapp contiene l'app di sondaggi Django basata sui dati che si ottiene seguendo l'esercitazione [Scrivere la prima app Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) nella documentazione di Django. Per praticità qui viene fornita l'app completata.

L'esempio è stato anche modificato per l'esecuzione in un ambiente di produzione come il Servizio app:

- Le impostazioni di produzione si trovano nel file *azuresite/production.py*. I dettagli di sviluppo si trovano in *azuresite/settings.py*.
- L'app usa le impostazioni di produzione quando la variabile di ambiente `DJANGO_ENV` è impostata su "production". Questa variabile di ambiente viene creata più avanti nell'esercitazione insieme ad altre usate per la configurazione del database PostgreSQL.

Queste modifiche sono specifiche per la configurazione di Django per l'esecuzione in qualsiasi ambiente di produzione e non sono peculiari del Servizio app. Per altre informazioni, vedere l'[elenco di controllo per la distribuzione di Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

## <a name="create-postgres-database-in-azure"></a>Creare un database Postgres in Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Installare l'estensione `db-up` per l'interfaccia della riga di comando di Azure:

```azurecli
az extension add --name db-up
```

Se il comando `az` non viene riconosciuto, verificare che sia installata l'interfaccia della riga di comando di Azure come descritto in [Configurare l'ambiente iniziale](#set-up-your-initial-environment).

Creare quindi il database Postgres in Azure con il comando [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up):

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgre-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- Sostituire *\<postgres-server-name>* con un nome univoco in tutto Azure (l'endpoint server è `https://\<postgres-server-name>.postgres.database.azure.com`). Un criterio valido consiste nell'usare una combinazione del nome della società e di un altro valore univoco.
- Per *\<admin-username>* e *\<admin-password>* specificare le credenziali per creare un utente amministratore per questo server Postgres.
- Il [piano tariffario](../postgresql/concepts-pricing-tiers.md) B_Gen5_1 (Basic, Gen5, 1 core) usato qui è il meno costoso. Per i database di produzione, omettere l'argomento `--sku-name` per usare invece il piano tariffario GP_Gen5_2 (General Purpose, Gen5, 2 core).

Questo comando esegue le azioni seguenti, che possono richiedere alcuni minuti:

- Crea un [gruppo di risorse](../azure-resource-manager/management/overview.md#terminology) denominato `DjangoPostgres-tutorial-rg` se non esiste già.
- Crea un server Postgres.
- Crea un account amministratore predefinito con un nome utente e una password univoci. Per specificare le proprie credenziali, usare gli argomenti `--admin-user` e `--admin-password` con il comando `az postgres up`.
- Crea un database `pollsdb`.
- Abilita l'accesso dall'indirizzo IP locale.
- Abilita l'accesso dai servizi di Azure.
- Crea un utente del database con accesso al database `pollsdb`.

È possibile eseguire tutti i passaggi separatamente con altri comandi `az postgres` e `psql`, ma `az postgres up` li esegue tutti insieme.

Quando il comando viene completato, restituisce come output un oggetto JSON contenente diverse stringhe di connessione per il database insieme all'URL del server, un nome utente generato (ad esempio, "joyfulKoala@msdocs-djangodb-12345") e una password GUID. Copiare il nome utente e la password in un file di testo temporaneo, in quanto saranno necessari più avanti in questa esercitazione.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, può essere impostato su una delle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). È possibile ottenere le aree disponibili per la sottoscrizione con il comando [`az account list-locations`](/cli/azure/account#az-account-list-locations). Per le app di produzione, inserire il database e l'app nella stessa posizione.

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

## <a name="deploy-the-code-to-azure-app-service"></a>Distribuire il codice nel Servizio app di Azure

In questa sezione si crea l'host delle app nell'app del Servizio app, si connette questa app al database Postgres e quindi si distribuisce il codice in tale host.

### <a name="create-the-app-service-app"></a>Creare l'app del Servizio app

Nel terminale assicurarsi di trovarsi nella radice del repository (`djangoapp`) che contiene il codice dell'app.

Creare un'app del Servizio app (processo host) con il comando [`az webapp up`](/cli/azure/webapp#az-webapp-up):

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Per l'argomento `--location`, usare la stessa posizione usata per il database nella sezione precedente.
- Sostituire *\<app-name>* con un nome univoco in tutto Azure (l'endpoint server è `https://\<app-name>.azurewebsites.net`). I caratteri consentiti per *\<app-name>* sono `A`-`Z`, `0`-`9` e `-`. Un criterio valido consiste nell'usare una combinazione del nome della società e di un identificatore dell'app.

Questo comando esegue le azioni seguenti, che possono richiedere alcuni minuti:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Crea il [gruppo di risorse](../azure-resource-manager/management/overview.md#terminology) se non esiste già. In questo comando viene usato lo stesso gruppo di risorse in cui in precedenza è stato creato il database.
- Crea il [piano di servizio app](overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* nel piano tariffario Basic (B1), se non esiste. `--plan` e `--sku` sono facoltativi.
- Crea l'app del Servizio app se non esiste.
- Abilita la registrazione predefinita per l'app, se non è già abilitata.
- Carica il repository usando la distribuzione ZIP con l'automazione della compilazione abilitata.

Al completamento della distribuzione, il comando genera un output JSON simile a quello dell'esempio seguente:

![Output di esempio del comando az webapp up](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

> [!TIP]
> Molti comandi dell'interfaccia della riga di comando di Azure memorizzano nella cache i parametri comuni, ad esempio il nome del gruppo di risorse e del piano di servizio app, nel file *.azure/config*. Non è pertanto necessario specificare tutti gli stessi parametri con i comandi successivi. Ad esempio, per ridistribuire l'app dopo aver apportato modifiche, è sufficiente eseguire di nuovo `az webapp up` senza parametri. Tuttavia, i comandi che provengono dalle estensioni dell'interfaccia della riga di comando, ad esempio `az postgres up`, attualmente non usano la cache, motivo per cui qui è stato necessario specificare il gruppo di risorse e la posizione con `az webapp up`.

> [!NOTE]
> Se si tenta di visitare l'URL dell'app a questo punto, viene generato l'errore "DisallowedHost at /". Questo errore si verifica perché l'app non è stata ancora configurata per l'uso delle impostazioni di produzione indicate in precedenza, operazione che verrà eseguita nella sezione seguente.

### <a name="configure-environment-variables-to-connect-the-database"></a>Configurare le variabili di ambiente per la connessione del database

Con il codice ora distribuito nel Servizio app, il passaggio successivo consiste nel connettere l'app al database Postgres in Azure.

Il codice dell'app prevede di trovare informazioni sul database in una serie di variabili di ambiente. Per impostare le variabili di ambiente nel Servizio app, è necessario creare "impostazioni dell'app" usando il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

```azurecli
az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Sostituire *\<postgres-server-name>* con il nome usato in precedenza con il comando `az postgres up`.
- Sostituire *\<username>* e *\<password>* con le credenziali generate anch'esse automaticamente dal comando.
- Il gruppo di risorse e il nome dell'app vengono ricavate a partire dai valori memorizzati nella cache nel file *.azure/config*.
- Il comando crea impostazioni denominate `DJANGO_ENV`, `DBHOST`, `DBNAME`, `DBUSER`e `DBPASS` come previsto dal codice dell'app.
- Nel codice Python è possibile accedere a queste impostazioni come variabili di ambiente con istruzioni come `os.environ.get('DJANGO_ENV')`. Per altre informazioni, vedere [Accedere alle variabili di ambiente](configure-language-python.md#access-environment-variables).

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

### <a name="run-django-database-migrations"></a>Eseguire le migrazioni di database Django

Le migrazioni di database Django assicurano che lo schema nel database PostgreSQL in Azure corrisponda a quanto descritto nel codice.

1. Aprire una sessione SSH nel browser passando a *https://\<app-name>.scm.azurewebsites.net/webssh/host* e accedere con le credenziali dell'account Azure, non con le credenziali del server di database.

1. Nella sessione SSH eseguire i comandi seguenti (è possibile incollare i comandi usando **CTRL**+**MAIUSC**+**V**):

    ```bash
    cd site/wwwroot
    
    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```
    
1. Il comando `createsuperuser` chiede di immettere le credenziali di utente con privilegi avanzati. Ai fini di questa esercitazione, usare il nome utente predefinito `root`, premere **INVIO** per l'indirizzo di posta elettronica per lasciarlo vuoto e immettere `Pollsdb1` per la password.

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="create-a-poll-question-in-the-app"></a>Creare una domanda del sondaggio nell'app

1. In un browser aprire l'URL *http:\//\<app-name>.azurewebsites.net*. L'app dovrebbe visualizzare un messaggio che indica che non sono disponibili sondaggi perché nel database non sono ancora presenti sondaggi specifici.

1. Passare a *http:\//\<app-name>.azurewebsites.net/admin*. Accedere usando le credenziali di utente con privilegi avanzati della sezione precedente (`root` e `Pollsdb1`). In **Polls** (Sondaggi) selezionare **Add** (Aggiungi) accanto a **Questions** (Domande) e creare una domanda del sondaggio con alcune scelte.

1. Passare di nuovo a *http:\//\<app-name>. azurewebsites.net/* per verificare che le domande ora vengano presentate all'utente. Rispondere alle domande come desiderato per generare alcuni dati nel database.

**Congratulazioni** Un'app Web Python Django è ora in esecuzione nel Servizio app di Azure per Linux, con un database Postgres attivo.

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

> [!NOTE]
> Il Servizio app rileva un progetto Django cercando in ogni sottocartella un file *wsgi.py*, che viene creato da `manage.py startproject` per impostazione predefinita. Quando trova tale file, il Servizio app carica l'app Web Django. Per altre informazioni, vedere [Configurare l'immagine Python predefinita](configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Apportare modifiche al codice e ripetere la distribuzione

In questa sezione si apportano modifiche locali all'app e si ridistribuisce il codice nel Servizio app. Nel processo viene configurato un ambiente virtuale Python che supporta il lavoro in corso.

### <a name="run-the-app-locally"></a>Eseguire l'app in locale

Eseguire i comandi seguenti in una finestra del terminale. Assicurarsi di seguire i prompt durante la creazione dell'utente con privilegi avanzati:

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

```cmd
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

Una volta che l'app Web è stata caricata completamente, il server di sviluppo Django fornisce l'URL dell'app locale nel messaggio di "avvio del server di sviluppo in http://127.0.0.1:8000/. Chiudere il server con CTRL+INTERR".

![Output di esempio del server di sviluppo Django](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Testare l'app localmente eseguendo i passaggi seguenti:

1. In un browser passare a *http:\//localhost: 8000*. Verrà visualizzato un messaggio che indica che non sono disponibili sondaggi. 

1. Passare a *http:\//localhost:8000/admin* e accedere con le credenziali dell'utente amministratore creato in precedenza. In **Polls** (Sondaggi) selezionare di nuovo **Add** (Aggiungi) accanto a **Questions** (Domande) e creare una domanda del sondaggio con alcune scelte. 

1. Passare di nuovo a *http:\//localhost: 8000* e rispondere alla domanda per testare l'app. 

1. Arrestare il server Django premendo **CTRL**+**C**.

Quando viene eseguita localmente, l'app usa un database Sqlite3 locale e non interferisce con il database di produzione. Se lo si desidera, è anche possibile usare un database PostgreSQL locale per simulare meglio l'ambiente di produzione.

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

### <a name="update-the-app"></a>Aggiornare l'app

In `polls/models.py`individuare la riga che inizia con `choice_text` e impostare il parametro `max_length` su 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Poiché il modello di dati è stato modificato, creare una nuova migrazione Django ed eseguire la migrazione del database:

```
python manage.py makemigrations
python manage.py migrate
```

Eseguire di nuovo il server di sviluppo con `python manage.py runserver` e testare l'app in *http:\//localhost:8000/admin*:

Arrestare di nuovo il server Web Django con **CTRL**+**C**.

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

### <a name="redeploy-the-code-to-azure"></a>Ridistribuire il codice in Azure

Eseguire il comando seguente nella radice del repository:

```azurecli
az webapp up
```

Questo comando usa i parametri memorizzati nella cache nel file *.azure/config*. Poiché il Servizio app rileva che l'app esiste già, il codice viene semplicemente ridistribuito.

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

### <a name="rerun-migrations-in-azure"></a>Eseguire di nuovo le migrazioni in Azure

Poiché sono state apportate modifiche al modello di dati, è necessario rieseguire le migrazioni del database nel Servizio app.

Aprire di nuovo una sessione SSH nel browser passando a *https://\<app-name>.scm.azurewebsites.net/webssh/host*. Eseguire quindi i comandi seguenti:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

### <a name="review-app-in-production"></a>Esaminare l'app nell'ambiente di produzione

Passare a *http:\//\<app-name>.azurewebsites.net* e testare di nuovo l'app nell'ambiente di produzione. Poiché è stata modificata solo la lunghezza di un campo del database, la modifica è evidente soltanto se si tenta di immettere una risposta più lunga quando si crea una domanda.

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

È possibile accedere ai log della console generati dall'interno del contenitore che ospita l'app in Azure.

Eseguire il comando seguente dell'interfaccia della riga di comando di Azure per visualizzare il flusso di log. Questo comando usa i parametri memorizzati nella cache nel file *.azure/config*.

```azurecli
az webapp log tail
```

Se i log di console non sono immediatamente visibili, controllare nuovamente dopo 30 secondi.

Per interrompere lo streaming dei log in qualsiasi momento, premere **CTRL**+**C**.

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

> [!NOTE]
> È anche possibile esaminare i file di log nel browser all'indirizzo `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
>
> `az webapp up` attiva automaticamente la registrazione predefinita. Per motivi di prestazioni, questa registrazione si disattiva dopo un certo periodo di tempo, ma viene riattivata ogni volta che si esegue di nuovo `az webapp up`. Per attivarla manualmente, eseguire il comando seguente:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="manage-your-app-in-the-azure-portal"></a>Gestire l'app nel portale di Azure

Nel [portale di Azure](https://portal.azure.com) cercare il nome dell'app e quindi selezionare l'app nei risultati.

![Passare all'app Python Django nel portale di Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Per impostazione predefinita, il portale visualizza la pagina di **panoramica** dell'app, che fornisce una visualizzazione generale delle prestazioni. Qui è possibile eseguire attività di gestione di base come esplorazione, arresto, riavvio ed eliminazione. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Gestire l'app Python Django nella pagina Panoramica del portale di Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole mantenere l'app o continuare con l'esercitazione successiva, andare a [Passaggi successivi](#next-steps). In caso contrario, per evitare di sostenere addebiti continui, è possibile eliminare il gruppo di risorse creato per questa esercitazione:

```azurecli
az group delete
```

Il comando usa il nome del gruppo di risorse memorizzato nella cache nel file *.azure/config*. Eliminando il gruppo di risorse, vengono inoltre deallocate ed eliminate tutte le risorse in esso contenute.

[Problemi? Segnalarli](https://aka.ms/DjangoCLITutorialHelp).

## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni su come eseguire il mapping di un nome DNS personalizzato all'app:

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](app-service-web-tutorial-custom-domain.md)

Vedere le informazioni sul modo in cui il Servizio app esegue un'app Python:

> [!div class="nextstepaction"]
> [Configurare un'app Python](configure-language-python.md)