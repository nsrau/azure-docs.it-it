<properties 
    pageTitle="Creare un'app Web con Python e Flask tramite DocumentDB | Azure" 
    description="Informazioni sull'uso di DocumentDB per archiviare e accedere ai dati da un'applicazione Web Python e Flask (MVC) ospitata in Azure." 
    services="documentdb" 
    documentationCenter="" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2015" 
    ms.author="crwilcox"/>

# Creare un'applicazione Web con Python e Flask (MVC) usando DocumentDB
------------------------------------------------------------------------

Per evidenziare il modo in cui i clienti possono sfruttare efficientemente Azure DocumentDB per archiviare ed eseguire query su documenti JSON, questo argomento fornisce una procedura dettagliata end-to-end per la creazione di un'applicazione Web di voto usando Azure Document DB.

Questa procedura dettagliata mostra come usare il servizio DocumentDB fornito da Azure per archiviare e accedere ai dati forniti da un'applicazione Web Python ospitata in Azure e presuppone che si siano già usati Python e Siti Web di Azure.

In questa procedura dettagliata verranno trattati i seguenti argomenti:

1. Creare ed eseguire il provisioning di un account DocumentDB

2. Creare un'applicazione MVC Python

3. Connettersi e usare Azure DocumentDB dall'applicazione Web

4. Distribuire l'applicazione Web in Siti Web di Azure

Seguendo questa procedura dettagliata verrà creata una semplice applicazione
di voto che consente di votare in un sondaggio.

![Alt text](./media/documentdb-python-application/image1.png)


## Prerequisiti

Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili i seguenti elementi:

- Visual Studio 2013 (o [Visual Studio Express][], che è la versione gratuita)

- Python Tools per Visual Studio, disponibile [qui][]

- Azure SDK per Visual Studio 2013, versione 2.4 o successiva, disponibile [qui][1]

- Python 2.7, disponibile [qui][2]

- Compilatore Microsoft Visual C++ per Python 2.7, disponibile [qui][3]

## Passaggio 1: Creare un account di database di DocumentDB

Per eseguire il provisioning di un account di database di DocumentDB in Azure, aprire il [portale di gestione di Azure][] e fare clic nel riquadro Raccolta di Azure nella home page oppure scegliere "+" nell'angolo inferiore sinistro dello schermo.

![Alt text](./media/documentdb-python-application/image2.png)


Verrà aperto Azure Marketplace, da cui sarà possibile selezionare uno tra i molti servizi di Azure disponibili. In Marketplace selezionare l'opzione relativa a dati e analisi dall'elenco di categorie.

![Alt text](./media/documentdb-python-application/image3.png)

A questo punto, eseguire la ricerca di documentdb e selezionare l'opzione relativa a DocumentDB

![Alt text](./media/documentdb-python-application/image4.png)

Scegliere "Crea" nella parte inferiore dello schermo

![Alt text](./media/documentdb-python-application/image5.png)

Verrà visualizzato il pannello relativo al nuovo DocumentDB, in cui è possibile specificare il nome, l'area geografica, la scalabilità, il gruppo di risorse e altre impostazioni relative al nuovo account.

![Alt text](./media/documentdb-python-application/image6.png)

Dopo aver specificato i valori dell'account, fare clic su "Crea". Il processo di provisioning avvierà la creazione dell'account di database.
Al termine del processo di provisioning, verrà visualizzata una notifica nella relativa area del portale e il riquadro nella schermata iniziale (se si è scelto di crearne uno) verrà modificato per riflettere il completamento dell'azione.

![Alt text](./media/documentdb-python-application/image7.png)

Al termine del processo di provisioning, facendo clic sul riquadro DocumentDB dalla schermata iniziale si avvierà il pannello principale dell'account DocumentDB appena creato.

![Alt text](./media/documentdb-python-application/image8.png)

Usando il pulsante "Chiavi" accedere all'URL e alla chiave primaria del proprio endpoint, copiare tali valori negli Appunti e mantenerli a portata di mano poiché verranno usati nell'applicazione Web che verrà creata in seguito.


## Passaggio 2: Creare una nuova applicazione Web Python Flask

Aprire Visual Studio, File -\> Nuovo progetto -\> Python -\> Progetto Web Flask con il nome **tutorial**. 

Se è la prima volta che si usa Flask, si tratta di un framework Web che consente di creare applicazioni Web in Python più velocemente. [Fare clic qui per accedere alle esercitazioni per Flask][].

![Alt text](./media/documentdb-python-application/image9.png)

Verrà chiesto se si vogliono installare pacchetti esterni. Fare clic su **Installa in un ambiente virtuale**. Assicurarsi di usare Python 2.7 come ambiente di base, poiché PyDocumentDB attualmente non supporta Python 3.x.  Questo configurerà l'ambiente virtuale Python per il progetto.

![Alt text](./media/documentdb-python-application/image10.png)


## Passaggio 3: Modificare l'applicazione Web Python Flask


### Aggiungere i pacchetti Flask al progetto

Dopo la configurazione del progetto sarà necessario aggiungere alcuni pacchetti Flask necessari per il progetto, compreso pydocumentdb, il pacchetto python per DocumentDB. Aprire il file denominato **requirements.txt** e sostituire il contenuto esistente con quello riportato di seguito:

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
    pydocumentdb>=0.9.4-preview

Fare clic con il pulsante destro del mouse su **env**, quindi fare clic su **installa da requirements.txt**

![Alt text](./media/documentdb-python-application/image11.png)

**Nota:** In rari casi, è possibile che venga visualizzato un errore nella finestra di output. In questo caso, controllare se l'errore è relativo alla pulizia. Talvolta la pulizia non riuscirà ma installazione verrà comunque completata (scorrere verso l'alto nella finestra di output per verificarlo).
<a name="verify-the-virtual-environment"></a> Se si verifica questa situazione, è possibile continuare senza problemi.


### Verifica dell'ambiente virtuale

È importante verificare che tutto sia installato correttamente. Avviare il sito Web premendo **F5**. Verranno avviati il server di sviluppo Flask e il Web browser. Dovrebbe essere visualizzata la pagina seguente:

![Alt text](./media/documentdb-python-application/image12.png)

### Creare database, raccolta e documento - definizione

Aggiungere un file Python facendo clic con il pulsante destro del mouse sulla cartella denominata **tutorial** in Esplora soluzioni.  Denominare il file **forms.py**.  Viene creata l'applicazione di voto.

    from flask.ext.wtf import Form
    from wtforms import RadioField

    class VoteForm(Form):
        deploy_preference  = RadioField('Deployment Preference', choices=[
            ('Web Site', 'Web Site'),
            ('Cloud Service', 'Cloud Service'),
            ('Virtual Machine', 'Virtual Machine')], default='Web Site')

### Aggiungere le importazioni necessarie a views.py

Aggiungere le istruzioni di importazione seguenti all'inizio del file **views.py**. Queste importeranno i pacchetti PythonSDK e Flask di DocumentDB.


    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    

### Create database, raccolta e documento

Aggiungere il codice seguente al file **views.py**. Verrà creato il database usato dal form. Non eliminare nessuna porzione del codice esistente dal file **views.py**. Aggiungere semplicemente la parte nuova alla fine del file.

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
            'name': config.DOCUMENTDB_DOCUMENT })
    
        return render_template(
            'create.html', 
            title='Create Page', 
            year=datetime.now().year,
            message='You just created a new database, collection, and document.  Your old votes have been deleted')


### Leggere il database, la raccolta e il documento e creazione del form di invio

Aggiungere il codice seguente al file **views.py**. Verrà configurato il form e verranno letti il database, la raccolta e il documento. Non eliminare nessuna porzione del codice esistente dal file **views.py**. Aggiungere semplicemente la parte nuova alla fine del file.
    
    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = VoteForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
            
            # Read databases and take first since id should not be duplicated.
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            
            # Read collections and take first since id should not be duplicated.
            coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))
    
            # Read documents and take first since id should not be duplicated.
            doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))
    
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


### Creare i file HTML

Aggiungere i seguenti file HTML nella cartella templates: create.html, results.html, vote.html

Aggiungere il codice seguente al file **create.html**. Questa operazione consente di visualizzare il messaggio che comunica l'avvenuta creazione di un nuovo database, di una raccolta e di un documento.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}

Aggiungere il codice seguente a **results.html**. Questa operazione consente di visualizzare i risultati del sondaggio.

    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
    <br />

    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
        <div class="col-sm-5">
            <div class="progress">
                <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" 
                     aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                    {{vote_object.choices[choice]}}
                </div>
            </div>
        </div>
    </div>
    {% endfor %}

    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}

Aggiungere il codice seguente al file **vote.html**. Questa operazione consente di visualizzare il sondaggio e accettare i voti. Alla registrazione dei voti il controllo viene passato a views.py che riconoscerà il voto espresso e ne effettuerà l'aggiunta al documento.

    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
        {{form.deploy_preference}}
        <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}

Sostituire il contenuto del file **index.html** con il codice seguente. Questo fungerà da pagina di destinazione per l'applicazione.

    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}


### Aggiungere un file di configurazione e modificare \_\_init\_\_.py

Fare clic con il pulsante destro del mouse sull'esercitazione relativa al nome di progetto e aggiungere un file: **config.py**.
Questo file config è richiesto per i moduli in Flask. È possibile usarlo anche per fornire una chiave privata. Tale chiave non sarà tuttavia necessaria per questa esercitazione. Aggiungere il codice seguente al file config.py   Modificare i valori di **DOCUMENTDB\_HOST** e **DOCUMENTDB\_KEY**.

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'


Analogamente, sostituire il contenuto di **\_\_init\_\_.py** con il codice seguente.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Dopo aver seguito i passaggi riportati in precedenza, questo sarà l'attuale aspetto
di Esplora soluzioni

![Alt text](./media/documentdb-python-application/image15.png)


## Passaggio 4: Eseguire l'applicazione in locale

Premere F5 o il pulsante Esegui in Visual Studio. Sullo schermo verrà visualizzato quanto segue.

![Alt text](./media/documentdb-python-application/image16.png)

Fare clic su "Create/Clear the Voting Database" per generare il database.

![Alt text](./media/documentdb-python-application/image17.png)

Fare clic su "Vote" e selezionare un'opzione.

![Alt text](./media/documentdb-python-application/image18.png)

Ogni voto espresso andrà a incrementare il contatore appropriato.

![Alt text](./media/documentdb-python-application/image19.png)


## Passaggio 5: Distribuire l'applicazione in Siti Web di Azure

Ora che l'applicazione completa funziona correttamente con DocumentDB, sarà possibile distribuirla in Siti Web di Azure. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni (assicurarsi che il progetto non sia ancora in esecuzione localmente) quindi selezionare Pubblica.  Selezionare quindi Siti Web di Microsoft Azure.

![Alt text](./media/documentdb-python-application/image20.png)


Configurare il sito Web di Azure fornendo le credenziali e fare clic su Pubblica.

![Alt text](./media/documentdb-python-application/image21.png)

Dopo alcuni secondi, Visual Studio completerà la pubblicazione dell'applicazione Web e avvierà un browser in cui sarà possibile ammirare il proprio lavoro in esecuzione in Azure.


## Passaggi successivi

Congratulazioni. Si è creata la prima applicazione Python usando Azure DocumentDB e la si è pubblicata in Siti Web di Azure.


  [Fare clic qui per accedere alle esercitazioni per Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [qui]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Installazione guidata piattaforma Web Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
  [Portale di gestione di Azure]: http://portal.azure.com
<!--HONumber=47-->
