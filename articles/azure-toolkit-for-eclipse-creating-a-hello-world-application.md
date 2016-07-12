<properties
    pageTitle="Creare un servizio cloud Hello World per Azure in Eclipse"
    description="Informazioni su come creare una semplice applicazione Hello World usando Azure Toolkit per Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="06/24/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# Creare un servizio cloud Hello World per Azure in Eclipse #

La procedura seguente illustra come creare e distribuire un'applicazione JSP di base in Azure usando Azure Toolkit per Eclipse. Per semplicità è riportato un esempio JSP, ma è possibile adottare una procedura molto simile anche per un servlet Java, per quanto riguarda la distribuzione di Azure.

L'applicazione sarà simile alla seguente:

![][ic600360]

## Prerequisiti ##

* Java Developer Kit (JDK) versione 1.7 o successiva.
* IDE Eclipse per sviluppatori Java EE, Indigo o versione successiva. È possibile scaricare il pacchetto all'indirizzo <http://www.eclipse.org/downloads/>.
* Distribuzione di un server applicazioni o un server Web basato su Java, ad esempio Apache Tomcat, GlassFish, JBoss Application Server, Jetty, or IBM® WebSphere® Application Server Liberty Core.
* Una sottoscrizione Azure, che può essere acquistata all'indirizzo <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure Toolkit per Eclipse. Per altre informazioni, vedere [Installazione di Azure Toolkit per Eclipse][].

## Creare un'applicazione Hello World JSP ##

Creare innanzitutto un progetto Java.

*  Avviare Eclipse e nel menu fare clic su **File**, **New** e quindi su **Dynamic Web Project**. Se **Dynamic Web Project** non è elencato tra i progetti disponibili dopo aver fatto clic su **File**, **New**, fare clic su **File**, **New**, **Project**, espandere **Web**, fare clic su **Dynamic Web Project** e quindi su **Next**.
*  Ai fini di questa esercitazione, denominare il progetto **MyHelloWorld**. Assicurarsi di usare questo nome, poiché i passaggi successivi dell'esercitazione prevedono che il file WAR sia denominato MyHelloWorld. L'aspetto della schermata sarà simile al seguente:
    ![][ic589576]
* Fare clic su **Finish**.
* Nella visualizzazione Project Explorer di Eclipse espandere **MyHelloWorld**. Fare clic con il pulsante destro del mouse su **WebContent**, scegliere **New** e quindi fare clic su **JSP File**.
* Nella finestra di dialogo **New JSP File** denominare il file **index.jsp**. Mantenere il nome **MyHelloWorld/WebContent** per la cartella padre, come illustrato di seguito:

    ![][ic659262]
* Per le finalità di questa esercitazione, nella finestra di dialogo **Select JSP Template** selezionare **New JSP File (html)** e fare clic su **Finish**.
* Quando in Eclipse viene aperto il file index.jsp, aggiungere il testo in modo da visualizzare **Hello World!** nell'elemento `<body>` esistente. Il contenuto `<body>` aggiornato risulterà simile al seguente:
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Salvare index.jsp.

## Per distribuire l'applicazione in Azure in modo semplice e rapido ##

Quando un'applicazione Web Java è pronta per il test, è possibile usare la scorciatoia seguente per provarla direttamente nel cloud di Azure.

1. In Project Explorer di Eclipse fare clic su **MyHelloWorld**.
1. Sulla barra degli strumenti di Eclipse, fare clic sul pulsante a discesa **Publish** (Pubblica) e quindi fare clic su **Publish As Azure Cloud Service** (Pubblica come servizio cloud di Azure)
    ![][publishDropdownButton]
1. Se si pubblica l'applicazione in Azure per la prima volta e non si è creato in precedenza un progetto di distribuzione di Azure per l'applicazione, verrà creato automaticamente un progetto di questo tipo. Dovrebbe essere visualizzato il prompt seguente, in cui sono elencati anche il pacchetto JDK e il server applicazioni che verranno distribuiti automaticamente per l'esecuzione dell'applicazione.
    ![][ic789598]

    Questo approccio rapido consente di testare l'applicazione in Azure in modo semplice e rapido senza dover configurare un server o un pacchetto JDK specifico, diverso da quello predefinito. Se si accettano i valori predefiniti, è possibile fare clic su **OK** per continuare con i passaggi successivi.
    Tuttavia, se si desidera modificare il pacchetto JDK o il server applicazioni da usare per l'applicazione, è possibile farlo in un secondo momento modificando il progetto di distribuzione di Azure che è stato creato automaticamente. In alternativa, è possibile fare clic su **Cancel** e leggere la sezione **Informazioni sui progetti di distribuzione di Azure** di questa esercitazione.
1. Nella finestra di dialogo **Publish to Azure**:
    1. Se nell'elenco **Subscription** non è ancora presente alcuna sottoscrizione da selezionare, seguire questa procedura per importare le informazioni sulla sottoscrizione:
        1. Fare clic su **Import from PUBLISH-SETTINGS file**.
        1. Nella finestra di dialogo **Import Subscription Information** fare clic su **Download PUBLISH-SETTINGS File**. Se non ci si è ancora connessi al proprio account Azure, verrà richiesto di farlo. Quindi verrà richiesto di salvare un file di impostazioni di pubblicazione di Azure. Salvarlo nel computer locale.
        1. Sempre nella finestra di dialogo **Import Subscription Information** fare clic sul pulsante **Browse**, selezionare il file di impostazioni di pubblicazione salvato in locale nel passaggio precedente e quindi fare clic su **Open**. La schermata dovrebbe essere simile alla seguente:

            ![][ic644267]
        1. Fare clic su **OK**.
    1. Per **Subscription** selezionare la sottoscrizione che si desidera usare per la distribuzione.
    1. Per **Storage account** selezionare l'account di archiviazione che si desidera usare oppure fare clic su **New** per creare un nuovo account di archiviazione.
    1. Per **Service name** selezionare il servizio cloud che si desidera usare oppure fare clic su **New** per creare un nuovo servizio cloud.
    1. Per **Target OS** selezionare la versione del sistema operativo che si desidera usare per la distribuzione.
    1. Per **Target environment** selezionare, ai fini di questa esercitazione, **Staging**. Quando si è pronti a eseguire la distribuzione nel sito di produzione, sarà necessario impostare questa opzione su **Production**.
    1. Facoltativo: assicurarsi che l'opzione **Overwrite previous deployment** sia selezionata se si desidera che la nuova distribuzione sovrascriva automaticamente la distribuzione precedente. Se si abilita questa opzione, non si verificheranno problemi di "conflitto 409" durante la pubblicazione nello stesso percorso. Si noti che la finestra di dialogo **Publish to Azure** contiene una sezione denominata **Remote Access**. Per impostazione predefinita, l'accesso remoto non è abilitato e non verrà abilitato per questo esempio. Per abilitare l'accesso remoto, immettere un nome utente e una password da usare quando si accede in remoto. Per altre informazioni sull'accesso remoto, vedere [Abilitazione dell'accesso remoto per distribuzioni di Azure in Eclipse][]. Verrà visualizzata una finestra di dialogo **Publish to Azure** simile alla seguente: ![][ic719488]
1. Fare clic su **Publish** per eseguire la pubblicazione nell'ambiente di gestione temporanea. Se viene richiesto di eseguire una build completa, fare clic su **Yes**. Questa operazione può richiedere alcuni minuti nel caso della prima build. Nella scheda **Azure Activity Log** della sezione a schede relativa alle visualizzazioni di Eclipse verrà visualizzato un log. ![][ic719489] È possibile usare questo log e la visualizzazione **Console** per vedere lo stato di avanzamento della distribuzione. In alternativa, è possibile accedere al [portale di gestione di Azure][] e monitorare lo stato mediante la sezione **Servizi cloud**.
1. Dopo aver eseguito la distribuzione, in **Azure Activity Log** verrà visualizzato lo stato **Published**. Fare clic su **Published**, come illustrato nell'immagine seguente, e nel browser verrà aperta un'istanza della distribuzione.
    ![][ic719490]

Poiché si tratta di una distribuzione in un ambiente di gestione temporanea, il formato del nome DNS sarà http://&lt;*guid*&gt;.cloudapp.net e l'URL conterrà il nome DNS e un suffisso relativo all'applicazione, ad esempio: http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (Per la parte **MyHelloWorld** viene fatta distinzione tra maiuscole e minuscole). È possibile vedere il nome DNS anche se si fa clic sul nome della distribuzione nell'area Servizi cloud del portale di gestione della piattaforma Azure.

Anche se questa procedura dettagliata è stata studiata per una distribuzione nell'ambiente di gestione temporanea, una distribuzione nell'ambiente di produzione segue gli stessi passaggi, tranne che nella finestra di dialogo **Publish to Azure** sarà necessario selezionare l'opzione **Production** anziché **Staging** per **Target environment**. In una distribuzione nell'ambiente di produzione l'URL si basa sul nome DNS scelto dall'utente anziché su un GUID come quello usato per la gestione temporanea.

>[AZURE.WARNING] A questo punto l'applicazione Azure è stata distribuita nel cloud. Prima di continuare, tuttavia, tenere presente che per un'applicazione distribuita, anche se non in esecuzione, viene accumulato tempo fatturabile per la sottoscrizione. È quindi estremamente importante eliminare le distribuzioni indesiderate dalla sottoscrizione Azure.

## Informazioni sui progetti di distribuzione di Azure ##

Per distribuire una o più applicazioni Java in Azure, è necessario un progetto di distribuzione di Azure. Questo svolge il ruolo di "pacchetto" in cui le applicazioni devono essere racchiuse per essere pubblicate in Azure.

Oltre alle informazioni relative alle applicazioni, un progetto di distribuzione di Azure contiene anche informazioni sugli altri componenti chiave della distribuzione, principalmente sul contenitore del server applicazioni in cui eseguire l'app Web e sul runtime Java su cui eseguirla. Azure supporta un'ampia gamma di runtime e server applicazioni Java tra cui è possibile scegliere.

Sebbene l'esempio usato in questo argomento sia notevolmente semplificato per scopi didattici, un progetto di distribuzione di Azure può contenere anche altre importanti informazioni di configurazione che consentono di creare con le applicazioni servizi cloud complessi, scalabili, a disponibilità elevata e a più livelli quasi arbitrariamente. È possibile abilitare l'**affinità di sessione ("sessioni permanenti")**, la **memorizzazione rapida nella cache**, il **debug remoto**, la **ripartizione del carico di lavoro SSL**, il **routing firewall/porta**, l'**accesso remoto** e diverse altre potenti funzionalità.

Se si è completata la sezione precedente di questa esercitazione ("Per distribuire l'applicazione in Azure in modo semplice e rapido"), in Esplora progetti verrà visualizzato un nuovo progetto di distribuzione di Azure generato automaticamente con il nome "**MyHelloWorld\_onAzure**".

L'esercitazione può essere stata avviata anche con la creazione di un progetto di distribuzione di Azure vuoto, a cui sono state aggiunte una o più applicazioni. Si tratta di un procedura più lunga, ma che garantisce fin dall'inizio un maggior controllo sulla configurazione iniziale.

Per creare un nuovo progetto di distribuzione di Azure da zero, fare clic sul pulsante **New Azure Deployment Project** ![][ic710876].

Indipendentemente dal fatto che si usi un progetto di distribuzione di Azure esistente o che se ne crei uno da zero, è possibile modificare le impostazioni di configurazione e i componenti, come il pacchetto JDK o il server applicazioni, con uguale facilità in qualsiasi momento.

Per modificare il pacchetto JDK, il server applicazioni o l'elenco di applicazioni in un progetto di distribuzione di Azure esistente:

1. Espandere il nodo del progetto, ad esempio **MyHelloWorld\_onAzure**, in Project Explorer.
2. Fare clic con il pulsante destro del mouse su **WorkerRole1**
3. Espandere il sottomenu **Azure** nel menu di scelta rapida.
4. Fare clic su **Server Configuration**.

Indipendentemente dal fatto che la procedura di configurazione server sia stata avviata con la modifica di un progetto di distribuzione di Azure esistente, come illustrato sopra, o con la creazione di un nuovo progetto da zero, verrà visualizzato lo stesso tipo di finestre di dialogo che consentono di configurare il pacchetto JDK, i componenti server e delle applicazioni. Per altre informazioni su come modificare le impostazioni nelle finestre di dialogo, ad esempio per modificare il pacchetto JDK, il server applicazioni e aggiungere o rimuovere applicazioni in una distribuzione, vedere l'articolo relativo alle [proprietà di configurazione server][].

## Solo Windows: per distribuire l'applicazione nell'emulatore di calcolo ##

>[AZURE.NOTE] L'emulatore di Azure è disponibile solo per Windows. Ignorare questa sezione se si usa un sistema operativo diverso da Windows.

Se si è creato un nuovo progetto di distribuzione di Azure seguendo la procedura descritta sopra, ovvero in modo implicito pubblicando l'applicazione in Azure, il pacchetto JDK e il server applicazioni sono stati configurati per il cloud, ma non per l'emulazione locale. Per preparare il progetto per il test nell'emulatore locale, seguire questa procedura:

1. In Project Explorer di Eclipse fare clic su **MyHelloWorld\_onAzure**.
1. Fare clic con il pulsante destro del mouse su **WorkerRole1**.
1. Espandere il sottomenu **Azure** nel menu di scelta rapida.
1. Fare clic su **Server Configuration**.
1. Nella scheda **JDK** verificare se il toolkit ha preconfigurato automaticamente un JDK locale predefinito. Se non è presente un JDK locale predefinito o se si vuole modificare le impostazioni predefinite, assicurarsi che sia selezionata la casella di controllo **Use the JDK from this file path for testing locally** e che sia specificato il percorso di installazione di JDK da usare. Per modificarlo, fare clic sul pulsante **Browse** e selezionare con gli appositi comandi il percorso di directory del pacchetto JDK da usare.
1. Fare clic sulla scheda **Server**.
1. Nel casella di testo **Local server path**, nella parte inferiore della finestra di dialogo, immettere il percorso di un server installato in locale che corrisponda al tipo e al numero di versione principale del server selezionato nella parte superiore della finestra di dialogo, sotto la casella di controllo **Deploy a server of this type**. Se si vuole usare un tipo o una versione principale del server applicazioni diversi, modificare prima la selezione sotto tale casella di controllo.
1. Fare clic su **OK**.
1. Nella barra degli strumenti di Eclipse fare clic sul pulsante **Run in Azure Emulator**, ![][ic710879]. Se il pulsante **Run in Azure Emulator** non è abilitato, verificare che **MyHelloWorld\_onAzure** sia selezionato in Project Explorer di Eclipse e assicurarsi che Project Explorer di Eclipse abbia lo stato attivo come finestra corrente. Verrà avviata prima una build completa del progetto e quindi verrà avviata l'applicazione Web Java nell'emulatore di calcolo. Si noti che a seconda delle caratteristiche delle prestazioni del computer, la prima build può richiedere da alcuni secondi fino ad alcuni minuti, mentre le build successive risulteranno più rapide. Una volta completato il passaggio relativo alla prima build, Controllo dell'account utente di Windows richiederà di consentire l'applicazione di apportare modifiche al computer in uso. Fare clic su **Yes**.

>[AZURE.IMPORTANT] Se non viene visualizzato il prompt di Controllo dell'account utente, verificare che sulla barra delle applicazioni di Windows sia presente l'icona corrispondente e selezionarla prima. Talvolta il prompt di Controllo dell'account utente non viene visualizzato come finestra in primo piano, ma è visibile solo come icona sulla barra delle applicazioni.

1. Esaminare l'output dell'interfaccia utente dell'emulatore di calcolo per determinare se sono presenti errori nel progetto. In base al contenuto della distribuzione, l'avvio completo dell'applicazione all'interno dell'emulatore di calcolo potrebbe richiedere alcuni minuti.
1. Avviare il browser e usare l'URL `http://localhost:8080/MyHelloWorld` come indirizzo (per la parte `MyHelloWorld` dell'URL viene fatta distinzione tra maiuscole e minuscole). L'applicazione MyHelloWorld (output di index.jsp) dovrebbe essere visualizzata in modo simile all'immagine seguente:

    ![][ic589579]

Quando si è pronti per arrestare l'esecuzione dell'applicazione nell'emulatore di calcolo, nella barra degli strumenti di Eclipse fare clic sul pulsante **Reset Azure Emulator**, ![][ic710880].

## Per eliminare la distribuzione ##

Per eliminare la distribuzione in Azure Toolkit per Eclipse, assicurarsi che Project Explorer di Eclipse sia la finestra attiva e che sia selezionato **MyHelloWorld\_onAzure** quindi fare clic sul pulsante **Unpublish** ![][ic710883] nella barra degli strumenti di Eclipse. È possibile effettuare la stessa operazione facendo clic con il pulsante destro del mouse su **MyHelloWorld\_onAzure** in Project Explorer di Eclipse, facendo clic su **Azure** e quindi su **Undeploy from Azure Cloud**. Verrà visualizzata la finestra di dialogo **Unpublish Azure Project**.

![][ic719491]

Selezionare la sottoscrizione e il servizio cloud contenente la distribuzione, selezionare la distribuzione che si desidera eliminare, quindi fare clic su **Unpublish**.

In alternativa all'uso del toolkit per eliminare la distribuzione, è possibile usare la sezione **Servizi cloud** del portale di gestione di Azure: passare alla distribuzione desiderata, selezionarla e fare clic sul pulsante **Elimina**. In questo modo la distribuzione viene arrestata e quindi eliminata. Se si desidera semplicemente arrestare la distribuzione e non eliminarla, fare clic sul pulsante **Interrompi** invece che sul pulsante **Elimina**. In questo caso però, come già indicato, se non si elimina la distribuzione si accumuleranno le spese fatturabili anche se la distribuzione viene arrestata.

## Vedere anche ##

[Toolkit di Azure per Eclipse][]

[Installazione di Azure Toolkit per Eclipse][]

[Novità di Azure Toolkit per Eclipse][]

Per altre informazioni sull'uso di Azure con Java, visitare il [centro per sviluppatori Java di Azure][].

<!-- URL List -->

[centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[portale di gestione di Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Abilitazione dell'accesso remoto per distribuzioni di Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Installazione di Azure Toolkit per Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[proprietà di configurazione server]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Novità di Azure Toolkit per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png

<!---HONumber=AcomDC_0629_2016-->