---
title: Usare Java SDK per sviluppare applicazioni in Azure Data Lake Store | Documentazione Microsoft
description: Usare Java SDK con Azure Data Lake Store per creare un account Data Lake Store ed eseguire le relative operazioni di base
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/23/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 0d8c4b4af9672ca40da44b19938604f668390569


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Introduzione ad Archivio Azure Data Lake con Java
> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Informazioni su come usare l'SDK Java con Azure Data Lake Store per eseguire le operazioni di base, ad esempio creare cartelle, caricare e scaricare i file di dati e così via. Per altre informazioni su Data Lake, vedere [Azure Data Lake Store](data-lake-store-overview.md).

Per la documentazione API dell'SDK Java per Azure Data Lake Store accedere a [Documentazione API Java di Azure Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Prerequisiti
* Kit di sviluppo Java (JDK 7 o versione successiva, usando Java versione 1.7 o successive)
* Account di Azure Data Lake Store. Seguire le istruzioni fornite in [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Questa esercitazione usa Maven per compilare e progettare le dipendenze. Sebbene sia possibile compilare senza ricorrere a un sistema di compilazione come Maven o Gradle, questi sistemi semplificano la gestione delle dipendenze.
* (Facoltativo) E IDE come [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) oppure [Eclipse](https://www.eclipse.org/downloads/) o simili.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Come si esegue l'autenticazione tramite Azure Active Directory?
In questa esercitazione si usa un segreto client dell'applicazione di Azure AD per recuperare un token di Azure Active Directory (autenticazione da servizio a servizio). Il token viene usato per creare un oggetto client di Data Lake Store per eseguire operazioni su file e directory. Per istruzioni su come eseguire l'autenticazione con Azure Data Lake Store con il segreto client, fare riferimento ai passaggi principali seguenti:

1. Creare un'applicazione Web di Azure AD
2. Recuperare l'ID client, il segreto client e l'endpoint token per l'applicazione Web di Azure AD.
3. Configurare l'accesso per l'applicazione Web di Azure AD per il file/cartella di Data Lake Store a cui si desidera accedere dall'applicazione Java che si sta creando.

Per istruzioni su come eseguire questi passaggi, vedere [Creare un'applicazione di Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Azure Active Directory offre anche altre opzioni anche per recuperare un token. È possibile scegliere tra una serie di meccanismi di autenticazione differenti da adattare allo scenario, come ad esempio un'applicazione in esecuzione in un browser, un'applicazione distribuita come applicazione desktop o un'applicazione server in esecuzione in locale o su una macchina virtuale di Azure. È anche possibile scegliere tra diversi tipi di credenziali quali password, certificati, autenticazione a 2 fattori e così via. Inoltre, Azure Active Directory consente di sincronizzare gli utenti di Active Directory locali con il cloud. Per i dettagli, vedere [Scenari di autenticazione per Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Creare un'applicazione Java
Il codice di esempio disponibile in [GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) offre una descrizione dei processi di creazione dei file nell'archivio, concatenazione dei file, download di un file ed eliminazione di alcuni file nell'archivio. In questa sezione dell'articolo vengono descritte le parti principali del codice.

1. Creare un progetto Maven usando l'[archetipo mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) dalla riga di comando o con un IDE. Per istruzioni su come creare un progetto Java usando IntelliJ, vedere [qui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Per istruzioni su come creare un progetto Java usando Eclipse, vedere [qui](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 
2. Aggiungere le dipendenze seguenti al file **pom.xml** di Maven. Aggiungere il frammento di testo seguente tra il tag **\</version>** e il tag **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.1</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    La prima dipendenza serve a usare l'SDK di Data Lake Store (`azure-datalake-store`) dal repository di Maven. La seconda dipendenza (`slf4j-nop`) serve a specificare quali framework di registrazione usare per questa applicazione. L'SDK di Data Lake Store usa la façade di registrazione [SLF4J](http://www.slf4j.org/), che consente di scegliere tra un numero di framework di registrazione comuni, come SLF4J, registrazione Java, logback e così via, o nessuna registrazione. In questo esempio la registrazione verrà disabilitata, quindi si farà ricorso all'associazione **SLF4J-nop**. Per usare altre opzioni di registrazione nell'applicazione, vedere [qui](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Aggiungere il codice dell'applicazione
Il codice si compone di tre parti principali.

1. Ottenere il token di Azure Active Directory
2. Usare il token per creare un client di Data Lake Store.
3. Usare il client di Data Lake Store per eseguire le operazioni.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Passaggio 1: Ottenere un token di Azure Active Directory.
L'SDK di Data Lake Store mette a disposizione metodi pratici che consentono di ottenere i token di sicurezza necessari per comunicare con l'account di Data Lake Store. Tuttavia, l'SDK non implica l'uso esclusivo di questi metodi. È possibile usare qualsiasi altro modo per ottenere i token, come l'[SDK di Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) o il proprio codice personalizzato.

Per usre l'SDK di Data Lake Store per ottenere token per l'applicazione Web di Active Directory creato in precedenza, usare i metodi statici nella classe `AzureADAuthenticator`. Sostituire **FILL-IN-HERE** (COMPLETARE QUI) con i valori effettivi per l'applicazione Web di Azure Active Directory.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Passaggio 2: Creare un oggetto client di Azure Data Lake Store (ADLStoreClient)
Per la creazione di un oggetto [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) si deve specificare il nome dell'account di Data Lake Store e il token di Azure Active Directory creato nell'ultimo passaggio. Si noti che il nome dell'account di Data Lake Store deve essere un nome di dominio completo. Ad esempio, sostituire **COMPLETARE QUI** con qualcosa simile a **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Passaggio 3: Usare l'oggetto ADLStoreClient per eseguire operazioni su file e directory
Il codice riportato di seguito contiene frammenti di esempio di alcune operazioni comuni. È possibile esaminare tutta la [documentazione API relativa all'SDK Java di Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/) dell'oggetto **ADLStoreClient** per vedere altre operazioni.

Si noti che i file vengono letti e scritti attraverso i flussi Java standard. Ciò significa che è possibile livellare uno qualsiasi dei flussi Java al livello superiore dei flussi di Data Lake Store per sfruttare la funzionalità Java standard (ad esempio i flussi di stampa per l'output formattato o uno dei flussi di compressione o crittografia per funzionalità aggiuntive del livello superiore, e così via).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Passaggio 4: Compilare ed eseguire l'applicazione
1. Per eseguirla da un IDE, individuare l'applicazione e premere il pulsante **Esegui**. Per eseguirla da Maven, usare [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Per produrre un file jar autonomo eseguibile dalla riga di comando, compilare il file jar con tutte le dipendenze incluse usando il [plug-in dell'assembly Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Il file pom.xml nell'[esempio di codice sorgente su GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) offre un esempio che descrive come eseguire questa operazione.

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Jan17_HO4-->


