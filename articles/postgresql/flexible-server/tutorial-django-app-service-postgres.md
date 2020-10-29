---
title: "Esercitazione: Distribuire un'app Django con il servizio app e il server flessibile di Database di Azure per PostgreSQL (anteprima) in una rete virtuale"
description: Distribuire un'app Django con il servizio app e il server flessibile di Database di Azure per PostgreSQL (anteprima) in una rete virtuale
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bcc9ca0175e0e03c62c2ce2b91d8ec337756a3cc
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490102"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Esercitazione: Distribuire un'app Django con il servizio app e il server flessibile di Database di Azure per PostgreSQL (anteprima)

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Questa esercitazione illustra come distribuire un'applicazione Django in Azure con il servizio app e il server flessibile di Database di Azure per PostgreSQL in una rete virtuale.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Per questo articolo è necessario eseguire in locale l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È necessario accedere all'account con il comando [az login](/cli/azure/authenticate-azure-cli). Si noti la proprietà **id** dell'output del comando per il nome della sottoscrizione corrispondente.

```azurecli
az login
```

Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Selezionare l'ID sottoscrizione specifico sotto l'account tramite il comando [az account set](/cli/azure/account). Sostituire il segnaposto "subscription ID" con la proprietà **subscription ID** dell'output di **az login** per la sottoscrizione.

```azurecli
az account set --subscription <subscription id>
```
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

Decomprimere il file ZIP in una cartella denominata *djangoapp* .

Aprire quindi una finestra del terminale in tale cartella *djangoapp* .

---

L'esempio djangoapp contiene l'app di sondaggi Django basata sui dati che si ottiene seguendo l'esercitazione [Scrivere la prima app Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) nella documentazione di Django. Per praticità qui viene fornita l'app completata.

L'esempio è stato anche modificato per l'esecuzione in un ambiente di produzione come il Servizio app:

- Le impostazioni di produzione si trovano nel file *azuresite/production.py* . I dettagli di sviluppo si trovano in *azuresite/settings.py* .
- L'app usa le impostazioni di produzione quando la variabile di ambiente `DJANGO_ENV` è impostata su "production". Questa variabile di ambiente viene creata più avanti nell'esercitazione insieme ad altre usate per la configurazione del database PostgreSQL.

Queste modifiche sono specifiche per la configurazione di Django per l'esecuzione in qualsiasi ambiente di produzione e non sono peculiari del Servizio app. Per altre informazioni, vedere l'[elenco di controllo per la distribuzione di Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Creare un server flessibile PostgreSQL in una nuova rete virtuale

Creare un server flessibile privato e un database all'interno di una rete virtuale con il comando seguente:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

Questo comando esegue le azioni seguenti, che possono richiedere alcuni minuti:

- Creare il gruppo di risorse se non esiste già.
- Genera un nome di server, se non specificato.
- Crea una nuova rete virtuale per il nuovo server PostgreSQL. **Prendere nota del nome della rete virtuale e del nome della subnet** creati per il server perché sarà necessario aggiungere l'app Web alla stessa rete virtuale.
- Crea il nome utente e la password dell'amministratore per il server, se non specificato. **Prendere nota del nome utente e della password** per l'uso nei passaggi successivi.
- Crea un database ```postgres``` utilizzabile per lo sviluppo. È possibile eseguire [**psql** per connettersi al database](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) per creare un database diverso.

> [!NOTE]
> Prendere nota della password che verrà generata se non è stata specificata. Se si dimentica la password, è necessario reimpostarla usando il comando ``` az postgres flexible-server update```


## <a name="deploy-the-code-to-azure-app-service"></a>Distribuire il codice nel Servizio app di Azure

In questa sezione si crea l'host delle app nell'app del Servizio app, si connette questa app al database Postgres e quindi si distribuisce il codice in tale host.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>Creare un'app Web del servizio app in una rete virtuale

Nel terminale assicurarsi di trovarsi nella radice del repository (`djangoapp`) che contiene il codice dell'app.

Creare un'app del Servizio app (processo host) con il comando [`az webapp up`](/cli/azure/webapp#az-webapp-up):

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- Per l'argomento `--location`, usare la stessa posizione usata per il database nella sezione precedente.
- Sostituire *\<app-name>* con un nome univoco in tutto Azure (l'endpoint server è `https://\<app-name>.azurewebsites.net`). I caratteri consentiti per *\<app-name>* sono `A`-`Z`, `0`-`9` e `-`. Un criterio valido consiste nell'usare una combinazione del nome della società e di un identificatore dell'app.
- Crea il [piano di servizio app](../../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* nel piano tariffario Basic (B1), se non esiste. `--plan` e `--sku` sono facoltativi.
- Crea l'app del Servizio app se non esiste.
- Abilita la registrazione predefinita per l'app, se non è già abilitata.
- Carica il repository usando la distribuzione ZIP con l'automazione della compilazione abilitata.
- Il comando **az webapp vnet-integration** aggiunge l'app Web nella stessa rete virtuale del server Postgres.
- Il codice dell'app prevede di trovare informazioni sul database in una serie di variabili di ambiente. Per impostare le variabili di ambiente nel Servizio app, è necessario creare "impostazioni dell'app" usando il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

> [!TIP]
> Molti comandi dell'interfaccia della riga di comando di Azure memorizzano nella cache i parametri comuni, ad esempio il nome del gruppo di risorse e del piano di servizio app, nel file *.azure/config* . Non è pertanto necessario specificare tutti gli stessi parametri con i comandi successivi. Ad esempio, per ridistribuire l'app dopo aver apportato modifiche, è sufficiente eseguire di nuovo `az webapp up` senza parametri.

### <a name="run-django-database-migrations"></a>Eseguire le migrazioni di database Django

Le migrazioni di database Django assicurano che lo schema nel database PostgreSQL in Azure corrisponda a quanto descritto nel codice.

1. Aprire una sessione SSH nel browser passando a *https://\<app-name>.scm.azurewebsites.net/webssh/host* e accedere con le credenziali dell'account Azure, non con le credenziali del server di database.

1. Nella sessione SSH eseguire i comandi seguenti (è possibile incollare i comandi usando **CTRL**+**MAIUSC**+**V** ):

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

1. Il comando `createsuperuser` chiede di immettere le credenziali di utente con privilegi avanzati. Ai fini di questa esercitazione, usare il nome utente predefinito `root`, premere **INVIO** per l'indirizzo di posta elettronica per lasciarlo vuoto e immettere `postgres1` per la password.

### <a name="create-a-poll-question-in-the-app"></a>Creare una domanda del sondaggio nell'app

1. In un browser aprire l'URL *http:\//\<app-name>.azurewebsites.net* . L'app dovrebbe visualizzare un messaggio che indica che non sono disponibili sondaggi perché nel database non sono ancora presenti sondaggi specifici.

1. Passare a *http:\//\<app-name>.azurewebsites.net/admin* . Accedere usando le credenziali di utente con privilegi avanzati della sezione precedente (`root` e `postgres1`). In **Polls** (Sondaggi) selezionare **Add** (Aggiungi) accanto a **Questions** (Domande) e creare una domanda del sondaggio con alcune scelte.

1. Passare di nuovo a *http:\//\<app-name>. azurewebsites.net/* per verificare che le domande ora vengano presentate all'utente. Rispondere alle domande come desiderato per generare alcuni dati nel database.

**Congratulazioni** Un'app Web Python Django è ora in esecuzione nel Servizio app di Azure per Linux, con un database Postgres attivo.

> [!NOTE]
> Il Servizio app rileva un progetto Django cercando in ogni sottocartella un file *wsgi.py* , che viene creato da `manage.py startproject` per impostazione predefinita. Quando trova tale file, il Servizio app carica l'app Web Django. Per altre informazioni, vedere [Configurare l'immagine Python predefinita](../../app-service/configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Apportare modifiche al codice e ripetere la distribuzione

In questa sezione si apportano modifiche locali all'app e si ridistribuisce il codice nel Servizio app. Nel processo viene configurato un ambiente virtuale Python che supporta il lavoro in corso.

### <a name="run-the-app-locally"></a>Eseguire l'app in locale

Eseguire i comandi seguenti in una finestra del terminale. Assicurarsi di seguire i prompt durante la creazione dell'utente con privilegi avanzati:

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
Una volta che l'app Web è stata caricata completamente, il server di sviluppo Django fornisce l'URL dell'app locale nel messaggio di "avvio del server di sviluppo in http://127.0.0.1:8000/. Chiudere il server con CTRL+INTERR".

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Output di esempio del server di sviluppo Django":::

Testare l'app localmente eseguendo i passaggi seguenti:

1. In un browser passare a *http:\//localhost: 8000* . Verrà visualizzato un messaggio che indica che non sono disponibili sondaggi.

1. Passare a *http:\//localhost:8000/admin* e accedere con le credenziali dell'utente amministratore creato in precedenza. In **Polls** (Sondaggi) selezionare di nuovo **Add** (Aggiungi) accanto a **Questions** (Domande) e creare una domanda del sondaggio con alcune scelte.

1. Passare di nuovo a *http:\//localhost: 8000* e rispondere alla domanda per testare l'app.

1. Arrestare il server Django premendo **CTRL**+**C** .

Quando viene eseguita localmente, l'app usa un database Sqlite3 locale e non interferisce con il database di produzione. Se lo si desidera, è anche possibile usare un database PostgreSQL locale per simulare meglio l'ambiente di produzione.



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

Eseguire di nuovo il server di sviluppo con `python manage.py runserver` e testare l'app in *http:\//localhost:8000/admin* :

Arrestare di nuovo il server Web Django con **CTRL**+**C** .


### <a name="redeploy-the-code-to-azure"></a>Ridistribuire il codice in Azure

Eseguire il comando seguente nella radice del repository:

```azurecli
az webapp up
```

Questo comando usa i parametri memorizzati nella cache nel file *.azure/config* . Poiché il Servizio app rileva che l'app esiste già, il codice viene semplicemente ridistribuito.



### <a name="rerun-migrations-in-azure"></a>Eseguire di nuovo le migrazioni in Azure

Poiché sono state apportate modifiche al modello di dati, è necessario rieseguire le migrazioni del database nel Servizio app.

Aprire di nuovo una sessione SSH nel browser passando a *https://\<app-name>.scm.azurewebsites.net/webssh/host* . Eseguire quindi i comandi seguenti:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Esaminare l'app nell'ambiente di produzione

Passare a *http:\//\<app-name>.azurewebsites.net* e testare di nuovo l'app nell'ambiente di produzione. Poiché è stata modificata solo la lunghezza di un campo del database, la modifica è evidente soltanto se si tenta di immettere una risposta più lunga quando si crea una domanda.

> [!TIP]
> È possibile usare [django-storages](https://django-storages.readthedocs.io/en/latest/backends/azure.html) per archiviare asset statici e multimediali nell'archiviazione di Azure. È possibile usare la rete CDN di Azure per comprimere i file statici in formato gzip.


## <a name="manage-your-app-in-the-azure-portal"></a>Gestire l'app nel portale di Azure

Nel [portale di Azure](https://portal.azure.com) cercare il nome dell'app e quindi selezionare l'app nei risultati.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Output di esempio del server di sviluppo Django":::

Per impostazione predefinita, il portale visualizza la pagina di **panoramica** dell'app, che fornisce una visualizzazione generale delle prestazioni. Qui è possibile eseguire attività di gestione di base come esplorazione, arresto, riavvio ed eliminazione. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Output di esempio del server di sviluppo Django":::


## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole mantenere l'app o continuare con l'esercitazione successiva, andare a [Passaggi successivi](#next-steps). In caso contrario, per evitare di sostenere addebiti continui, è possibile eliminare il gruppo di risorse creato per questa esercitazione:

```azurecli
az group delete -g myresourcegroup
```

Il comando usa il nome del gruppo di risorse memorizzato nella cache nel file *.azure/config* . Eliminando il gruppo di risorse, vengono inoltre deallocate ed eliminate tutte le risorse in esso contenute.

## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni su come eseguire il mapping di un nome DNS personalizzato all'app:

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](../../app-service/app-service-web-tutorial-custom-domain.md)

Vedere le informazioni sul modo in cui il Servizio app esegue un'app Python:

> [!div class="nextstepaction"]
> [Configurare un'app Python](../../app-service/configure-language-python.md)
