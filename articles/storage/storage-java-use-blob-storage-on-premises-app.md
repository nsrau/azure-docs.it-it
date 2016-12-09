---
title: Applicazione locale con archiviazione BLOB (Java) | Microsoft Docs
description: Informazioni su come creare un&quot;applicazione console che carica un&quot;immagine in Azure e quindi visualizza l&quot;immagine nel browser. Esempi di codice in Java.
services: storage
documentationcenter: java
author: tamram
manager: carmonm
editor: tysonn
ms.assetid: ccc9a7d7-6fe4-467b-b7fd-a73f17539e3f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/17/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 27d088291d93ce936a9b2465a13a47e62270fb84


---
# <a name="on-premises-application-with-blob-storage"></a>Applicazione locale con archiviazione BLOB
## <a name="overview"></a>Overview
Nell'esempio seguente viene illustrato come utilizzare Azure per archiviare immagini in Azure. Il codice in questo articolo è per un'applicazione console che carica un'immagine in Azure e quindi crea un file HTML che visualizza l'immagine nel browser.

## <a name="prerequisites"></a>Prerequisiti
* È installato Java Developer Kit (JDK) v1.6 o versione successiva.
* Azure SDK installato.
* JAR per le librerie di Azure per Java e qualsiasi JAR di dipendenza applicabile installati e presenti nel percorso di compilazione utilizzato dal compilatore Java. Per informazioni sull'installazione delle librerie di Azure per Java, vedere [Download di Azure SDK per Java](../java-download-azure-sdk.md).
* Account di Archiviazione di Azure configurato. Il nome account e la chiave dell'account per l'account di archiviazione verranno utilizzati dal codice in questo articolo. Vedere [Come creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account) per informazioni sulla creazione di un account di archiviazione e [Visualizzare e copiare le chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#view-and-copy-storage-access-keys) per informazioni sul recupero della chiave dell'account.
* Aver creato un file di immagine locale denominato archiviato nel percorso cc:\\myimages\\image1.jpg. In alternativa, modificare il costruttore   **FileInputStream** nell'esempio per usare un percorso e un nome file di immagine diversi.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>Per usare l'archiviazione BLOB di Azure per caricare un file
Di seguito viene presentata una procedura dettagliata. Se si desidera ignorarla, l’intero codice viene presentato più avanti in questo articolo.

Includere all'inizio del codice le importazioni per le classi di archiviazione della memoria centrale di Azure, le classi client BLOB di Azure, le classi IO Java e la classe **URISyntaxException** :

```java
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
import java.io.*;
import java.net.URISyntaxException;
```

Dichiarare un classe denominata **StorageSample** e includere la parentesi aperta, **{**.

```java
public class StorageSample {
```

All'interno della classe **StorageSample** dichiarare una variabile di tipo stringa che conterrà il protocollo endpoint predefinito, il nome account di archiviazione e la chiave di accesso alle risorse di archiviazione, come specificato nell'account di archiviazione di Azure. Sostituire i valori segnaposto **your\_account\_name** e **your\_account\_key** con il proprio nome account e la propria chiave dell'account, rispettivamente.

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_account_name;" +
    "AccountKey=your_account_name";
```

Aggiungere la dichiarazione per **main**, includere un blocco **try** e includere le necessarie parentesi aperte, **{**.

```java
    public static void main(String[] args)
    {
        try
        {
```

Dichiarare le variabili del tipo seguente (le descrizioni sono relative all'utilizzo in questo esempio):

* **CloudStorageAccount**: usata per inizializzare l'oggetto account con la chiave e il nome account di archiviazione di Azure e per creare l'oggetto client BLOB.
* **CloudBlobClient**: usata per accedere al servizio BLOB.
* **CloudBlobContainer**: usata per creare un contenitore BLOB, elencare i BLOB nel contenitore ed eliminare il contenitore.
* **CloudBlockBlob**: usata per caricare un file di immagine locale nel contenitore.

<!-- -->

```java
    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;
```

Assegnare un valore alla variabile **account** .

```java
account = CloudStorageAccount.parse(storageConnectionString);
```

Assegnare un valore alla variabile **serviceClient** .

```java
serviceClient = account.createCloudBlobClient();
```

Assegnare un valore alla variabile **container** . Si otterrà un riferimento a un contenitore denominato **gettingstarted**.

```java
// Container name must be lower case.
container = serviceClient.getContainerReference("gettingstarted");
```

Creare il contenitore. Questo metodo creerà il contenitore se non esiste (e restituirà **true**). Se il contenitore esiste, restituirà **false**. Un'alternativa a **createIfNotExists** è il metodo **create**, che restituisce un errore se il contenitore è già presente.

```java
container.createIfNotExists();
```

Impostare l'accesso anonimo al contenitore.

```java
// Set anonymous access on the container.
BlobContainerPermissions containerPermissions;
containerPermissions = new BlobContainerPermissions();
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
container.uploadPermissions(containerPermissions);
```

Ottenere un riferimento al BLOB in blocchi, che rappresenterà il BLOB nell'archiviazione di Azure.

```java
blob = container.getBlockBlobReference("image1.jpg");
```

Utilizzare il costruttore **File** per ottenere un riferimento al file creato localmente che si caricherà. Assicurarsi di aver creato questo file prima di eseguire il codice.

```java
File fileReference = new File ("c:\\myimages\\image1.jpg");
```

Caricare il file locale tramite una chiamata al metodo **CloudBlockBlob.upload** . Il primo parametro per il metodo **CloudBlockBlob.upload** è un oggetto **FileInputStream** che rappresenta il file locale che verrà caricato nell'archiviazione di Azure. Il secondo parametro è la dimensione, in byte, del file.

```java
blob.upload(new FileInputStream(fileReference), fileReference.length());
```

Chiamare una funzione helper denominata **MakeHTMLPage**, per creare una pagina HTML di base contenente un elemento **&lt;image&gt;** con l'origine impostata sul BLOB che si trova ora nell'account di archiviazione di Azure. Il codice per **MakeHTMLPage** verrà descritto più avanti in questo articolo.

```java
MakeHTMLPage(container);
```

Visualizzare un messaggio di stato e le informazioni sulla pagina HTML creata.

```java
System.out.println("Processing complete.");
System.out.println("Open index.html to see the images stored in your storage account.");
```

Chiudere il blocco **try** inserendo una parentesi chiusa: **}**

Gestire le eccezioni seguenti:

* **FileNotFoundException**: può essere generata dai costruttori **FileInputStream** o **FileOutputStream**.
* **StorageException**: può essere generata dalla libreria di archiviazione client di Azure.
* **URISyntaxException**: può essere generata dal metodo **ListBlobItem.getUri**.
* **Exception**: gestione eccezioni generica.

<!-- -->

```java
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
```

Chiudere **main** inserendo una parentesi chiusa: **}**

Creare un metodo denominato **MakeHTMLPage** per creare una pagina HTML di base. Questo metodo dispone di un parametro di tipo **CloudBlobContainer**, che verrà utilizzato per scorrere l'elenco di BLOB caricati. Questo metodo genererà eccezioni di tipo **FileNotFoundException**, che può essere generata dal costruttore **FileOutputStream**, e **URISyntaxException**, che può essere generata dal metodo **ListBlobItem.getUri**. Includere la parentesi di apertura, **{**.

```java
public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
{
```

Creare un file locale denominato **index.html**.

```java
PrintStream stream;
stream = new PrintStream(new FileOutputStream("index.html"));
```

Scrivere nel file locale, aggiungendo gli elementi **&lt;&gt;html**, **&lt;header&gt;** e **&lt;body&gt;**.

```java
stream.println("<html>");
stream.println("<header/>");
stream.println("<body>");
```

Scorrere l'elenco di BLOB caricati. Per ogni BLOB, nella pagina HTML creare un elemento **&lt;img&gt;** il cui attributo **src** è stato inviato all'URI del BLOB perché esiste nell'account di archiviazione di Azure.
Anche se in questo esempio è stata aggiunta una sola immagine, se se ne aggiungono altre, il codice le itererà tutte.

Per maggior semplicità, in questo esempio si presume che ogni BLOB caricato sia un'immagine. Se sono stati caricati BLOB non corrispondenti a immagini o BLOB di pagine invece di BLOB in blocchi, modificare il codice, se necessario.

```java
// Enumerate the uploaded blobs.
for (ListBlobItem blobItem : container.listBlobs()) {
// List each blob as an <img> element in the HTML body.
stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
}
```

Chiudere l'elemento **&lt;body&gt;** e l'elemento **&lt;html&gt;**.

```java
stream.println("</body>");
stream.println("</html>");
```

Chiudere il file locale.

```java
stream.close();
```

Chiudere **MakeHTMLPage** inserendo una parentesi chiusa: **}**

Chiudere **StorageSample** inserendo una parentesi chiusa: **}**

Di seguito è riportato il codice completo per questo esempio. Si ricordi di modificare i valori segnaposto **your\_account\_name** e **your\_account\_key** in modo da usare il proprio nome account e la propria chiave dell'account, rispettivamente.

```java
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
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

    public static void main(String[] args) {
        try {
            CloudStorageAccount account;
            CloudBlobClient serviceClient;
            CloudBlobContainer container;
            CloudBlockBlob blob;

            account = CloudStorageAccount.parse(storageConnectionString);
            serviceClient = account.createCloudBlobClient();
            // Container name must be lower case.
            container = serviceClient.getContainerReference("gettingstarted");
            container.createIfNotExists();

            // Set anonymous access on the container.
            BlobContainerPermissions containerPermissions;
            containerPermissions = new BlobContainerPermissions();
            containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
            container.uploadPermissions(containerPermissions);

            // Upload an image file.
            blob = container.getBlockBlobReference("image1.jpg");

            File fileReference = new File("c:\\myimages\\image1.jpg");
            blob.upload(new FileInputStream(fileReference), fileReference.length());

            // At this point the image is uploaded.
            // Next, create an HTML page that lists all of the uploaded images.
            MakeHTMLPage(container);

            System.out.println("Processing complete.");
            System.out.println("Open index.html to see the images stored in your storage account.");

        } catch (FileNotFoundException fileNotFoundException) {
            System.out.print("FileNotFoundException encountered: ");
            System.out.println(fileNotFoundException.getMessage());
            System.exit(-1);
        } catch (StorageException storageException) {
            System.out.print("StorageException encountered: ");
            System.out.println(storageException.getMessage());
            System.exit(-1);
        } catch (URISyntaxException uriSyntaxException) {
            System.out.print("URISyntaxException encountered: ");
            System.out.println(uriSyntaxException.getMessage());
            System.exit(-1);
        } catch (Exception e) {
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
```

Oltre a caricare il file di immagine locale nell'archiviazione di Azure, il codice di esempio crea un file di immagine namedindex.html, che è possibile aprire nel browser per visualizzare l'immagine caricata.

Poiché il codice contiene il nome account e la chiave dell'account, verificare che il codice sorgente sia sicuro.

## <a name="to-delete-a-container"></a>Per eliminare un contenitore
Poiché l'archiviazione viene addebitata, potrebbe essere necessario eliminare il contenitore **gettingstarted** dopo essersi esercitati con questo esempio. Per eliminare un contenitore, utilizzare il metodo **CloudBlobContainer.delete** :

```java
container = serviceClient.getContainerReference("gettingstarted");
container.delete();
```

Per chiamare il metodo **CloudBlobContainer.delete**, il processo di inizializzazione degli oggetti **CloudStorageAccount**, **ClodBlobClient**, **CloudBlobContainer** è lo stesso descritto per il metodo **createIfNotExist**. Di seguito è riportato un esempio completo per l'eliminazione del contenitore denominato **gettingstarted**.

```java
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;

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
```

Per una panoramica delle altre classi e degli altri metodi di archiviazione BLOB, vedere [Come usare l'archiviazione BLOB da Java](storage-java-how-to-use-blob-storage.md).

## <a name="next-steps"></a>Passaggi successivi
Seguire i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

* [Azure Storage SDK per Java](https://github.com/azure/azure-storage-java)
* [Riferimento all'SDK del client di archiviazione di Azure](http://dl.windowsazure.com/storage/javadoc/)
* [API REST dei servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)




<!--HONumber=Nov16_HO3-->


