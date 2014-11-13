<properties urlDisplayName="Image Gallery w/ Storage" pageTitle="Applicazione locale con archiviazione BLOB (Java) | Microsoft Azure" metaKeywords="Azure blob storage, Azure blob Java, Azure blob example, Azure blob tutorial" description="Informazioni su come creare un'applicazione console che carica un'immagine in Azure e quindi visualizza l'immagine nel browser. Esempi di codice in Java." metaCanonical="" services="storage" documentationCenter="Java" title="Applicazione locale con archiviazione BLOB" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Applicazione locale con archiviazione BLOB

L'esempio seguente illustra come usare il servizio Archiviazione di Azure per archiviare
immagini in Azure. Il codice seguente è per un'applicazione
console che carica un'immagine in Azure e quindi crea un file
HTML che visualizza l'immagine nel browser.

## Sommario

-   [Prerequisiti][Prerequisiti]
-   [Per usare l'archivio BLOB di Azure per caricare un file][Per usare l'archivio BLOB di Azure per caricare un file]
-   [Per eliminare un contenitore][Per eliminare un contenitore]

## <a name="bkmk_prerequisites"> </a>Prerequisiti

1.  Java Developer Kit (JDK) v1.6 o versione successiva installato.
2.  Azure SDK installato.
3.  JAR per le librerie di Azure per Java e qualsiasi JAR
    di dipendenza applicabile installati e presenti nel percorso di compilazione usato dal
    compilatore Java. Per informazioni sull'installazione delle librerie di Azure per Java, vedere [Download di
    Azure SDK per Java][Download di
    Azure SDK per Java].
4.  Account di Archiviazione di Azure configurato. Il nome
    account e la chiave dell'account per l'account di archiviazione verranno usati dal codice
    seguente. Vedere [Come creare un account di archiviazione][Come creare un account di archiviazione] per informazioni sulla creazione di un account di archiviazione
    e [Come gestire gli account di archiviazione][Come gestire gli account di archiviazione] per informazioni sul recupero della chiave
    dell'account.
5.  Avere creato un file di immagine locale denominato archiviato nel percorso
    c:\\myimages\\image1.jpg. In alternativa, modificare il costruttore
    **FileInputStream** nell'esempio per usare un percorso e un nome
    file di immagine diversi.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <a name="bkmk_uploadfile"> </a>Per usare l'archivio BLOB di Azure per caricare un file

Di seguito viene presentata una procedura dettagliata. Se si preferisce ignorarla,
l'intero codice viene presentato più avanti in questo argomento.

Includere all'inizio del codice le importazioni per le classi di archiviazione
di base di Azure, le classi client BLOB di Azure, le classi IO Java e la
classe **URISyntaxException**:

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

Dichiarare un classe denominata **StorageSample** e includere la parentesi aperta,
**{**.

    public class StorageSample {

All'interno della classe **StorageSample** dichiarare una variabile di tipo stringa che conterrà
il protocollo endpoint predefinito, il nome account di archiviazione e la
chiave di accesso alle risorse di archiviazione, come specificato nell'account di archiviazione
di Azure. Sostituire i valori segnaposto **your\_account\_name** e
**your\_account\_key** con il proprio nome account e la propria chiave dell'account,
rispettivamente.

    public static final String storageConnectionString = 
           "DefaultEndpointsProtocol=http;" + 
               "AccountName=your_account_name;" + 
               "AccountKey=your_account_name"; 

Aggiungere la dichiarazione per **main**, includere un blocco **try** e includere
le parentesi aperte necessarie, **{**.

    public static void main(String[] args) 
    {
        try
        {

Dichiarare le variabili del tipo seguente (le descrizioni illustrano come
vengono usate in questo esempio):

-   **CloudStorageAccount**: usata per inizializzare l'oggetto account con
    la chiave e il nome account di archiviazione di Azure e per creare
    l'oggetto client BLOB.
-   **CloudBlobClient**: usata per accedere al servizio BLOB.
-   **CloudBlobContainer**: usata per creare un contenitore BLOB, elencare i
    BLOB nel contenitore ed eliminare il contenitore.
-   **CloudBlockBlob**: usata per caricare un file di immagine locale nel
    contenitore.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Assegnare un valore alla variabile **account**.

    account = CloudStorageAccount.parse(storageConnectionString);

Assegnare un valore alla variabile **serviceClient**.

    serviceClient = account.createCloudBlobClient();

Assegnare un valore alla variabile **container**. Si otterrà un riferimento a un
contenitore denominato **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Creare il contenitore. Questo metodo consente di creare il contenitore se non
esiste (e restituirà **true**). Se il contenitore esiste, restituirà
**false**. Un'alternativa a **createIfNotExist** è il metodo **create**
 (che restituirà un errore se il contenitore esiste già).

    container.createIfNotExist();

Impostare l'accesso anonimo al contenitore.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Ottenere un riferimento al BLOB in blocchi, che rappresenterà il BLOB nell'archiviazione
di Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Usare il costruttore **File** per ottenere un riferimento al file
creato localmente che si intende caricare. Assicurarsi di avere creato questo file prima
di eseguire il codice.

    File fileReference = new File ("c:\myimages\image1.jpg");

Caricare il file locale tramite una chiamata al metodo **CloudBlockBlob.upload**
. Il primo parametro per il metodo **CloudBlockBlob.upload** è un oggetto
**FileInputStream** che rappresenta il file locale che verrà
caricato nell'archiviazione di Azure. Il secondo parametro è la dimensione, in
byte, del file.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Chiamare una funzione helper denominata **MakeHTMLPage** per creare una pagina HTML
di base contenente un elemento **\<image\>** con l'origine impostata sul BLOB
che si trova ora nell'account di archiviazione di Azure. Il codice per
**MakeHTMLPage** verrà descritto più avanti in questo argomento.

    MakeHTMLPage(container);

Visualizzare un messaggio di stato e le informazioni sulla pagina HTML creata.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Chiudere il blocco **try** inserendo una parentesi chiusa: **}**

Gestire le eccezioni seguenti:

-   **FileNotFoundException**: può essere generata dai costruttori **FileInputStream**
     o **FileOutputStream**
    .
-   **StorageException**: può essere generata dalla libreria di archiviazione
    client di Azure.
-   **URISyntaxException**: può essere generata dal metodo **ListBlobItem.getUri**
    .
-   **Exception**: gestione eccezioni generica.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Chiudere **main** inserendo una parentesi chiusa: **}**

Creare un metodo denominato **MakeHTMLPage** per creare una pagina HTML di base. Questo
metodo contiene un parametro di tipo **CloudBlobContainer**, che verrà
usato per scorrere l'elenco di BLOB caricati. Questo metodo genererà
eccezioni di tipo **FileNotFoundException**, che può essere generata
dal costruttore **FileOutputStream** e **URISyntaxException**,
 che può essere generata dal metodo **ListBlobItem.getUri**. Includere la
parentesi di apertura, **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Creare un file locale denominato **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Scrivere nel file locale, aggiungendo gli elementi **\<html\>**, **\<header\>** e
**\<body\>**.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Scorrere l'elenco di BLOB caricati. Per ogni BLOB, nella pagina
HTML creare un elemento **\<img\>** il cui attributo **src** è stato inviato
all'URI del BLOB poiché esiste nell'account di archiviazione di Azure.
Sebbene in questo esempio sia stata aggiunta solo un'immagine, se ne fossero state aggiunte altre,
questo codice le itererebbe tutte.

Per maggior semplicità, in questo esempio si presume che ogni BLOB caricato sia un'immagine. Se
sono stati caricati BLOB non corrispondenti a immagini o BLOB di pagine invece di BLOB
in blocchi, modificare il codice, se necessario.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Chiudere l'elemento **\<body\>** e l'elemento **\<html\>**.

    stream.println("</body>");
    stream.println("</html>");

Chiudere il file locale.

    stream.close();

Chiudere **MakeHTMLPage** inserendo una parentesi chiusa: **}**

Chiudere **StorageSample** inserendo una parentesi chiusa: **}**

Di seguito è riportato il codice completo per questo esempio. Ricordare di modificare
i valori segnaposto **your\_account\_name** e
**your\_account\_key** in modo da usare il proprio nome account e la propria chiave dell'account,
rispettivamente.

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor 
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_name"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExist();
                
                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");
                File fileReference = new File ("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            }
            catch (FileNotFoundException fileNotFoundException)
            {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (URISyntaxException uriSyntaxException)
            {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

Oltre a caricare il file immagine locale nell'archiviazione di Azure,
il codice di esempio crea un file namedindex.html locale, che è possibile aprire
nel browser per visualizzare l'immagine caricata.

Poiché il codice contiene il nome e la chiave dell'account, verificare che
il codice sorgente sia sicuro.

## <a name="bkmk_deletecontainer"> </a>Per eliminare un contenitore

Poiché l'archiviazione viene addebitata, potrebbe essere necessario eliminare il contenitore
**gettingstarted** dopo essersi esercitati con questo
esempio. Per eliminare un contenitore, usare il metodo **CloudBlobContainer.delete**
:

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Per chiamare il metodo **CloudBlobContainer.delete**, il processo di
inizializzazione degli oggetti **CloudStorageAccount**, **ClodBlobClient**,
**CloudBlobContainer** è lo stesso descritto per il metodo
**createIfNotExist**. Di seguito è riportato un esempio completo per
l'eliminazione del contenitore denominato **gettingstarted**.

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

    public class DeleteContainer {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_key"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();
                
                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Per una panoramica delle altre classi e degli altri metodi di archiviazione BLOB, vedere [Procedura
Usare il servizio di archiviazione BLOB da Java][Procedura
Usare il servizio di archiviazione BLOB da Java].

  [Prerequisiti]: #bkmk_prerequisites
  [Per usare l'archivio BLOB di Azure per caricare un file]: #bkmk_uploadfile
  [Per eliminare un contenitore]: #bkmk_deletecontainer
  [Come creare un account di archiviazione]: http://www.windowsazure.com/it-it/manage/services/storage/how-to-create-a-storage-account/
  [Come gestire gli account di archiviazione]: http://www.windowsazure.com/it-it/manage/services/storage/how-to-manage-a-storage-account/
