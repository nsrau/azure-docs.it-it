---
title: 'Java SDK: Operazioni del file system in Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Usare Java SDK di Azure Data Lake Storage Gen1 per eseguire operazioni del file system in Data Lake Storage Gen1, ad esempio creare cartelle e così via.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bc6e0718cdc4ccb18480dc760279da9c177db4cb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883548"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Operazioni del file system in Azure Data Lake Storage Gen1 con Java SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Informazioni su come usare Java SDK di Azure Data Lake Storage Gen1 per eseguire le operazioni di base, ad esempio creare cartelle, caricare e scaricare i file di dati e così via. Per altre informazioni su Data Lake Storage Gen1, vedere [Azure Data Lake Storage Gen1](data-lake-store-overview.md).

Per la documentazione dell'API Java SDK per Data Lake Storage Gen1 accedere a [Documentazione dell'API Java di Azure Data Lake Storage Gen1](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Prerequisiti
* Kit di sviluppo Java (JDK 7 o versione successiva, usando Java versione 1.7 o successive)
* Account Data Lake Storage Gen1. Seguire le istruzioni fornite in [Introduzione ad Azure Data Lake Storage Gen1 con il portale di Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Questa esercitazione usa Maven per compilare e progettare le dipendenze. Sebbene sia possibile compilare senza ricorrere a un sistema di compilazione come Maven o Gradle, questi sistemi semplificano la gestione delle dipendenze.
* (Facoltativo) E IDE come [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) oppure [Eclipse](https://www.eclipse.org/downloads/) o simili.

## <a name="create-a-java-application"></a>Creare un'applicazione Java
Il codice di esempio disponibile in [GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) offre una descrizione dei processi di creazione dei file nell'archivio, concatenazione dei file, download di un file ed eliminazione di alcuni file nell'archivio. Questa sezione dell'articolo descrive in modo dettagliato le parti principali del codice.

1. Creare un progetto Maven usando l'[archetipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) dalla riga di comando o con un IDE. Per istruzioni su come creare un progetto Java usando IntelliJ, vedere [qui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Per istruzioni su come creare un progetto Java usando Eclipse, vedere [qui](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Aggiungere le dipendenze seguenti al file **pom.xml** di Maven. Aggiungere il frammento di codice seguente prima del tag **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    La prima dipendenza consiste nell'uso di Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`) dal repository maven. La seconda dipendenza serve a specificare il framework di registrazione (`slf4j-nop`) da usare per questa applicazione. Data Lake Storage Gen1 SDK usa l'interfaccia di registrazione [slf4j](https://www.slf4j.org/) che consente di scegliere tra diversi framework di registrazione comuni, ad esempio log4j, registrazione con Java, logback e così via o nessuna registrazione. Per questo esempio la registrazione viene disabilitata, quindi si usa il binding **slf4j-nop**. Per usare altre opzioni di registrazione nell'applicazione, vedere [qui](https://www.slf4j.org/manual.html#projectDep).

3. Aggiungere le istruzioni import seguenti all'applicazione.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Authentication

* Per l'autenticazione dell'utente finale per l'applicazione, vedere [Autenticazione dell'utente finale con Data Lake Storage Gen1 con Java](data-lake-store-end-user-authenticate-java-sdk.md).
* Per l'autenticazione da servizio a servizio per l'applicazione, vedere [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-a-data-lake-storage-gen1-client"></a>Creare un client Data Lake Storage Gen1
Per la creazione di un oggetto [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) è necessario specificare il nome dell'account Data Lake Storage Gen1 e il provider di token generato quando è stata eseguita l'autenticazione con Data Lake Storage Gen1 (vedere la sezione [Autenticazione](#authentication)). Il nome dell'account Data Lake Storage Gen1 deve essere un nome di dominio completo. Ad esempio, sostituire **COMPLETARE QUI** con qualcosa simile a **mydatalakestoragegen1.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

I frammenti di codice nelle sezioni seguenti contengono esempi di alcune operazioni comuni del file system. È possibile esaminare tutta la [documentazione dell'API Java SDK di Data Lake Storage Gen1](https://azure.github.io/azure-data-lake-store-java/javadoc/) dell'oggetto **ADLStoreClient** per vedere altre operazioni.

## <a name="create-a-directory"></a>Creare una directory

Il frammento di codice seguente crea una struttura di directory nella radice dell'account Data Lake Storage Gen1 specificato.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Creare un file

Il frammento di codice seguente crea un file (c.txt) nella struttura di directory e scrive alcuni dati nel file.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

È anche possibile creare un file (d.txt) usando matrici di byte.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

La definizione per la funzione `getSampleContent` usata nel frammento di codice precedente è disponibile come parte dell'esempio [su GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/). 

## <a name="append-to-a-file"></a>Aggiungere a un file

Il frammento di codice seguente aggiunge contenuto a un file esistente.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

La definizione per la funzione `getSampleContent` usata nel frammento di codice precedente è disponibile come parte dell'esempio [su GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="read-a-file"></a>Leggere un file

Il frammento di codice seguente legge il contenuto di un file nell'account Data Lake Storage Gen1.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Concatenare file

Il frammento di codice seguente concatena due file in un account Data Lake Storage Gen1. Se l'operazione ha esito positivo, il file concatenato sostituisce i due file esistenti.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Rinominare un file

Il frammento di codice seguente rinomina un file in un account Data Lake Storage Gen1.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Ottenere i metadati per un file

Il frammento di codice seguente recupera i metadati di un file nell'account Data Lake Storage Gen1.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Impostare le autorizzazioni in un file

Il frammento di codice seguente imposta le autorizzazioni nel file creato nella sezione precedente.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Il frammento di codice seguente elenca il contenuto di una directory, in modo ricorsivo.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

La definizione per la funzione `printDirectoryInfo` usata nel frammento di codice precedente è disponibile come parte dell'esempio [su GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="delete-files-and-folders"></a>Eliminare file e cartelle

Il frammento di codice seguente elimina le cartelle e i file specificati in un account Data Lake Storage Gen1, in modo ricorsivo.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione
1. Per eseguirla da un IDE, individuare l'applicazione e premere il pulsante **Esegui**. Per eseguirla da Maven, usare [exec:exec](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Per produrre un file jar autonomo eseguibile dalla riga di comando, compilare il file jar con tutte le dipendenze incluse usando il [plug-in dell'assembly Maven](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Il file pom.xml nell'[esempio di codice sorgente su GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) offre un esempio.

## <a name="next-steps"></a>Passaggi successivi
* [Explore JavaDoc for the Java SDK](https://azure.github.io/azure-data-lake-store-java/javadoc/) (Cercare Java SDK in JavaDoc)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)


