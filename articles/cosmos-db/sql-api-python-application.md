---
title: Esercitazione per un'applicazione Web Python Flask per Azure Cosmos DB | Microsoft Docs
description: Esaminare un'esercitazione del database sull'utilizzo di Azure Cosmos DB per archiviare e accedere ai dati da un'applicazione web Python Flask ospitata in Azure. Trovare soluzioni di sviluppo dell'applicazione.
keywords: Sviluppo di applicazioni, Python Flask, applicazione Web Python, sviluppo Web Python
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 20ebec18-67c2-4988-a760-be7c30cfb745
ms.service: cosmos-db
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 10/17/2017
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b83e096cbb677653db8a13b6b7c04e6c705fd2f3
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>Creare un'applicazione Web Python Flask con Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Questa esercitazione illustra come usare Azure Cosmos DB per archiviare e accedere ai dati da un'applicazione Web Python Flask ospitata in Servizio app di Azure. Questa esercitazione presuppone una certa esperienza nell'uso di Python e dei siti Web di Azure.

In questa esercitazione del database vengono trattati i seguenti argomenti:

1. Creazione e provisioning di un account Azure Cosmos DB.
2. Creazione di un'applicazione Python Flask.
3. Connessione ad Azure Cosmos DB e relativo utilizzo dall'applicazione Web.
4. Distribuzione dell'applicazione Web in Servizio app di Azure.

Seguendo questa esercitazione, si creerà una semplice applicazione di voto che consente di votare per un sondaggio.

![Screenshot dell'applicazione di voto creata in questa esercitazione del database](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>Prerequisiti per l'esercitazione del database
Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili i seguenti elementi:

* [Una sottoscrizione di Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) con abilitazione dello **sviluppo in Azure** e dello **sviluppo in Python**. È possibile controllare se questi prerequisiti sono installati ed eventualmente installarli avviando il **Programma di installazione di Visual Studio** localmente.   
* [Microsoft Azure SDK per Python 2.7](https://azure.microsoft.com/downloads/). 
* [Python 2.7](https://www.python.org/downloads/windows/). È possibile usare l'installazione a 32 o a 64 bit.

> [!IMPORTANT]
> Se si installa Python 2.7 per la prima volta, nella schermata Customize Python 2.7.13 (Personalizza Python 2.7.13) assicurarsi di selezionare **Add python.exe to Path** (Aggiungi python.exe a percorso).
> 
> ![Schermata Customize Python 2.7.11, in cui è necessario selezionare Add python.exe to Path](./media/sql-api-python-application/cosmos-db-python-install.png)
> 
> 

* [Compilatore Microsoft Visual C++ per Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266).

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>Passaggio 1: Creare un account del database di Azure Cosmos DB
Creare prima di tutto un account Azure Cosmos DB. Se si ha già un account o si usa l'emulatore Azure Cosmos DB per questa esercitazione, è possibile andare al [Passaggio 2: Creare una nuova applicazione Web Python Flask](#step-2-create-a-new-python-flask-web-application).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
Viene ora illustrata in dettaglio la procedura per creare un'applicazione Web Python Flask completamente nuova.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Passaggio 2: Creare una nuova applicazione Web Python Flask
1. Scegliere **Nuovo** dal menu **File** di Visual Studio e quindi fare clic su **Progetto**.
   
    Verrà visualizzata la finestra di dialogo **Nuovo progetto** .
2. Nel riquadro sinistro espandere **Modelli** e quindi **Python**, quindi fare clic su **Web**. 
3. Selezionare **Flask Web Project** (Progetto Web Flask) nel riquadro centrale, quindi digitare **tutorial** (esercitazione) nella casella **Nome** e fare clic su **OK**. Tenere presente che i nomi di pacchetto Python devono essere tutti in lettere minuscole, come descritto nella [guida di stile per il codice Python](https://www.python.org/dev/peps/pep-0008/#package-and-module-names).
   
    Se è la prima volta che si usa Python Flask, si tratta di un framework di sviluppo di applicazioni Web che consente di creare applicazioni Web in Python più velocemente.
   
    ![Schermata della finestra Nuovo progetto in Visual Studio con Python selezionato a sinistra, progetto web Flask selezionato nella parte centrale ed esercitazioni sul nome nella casella nome](./media/sql-api-python-application/image9.png)
4. Nella finestra **Python Tools for Visual Studio** selezionare **Install into a virtual environment** (Installa in un ambiente virtuale). 
   
    ![Schermata dell'esercitazione del database - Python Tools per la finestra di Visual Studio](./media/sql-api-python-application/python-install-virtual-environment.png)
5. Nella finestra **Aggiungi ambiente virtuale** selezionare Python 2.7 o Python 3.5 nella casella di selezione di un interprete, accettare gli altri valori predefiniti e quindi fare clic su **Crea**. Questo permette di configurare l'ambiente virtuale Python per il progetto.
   
    ![Schermata dell'esercitazione del database - Python Tools per la finestra di Visual Studio](./media/sql-api-python-application/image10_A.png)
   
    Se l'installazione dell'ambiente riesce, nella finestra di output verrà visualizzato il messaggio `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.` .

## <a name="step-3-modify-the-python-flask-web-application"></a>Passaggio 3: Modificare l'applicazione Web Python Flask
### <a name="add-the-python-flask-packages-to-your-project"></a>Aggiungere i pacchetti Python Flask al progetto
Una volta configurato il progetto, è necessario aggiungere i pacchetti pallone richiesti al progetto, inclusi pydocumentdb, il pacchetto di Python per l'API di SQL Azure DB Cosmos.

1. In Esplora soluzioni aprire il file denominato **requirements.txt** e sostituire il contenuto esistente con quello riportato di seguito:
   
        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0
2. Salvare il file **requirements.txt** . 
3. In Esplora soluzioni fare clic con il pulsante destro del mouse su **env** e scegliere **Install from requirements.txt**.
   
    ![Schermata che mostra env (Python 2.7) selezionata con l'installazione da requirements.txt evidenziato nell'elenco](./media/sql-api-python-application/cosmos-db-python-install-from-requirements.png)
   
    Al termine dell'installazione, la finestra di output visualizza quanto segue:
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > In rari casi è possibile che venga visualizzato un errore nella finestra di output. Verificare in tal caso se l'errore è correlato all'operazione di pulizia. La pulizia ha talvolta esito negativo, ma l'installazione viene comunque completata (scorrere la finestra di output verso l'alto per verificarne l'esito). È possibile controllare l'installazione [verificando l'ambiente virtuale](#verify-the-virtual-environment). Se l'installazione non è riuscita, ma la verifica ha esito positivo, è possibile continuare.
   > 
   > 

### <a name="verify-the-virtual-environment"></a>Verifica dell'ambiente virtuale
È importante verificare che tutto sia installato correttamente.

1. Premere **CTRL**+**MAIUSC**+**B** per compilare la soluzione.
2. Al termine della compilazione, avviare il sito Web premendo **F5**. Verranno avviati il server di sviluppo Flask e il Web browser. Dovrebbe essere visualizzata la pagina seguente:
   
    ![Il progetto di sviluppo web Python Flask vuoto visualizzato in un browser](./media/sql-api-python-application/image12.png)
3. Per arrestare il debug del sito Web, premere **MAIUSC**+**F5**.

### <a name="create-database-collection-and-document-definitions"></a>Creare definizioni di database, raccolta e documento
Ora viene creata l'applicazione di voto aggiungendo nuovi file e aggiornandone altri.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **tutorial**, (esercitazione) quindi scegliere **Aggiungi** e infine **Nuovo elemento**. Selezionare **Empty Python File** (File di Python vuoto) e denominare il file **forms.py**.  
2. Aggiungere il codice seguente al file forms.py e quindi salvare il file.

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>Aggiungere le importazioni necessarie a views.py
1. In Esplora soluzioni espandere la cartella **tutorial** (esercitazione) e aprire il file **views.py**. 
2. Aggiungere le istruzioni import seguenti all'inizio del file **views.py** e quindi salvare il file. Queste istruzioni importano i pacchetti Flask e Python SDK di Azure Cosmos DB.
   
    ```python
    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>Creare il database, la raccolta e il documento
* Ancora in **views.py**aggiungere il codice seguente alla fine del file. Verrà creato il database usato dal form. Non eliminare nessuna porzione del codice esistente dal file **views.py**. Aggiungere semplicemente la parte nuova alla fine del file.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config.DOCUMENTDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config.DOCUMENTDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>Leggere il database, la raccolta e il documento e inviare il form
* Ancora in **views.py**aggiungere il codice seguente alla fine del file. Verrà configurato il form e verranno letti il database, la raccolta e il documento. Non eliminare nessuna porzione del codice esistente dal file **views.py**. Aggiungere semplicemente la parte nuova alla fine del file.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.COSMOSDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.COSMOSDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### <a name="create-the-html-files"></a>Creare i file HTML
1. In Esplora soluzioni, nella cartella **tutorial** (esercitazione) fare clic con il pulsante destro del mouse sulla cartella **modelli**, fare clic su **Aggiungi** e quindi su **Nuovo elemento**. 
2. Selezionare **Pagina HTML** e nella casella del nome digitare **create.html**. 
3. Ripetere i passaggi 1 e 2 per creare altri due file HTML: results.html e vote.html.
4. Aggiungere il codice seguente al file **create.html** in the `<body>` . Consente di visualizzare un messaggio che comunica l'avvenuta creazione di un nuovo database, di una raccolta e di un documento.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. Aggiungere il codice seguente al file **results.html** nell'elemento `<body`>. Consente di visualizzare i risultati del sondaggio.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. Aggiungere il codice seguente al file **vote.html** nell'elemento `<body`>. Consente di visualizzare il sondaggio e di accettare i voti. Durante la registrazione dei voti, il controllo viene passato a views.py, dove Azure Cosmos DB riconosce il voto espresso e aggiunge il documento di conseguenza.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. Nella cartella **modelli** sostituire il contenuto del file **index.html** con il codice seguente. Questo fungerà da pagina di destinazione per l'applicazione.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Aggiungere un file di configurazione e modificare \_\_init\_\_.py
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **tutorial** (esercitazione), scegliere **Aggiungi**, **Nuovo elemento** e infine **Empty Python File** (File di Python vuoto), quindi denominare il file **config.py**. Questo file config è necessario per i moduli in Flask. È possibile usarlo anche per fornire una chiave privata. Tale chiave non sarà tuttavia necessaria per questa esercitazione.
2. Aggiungere il seguente codice al config.py, sarà necessario modificare i valori di **COSMOSDB\_HOST** e **COSMOSDB\_chiave** nel passaggio successivo.
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    COSMOSDB_HOST = 'https://YOUR_COSMOSDB_NAME.documents.azure.com:443/'
    COSMOSDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    COSMOSDB_DATABASE = 'voting database'
    COSMOSDB_COLLECTION = 'voting collection'
    COSMOSDB_DOCUMENT = 'voting document'
    ```
3. Nel [portale di Azure](https://portal.azure.com/) passare alla pagina **Chiavi** facendo clic su **Sfoglia**, **Account Azure Cosmos DB**, fare doppio clic sul nome dell'account da usare e quindi fare clic sul pulsante **Chiavi** nell'area **Informazioni di base**. Nel **chiavi** copiare il **URI** valore e incollarlo il **config.py** file, come valore per il **COSMOSDB\_HOST**proprietà. 
4. Eseguire il backup nel portale di Azure, nel **chiavi** copiare il valore della **chiave primaria** o **chiave secondaria**e incollarlo nella **config.py**file, come valore per il **COSMOSDB\_chiave** proprietà.
5. Nel file **\_\_init\_\_.py** aggiungere la riga seguente: 
   
        app.config.from_object('config')
   
    Il contenuto del file deve essere analogo al seguente:
   
    ```python
    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views
    ```
6. Dopo aver aggiunto tutti i file, l'aspetto di Esplora soluzioni dovrebbe risultare simile al seguente:
   
    ![Schermata della finestra Esplora soluzioni di Visual Studio](./media/sql-api-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>Passaggio 4: Esecuzione dell'applicazione Web in locale
1. Premere **CTRL**+**MAIUSC**+**B** per compilare la soluzione.
2. Al termine della compilazione, avviare il sito Web premendo **F5**. Dovrebbe venire visualizzata la schermata seguente.
   
    ![Screenshot di Python e dell'applicazione per votazione di Azure Cosmos DB visualizzati in un Web browser](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)
3. Fare clic su **Create/Clear the Voting Database** per generare il database.
   
    ![Schermata della pagina di creazione dell'applicazione web - informazioni dettagliate sullo sviluppo](./media/sql-api-python-application/cosmos-db-python-run-create-page.png)
4. Fare quindi clic su **Voto** e selezionare un'opzione.
   
    ![Schermata dell'applicazione Web con una domanda relativa al voto](./media/sql-api-python-application/cosmos-db-vote.png)
5. Ogni voto espresso va a incrementare il contatore appropriato.
   
    ![Schermata dei risultati della pagina voto visualizzata](./media/sql-api-python-application/cosmos-db-voting-results.png)
6. Per arrestare il debug del sito Web, premere MAIUSC+F5.

## <a name="step-5-deploy-the-web-application-to-azure"></a>Passaggio 5: Distribuire l'applicazione Web in Azure
L'applicazione completa a questo punto funziona correttamente con Azure Cosmos DB localmente. Si procede quindi con la creazione di un file web.config, l'aggiornamento dei file nel server in modo che corrispondano all'ambiente locale e quindi la visualizzazione dell'app completata in Azure. Questa procedura è specifica di Visual Studio 2017. Se si usa una versione diversa di Visual Studio, vedere la pagina relativa alla [pubblicazione in Servizio app di Azure](/visualstudio/python/publishing-to-azure.md).

1. In **Esplora soluzioni** di Visual Studio fare clic con il pulsante destro del mouse sul progetto e selezionare **Aggiungi > Nuovo elemento**. Nella finestra di dialogo che viene visualizzata selezionare il modello **Azure web.config (Fast CGI)** e fare clic su **OK**. Verrà creato un file `web.config` nella radice del progetto. 

2. Modificare la sezione `<system.webServer>` in `web.config` in modo che il percorso corrisponda all'installazione di Python. Per Python 2.7 x64, ad esempio, la voce deve apparire come segue:
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. Impostare la voce `WSGI_HANDLER` in `web.config` come `tutorial.app` in modo da corrispondere al nome del progetto. 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. In **Esplora soluzioni** di Visual Studio espandere la cartella **tutorial** (esercitazione), fare clic con il pulsante destro del mouse sulla cartella `static`, selezionare **Aggiungi > Nuovo elemento**, selezionare il modello "web.config di file statici di Azure" e fare clic su **OK**. Questa azione crea un altro file `web.config` nella cartella `static` che disabilita l'elaborazione Python per la cartella. Questa configurazione invia richieste di file statici al server Web predefinito, anziché usare l'applicazione Python.

5. Salvare i file, fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni (assicurarsi che non sia ancora in esecuzione localmente) e selezionare **Pubblica**.  
   
     ![Schermata dell'esercitazione selezionata in Esplora soluzioni, con l'opzione Pubblica evidenziata](./media/sql-api-python-application/image20.png)
6. Nella finestra di dialogo **Pubblica** selezionare **Servizio app di Microsoft Azure**, selezionare **Crea nuovo** e quindi fare clic su **Pubblica**.
   
    ![Screenshot della finestra Pubblica sul Web con Servizio app di Microsoft Azure evidenziato](./media/sql-api-python-application/cosmos-db-python-publish.png)
7. Nella finestra di dialogo **Crea servizio app** immettere il nome dell'app Web, specificando anche **Sottoscrizione**, **Gruppo di risorse** e **Piano di Servizio app**, quindi fare clic su **Crea**.
   
    ![Screenshot della finestra App Web di Microsoft Azure](./media/sql-api-python-application/cosmos-db-python-create-app-service.png)
8. In pochi secondi Visual Studio completa la copia dei file nel server e visualizza il messaggio "Impossibile visualizzare la pagina a causa di un errore interno del server." nella pagina `http://<your app service>.azurewebsites.net/`.

9. Nel portale di Azure aprire il nuovo account del servizio app, quindi nel menu di spostamento scorrere verso il basso fino alla sezione **Strumenti di sviluppo**, selezionare **Estensioni** e quindi fare clic su **+ Aggiungi**.

10. Nella pagina **Scegliere l'estensione** scorrere fino all'installazione di Python 2.7 più recente e selezionare l'opzione x86 o x64 bit. Fare quindi clic su **OK** per accettare le note legali.  
   
11. Usare la console di Kudu, che è disponibile in `https://<your app service name>.scm.azurewebsites.net/DebugConsole`, per installare i pacchetti elencati nel file `requirements.txt` dell'app. A tale scopo, nella console di diagnostica di Kudu, passare alla cartella di Python `D:\home\Python27` quindi eseguire questo comando, come descritto nella sezione [Console di Kudu](/visual-studio/python/managing-python-on-azure-app-service.md#azure-app-service-kudu-console):

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. Riavviare il servizio app nel portale di Azure dopo avere installato i nuovi pacchetti scegliendo il pulsante **Riavvia**. 

    > [!Tip] 
    > Se si apportano modifiche al file `requirements.txt` dell'app, assicurarsi di usare di nuovo la console di Kudu per installare i pacchetti che sono ora elencati nel file. 

13. Dopo aver configurato completamente l'ambiente del server, aggiornare la pagina nel browser. Verrà visualizzata l'app Web.

    ![Risultati della pubblicazione di app Bottle, Flask e Django nel servizio app](./media/sql-api-python-application/python-published-app-services.png)

    > [!Tip] 
    > Se la pagina Web non viene visualizzata o se viene visualizzato di nuovo il messaggio "Impossibile visualizzare la pagina a causa di un errore interno del server.", aprire il file web.config in Kudo e aggiungere ` <httpErrors errorMode="Detailed"></httpErrors>` nella sezione system.webServer, quindi aggiornare la pagina. Verrà visualizzato l'output dettagliato dell'errore nel browser. 

## <a name="troubleshooting"></a>risoluzione dei problemi
Se si tratta della prima app Python eseguita nel computer, assicurarsi che le cartelle seguenti o i percorsi di installazione equivalenti siano inclusi nella variabile PATH:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Se si riceve un errore nella pagina di voto e il progetto ha un nome diverso da **tutorial** (esercitazione), assicurarsi che **\_\_init\_\_.py** faccia riferimento al nome di progetto corretto nella riga `import tutorial.view`.

## <a name="next-steps"></a>Passaggi successivi
Congratulazioni! È stata appena creata la prima applicazione Web Python con Azure Cosmos DB ed è stata pubblicata in Azure.

Per aggiungere altre funzionalità all'applicazione Web, esaminare le API disponibili in [Azure Cosmos DB Python SDK](sql-api-sdk-python.md).

Per altre informazioni su Azure, Visual Studio e Python, vedere il [centro per sviluppatori di Python](https://azure.microsoft.com/develop/python/). 

Per altre esercitazioni su Python Flask, vedere [The Flask Mega-Tutorial, Part I: Hello, World!](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)(Esercitazione su Flask, parte I: Hello, World!). 

[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[2]: https://www.python.org/downloads/windows/
[3]: https://www.microsoft.com/download/details.aspx?id=44266
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Azure portal]: http://portal.azure.com
