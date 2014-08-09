<properties linkid="dev-nodejs-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Node.js) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Blob Service from Node.js" authors="" solutions="" manager="" editor="" />

Come utilizzare il servizio BLOB da Node.js
===========================================

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio BLOB di Azure. Gli esempi sono scritti utilizzando l'API Node.js. Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB. Per ulteriori informazioni sui BLOB, vedere la sezione [Passaggi successivi](#next-steps).

Sommario
--------

-   [Informazioni sul servizio BLOB](#what-is)
-   [Concetti](#concepts)
-   [Creazione di un account di archiviazione di Azure](#create-account)
-   [Creazione di un'applicazione Node.js](#create-app)
-   [Configurazione dell'applicazione per l'accesso all'archiviazione](#configure-access)
-   [Configurazione di una stringa di connessione di archiviazione di Azure](#setup-connection-string)
-   [Procedura: Creare un contenitore](#create-container)
-   [Procedura: Caricare un BLOB in un contenitore](#upload-blob)
-   [Procedura: Elencare i BLOB in un contenitore](#list-blob)
-   [Procedura: Scaricare BLOB](#download-blobs)
-   [Procedura: Eliminare un BLOB](#delete-blobs)
-   [Passaggi successivi](#next-steps)

Informazioni sul servizio BLOB
------------------------------

L'archiviazione BLOB di Azure è un servizio per l'archiviazione di quantità elevate di dati non strutturati a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Un singolo BLOB può consistere in centinaia di gigabyte di dimensione e un singolo account di archiviazione può contenere fino a 100 TB di BLOB. Quelli di seguito sono gli impieghi più comuni dei BLOB:

-   Invio di immagini o documenti direttamente in un browser
-   Archiviazione di file per l'accesso distribuito
-   Flussi audio e video
-   Esecuzione di backup e ripristini di emergenza sicuri
-   Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure

L'archiviazione BLOB può essere utilizzata per esporre dati pubblicamente a livello mondiale o privatamente a scopo di archiviazione interna per le applicazioni.

Concetti
--------

Il servizio BLOB è composto dai componenti seguenti:

![BLOB1](./media/storage-nodejs-how-to-use-blob-storage/blob1.jpg)

-   **Formato dell'URL:** i BLOB sono indirizzabili utilizzando il formato dell'URL seguente:

        http://storageaccount.blob.core.windows.net/container/blob  

    L'URL seguente fa riferimento a uno dei BLOB nel diagramma:

        http://sally.blob.core.windows.net/movies/MOV1.AVI

-   **Account di archiviazione:** l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Questo è il livello più alto dello spazio dei nomi per accedere ai BLOB. Un account può contenere un numero illimitato di contenitori, purché la dimensione totale di questi sia inferiore a 100 TB.

-   **Contenitore:** un contenitore è il raggruppamento di un set di BLOB. Tutti i BLOB devono trovarsi in un contenitore. In un account può esistere un numero illimitato di contenitori. In un contenitore può essere archiviato un numero illimitato di BLOB.

-   **BLOB:** file di qualsiasi tipo e dimensione. Esistono due tipi di BLOB: in blocchi e di pagine. La maggior parte dei file sono BLOB in blocchi. Un singolo BLOB in blocchi può raggiungere fino a 200 GB di dimensione. In questa esercitazione vengono utilizzati BLOB in blocchi. I BLOB dell'altro tipo, di pagine, possono raggiungere dimensioni fino a 1 TB e risultano più efficienti quando vi sono intervalli di byte all'interno del file soggetti a modifiche frequenti.

Creazione di un account di archiviazione di Azure
-------------------------------------------------

Per effettuare operazioni di archiviazione, è necessario un account di archiviazione di Azure. Per creare un account di archiviazione, attenersi alla procedura riportata di seguito (è possibile creare un account di archiviazione anche [utilizzando l'API REST](http://msdn.microsoft.com/it-it/library/windowsazure/hh264518.aspx)).

1.  Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2.  Nella parte inferiore del pannello di navigazione fare clic su **+NEW**.

    ![+new](./media/storage-nodejs-how-to-use-blob-storage/plus-new.png)

3.  Fare clic su **Storage Account** e quindi su **Quick Create**.

    ![Finestra di dialogo Creazione rapida](./media/storage-nodejs-how-to-use-blob-storage/quick-storage.png)

4.  Nel campo URL, digitare un nome di sottodominio da utilizzare nell'URI per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo valore diventa il nome host all'interno dell'URI utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

5.  Scegliere una regione o un gruppo di affinità in cui situare l'archiviazione. Se si utilizzerà l'archiviazione dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

6.  Fare clic su **Create Storage Account**.

Creazione di un'applicazione Node.js
------------------------------------

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Servizio cloud Node.js]({localLink:2221} "Applicazione Web Node.js") (utilizzando Windows PowerShell) o [Sito Web con WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

Configurazione dell'applicazione per l'accesso all'archiviazione
----------------------------------------------------------------

Per utilizzare l'archiviazione di Azure, è necessario scaricare e utilizzare il pacchetto Node.js Azure, che comprende un set di librerie che comunicano con i servizi di archiviazione REST.

### Utilizzare Node Package Manager (NPM) per ottenere il pacchetto

1.  Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure** nella finestra di comando, che dovrebbe restituire il seguente output:

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. All'interno di tale cartella individuare il pacchetto **azure**, che contiene le librerie necessarie per accedere all'archiviazione.

### Importare il pacchetto

Utilizzando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file **server.js** dell'applicazione dove si intende utilizzare l'archiviazione:

    var azure = require('azure');

Configurazione di una connessione di archiviazione di Azure
-----------------------------------------------------------

Il modulo di Azure leggerà le variabili di ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **createBlobService**.

Per un esempio di impostazione delle variabili di ambiente in un file di configurazione per un servizio cloud di Azure, vedere [Servizio cloud Node.js con archiviazione](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con archiviazione](/en-us/develop/nodejs/tutorials/web-site-with-storage/)

Procedura: Creare un contenitore
--------------------------------

L'oggetto **BlobService** consente di lavorare con contenitori e BLOB. Il codice seguente consente di creare un oggetto **BlobService**. Aggiungere il codice seguente nella parte superiore di **server.js**:

    var blobService = azure.createBlobService();

Tutti i BLOB risiedono in un contenitore. La chiamata a **createContainerIfNotExists** sull'oggetto **BlobService** restituirà il contenitore specificato se esistente o ne creerà uno nuovo con il nome specificato, se non esiste ancora. Per impostazione predefinita, il nuovo contenitore è privato ed è necessario utilizzare la chiave di accesso per scaricare BLOB da questo contenitore.

    blobService.createContainerIfNotExists(containerName, function(error){
        if(!error){
            // Container exists and is private
        }
    });

Se si desidera rendere pubblici i file nel contenitore affinché siano accessibili senza richiedere la chiave di accesso, è possibile impostare il livello di accesso del contenitore su **blob** o **container**. L'impostazione del livello di accesso su **blob** consente l'accesso anonimo in lettura al contenuto e ai metadati del BLOB all'interno di quel contenitore, ma non ai metadati del contenitore, come ad esempio l'elenco di tutti i BLOB all'interno di un contenitore. L'impostazione del livello di accesso su **container** consente l'accesso anonimo in lettura al contenuto e ai metadati del BLOB nonché ai metadati del contenitore. Nell'esempio seguente viene illustrata l'impostazione del livello di accesso su **blob**:

    blobService.createContainerIfNotExists(containerName
        , {publicAccessLevel : 'blob'}
        , function(error){
            if(!error){
                // Container exists and is public
            }
        });

In alternativa, è possibile modificare il livello di accesso di un contenitore utilizzando **setContainerAcl** per specificare il livello di accesso. Nell'esempio seguente viene illustrata la modifica del livello di accesso al contenitore:

    blobService.setContainerAcl(containerName
        , 'container'
        , function(error){
            if(!error){
                // Container access level set to 'container'
            }
        });

### Filtri

Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite utilizzando **BlobService**. Le operazioni di filtro possono includere registrazione, ripetizione automatica di tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

     function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

     function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

Sono inclusi due filtri che implementano la logica di ripetizione dei tentativi con Azure SDK per Node.js: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **BlobService** che utilizza **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobService = azure.createBlobService().withFilter(retryOperations);

Procedura: Caricare un BLOB in un contenitore
---------------------------------------------

Per caricare dati in un BLOB, utilizzare i metodi **createBlockBlobFromFile**, **createBlockBlobFromStream** o **createBlockBlobFromText**. **createBlockBlobFromFile** consente di caricare il contenuto di un file, mentre **createBlockBlobFromStream** consente di caricare i contenuti di un flusso. **createBlockBlobFromText** consente di caricare il valore del testo specificato.

Nell'esempio seguente viene caricato il contenuto del file **test1.txt** nel BLOB "test1".

    blobService.createBlockBlobFromFile(containerName
        , 'test1'
        , 'test1.txt'
        , function(error){
            if(!error){
                // File has been uploaded
            }
        });

Procedura: Elencare i BLOB in un contenitore
--------------------------------------------

Per elencare i BLOB in un contenitore, utilizzare il metodo **listBlobs** con un ciclo **for** per visualizzare il nome di ogni BLOB nel contenitore. Il codice seguente consente di inviare alla console il valore **name** di ogni BLOB in un contenitore.

    blobService.listBlobs(containerName, function(error, blobs){
        if(!error){
            for(var index in blobs){
                console.log(blobs[index].name);
            }
        }
    });

Procedura: Scaricare BLOB
-------------------------

Per scaricare dati da un blob, utilizzare **getBlobToFile**, **getBlobToStream** o **getBlobToText**. Nell'esempio seguente viene illustrato l'utilizzo di **getBlobToStream** per scaricare il contenuto del BLOB **test1** e archiviarlo nel file **output.txt** utilizzando un flusso:

    var fs=require('fs');
    blobService.getBlobToStream(containerName
        , 'test1'
        , fs.createWriteStream('output.txt')
        , function(error){
            if(!error){
                // Wrote blob to stream
            }
        });

Procedura: Eliminare un BLOB
----------------------------

Per eliminare un BLOB, infine, chiamare **deleteBlob**. Nell'esempio seguente viene eliminato il BLOB denominato "blob1".

    blobService.deleteBlob(containerName
        , 'blob1'
        , function(error){
            if(!error){
                // Blob has been deleted
            }
        });

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx)
-   [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   Archivio [Azure SDK for Node](https://github.com/WindowsAzure/azure-sdk-for-node) su GitHub

