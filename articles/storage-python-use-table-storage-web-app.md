<properties linkid="develop-python-web-app-with-blob-storage" urlDisplayName="Web App with Blob Storage" pageTitle="Python web app with table storage | Microsoft Azure" metaKeywords="Azure table storage Python, Azure Python application, Azure Python tutorial, Azure Python example" description="A tutorial that teaches you how to create a Python web application using the Azure Client Libraries. Django is used as the web framework." metaCanonical="" services="storage" documentationCenter="Python" title="Python Web Application using Table Storage" authors="" solutions="" videoId="" scriptId="" manager="" editor="mollybos" />

Applicazione Web Python con Archiviazione tabelle
======================================

In questa esercitazione si apprenderà come creare un'applicazione che utilizza Archiviazione tabelle con le librerie client di Azure per Python. Se questa è la prima applicazione Azure per Python è consigliabile consultare prima l'articolo [Applicazione Web Hello World Django](http://windowsazure.com/it-it/documentation/articles/virtual-machines-python-django-web-app-windows-server).

Per questa guida verrà creata un'applicazione elenco di attività basata sul Web che sarà possibile distribuire in Azure. L'elenco di attività consente a un utente di recuperare le attività, aggiungerne di nuove e contrassegnarle come completate. Verrà utilizzato Django come framework Web.

Gli elementi attività vengono archiviati in Archiviazione di Azure. Archiviazione di Azure consente l'archiviazione di dati non strutturati, a tolleranza di errore e a disponibilità elevata. Archiviazione di Azure include diverse strutture di dati in cui è possibile archiviare i dati e accedervi ed è possibile utilizzare i servizi di archiviazione dalle API incluse in Azure SDK per Python o tramite le API REST. Per ulteriori informazioni, vedere [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx).

Si apprenderà come:

-   Utilizzare il servizio di archiviazione tabelle di Azure

La schermata dell'applicazione completata avrà un aspetto simile al seguente (le attività aggiunte saranno diverse):

![](./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Configurazione dell'ambiente di sviluppo
------------

**Nota:** se è necessario installare Python o le librerie client, vedere la [guida all'installazione di Python](http://windowsazure.com/it-it/documentation/articles/python-how-to-install).

*Nota per Windows*: se è stato utilizzato il programma di installazione WebPI di Windows sono stati già installati Django e le librerie client.

Creare un account di archiviazione in Azure
-------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Creare un progetto Django
----------------

Di seguito sono descritti i passaggi per la creazione dell'applicazione:

-   Creare un progetto Django predefinito denominato 'TableserviceSample'
-   Dalla riga di comando, passare alla directory in cui si desidera archiviare il codice, quindi eseguire il comando seguente:

        django-admin.py startproject TableserviceSample

-   Aggiungere un nuovo file Python **views.py** al progetto
-   Aggiungere il codice seguente al file **views.py** per importare il supporto Django richiesto:

        from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   Creare una nuova cartella denominata **templates** nella cartella **TableserviceSample/TableserviceSample**.
-   Modificare le impostazioni dell'applicazione in modo che sia possibile individuare i modelli. Aprire il file **settings.py** e aggiunger la voce seguente a INSTALLED\_APPS:

        'TableserviceSample',

-   Aggiungere un nuovo file modello Django **mytasks.html** alla cartella **templates** e aggiungervi il codice seguente:

``` {}
    <html>
    <head><title></title></head>
    <body>
    <h2>My Tasks</h2> <br>
    <table border="1"> 
    <tr>
    <td>Name</td><td>Category</td><td>Date</td><td>Complete</td><td>Action</td></tr>
    {% for entity in entities %}
    <form action="update_task" method="GET">
    <tr><td>{{entity.name}} <input type="hidden" name='name' value="{{entity.name}}"></td>
    <td>{{entity.category}} <input type="hidden" name='category' value="{{entity.category}}"></td>
    <td>{{entity.date}} <input type="hidden" name='date' value="{{entity.date}}"></td>
    <td>{{entity.complete}} <input type="hidden" name='complete' value="{{entity.complete}}"></td>

    <td><input type="submit" value="Complete"></td>
    </tr>
    </form>
    {% endfor %}
    </table>
    <br>
    <hr>
    <table border="1">
    <form action="add_task" method="GET">
    <tr><td>Name:</td><td><input type="text" name="name"></input></td></tr>
    <tr><td>Category:</td><td><input type="text" name="category"></input></td></tr>
    <tr><td>Item Date:</td><td><input type="text" name="date"></input></td></tr>
    <tr><td><input type="submit" value="add task"></input></td></tr>
    </form>
    </table>
    </body>
    </html>    
```

Importazione del modulo di archiviazione di Azure
----------------------------

Aggiungere il codice seguente all'inizio del file **views.py**, poco dopo le importazioni di Django

    from azure.storage import TableService

Individuazione del nome e della chiave dell'account di archiviazione
----------------------------

Aggiungere il codice seguente al file **views.py** poco dopo l'importazione di Azure e sostituire 'youraccount' e 'yourkey' con il nome e la chiave dell'account effettivi. È possibile ottenere un nome e una chiave dal portale di gestione di Azure.

    account_name = 'youraccount'
    account_key = 'yourkey'

Creazione di TableService
-----------------

Aggiungere il codice seguente dopo **account\_name**:

    table_service = TableService(account_name=account_name, account_key=account_key)
    table_service.create_table('mytasks')

Elenco delle attività
--------

Aggiungere la funzione list\_tasks al file **views.py**:

    def list_tasks(request): 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

Aggiunta di un'attività
--------

Aggiungere la funzione add\_task al file **views.py**:

    def add_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

Aggiornamento dello stato dell'attività
------------

Aggiungere la funzione update\_task al file **views.py**:

    def update_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        partition_key = name + category
        row_key = date
        table_service.update_entity('mytasks', partition_key, row_key, {'PartitionKey':partition_key, 'RowKey':row_key, 'name': name, 'category':category, 'date':date, 'complete':'Yes'})
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

Mapping degli URL
--------

A questo punto è necessario eseguire il mapping degli URL nell'applicazione Django. Aprire **urls.py** e aggiungere i mapping seguenti agli urlpattern:

    url(r'^$', 'TableserviceSample.views.list_tasks'),
    url(r'^list_tasks$', 'TableserviceSample.views.list_tasks'),
    url(r'^add_task$', 'TableserviceSample.views.add_task'),
    url(r'^update_task$', 'TableserviceSample.views.update_task'),

Esecuzione dell'applicazione
------------

-   Qualora non sia già stato fatto, passare alla directory **TableserviceSample** ed eseguire il comando:

    python manage.py runserver

-   Inserire nel browser l'indirizzo seguente: `http://127.0.0.1:8000/`. Sostituire 8000 con il numero effettivo della porta.

È ora possibile fare clic su **Add Task** per creare un'attività e quindi fare clic sul pulsante **Complete** per aggiornare l'attività e impostarne lo stato su Yes.

Esecuzione dell'applicazione nell'emulatore di calcolo, pubblicazione e arresto/eliminazione dell'applicazione
-------------------------------------------------------

Dopo aver eseguito correttamente l'applicazione sul server Django incorporato è possibile testarla ulteriormente eseguendone la distribuzione nell'emulatore di Azure (solo Windows) e quindi pubblicandola in Azure. Per istruzioni generali su come procedere, consultare l'articolo [Applicazione Web Hello World Django](http://windowsazure.com/it-it/documentation/articles/virtual-machines-python-django-web-app-windows-server) in cui si discute questa procedura nel dettaglio.

Passaggi successivi
--------

A questo punto, dopo aver appreso le nozioni di base del servizio di archiviazione tabelle di Azure, visitare i collegamenti seguenti per ulteriori informazioni su come eseguire attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx)
-   Blog del team di Archiviazione di Azure: &lt;http://blogs.msdn.com/b/windowsazurestorage/\&gt;

