<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="Web App with Storage" pageTitle="Web app with table storage (Node.js) | Microsoft Azure" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="A tutorial that builds on the Web App with Express tutorial by adding Azure Storage services and the Azure module." metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="Node.js Web Application using Storage" authors="" solutions="" manager="" editor="" />

Creazione di un'applicazione Web Node.js con Archiviazione
==========================================================

In questa esercitazione, si estenderà l'applicazione creata nell'esercitazione [Creazione di un'applicazione Node.js con Express](http://www.windowsazure.com/en-us/develop/nodejs/tutorials/web-app-with-express/) utilizzando le librerie client di Azure per Node.js per lavorare con i servizi di gestione dati. L'applicazione verrà estesa in modo da creare un'applicazione elenco di attività basata sul Web che sarà possibile distribuire in Azure. L'elenco di attività consente a un utente di recuperare le attività, aggiungerne di nuove e contrassegnarle come completate.

Gli elementi attività vengono archiviati in Archiviazione di Azure. Archiviazione di Azure consente l'archiviazione di dati non strutturati, a tolleranza di errore e a disponibilità elevata. Archiviazione di Azure include diverse strutture di dati in cui è possibile archiviare i dati e accedervi ed è possibile utilizzare i servizi di archiviazione dalle API incluse in Azure SDK per Node.js o tramite le API REST. Per ulteriori informazioni, vedere [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).

In questa esercitazione si presume che siano state completate le esercitazioni [Creazione e distribuzione di un'applicazione Node.js a un Servizio cloud di Azure](http://www.windowsazure.com/en-us/develop/nodejs/tutorials/getting-started/) e [Creazione di un'applicazione Web Node.js utilizzando Express in un servizio cloud di Azure](http://www.windowsazure.com/en-us/develop/nodejs/tutorials/web-app-with-express/).

Si apprenderà come:

-   Utilizzare il motore dei modelli Jade
-   Utilizzare i servizi di gestione dati di Azure

Di seguito è riportata una schermata dell'applicazione completata:

![Pagina Web completata in Internet Explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

Impostazione delle credenziali di archiviazione in Web.Config
-------------------------------------------------------------

Per accedere ad Archiviazione di Azure, è necessario passare le credenziali di archiviazione. A questo scopo, si utilizzano le impostazioni dell'applicazione web.config. Tali impostazioni verranno passate a Node come variabili di ambiente, che verranno quindi lette da Azure SDK.

**Nota**

Le credenziali di archiviazione vengono utilizzate solo quando l'applicazione viene distribuita in Azure. L'applicazione, quando viene eseguita nell'emulatore, utilizzerà l'emulatore di archiviazione.

Eseguire i passaggi seguenti per recuperare le credenziali dell'account di archiviazione e aggiungerle alle impostazioni di web.config:

1.  Se non è già aperta, avviare Azure PowerShell dal menu **Start** espandendo **Tutti i programmi, Azure**, fare clic con il pulsante destro del mouse su **Azure PowerShell** e quindi scegliere **Esegui come amministratore**.

2.  Sostituire le directory con la cartella contenente l'applicazione. Ad esempio, C:\\node\\tasklist\\WebRole1.

3.  Nella finestra Azure Powershell immettere il cmdlet seguente per recuperare le informazioni account di archiviazione:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    Verrà recuperato l'elenco di account di archiviazione e di chiavi dell'account associati al servizio ospitato.

    **Nota**

    Dal momento che Azure SDK crea un account di archiviazione quando si distribuisce un servizio, esisterà già un account di archiviazione dalla distribuzione dell'applicazione nelle guide precedenti.

4.  Aprire il file web.cloud.config contenente le impostazioni dell'ambiente utilizzate quando l'applicazione viene distribuita in Azure:

        PS C:\node\tasklist\WebRole1> notepad web.cloud.config

5.  Inserire il blocco seguente sotto l'elemento **configuration**, sostituendo {STORAGE ACCOUNT} e {STORAGE ACCESS KEY} con il nome account e con la chiave primaria per l'account di archiviazione che si desidera utilizzare per la distribuzione:

        <appSettings>
          <add key="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}"/>
          <add key="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}"/>
        </appSettings>

    ![Contenuto file web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Salvare il file e chiudere Blocco note.

Installare i moduli
-------------------

Per utilizzare i servizi di gestione dati di Azure, è necessario installare il modulo Azure per il nodo. È inoltre necessario installare il modulo node-uuid, che verrà utilizzato per generare gli identificatori univoci universali (UUID). Per installare questi moduli, immettere il comando seguente:

    PS C:\node\tasklist\WebRole1> npm install node-uuid azure

Al termine dell'esecuzione del comando, i moduli risulteranno aggiunti alla cartella **node\_modules**. Eseguire i passaggi seguenti per utilizzare questi moduli nell'applicazione:

1.  Aprire il file server.js:

        PS C:\node\tasklist\WebRole1> notepad server.js

2.  Aggiungere il codice seguente dopo la riga che termina con express.createServer() per includere i moduli node-uuid, home e azure. Il modulo home non esiste ancora, ma verrà creato a breve.

    ![Codice di server.js code con la riga line app = modules.exports evidenziata](./media/storage-nodejs-use-table-storage-cloud-service-app/node38.png)

        var uuid = require('node-uuid');
        var Home = require('./home');
        var azure = require('azure');

3.  Aggiungere il codice per creare un client tabelle di archiviazione che passa le informazioni sugli account di archiviazione e sulle chiavi di accesso.

    **Nota**

    L'SDK, quando viene eseguito nell'emulatore, utilizzerà automaticamente l'emulatore anche se le informazioni sugli account di archiviazione sono state fornite tramite web.config.

        var client = azure.createTableService();

4.  Creare quindi in Archiviazione di Azure una tabella denominata tasks. La logica seguente crea una nuova tabella, se non esiste, e la popola con alcuni dati predefiniti.

        //table creation
        client.createTableIfNotExists('tasks', function(error){
            if(error){
                throw error;
            }

            var item = {
                name: 'Add readonly task list',
                category: 'Site work',
                date: '12/01/2011',
                RowKey: uuid(),
                PartitionKey: 'partition1',
                completed: false
            };

            client.insertEntity('tasks', item, function(){});

        });

5.  Sostituire il codice esistente nella sezione route con il codice seguente, che crea un'istanza del controller home a cui instrada tutte le richieste per **/** o **/home**.

    ![File server.js con la sezione routes evidenziata.](./media/storage-nodejs-use-table-storage-cloud-service-app/node39.png)

        var home = new Home(client);
        app.get('/', home.showItems.bind(home));
        app.get('/home', home.showItems.bind(home));

    Si noti che, invece di gestire la richiesta inline, si sta ora delegando il comando a un oggetto Home. Il comando **bind** è necessario per garantire che questi riferimenti vengano risolti correttamente in locale all'interno del controller home.

Creazione del controller home
-----------------------------

È ora necessario creare un controller home, che gestisce tutte le richieste per il sito dell'elenco di attività. Procedere come descritto di seguito per creare il controller:

1.  Creare un nuovo file home.js file nel Blocco note. Questo file conterrà il codice del controller che gestisce la logica per l'elenco di attività.

        PS C:\node\tasklist\WebRole1> notepad home.js

2.  Sostituire i contenuti con il codice seguente e salvare il file. Il codice seguente utilizza il pattern del modulo JavaScript. Esporta una funzione Home. Il prototipo Home contiene le funzioni per gestire le richieste effettive.

        var azure=require('azure');
        module.exports = Home;

        function Home (client) {
            this.client = client;
            };
            
            Home.prototype = {
            showItems: function (req, res) {
                var self = this;
                this.getItems(false, function (resp, tasklist) {
                    if (!tasklist) {
                        tasklist = [];
                        }           
                        self.showResults(res, tasklist);
                    });
                },
            
                getItems: function (allItems, callback) {
                var query = azure.TableQuery
                    .select()
                    .from('tasks');
                
                if (!allItems) {
                    query = query.where('completed eq 
            ', 'false');
                    }
                    this.client.queryEntities(query, callback);
                 },
            
                 showResults: function (res, tasklist) {
                res.render('home', { 
                    title: 'Todo list', 
                    layout: false, 
                    tasklist: tasklist });
                 },
            };

    Il controller home include ora tre funzioni:

    -   *showItems* gestisce la richiesta.
    -   *getItems* utilizza il client tabelle per recuperare gli elementi attività aperte per la tabella tasks. Si noti che alla query possono essere applicati ulteriori filtri. I filtri della query precedente, ad esempio, mostrano solo le attività in cui completed è uguale a false.
    -   *showResults* chiama la funzione di rendering di Express per eseguire il rendering della pagina utilizzando la visualizzazione home che si creerà nella sezione successiva.

### Modifica della visualizzazione Home

Il motore dei modelli Jade utilizza una sintassi di markup meno dettagliata di quella HTML ed è il motore predefinito per utilizzare Express. Eseguire i passaggi seguenti per creare una visualizzazione che consenta di visualizzare gli elementi dell'elenco di attività:

1.  Nella finestra di comando di Windows PowerShell modificare il file home.jade utilizzando il comando seguente:

        PS C:\node\tasklist\WebRole1\views> notepad home.jade

2.  Sostituire i contenuti del file home.jade con il codice seguente e salvare il file. Il form seguente contiene la funzionalità per leggere e aggiornare gli elementi attività. Si noti che attualmente il controller home supporta solo la lettura. Questa impostazione verrà modificata in seguito. Il form contiene i dettagli per ogni elemento dell'elenco di attività.

        html
        head
            title Index
        body
            h1 My ToDo List

            form
                table(border="1")
                    tr
                        td Name
                        td Category
                        td Date
                        td Complete

                        each item in tasklist
                            tr
                                td #{item.name}
                                td #{item.category} 
                                td #{item.date} 
                                td 
                                    input(type="checkbox", name="completed", value="#{item.RowKey}") 

Esecuzione dell'applicazione nell'emulatore di calcolo
------------------------------------------------------

1.  Nella finestra di Windows PowerShell immettere il cmdlet seguente per avviare il servizio nell'emulatore di calcolo e visualizzare una pagina Web che chiama il servizio.

        PS C:\node\tasklist\WebRole1> Start-AzureEmulator -launch

    Nel browser viene visualizzata la pagina seguente, che mostra l'elemento attività recuperato da Archiviazione di Azure:

    ![In Internet Explorer viene visualizzata una pagina My Tasklist con un elemento in una tabella.](./media/storage-nodejs-use-table-storage-cloud-service-app/node40.png)

Aggiunta della funzionalità per le nuove attività
-------------------------------------------------

In questa sezione l'applicazione viene aggiornata per supportare l'aggiunta di nuovi elementi attività.

### Aggiunta di una nuova route a Server.js

Nel file server.js aggiungere la riga seguente dopo la voce dell'ultima route per **/home** e quindi salvare il file.

![File server.js con la riga contenente la route per home evidenziata.](./media/storage-nodejs-use-table-storage-cloud-service-app/node41.png)

        app.post('/home/newitem', home.newItem.bind(home));

    La sezione routes sarà ora come indicato di seguito:

       // Routes

       var home = new Home(client);
       app.get('/', home.showItems.bind(home));
       app.get('/home', home.showItems.bind(home));
       app.post('/home/newitem', home.newItem.bind(home));

### Aggiunta del modulo Node-UUID

Per utilizzare il modulo node-uuid per creare un identificatore univoco, aggiungere la riga seguente nella parte superiore del file home.js dopo la prima riga in cui il modulo viene esportato.

![File home.js con la riga module.exports = Home evidenziata.](./media/storage-nodejs-use-table-storage-cloud-service-app/node42.png)

       var uuid = require('node-uuid');

### Aggiunta della funzione per un nuovo elemento al controller home

Per implementare la funzionalità per un nuovo elemento, creare una funzione **newItem**. Nel file home.js incollare il codice seguente dopo l'ultima funzione e quindi salvare il file.

![Funzione showresults evidenziata](./media/storage-nodejs-use-table-storage-cloud-service-app/node43.png)

       newItem: function (req, res) {
           var self = this;
           var createItem = function (resp, tasklist) {
               if (!tasklist) {
                   tasklist = [];
               }

               var count = tasklist.length;

               var item = req.body.item;
               item.RowKey = uuid();
               item.PartitionKey = 'partition1';
               item.completed = false;

               self.client.insertEntity('tasks', item, function (error) {
                   if(error){  
                       throw error;
                   }
                   self.showItems(req, res);
               });
           };

           this.getItems(true, createItem);
       },

La funzione **newItem** esegue le attività seguenti:

-   Estrae l'elemento l'elemento inviato dal corpo.
-   Imposta i valori **RowKey** e **PartitionKey** per il nuovo elemento. Questi valori sono necessari per inserire l'elemento nella tabella di Azure. Per il valore **RowKey** viene generato uno UUID.
-   Inserisce l'elemento nella tabella tasks chiamando la funzione **insertEntity**.
-   Esegue il rendering della pagina chiamando la funzione **getItems**.

### Aggiunta del form per un nuovo elemento alla visualizzazione Home

Aggiornare ora la visualizzazione aggiungendo un nuovo form per consentire all'utente di aggiungere un elemento. Nel file home.jade incollare il codice seguente alla fine del file e salvare.

**Nota**

Poiché in Jade gli spazi vuoti sono significativi, non rimuovere nessuna delle spaziature sotto.

        hr
        form(action="/home/newitem", method="post")
            table(border="1")    
                tr
                    td Item Name: 
                    td 
                        input(name="item[name]", type="textbox")
                tr
                    td Item Category: 
                    td 
                        input(name="item[category]", type="textbox")
                tr
                    td Item Date: 
                    td 
                        input(name="item[date]", type="textbox")
            input(type="submit", value="Add item")

### Esecuzione dell'applicazione nell'emulatore

1.  Poiché l'emulatore di Azure è già in esecuzione, è possibile sfogliare l'applicazione aggiornata:

        PS C:\node\tasklist\WebRole1> start http://localhost:81/home

    Il browser si apre e visualizza la pagina seguente:

    ![Pagina Web intitolata My Task List con una tabella contenente le attività e i campi per aggiungere una nuova attività.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

2.  Per **Item Name:** immettere "New task functionality", per **Item Category:** "Site work"? e per **Item Date:** "12/02/2011". Fare quindi clic su **Add item**.

    L'elemento viene aggiunto alla tabella tasks in Archiviazione di Azure e visualizzato come nella schermata seguente.

    ![Pagina Web intitolata My Task List con una tabella contenente le attività, dopo aver aggiunto un attività all'elenco.](./media/storage-nodejs-use-table-storage-cloud-service-app/node45.png)

Nuova pubblicazione dell'applicazione in Azure
----------------------------------------------

Ora che l'applicazione è stata completata, pubblicarla in Azure aggiornando la distribuzione nel servizio ospitato esistente.

1.  Nella finestra di Windows PowerShell chiamare il cmdlet seguente per ridistribuire il servizio ospitato in Azure. Le impostazioni e il percorso di archiviazione sono stati salvati in precedenza e pertanto non è necessario immetterli nuovamente.

        PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

    Al termine della distribuzione, verrà visualizzata una risposta analoga alla seguente:

    ![Messaggio di stato visualizzato durante la distribuzione.](./media/storage-nodejs-use-table-storage-cloud-service-app/node35.png)

    Come in precedenza, poiché è stata specificata l'opzione **-launch**, il browser si apre e visualizza l'applicazione in esecuzione in Azure al termine della pubblicazione.

    ![Finestra del browser in cui è visualizzata la pagina My Task List. L'URL indica che la pagina è ora ospitata su Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/node47.png)

Arrestare ed eliminare l'applicazione
-------------------------------------

Dopo aver distribuito l'applicazione, se necessario è possibile disabilitarla per evitare costi o creare e distribuire altre applicazioni entro il termine del periodo di valutazione gratuita.

Azure addebita le istanze del ruolo Web al consumo, in base all'utilizzo di tempo del server su base oraria. Un'applicazione distribuita utilizza tempo del server anche se le istanze non sono in esecuzione e sono in stato arrestato.

Nella procedura seguente viene illustrato come arrestare ed eliminare l'applicazione.

1.  Nella finestra di Windows PowerShell arrestare la distribuzione del servizio creato nella sezione precedente con il cmdlet seguente:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    L'arresto del servizio può richiedere diversi minuti. Dopo l'arresto del servizio, viene visualizzato un messaggio di conferma dell'arresto.

    ![Messaggi di stato indicanti che il servizio è stato arrestato.](./media/storage-nodejs-use-table-storage-cloud-service-app/node48.png)

2.  Per eliminare il servizio, chiamare il cmdlet seguente:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    Quando richiesto, immettere **Y** per eliminare il servizio.

    L'eliminazione del servizio può richiedere diversi minuti. Al termine dell'eliminazione del servizio, verrà visualizzato un messaggio di conferma dell'eliminazione.

    ![Messaggi di stato indicanti che il servizio è stato eliminato.](./media/storage-nodejs-use-table-storage-cloud-service-app/node49.png)


