<properties linkid="manage-services-how-to-use-appdynamics" urlDisplayName="Monitor with AppDynamics" pageTitle="How to use AppDynamics with Azure" metaKeywords="" description="Learn how to use AppDynamics for Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How To Use AppDynamics for Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Come usare AppDynamics per Azure

In questo argomento viene descritto come iniziare a usare AppDynamics per Azure.

## Sommario

-   [Informazioni su AppDynamics][Informazioni su AppDynamics]
-   [Prerequisiti][Prerequisiti]
-   [Registrazione per un account AppDynamics][Registrazione per un account AppDynamics]
-   [Download dell'agente .NET da AppDynamics][Download dell'agente .NET da AppDynamics]
-   [Aggiunta dell'agente .NET ai ruoli di Azure e modifica dell'avvio][Aggiunta dell'agente .NET ai ruoli di Azure e modifica dell'avvio]
-   [Pubblicazione dell'applicazione instrumentata con AppDynamics in Azure][Pubblicazione dell'applicazione instrumentata con AppDynamics in Azure]
-   [Monitoraggio dell'applicazione][Monitoraggio dell'applicazione]

## <span id="what"></span></a>Informazioni su AppDynamics

AppDynamics è una soluzione per il monitoraggio delle prestazioni delle applicazioni che consente di:

-   Identificare problemi, quali richieste utente lente e bloccate, ed errori, in un ambiente di produzione

-   Risolvere i problemi e isolarne le cause

AppDynamics include due componenti:

-   L'agente server dell'applicazione: l'agente .NET server dell'applicazione raccoglie dati dai server. È necessario eseguire un agente distinto in ogni istanza del ruolo che si desidera monitorare. È possibile scaricare l'agente dal portale di AppDynamics.

-   Controller AppDynamics: l'agente invia le informazioni al servizio ospitato del controller AppDynamics in Azure. Tramite una console basata su Web browser, è possibile accedere al controller per monitorare, analizzare e risolvere i problemi relativi all'applicazione.

    ![Diagramma di AppDynamics][Diagramma di AppDynamics]

## <span id="prereq"></span></a>Prerequisiti

-   Visual Studio 2010 o versione successiva
-   Una soluzione Visual Studio da monitorare
-   Azure SDK
-   Account Azure

## <span id="register"></span></a>Registrazione per un account AppDynamics

Per eseguire la registrazione per un account AppDynamics per Azure:

1.  Fare clic su **Prova gratuita** o **Iscriviti** per AppDynamics in Azure Marketplace all'indirizzo [][]<https://datamarket.azure.com/browse/Applications></a>.

    Se si sceglie **Iscriviti**, si riceverà una versione gratuita di AppDynamics Pro per Azure completamente funzionante, che dopo 30 giorni verrà convertita in una versione di AppDynamics Lite per Azure con funzionalità limitate. Non è necessario fornire i dati di una carta di credito per questa opzione. È possibile eseguire l'aggiornamento ad AppDynamics Pro per Azure in qualsiasi momento.

    Se si sceglie **Prova gratuita**, si riceverà una versione gratuita di AppDynamics Pro per Azure completamente funzionante. Per questa opzione è necessario fornire i dati di una carta di credito. Dopo 30 giorni, il costo di AppDynamics Pro per Azure verrà addebitato sul conto della carta di credito e si potrà continuare a usare il prodotto, a meno che non si annulli la sottoscrizione.

    È necessaria una licenza agente per ogni istanza del ruolo da monitorare. Ad esempio, un sito in cui vengono eseguite 2 istanze del suolo Web e 2 istanze del ruolo di lavoro richiede 4 licenze agente.

2.  Nella pagina di registrazione, immettere le proprie informazioni utente, una password, un indirizzo di posta elettronica, il nome della società e il nome dell'applicazione monitorata come verrà pubblicata con Azure.

3.  Fare clic su **Registra**.

    Si riceveranno le credenziali di AppDynamics e l'URL del controller AppDynamics (host e porta) assegnati all'account tramite un messaggio di posta elettronica inviato all'indirizzo indicato nella pagina di registrazione. Salvare queste informazioni.

    Se si dispone già di credenziali AppDynamics create in precedenza per un altro prodotto, è possibile accedere con tali credenziali.

    Verrà anche fornita una home page per l'account AppDynamics,

    che verrà aperta in fase di accesso.

    La home page dell'account AppDynamics include:

    -   URL del controller: URL da cui accedere all'account nel servizio ospitato del controller AppDynamics

    -   Credenziali AppDynamics: nome account e chiave di accesso

    -   Collegamento al sito di download di AppDynamics: sito da cui scaricare l'agente .NET di AppDynamics

    -   Numero di giorni rimanenti per la versione di prova di AppDynamics Pro

    -   Collegamenti a video e documentazione per iniziare a usare AppDynamics

    È possibile accedere alla home page dell'account AppDynamics in qualsiasi momento specificando l'URL in un Web browser ed effettuando l'accesso con le credenziali AppDynamics.

## <span id="download"></span></a>Download dell'agente .NET da AppDynamics

1.  Passare al sito di download di AppDynamics. L'URL è indicato nel messaggio di benvenuto e nella home page dell'account AppDynamics.

2.  Effettuare l'accesso con il nome account AppDynamics e la chiave d'accesso personali.

3.  Scaricare il file denominato AppDynamicsdotNetAgentSetup64.msi. Non eseguire il file.

## <span id="addagent"></span></a>Aggiunta dell'agente .NET ai ruoli di Azure e modifica dell'avvio

Questo passaggio consente di instrumentare i ruoli nella soluzione di Visual Studio per il monitoraggio da parte di AppDynamics. Per usare AppDynamics per Azure non è disponibile una procedura guidata per l'installazione come in Windows.

1.  Creare un nuovo progetto Azure in Visual Studio o aprire un progetto Azure esistente.

2.  Se si crea un nuovo progetto, aggiungere il progetto di ruolo Web e/o ruolo di lavoro alla soluzione.

3.  Aggiungere il file MSI scaricato dell'agente .NET a ogni progetto di ruolo Web e di lavoro che si desidera monitorare.

    Si noti che ogni *progetto di ruolo* ha un solo file MSI dell'agente .NET associato, mentre ogni *istanza del ruolo* del progetto richiede una licenza distinta per l'agente .NET.

4.  A ogni progetto di ruolo Web e di lavoro che si desidera monitorare aggiungere un file di testo denominato startup.cmd e incollarvi le righe di codice seguenti:

        if defined COR_PROFILER GOTO END 
        SETLOCAL EnableExtensions 
        REM Run the agent installer 
        AppDynamicsdotNetAgentSetup64.msi AD_Agent_Environment=Azure AD_Agent_ControllerHost=%1 AD_Agent_ControllerPort=%2 AD_Agent_AccountName=%3 AD_Agent_AccessKey=%4 AD_Agent_ControllerApplication=%5 /quiet /log d:\adInstall.log  
        SHUTDOWN /r /c "Rebooting the instance after the installation of AppDynamics Monitoring Agent" /t 0 
        GOTO END   
        :END

5.  Per ogni ruolo Web e di lavoro che si desidera monitorare, impostare la proprietà **Copia in directory di output** per il file MSI dell'agente AppDynamics e **Copia sempre** per il file startup.cmd.

    ![Copia sempre][Copia sempre]

6.  Nel file ServiceDefinition.csdef per il progetto Azure aggiungere un elemento attività all'avvio che richiama startup.cmd con i parametri per ogni elemento WorkerRole e WebRole.

    Aggiungere le righe seguenti:

        <Startup>
        <Task commandLine="startup.cmd [your_controller_host] [your_controller_port] [your_account_name] [your_access_key] [your_application_name]" executionContext="elevated" taskType="simple"/>
        </Startup>

    dove:

    -   *your controller host* e *your controller port* rappresentano l'host e la porta del controller assegnati all'account, mentre *your account name* e *your access key* rappresentano le credenziali assegnate da AppDynamics. Queste informazioni sono disponibili nel messaggio ricevuto dopo la registrazione con AppDynamics e nella home page dell'account AppDynamic. Vedere [Registrazione per un account AppDynamics][Registrazione per un account AppDynamics].

    -   *your application name* rappresenta il nome scelto per l'applicazione. Questo nome identificherà l'applicazione nell'interfaccia del controller AppDynamics.

    Il file ServiceDefinition.csdef avrà un aspetto simile al seguente:

    ![Definizione del servizio][Definizione del servizio]

## <a name="publish"></a>Pubblicazione dell'applicazione instrumentata con AppDynamics in Azure

Per ogni progetto di ruolo instrumentato con AppDynamics:

1.  In Visual Studio selezionare il progetto di ruolo.

2.  Selezionare Publish to Azure.

## <a name="monitor"></a>Monitoraggio dell'applicazione

1.  Accedere al controller AppDynamics con l'URL indicato nel messaggio di benvenuto e nella home page dell'account AppDynamics.

2.  Inviare alcune richieste all'applicazione in modo che ci sia del traffico da monitorare e attendere alcuni minuti.

3.  Nel controller AppDynamics selezionare l'applicazione.

4.  Monitorare l'applicazione.

## <a name="learn"></a>Altre informazioni

Per collegamenti alla documentazione e ai video, vedere la home page dell'account AppDynamics.

Gli ultimi aggiornamenti a questo documento si trovano nella versione wiki all'indirizzo [][1]<http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure></a>.

  [Informazioni su AppDynamics]: #what
  [Prerequisiti]: #prereq
  [Registrazione per un account AppDynamics]: #register
  [Download dell'agente .NET da AppDynamics]: #download
  [Aggiunta dell'agente .NET ai ruoli di Azure e modifica dell'avvio]: #addagent
  [Pubblicazione dell'applicazione instrumentata con AppDynamics in Azure]: #publish
  [Monitoraggio dell'applicazione]: #monitor
  [Diagramma di AppDynamics]: ./media/cloud-services-how-to-appdynamics/addiagram.png
  []: https://datamarket.azure.com/browse/Applications
  [Copia sempre]: ./media/cloud-services-how-to-appdynamics/adcopyalways.png
  [Definizione del servizio]: ./media/cloud-services-how-to-appdynamics/adscreen.png
  [1]: http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure
