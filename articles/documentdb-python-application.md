<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="Build a web app with Python and Flask using DocumentDB | Azure" description="Learn how to use DocumentDB to store and access data from an Python and Flask (MVC) web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong" />

<a name="_Toc395888515"></a><a name="_Toc395809324">Creare un'applicazione Web con Python e Flask (MVC) usando DocumentDB</a>
===========================================================================================================================================

<a name="_Toc395809324">

<a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">Panoramica</a>
========================================================================================================================

<a name="_Toc395888517"></a><a name="_Toc395809326">Scenario</a>
----------------------------------------------------------------

Per evidenziare il modo in cui i clienti possono sfruttare efficientemente Azure DocumentDB per
archiviare ed eseguire query su documenti JSON, questo argomento fornisce una procedura dettagliata
end-to-end per la creazione di un'applicazione Web di voto usando Azure Document
DB.

Questa procedura dettagliata mostra come usare il servizio DocumentDB fornito da
Azure per archiviare e accedere ai dati forniti da un'applicazione Web Python ospitata in
Azure e presuppone che si siano già usati Python e
Siti Web di Azure.

Si apprenderà come:

1\. Creare ed eseguire il provisioning di un account DocumentDB

2\. Creare un'applicazione MVC Python

3\. Connettersi e usare Azure DocumentDB dall'applicazione Web

4\. Distribuire l'applicazione Web in Siti Web di Azure

Seguendo questa procedura dettagliata, si creerà una semplice applicazione di voto
che consente di votare per un sondaggio.

![Alt text](./media/documentdb-python-application/image1.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">Prerequisiti</a>


Prima di seguire le istruzioni di questo articolo, verificare
che siano installati i seguenti elementi:

Visual Studio 2013 (o [Visual Studio Express][], che è la versione
gratuita)

Python Tools per Visual Studio, disponibile [qui][]

Azure SDK per Visual Studio 2013, versione 2.4 o successiva, disponibile
[qui][1]

Strumenti della riga di comando multipiattaforma di Azure, disponibili tramite [Installazione
guidata piattaforma Web Microsoft][]

<a name="_Toc395888519"></a><a name="_Toc395809328">Creare un account di database di DocumentDB</a>
============================================================================================

Per eseguire il provisioning di un account di database di DocumentDB in Azure, aprire il
portale di gestione di Azure e fare clic nel riquadro Raccolta di Azure nella
home page oppure scegliere "+" nell'angolo inferiore sinistro dello schermo.

![Alt text](./media/documentdb-python-application/image2.png)


Verrà aperta la Raccolta di Azure, da cui sarà possibile selezionare uno tra i molti
servizi di Azure disponibili. Nella raccolta scegliere "Dati, archiviazione, cache e
backup" dall'elenco delle categorie.

![Alt text](./media/documentdb-python-application/image3.png)

Scegliere l'opzione relativa ad Azure DocumentDB

![Alt text](./media/documentdb-python-application/image4.png)

Scegliere "Crea" nella parte inferiore dello schermo

![Alt text](./media/documentdb-python-application/image5.png)

Verrà aperto il pannello "Nuovo DocumentDB" in cui sarà possibile specificare 
nome, area, scala, gruppo di risorse e altre impostazioni per il nuovo
account.

![Alt text](./media/documentdb-python-application/image6.png)

Dopo aver fornito i valori per l'account, fare clic su "Crea"
e il processo di provisioning avvierà la creazione dell'account di database.
Al termine del processo verrà visualizzata una notifica
nell'area di notifica del portale e il riquadro nella schermata 
iniziale (se si è scelto di crearne una) verrà modificato per riflettere
il completamento dell'azione.

![Alt text](./media/documentdb-python-application/image7.png)

Dopo il completamento del provisioning, facendo clic sul pannello DocumentDB della schermata
iniziale si avvierà il pannello principale per l'account
DocumentDB appena creato.

![Alt text](./media/documentdb-python-application/image8.png)
![Alt text](./media/documentdb-python-application/image9.png)



Usando il pulsante "Chiavi" accedere all'URL e alla chiave primaria del proprio endpoint,
copiare tali valori negli Appunti e mantenerli a portata di mano poiché verranno usati
nell'applicazione Web che verrà creata in seguito.

</a>

<a name="_Toc395888520"></a><a name="_Toc395809329">Creare una nuova applicazione Web Python Flask</a>
=================================================================================================

Aprire Visual Studio, File -\> Nuovo progetto -\> Python -\> Progetto Web
Flask, con il nome **tutorial**. Verrà chiesto se si vogliono
installare pacchetti esterni. Fare clic su Installa in un ambiente virtuale
e fare clic su Crea. Questo configurerà l'ambiente virtuale
Python per il progetto.

Se è la prima volta che si usa Flask, si tratta di un framework Web che consente di creare applicazioni
Web in Python più velocemente. [Fare clic qui per accedere alle esercitazioni per Flask][].

![Alt text](./media/documentdb-python-application/image10.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">Aggiungere i pacchetti Flask al progetto</a>
==================================

Dopo avere impostato il progetto, è necessario aggiungere alcuni pacchetti Flask
necessari per il progetto, ad esempio i moduli. Fare clic con il pulsante destro del mouse su **env**
e **installare i seguenti pacchetti Python Packages (è importante rispettare l'ordine
seguente)**:

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![Alt text](./media/documentdb-python-application/image11.png)

**Nota:** in rari casi è possibile che venga visualizzato un errore nella finestra di output. In
un'eventualità di questo tipo, verificare se l'errore è correlato alla pulizia. Talvolta la
pulizia può avere esito negativo ma l'installazione viene comunque completata correttamente (scorrere
verso l'alto nella finestra di output per verificarlo).
<a name="verify-the-virtual-environment"></a> Se si verifica questa situazione, è possibile continuare.

</h1>
<a name="_Toc395888522"></a><a name="_Toc395809331">Verifica dell'ambiente virtuale</a>
======================================================================================


È importante verificare che tutto sia installato correttamente. Avviare il sito
Web premendo **F5**. Verranno avviati il server di sviluppo Flask
e il Web browser. Dovrebbe essere visualizzata la pagina seguente:

![Alt text](./media/documentdb-python-application/image12.png)

<a name="_Toc395888523"></a><a name="_Toc395809332">Aggiungere DocumentDB al progetto</a>
=========================================================================================

L'SDK Python di DocumentDB è ospitato in PyPi e può essere installato con
pip.

Espandere il nodo Ambienti Python in Esplora soluzioni, fare clic con il pulsante destro del mouse sul
proprio ambiente e selezionare "Installa pacchetto Python"

![Alt text](./media/documentdb-python-application/image13.png)

Digitare "--pre pydocumentdb" che è il nome del pacchetto PyPi. È facoltativamente
possibile fornire una versione conosciuta se si vuole controllare la versione
da installare, tuttavia se non si include la versione, si sarà
certi che verrà install'ata la versione stabile più recente. Notare che sarà necessario immettere per
intero il nome "--pre pydocumentdb".

![Alt text](./media/documentdb-python-application/image14.png)

Verrà scaricato e installato il pacchetto pydocumentdb
nell'ambiente e al termine dell'operazione pydocumentdb dovrebbe essere elencato come
modulo nel proprio ambiente.

</h1>
<a name="_Toc395888524"></a><a name="_Toc395809333">Creare database, raccolta e documento - definizione</a>
============================================================================================================

Aggiungere il codice seguente a un file python (**forms.py**) e aggiungere quest'ultimo alla cartella
denominata tutorial in Esplora soluzioni. Aggiungere il codice seguente a
forms.py per procedere alla creazione dell'applicazione di voto. Vengono creati
tre campi booleani che verranno attivati o disattivati in base all'input dell'utente.

</h1>

    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

<a name="_Toc395888520"></a>
============================

### <a name="_Toc395809338">Create database, raccolta e documento</a>

\#Nota: è considerato più sicuro conservare le proprie credenziali di autenticazione
in un file config anziché nell'applicazione. Per semplicità, in questa esercitazione verranno
inserite nel codice sorgente. Creare una nuova funzione in views.py e denominarla
"create". Aggiungere il codice seguente a **views.py**, Non eliminare alcuna parte
del codice esistente.

</h1>

    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

<a name="_Toc395888529"></a><a name="_Toc395809338">Accettare i voti degli utenti e aggiornare il documento</a>
=================================================================================================

### <a name="_Toc395809338">Aggiungere le importazioni richieste

<a name="_Toc395888529"></a>
============================

Aggiungere le istruzioni import seguenti all'inizio del file **views.py**. Queste
importeranno i pacchetti PythonSDK e Flask di DocumentDB.


    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">Leggere database, raccolta e documento</a>

Aggiungere il codice seguente al file **views.py**. Questa operazione consente di impostare
il modulo e di leggere database, raccolta e documento. Non eliminare
alcuna parte del codice esistente in views.py. Limitarsi ad aggiungere questo codice alla fine.

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document
    
### <a name="_Toc395809338">Registering the vote and modifying the document</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">Visualizzare i risultati</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')


### <a name="_Toc395809338">Creare i file HTML</a>

Nella cartella dei modelli aggiungere i seguenti file html.
create.html, results.html, vote.html

Aggiungere il codice seguente al file **create.html**. Questa operazione consente di visualizzare
il messaggio che comunica l'avvenuta creazione di un nuovo database, di una raccolta e di un documento.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

Aggiungere il codice seguente a **results.html**. Questa operazione consente di visualizzare
i risultati del sondaggio.
    
    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

Aggiungere il codice seguente a **vote.html**. Questa operazione consente di visualizzare il
sondaggio e accettare i voti. Alla registrazione dei voti il controllo
viene passato a views.py che riconoscerà il voto espresso e ne effettuerà
l'aggiunta al documento.

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}
    
<a name="_Toc395888529"></a>
============================

Eliminare il codice in **layout.html** e sostituirlo con quello seguente. Questa
operazione consente di impostare la barra di navigazione e i rispettivi URL per il routing

Eliminare il codice in index.htm e sostituirlo con quello seguente. Questo
fungerà da pagina di destinazione per l'applicazione

</h1>
<a name="_Toc395888532"></a><a name="_Toc395809341">Aggiungere un file di configurazione e modificare \_\_init\_\_.py</a>.
----------------------------------------------------------------------------------------------------------------

Fare clic sull'esercitazione relativa al nome di progetto e aggiungere un file - **config.py**.
Questo file config è richiesto per i moduli in Flask. È possibile usarla anche per fornire una
chiave privata. Aggiungere il codice seguente al file config.py
    
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

Aggiungere in modo analogo il **file \_\_init\_\_.py**. Individuarlo nella
cartella denominata tutorial. Sostituire il codice originale con il codice riportato di seguito. Questo consente
di stabilire la connessione tra le visualizzazioni e il file config.py.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Dopo avere eseguito i passaggi illustrati sopra, l'aspetto previsto di Esplora
soluzioni sarà simile al seguente

![Alt text](./media/documentdb-python-application/image15.png)

<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Eseguire l'applicazione in locale</a>
============================

Premere F5 o il pulsante Esegui in Visual Studio. Sullo schermo
verrà visualizzato quanto segue.

![Alt text](./media/documentdb-python-application/image16.png)

Fare clic su Vote e selezionare un'opzione.

![Alt text](./media/documentdb-python-application/image17.png)

Ogni voto espresso andrà a incrementare il contatore appropriato. Quando
si voterà la prossima volta, sarà possibile visualizzare i risultati.

![Alt text](./media/documentdb-python-application/image18.png)

</h1>
<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Distribuire l'applicazione in Siti Web di Azure</a>
========================================================================================================================

Ora che l'applicazione completa funziona correttamente con
DocumentDB, sarà possibile distribuirla in Siti Web di Azure. Fare clic con il pulsante destro del mouse sul
progetto in Esplora soluzioni (assicurarsi che il progetto non sia ancora in esecuzione
localmente) quindi selezionare Pubblica.

![Alt text](./media/documentdb-python-application/image19.png)


Configurare il sito Web di Azure fornendo le credenziali e creando
un nuovo sito Web o riutilizzandone uno esistente.

![Alt text](./media/documentdb-python-application/image20.png)


Dopo alcuni secondi, Visual Studio completerà la pubblicazione dell'applicazione
Web e avvierà un browser in cui sarà possibile ammirare il proprio lavoro
in esecuzione in Azure.

</h1>
<a name="_Toc395809338">Conclusioni</a>
==========

Congratulazioni. Si è creata la prima applicazione Python
usando Azure DocumentDB e la si è pubblicata in Siti Web di Azure.

Per ottenere la soluzione completa, [fare clic qui][]. Nota: Sarà comunque
necessario aggiungere l'ambiente virtuale, installare gli strumenti Python e i pacchetti
menzionati in precedenza

</h1>

  [fare clic qui]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
  [Fare clic qui per accedere alle esercitazioni per Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [qui]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Installazione guidata piattaforma Web Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx

            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document
    
### <a name="_Toc395809338">Registering the vote and modifying the document</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">Displaying the results</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')


### <a name="_Toc395809338">Create the html files</a>

Under the templates folder add the following html files to it.
create.html, results.html, vote.html

Add the following code to **create.html**. It takes care of displaying
the message that we created a new database, collection and document.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

Add the following code to **results.html**. It takes care of displaying
the results of the poll.
    
    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

Add the following code to **vote.html**. It takes care of displaying the
poll and accepting the votes. On registering the votes the control is
passed over to views.py where we will recognize the vote casted and
append the document accordingly.

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}
    
<a name="_Toc395888529"></a>
============================

Delete the code in **layout.html** and replace it with the following. It
takes care of setting up navbar and the respective urls for routing

Delete the code in index.html and replace it with the following. This
serves as the landing page for your application

</h1>
<a name="_Toc395888532"></a><a name="_Toc395809341">Add a configuration file and change the \_\_init\_\_.py</a>.
----------------------------------------------------------------------------------------------------------------

Right click on the project name tutorial and add a file - **config.py**.
This config is required by forms in flask. You may use it to provide a
secret key as well. Add the following code to config.py
    
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

Similarly append the **\_\_init\_\_.py file**. Locate it under the
folder called tutorial. Replace the original code with the following. It
takes care of the connection between the views and the config.py file.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

After following the above mentioned steps, this is how your solution
explorer should look like

![Alt text](./media/documentdb-python-application/image15.png)

<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Run your application locally</a>
============================

Hit F5 or the run button in visual studio and you should see the
following on your screen.

![Alt text](./media/documentdb-python-application/image16.png)

Click on vote and select your option

![Alt text](./media/documentdb-python-application/image17.png)

For every vote you cast it will increment the appropriate counter. When
you vote the next time you will be able to see the results.

![Alt text](./media/documentdb-python-application/image18.png)

</h1>
<a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Deploy application to Azure Websites</a>
========================================================================================================================

Now that you have the complete application working correctly against
DocumentDB we're going to deploy this to Azure Websites. Right Click on
the Project in Solution Explorer (make sure you're not still running it
locally) and select Publish.

![Alt text](./media/documentdb-python-application/image19.png)


Configure your Azure Website by providing your credentials and creating
a new website/reusing an old website.

![Alt text](./media/documentdb-python-application/image20.png)


In a few seconds, Visual Studio will finish publishing your web
application and launch a browser where you can see your handy work
running in Azure!

</h1>
<a name="_Toc395809338">Conclusion</a>
==========

Congratulations! You have just built your first Python Application using
Azure DocumentDB and published it to Azure Websites.

If you would like the complete solution, [click here][]. (Note: You will
still have to add the virtual environment, install the python tools and
packages as mentioned above)

</h1>

  [click here]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
  [Click here to access Flask tutorials]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [here]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
