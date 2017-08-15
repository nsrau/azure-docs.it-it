---
title: Creare un'app Web in Azure App Service con Azure SDK per Java
description: Informazioni su come creare un'app Web in Azure App Service a livello di codice usando Azure SDK per Java.
tags: azure-classic-portal
services: app-service-web
documentationcenter: Java
author: donntrenton
manager: erikre
editor: jimbe
ms.assetid: 8954c456-1275-4d57-aff4-ca7d6374b71e
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 02/25/2016
ms.author: v-donntr
ms.translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 19ddcc3e8e1bb3b52eeb06d81e27793c25c1e230
ms.contentlocale: it-it
ms.lasthandoff: 03/01/2017

---
# <a name="create-a-web-app-in-azure-app-service-using-the-azure-sdk-for-java"></a>Creare un'app Web in Azure App Service con Azure SDK per Java
<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## <a name="overview"></a>Panoramica
Questa procedura dettagliata mostra come creare un'applicazione con Azure SDK per Java per la creazione di un'app Web nel [Servizio app di Azure][Azure App Service] e la distribuzione di un'applicazione nel servizio. È costituita da due parti:

* La parte 1 illustra come compilare un'applicazione Java per la creazione di un'app Web.
* La parte 2 illustra come creare una semplice applicazione "Hello World" JSP e quindi usare un client FTP per distribuire il codice al servizio app.

## <a name="prerequisites"></a>Prerequisiti
### <a name="software-installations"></a>Installazioni software
Il codice dell'applicazione AzureWebDemo in questo articolo è stato scritto con Azure Java SDK 0.7.0, che è possibile installare usando l'[Installazione guidata piattaforma Web][Web Platform Installer] (WebPI). Accertarsi anche di usare l'ultima versione di [Azure Toolkit per Eclipse][Azure Toolkit for Eclipse]. Dopo avere installato l'SDK, aggiornare le dipendenze nel progetto Eclipse eseguendo **Update Index** (Aggiorna indice) in **Maven Repositories** (Repository Maven), quindi aggiungere nuovamente l'ultima versione di ogni pacchetto nella finestra **Dependencies** (Dependencies). È possibile verificare la versione del software installato in Eclipse facendo clic su **Help > Installation Details** (? > Dettagli installazione). Sono necessarie almeno le versioni seguenti:

* Pacchetto per Librerie di Microsoft Azure per Java 0.7.0.20150309
* Eclipse IDE per sviluppatori Java EE 4.4.2.20150219

### <a name="create-and-configure-cloud-resources-in-azure"></a>Creare e configurare risorse cloud in Azure
Prima di iniziare questa procedura, è necessario disporre di una sottoscrizione attiva di Azure e configurare una directory predefinita di Active Directory (AD) in Azure.

### <a name="create-an-active-directory-ad-in-azure"></a>Creare una directory di Active Directory (AD) in Azure
Se non si dispone già di una directory di Active Directory (AD) nella sottoscrizione di Azure, accedere al [portale di Azure classico][Azure classic portal] con l'account Microsoft. Se si dispone di più sottoscrizioni, fare clic su **Sottoscrizioni** e selezionare la directory predefinita della sottoscrizione da usare per questo progetto. Quindi fare clic su **Applica** per passare alla vista di quella sottoscrizione.

1. Selezionare **Active Directory** dal menu a sinistra. **Fare clic su Nuovo > Directory > Creazione personalizzata**.
2. In **Aggiungi directory** selezionare **Crea nuova directory**.
3. In **Nome**immettere il nome della directory.
4. In **Dominio**immettere il nome del dominio. È un nome di dominio di base che viene incluso nella directory per impostazione predefinita, nel seguente formato: `<domain_name>.onmicrosoft.com`. È possibile assegnargli un nome basato su quello della directory o di un altro dominio di cui si è proprietari. In un secondo tempo è possibile aggiungere un altro nome di dominio già usato dall'organizzazione.
5. In **Paese o area geografica**selezionare le impostazioni locali.

Per altre informazioni su AD, vedere [Che cos'è una directory di Azure AD][What is an Azure AD directory]?

### <a name="create-a-management-certificate-for-azure"></a>Creare un certificato di gestione per Azure
Azure SDK per Java usa i certificati di gestione per l'autenticazione con le sottoscrizioni di Azure. Sono certificati X.509 v3 usati per autenticare un'applicazione client che usa l'API di gestione dei servizi per agire per conto del proprietario della sottoscrizione e poter gestire le risorse della sottoscrizione.

Il codice in questa procedura usa un certificato autofirmato per l'autenticazione con Azure. Per questa procedura, prima è necessario creare un certificato e caricarlo nel [portale di Azure classico][Azure classic portal]. Questo include i passaggi seguenti:

* Generare un file PFX che rappresenta il certificato client e salvarlo in locale.
* Generare un certificato di gestione (file CER) dal file PFX.
* Caricare il file CER nella sottoscrizione di Azure.
* Convertire il file PFX in JKS, perché Java usa questo formato per l'autenticazione con i certificati.
* Scrivere il codice di autenticazione dell'applicazione, che fa riferimento al file JKS locale.

Al termine di questa procedura, il certificato CER si troverà nella sottoscrizione di Azure, mentre il certificato JKS si troverà nell'unità locale. Per altre informazioni sui certificati di gestione, vedere [Creare e caricare un certificato di gestione per Azure][Create and Upload a Management Certificate for Azure].

#### <a name="create-a-certificate"></a>Creare un certificato
Per creare il proprio certificato autofirmato, aprire una console dei comandi nel sistema operativo ed eseguire i comandi seguenti.

> **Nota:** nel computer in cui si esegue questo comando deve essere installato JDK. Inoltre, il percorso dello strumento Keytool dipende dalla posizione in cui si installa JDK. Per altre informazioni, vedere l'articolo relativo allo [strumento di gestione di chiavi e certificati (Keytool)][Key and Certificate Management Tool (keytool)] nella documentazione online di Java.
> 
> 

Per creare il file pfx:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Per creare il file cer:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

dove:

* `<java-install-dir>` è il percorso della directory in cui è installato Java.
* `<keystore-id>` è l'identificatore della voce dell'archivio chiavi (ad esempio, `AzureRemoteAccess`).
* `<cert-store-dir>` è il percorso della directory in cui archiviare i certificati (ad esempio, `C:/Certificates`).
* `<cert-file-name>` è il nome del file del certificato (ad esempio `AzureWebDemoCert`).
* `<password>` è la password che si sceglie per proteggere il certificato. Deve essere di almeno 6 caratteri. È possibile non immettere alcuna password, anche se non è consigliato.
* `<dname>` è il nome distinto X.500 da associare all'alias e viene usato come campo dell'autorità di certificazione e dell'oggetto nel certificato autofirmato.

Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure][Create and Upload a Management Certificate for Azure].

#### <a name="upload-the-certificate"></a>Caricare il certificato
Per caricare un certificato autofirmato in Azure, visitare la pagina **Impostazioni** del portale classico, quindi fare clic sulla scheda **Certificati di gestione**. Fare clic su **Carica** nella pagina in basso e passare alla posizione del file CER creato.

#### <a name="convert-the-pfx-file-into-jks"></a>Convertire il file PFX in JKS
Nel prompt dei comandi di Windows (in esecuzione come amministratore) passare alla directory contenente i certificati ed eseguire il comando seguente, dove `<java-install-dir>` è la directory del computer in cui si è installato Java:

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Quando richiesto, immettere la password keystore di destinazione. Sarà la password per il file JKS.
2. Quando richiesto, immettere la password keystore di origine. È la password specificata per il file PFX.

Le due password non devono essere uguali. È possibile non immettere alcuna password, anche se non è consigliato.

## <a name="build-a-web-app-creation-application"></a>Compilare un'applicazione per la creazione di app Web
### <a name="create-the-eclipse-workspace-and-maven-project"></a>Creare l'area di lavoro di Eclipse e il progetto Maven
In questa sezione si creano un'area di lavoro e un progetto Maven per l'applicazione per la creazione di app Web, denominata AzureWebDemo.

1. Creare un nuovo progetto Maven. Fare clic su **File > New > Maven Project** (File > Nuovo > Progetto Maven). In **New Maven Project** (Nuovo progetto Maven) selezionare **Create a simple project** (Crea progetto semplice) e **Use default workspace location** (Usa percorso area di lavoro predefinito).
2. Nella seconda pagina di **New Maven Project**specificare quanto segue:
   
   * Group ID: `com.<username>.azure.webdemo`
   * Artifact ID: AzureWebDemo
   * Version: 0.0.1-SNAPSHOT
   * Packaging: jar
   * Name: AzureWebDemo
     
     Fare clic su **Finish**.
3. Aprire il file pom.xml del nuovo progetto in Project Explorer. Selezionare la scheda **Dependencies** . Essendo un nuovo progetto, non è ancora elencato nessun pacchetto.
4. Aprire la visualizzazione Maven Repositories. Fare clic su **Window > Show View > Other > Maven > Maven Repositories** (Finestra > Mostra vista > Altro > Maven > Repository Maven) e fare clic su **OK**. La visualizzazione **Maven Repositories** apparirà in basso nell'IDE.
5. Aprire **Global Repositories** (Repository globali), fare clic con il pulsante destro del mouse sull'archivio **centralizzato** e scegliere **Rebuild Index** (Ricompila indice).
   
    ![][1]
   
    Questo passaggio può richiedere alcuni minuti a seconda della velocità della connessione. Quando l'indice viene ricompilato, si dovrebbero vedere i pacchetti di Microsoft Azure nell'archivio Maven **central** .
6. In **Dependencies** (Dipendenze) fare clic su **Add** (Aggiungi). In **Enter Group ID...** (Immettere ID gruppo...) immettere `azure-management`. Selezionare i pacchetti per la gestione di base e la gestione di app Web del servizio app:
   
        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites
   
   > **Nota:** se si stanno aggiornando le dipendenze in seguito al rilascio di una nuova versione, è necessario riaggiungere ogni dipendenza di questo elenco.
   > Dopo avere fatto clic su **Add** (Aggiungi) e selezionato ogni dipendenza, le dipendenze appaiono con il nuovo numero di versione nell'elenco **Dependencies** (Dipendenze).
   > 
   > 

Fare clic su **OK**. I pacchetti di Azure appaiono quindi nell'elenco **Dependencies** .

### <a name="writing-java-code-to-create-a-web-app-by-calling-the-azure-sdk"></a>Scrittura di codice Java per creare un'app Web chiamando Azure SDK
Scrivere ora il codice che chiama le API in Azure SDK in modo che Java crei l'app Web del servizio app.

1. Creare una classe Java in cui includere il codice del punto di ingresso principale. In Project Explorer fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **New > Class** (Nuovo > Classe).
2. In **New Java Class`WebCreator` (Nuova classe Java) assegnare alla classe il nome ** e selezionare la casella di controllo **public static void main**. Le selezioni dovrebbero essere come le seguenti:
   
    ![][2]
3. Fare clic su **Finish**. Il file WebCreator.java appare in Project Explorer.

### <a name="calling-the-azure-api-to-create-an-app-service-web-app"></a>Chiamata all'API di Azure per creare un app Web del servizio app
#### <a name="add-necessary-imports"></a>Aggiungere le importazioni necessarie
In WebCreator.java aggiungere le importazioni seguenti. Queste importazioni forniscono l'accesso alle classi nelle librerie di gestione per usare le API di Azure:

    // General imports
    import java.net.URI;
    import java.util.ArrayList;

    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;

    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;

    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;

    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### <a name="define-the-main-entry-point-class"></a>Definire la classe del punto di ingresso principale
Poiché lo scopo dell'applicazione AzureWebDemo è la creazione di un'app Web del servizio app, assegnare alla classe principale di questa applicazione il nome `WebAppCreator`. Questa classe fornisce il codice del punto di ingresso principale che chiama l'API di gestione del servizio Azure per creare l'app Web.

Aggiungere le seguenti definizioni dei parametri per l'app Web e lo spazio Web. Sarà necessario fornire l'ID sottoscrizione di Azure e le informazioni certificato.

    public class WebAppCreator {

        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";

        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

dove:

* `<subscription-id>` è l'ID sottoscrizione di Azure in cui creare la risorsa.
* `<certificate-store-path>` è il percorso e il nome del file JKS nella directory dell'archivio certificati locale. Ad esempio, `C:/Certificates/CertificateName.jks` per Linux e `C:\Certificates\CertificateName.jks` per Windows.
* `<certificate-password>` è la password specificata quando si è creato il certificato JKS.
* `webAppName` può essere un nome qualsiasi. In questa procedura si usa il nome `WebDemoWebApp`. Il nome di dominio completo è `webAppName` a cui viene aggiunto `domainName`, quindi in questo caso il dominio completo è `webdemowebapp.azurewebsites.net`.
* `domainName` deve essere specificato come indicato sopra.
* `webSpaceName` deve essere uno dei valori definiti nella classe [WebSpaceNames][WebSpaceNames].
* `appServicePlanName` deve essere specificato come indicato sopra.

> **Nota:`appServicePlanName` ogni volta che si esegue questa applicazione, è necessario cambiare il valore di ** e `webAppName` (o eliminare l'app Web nel portale di Azure) prima di eseguire di nuovo l'applicazione. In caso contrario, l'esecuzione non riuscirà perché la stessa risorsa esiste già in Azure.
> 
> 

#### <a name="define-the-web-creation-method"></a>Definire il metodo di creazione web
Definire ora un metodo per creare l'app Web. Questo metodo, `createWebApp`, specifica i parametri dell'app Web e dello spazio Web. Il metodo crea e configura anche il client di gestione delle app Web del servizio app, definito dall'oggetto [WebSiteManagementClient][WebSiteManagementClient]. Il client di gestione è fondamentale per la creazione di app Web. Fornisce i servizi Web RESTful che consentono alle applicazioni di gestire le app Web (eseguendo operazioni come la creazione, l'aggiornamento e l'eliminazione) chiamando l'API di gestione dei servizi.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

Il codice restituirà lo stato HTTP della risposta indicante l'esito positivo o negativo e, se positivo, restituirà il nome dell'app Web creata.

#### <a name="define-the-main-method"></a>Definire il metodo main()
Fornire il codice del metodo main() che chiama createWebApp() per creare l'app Web.

Infine chiamare `createWebApp` da `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### <a name="run-the-application-and-verify-web-app-creation"></a>Eseguire l'applicazione e verificare la creazione dell'app Web
Per verificare che l'applicazione venga eseguita, fare clic su **Run > Run** (Esegui > Esegui). Quando l'esecuzione dell'applicazione termina, si dovrebbe vedere il seguente output nella console di Eclipse:

    ----------
    Web app created - HTTP response 200

    ----------

    Name of web app created: WebDemoWebApp

    ----------

Accedere al portale di Azure classico e fare clic su **App Web**. La nuova app Web dovrebbe apparire nell'elenco App Web in pochi minuti.

## <a name="deploying-an-application-to-the-web-app"></a>Distribuzione di un'applicazione nell'app Web
Dopo avere eseguito AzureWebDemo e creato la nuova app Web, accedere al portale classico, fare clic su **App Web**e scegliere **WebDemoWebApp** in the **App Web** . Nella pagina del dashboard dell'app Web fare clic su **Sfoglia** (o fare clic sull'URL, `webdemowebapp.azurewebsites.net`) per aprirla. Si vedrà una pagina segnaposto vuota, perché nell'app Web non è ancora stato pubblicato nulla.

Ora si creerà un'applicazione "Hello World" e la si distribuirà nell'app Web.

### <a name="create-a-jsp-hello-world-application"></a>Creare un'applicazione Hello World JSP
#### <a name="create-the-application"></a>Creazione dell'applicazione
Per illustrare la distribuzione di un'applicazione sul Web, la seguente procedura mostra come creare una semplice applicazione Java "Hello World" e come caricarla nell'app Web del servizio app creata dall'applicazione.

1. Fare clic su **File > New > Dynamic Web Project** (File > Nuovo > Progetto Web dinamico). Denominarlo `JSPHello`. Non è necessario cambiare nessuna altra impostazione di questa finestra di dialogo. Fare clic su **Fine**.
   
    ![][3]
2. In Project Explorer espandere il progetto **JSPHello**, fare clic con il pulsante destro del mouse su **WebContent**, quindi fare clic su **New > JSP File** (Nuovo > File JSP). Nella finestra di dialogo New JSP File denominare il nuovo file `index.jsp`. Fare clic su **Avanti**.
3. Nella finestra di dialogo **Select JSP Template** (Seleziona modello JSP) selezionare **New JSP File (html)** (Nuovo file JSP (html)) e fare clic su **Finish** (Fine).
4. In index.jsp aggiungere il seguente codice nelle sezioni dei tag `<head>` e `<body>`:
   
        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
   
        <body>
          Hello, the time is <%= date %> 
        </body>

#### <a name="run-the-hello-world-application-in-localhost"></a>Eseguire l'applicazione Hello World in localhost
Prima di eseguire questa applicazione, è necessario configurare alcune proprietà.

1. Fare clic con il pulsante destro del mouse sul progetto **JSPHello**, quindi scegliere **Properties** (Proprietà).
2. Nella finestra di dialogo **Properties** (Proprietà) selezionare **Java Build Path** (Percorso build Java), selezionare la scheda **Order and Export** (Ordina ed esporta), scegliere **JRE System Library** (Libreria di sistema JRE), quindi fare clic su **Up** (Su) per spostarla all'inizio dell'elenco.
   
    ![][4]
3. Sempre nella finestra di dialogo **Properties** (Proprietà) selezionare **Targeted Runtimes** (Runtime mirati) e fare clic su **New** (Nuovo).
4. Nella finestra di dialogo **New Server Runtime Environment** (Nuovo ambiente di runtime server) selezionare un server, ad esempio **Apache Tomcat v7.0**, e fare clic su **Next** (Avanti). Nella finestra di dialogo **Tomcat Server** (Server Tomcat) impostare **Name** (Nome) su ** e impostare `Apache Tomcat v7.0`Tomcat Installation Directory** (Directory di installazione Tomcat) sulla directory in cui è stata installata la versione del server Tomcat da usare.
   
    ![][5]
   
    Fare clic su **Fine**.
5. Si torna quindi alla pagina **Targeted Runtimes** (Runtime mirati) della finestra di dialogo **Properties** (Proprietà). Selezionare **Apache Tomcat v7.0**, quindi fare clic su **OK**.
   
    ![][6]
6. Nel menu **Run** (Esegui) di Eclipse fare clic su **Run** (Esegui). Nella finestra di dialogo **Run As** (Esegui come) selezionare **Run on Server** (Esegui su server). Nella finestra di dialogo **Run on Server** (Esegui su server) selezionare **Tomcat v7.0 Server** (Server Tomcat v7.0):
   
    ![][7]
   
    Fare clic su **Fine**.
7. Quando l'applicazione viene eseguita, la pagina **JSPHello** dovrebbe apparire in una finestra localhost di Eclipse (`http://localhost:8080/JSPHello/`), con il messaggio seguente:
   
    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`

#### <a name="export-the-application-as-a-war"></a>Esportare l'applicazione come file WAR
Esportare i file di progetto Web come file di archivio Web (WAR) per poterli distribuire nell'app Web. I seguenti file di progetto Web si trovano nella cartella WebContent:

    META-INF
    WEB-INF
    index.jsp

1. Fare clic con il pulsante destro del mouse sulla cartella WebContent e scegliere **Export**.
2. Nella finestra di dialogo **Export Select** (Esporta selezione) fare clic sul file **Web > WAR**, quindi fare clic su **Next**.
3. Nella finestra di dialogo **WAR Export** selezionare la directory src nel progetto corrente e includere il nome del file WAR alla fine. ad esempio:
   
    `<project-path>/JSPHello/src/JSPHello.war`

Per altre informazioni sulla distribuzione di file WAR, vedere [Aggiungere un'applicazione Java alle app Web del servizio app di Azure](web-sites-java-add-app.md).

### <a name="deploying-the-hello-world-application-using-ftp"></a>Distribuzione dell'applicazione Hello World tramite FTP
Selezionare un client FTP di terze parti per pubblicare l'applicazione. Questa procedura descrive due opzioni: la console Kudu, integrata in Azure, e FileZilla, uno strumento di ampia diffusione con un'interfaccia utente grafica e intuitiva.

> **Nota:** il Toolkit Azure per Eclipse supporta la distribuzione in account di archiviazione e servizi cloud, ma attualmente non supporta la distribuzione nelle app Web. Per la distribuzione negli account di archiviazione e nei servizi cloud, è possibile usare un progetto di distribuzione di Azure, come spiegato in [Creazione di un'applicazione Hello World per Azure in Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), ma non per la distribuzione nelle app Web. Per trasferire i file nelle app Web, usare altri metodi come FTP o GitHub.
> 
> **Nota:** non è consigliabile usare FTP dal prompt dei comandi di Windows (l'utilità FTP.EXE della riga di comando fornita con Windows). I client FTP che usano un FTP attivo, ad esempio FTP.EXE, spesso non riescono a superare i firewall. FTP attivo specifica un indirizzo interno basato su LAN, a cui è probabile che un server FTP non riesca a connettersi.
> 
> 

Per altre informazioni sulla distribuzione in un'app Web del servizio app tramite FTP, vedere gli argomenti seguenti:

* [Distribuire mediante un'utilità FTP](web-sites-deploy.md)

#### <a name="set-up-deployment-credentials"></a>Imposta credenziali di distribuzione
Assicurarsi di aver eseguito l'applicazione **AzureWebDemo** per creare un'app Web. I file verranno trasferiti in questa posizione.

1. Accedere al portale classico e fare clic su **App Web**. Assicurarsi che **WebDemoWebApp** sia presente nell'elenco di app Web e che sia in esecuzione. Fare clic su **WebDemoWebApp** per aprire la pagina **Dashboard**.
2. Nella pagina **Dashboard**, in **Riepilogo rapido** fare clic su **Set up your deployment credentials** (Imposta credenziali di distribuzione). Se si dispone già di credenziali di distribuzione, questa opzione sarà sostituita da **Reimposta le credenziali di distribuzione**.
   
    Le credenziali di distribuzione sono associate a un account Microsoft. È necessario specificare un nome utente e una password da usare per la distribuzione tramite Git ed FTP. È possibile usare queste credenziali per la distribuzione in qualsiasi app Web in tutte le sottoscrizioni di Azure associate all'account Microsoft. Specificare le credenziali di distribuzione tramite Git ed FTP nella finestra di dialogo e prendere nota del nome utente e della password per un uso futuro.

#### <a name="get-ftp-connection-information"></a>Ottenere informazioni di connessione a FTP
Per usare FTP per distribuire i file dell'applicazione nell'app Web appena creata, è necessario ottenere le informazioni di connessione. Per ottenere le informazioni di connessione, è possibile procedere in due modi. Il primo consiste nel visitare la pagina **Dashboard** dell'app Web, il secondo nello scaricare il profilo di pubblicazione dell'app Web. Il profilo di pubblicazione è un file XML che fornisce informazioni, ad esempio il nome host FTP e le credenziali di accesso per le app Web in Azure App Service. È possibile usare il nome utente e la password per la distribuzione in qualsiasi app Web in tutte le sottoscrizioni associate all'account di Azure, non solo in questa.

Per ottenere le informazioni di connessione a FTP dal pannello dell'app Web nel [portale di Azure][Azure Portal]:

1. In **Essentials** trovare e copiare il **Nome host FTP**. È un URI simile a `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.
2. In **Essentials** trovare e copiare il **Nome utente FTP/distribuzione**. Il formato sarà *nomeappweb\nomeutente-distribuzione*, ad esempio `WebDemoWebApp\deployer77`.

Per ottenere le informazioni di connessione a FTP dal profilo di pubblicazione:

1. Nel pannello dell'app Web fare clic su **Recupera profilo**. Verrà scaricato un file publishsettings nell'unità locale.
2. Aprire il file publishsettings in un editor XML o nell'editor di testo e trovare l'elemento `<publishProfile>` contenente `publishMethod="FTP"`. Il codice sarà simile al seguente:
   
        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>
3. Si noti che le impostazioni `publishProfile` dell'app Web corrispondono alle impostazioni di FileZilla Site Manager indicate di seguito:

* `publishUrl` equivale a **Nome host FTP**, il valore impostato in **Host**.
* `publishMethod="FTP"` indica che **Protocollo** è stato impostato su **FTP - File Transfer Protocol** e **Criptazione** su **Usa solo FTP non sicuro**.
* `userName` e `userPWD` sono le chiavi dei valori effettivi di nome utente e password specificati quando sono state reimpostate le credenziali di distribuzione. `userName` equivale a **Utente FTP/distribuzione**. Corrispondono a **Nome utente** e **Password** in FileZilla.
* `ftpPassiveMode="True"` indica che il sito FTP usa il trasferimento FTP passivo. Selezionare **Passiva** nella scheda **Impostazioni trasferimento**.

#### <a name="configure-the-web-app-to-host-a-java-application"></a>Configurare l'app Web per ospitare un'applicazione Java
Prima di pubblicare l'applicazione, è necessario cambiare alcune impostazioni di configurazione in modo che l'app Web possa ospitare un'applicazione Java.

1. Nel portale classico accedere alla pagina **Dashboard** dell'app Web e fare clic su **Configura**. Nella pagina **Configura** specificare le impostazioni seguenti.
2. In **Versione Java** l'impostazione predefinita è **Off**. Selezionare la versione di Java per l'applicazione, ad esempio 1.7.0_51. Dopodiché, verificare anche che **Contenitore Web** sia impostato su una versione di Tomcat Server.
3. In **Documenti predefiniti**aggiungere index.jsp e spostarlo all'inizio dell'elenco. Il file predefinito per le app Web è hostingstart.html.
4. Fare clic su **Save**.

#### <a name="publish-your-application-using-kudu"></a>Pubblicare l'applicazione con Kudu
Un modo per pubblicare l'applicazione consiste nell'usare la console di debug Kudu integrata in Azure. Kudu è stabile e coerente con le app Web del servizio app e Server Tomcat. Per accedere alla console per l'app Web, passare a un URL nel formato seguente:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Per questa procedura, la console Kudu si trova all'URL seguente. Passare a questa posizione:
   
    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`
2. Nel menu in alto selezionare **Debug Console > CMD** (Console di debug > CMD).
3. Nella riga di comando della console andare a `/site/wwwroot` (o fare clic su `site`, quindi su `wwwroot` nella visualizzazione directory in alto nella pagina):
   
    `cd /site/wwwroot`
4. Dopo aver specificato **Java version**, il server Tomcat dovrebbe creare una directory webapps. Nella riga di comando della console andare alla directory webapps:
   
    `mkdir webapps`
   
    `cd webapps`
5. Trascinare Drag JSPHello.war da `<project-path>/JSPHello/src/` e rilasciarlo nella visualizzazione directory di Kudu in `/site/wwwroot/webapps`. Non trascinarlo nell'area "Drag here to upload and zip", perché Tomcat lo decomprimerà.
   
   ![][8]

All'inizio JSPHello.war appare da solo nell'area delle directory:

  ![][9]

In poco tempo (probabilmente meno di 5 minuti) Tomcat Server decomprimerà il file WAR in una directory JSPHello decompressa. Fare clic sulla directory ROOT per verificare se index.jsp è stato decompresso e copiato qui. In tal caso, tornare alla directory webapps per verificare se è stata creata la directory JSPHello decompressa. Se questi elementi non sono visibili, attendere e riprovare.

  ![][10]

#### <a name="publish-your-application-using-filezilla-optional"></a>Pubblicare l'applicazione con FileZilla (facoltativo)
Un altro strumento che è possibile usare per pubblicare l'applicazione è FileZilla, un diffuso client FTP di terze parti con un' interfaccia utente grafica e intuitiva. È possibile scaricare e installare FileZilla da [http://filezilla-project.org/](http://filezilla-project.org/) se non lo si ha già. Per altre informazioni sull'uso del client, vedere la [documentazione di FileZilla](https://wiki.filezilla-project.org/Documentation) e questo post del blog in [FTP Clients - Part 4: FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx) (Client FTP - Parte 4: FileZilla).

1. In FileZilla fare clic su **File > Gestore siti**.
2. Nella finestra di dialogo **Gestore siti** fare clic su **Nuovo sito**. In **Select Entry** apparirà un nuovo sito FTP vuoto a cui assegnare un nome. Per questa procedura, denominarlo `AzureWebDemo-FTP`.
   
    Nella pagina **Generale** specificare le impostazioni seguenti:
   
   * **Host:** immettere il **nome dell'host FTP** copiato dal dashboard.
   * **Porta:** lasciarla non configurata, poiché si tratta di un trasferimento passivo per il quale il server determinerà la porta da usare.
   * **Protocollo:** protocollo per il trasferimento del file FTP
   * **Crittografia:** usare FTP semplice
   * **Tipo di accesso:** normale
   * **Utente:** immettere l'utente FTP/distribuzione copiato dal dashboard. Si tratta del nome completo dell'utente dell'FTP, che presenta il l formato *nomeappweb\nomeutente*.
   * **Password:** immettere la password specificata quando si sono impostate le credenziali di distribuzione.
     
     Nella scheda **Impostazioni di trasferimento** selezionare **Passiva**.
3. Fare clic su **Connect**. Se l'esito è positivo, la console di FileZilla visualizzerà un messaggio `Status: Connected` ed eseguirà un comando `LIST` per elencare il contenuto della directory.
4. Nel pannello del sito **Local** (Locale) selezionare la directory di origine in cui si trova il file JSPHello.war. Il percorso sarà simile al seguente:
   
    `<project-path>/JSPHello/src/`
5. Nel pannello del sito **Remote** selezionare la cartella di destinazione. Il file WAR verrà distribuito nella directory `webapps` sotto la radice dell'app Web. Passare a **, fare clic con il pulsante destro del mouse su ** e scegliere `/site/wwwroot`Create directory`wwwroot` (Crea directory). Assegnare alla directory il nome `webapps` e aprirla.
6. Trasferire JSPHello.war in `/site/wwwroot/webapps`. Selezionare JSPHello.war nell'elenco di file **Local** (Locale), fare clic con il pulsante destro del mouse su di esso e scegliere **Upload** (Carica). Dovrebbe venire visualizzato in `/site/wwwroot/webapps`.
7. Dopo avere copiato JSPHello.war nella directory webapps, Tomcat Server decomprimerà automaticamente i file del file WAR. Anche se Tomcat Server inizia la decompressione quasi immediatamente, potrebbe volerci molto tempo (anche ore) prima che i file vengano visualizzati nel client FTP.

#### <a name="run-the-hello-world-application-on-the-web-app"></a>Eseguire l'applicazione Hello World nell'app Web
1. Dopo avere caricato il file WAR e verificato che il server Tomcat abbia creato una directory `JSPHello` decompressa, passare a `http://webdemowebapp.azurewebsites.net/JSPHello` per eseguire l'applicazione.
   
   > **Nota:** se si fa clic su **Esplora risorse** nel portale classico, è possibile che si apra la pagina Web predefinita, che indica che l'applicazione Web basata su Java è stata creata correttamente. Potrebbe essere necessario aggiornare la pagina Web per visualizzare l'output dell'applicazione invece della pagina Web predefinita.
   > 
   > 
2. Quando viene eseguita l'applicazione, si dovrebbe aprire una pagina web con il seguente output:
   
    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`

#### <a name="clean-up-azure-resources"></a>Pulire le risorse di Azure
Questa procedura crea un'app web del servizio app. Finché la risorsa esiste, i relativi costi verranno fatturati. A meno che non si preveda di continuare a usare l'app Web per il test o lo sviluppo, è consigliabile arrestarla o eliminarla. Anche se un'app Web viene arrestata, è ugualmente prevista una piccola spesa, ma è possibile riavviarla in qualsiasi momento. Con l'eliminazione di un'app Web vengono cancellati tutti i dati che erano stati caricati.

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
[2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
[3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
[4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
[5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
[6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
[7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
[8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
[9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
[10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png


[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Web Platform Installer]: http://go.microsoft.com/fwlink/?LinkID=252838
[Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh690946.aspx
[Azure classic portal]: https://manage.windowsazure.com
[What is an Azure AD directory]: http://technet.microsoft.com/library/jj573650.aspx
[Create and Upload a Management Certificate for Azure]: ../cloud-services/cloud-services-certs-create.md
[Key and Certificate Management Tool (keytool)]: http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html
[WebSiteManagementClient]: http://azure.github.io/azure-sdk-for-java/com/microsoft/azure/management/websites/WebSiteManagementClient.html
[WebSpaceNames]: http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html
[Azure Portal]: https://portal.azure.com

