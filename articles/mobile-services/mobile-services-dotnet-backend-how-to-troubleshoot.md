<properties
	pageTitle="Risolvere i problemi del back-end .NET di Servizi mobili | Microsoft Azure"
	description="Informazioni su come diagnosticare e correggere problemi con Servizi mobili tramite il back-end .NET"
	services="mobile-services"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016" 
	ms.author="wesmc;ricksal"/>

# Risolvere i problemi del back-end .NET di Servizi mobili

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Lo sviluppo con Servizi mobili è di solito facile e senza problemi, ma talvolta qualcosa può andare storto. Questa esercitazione illustra alcune tecniche che consentono di risolvere i problemi comuni che possono presentarsi con il back-end .NET di Servizi mobili.

1. [Debug HTTP](#HttpDebugging)
2. [Debug del runtime](#RuntimeDebugging)
3. [Analisi dei log di diagnostica](#Logs)
4. [Debug della risoluzione dell'assembly cloud](#AssemblyResolution)
5. [Risoluzione dei problemi relativi alle migrazioni di Entity Framework](#EFMigrations)

<a name="HttpDebugging"></a>
## Debug HTTP

Quando si sviluppano app con Servizi mobili, di solito si sfrutta l'SDK del client di Servizi mobili per la piattaforma in uso (Windows Store, iOS, Android, ecc.). A volte, però, è utile scendere fino al livello HTTP e osservare le chiamate non elaborate durante la loro esecuzione in rete. Questo approccio è particolarmente utile durante il debug dei problemi di connettività e serializzazione. Con il back-end .NET di Servizi mobili è possibile usare questo approccio in combinazione con il debug locale e remoto di Visual Studio (di cui si discuterà più ampiamente nella sezione successiva) per farsi un'idea globale del percorso effettuato da una chiamata HTTP prima di richiamare il codice di servizio.

È possibile usare qualsiasi debugger HTTP per inviare e ispezionare il traffico HTTP. [Fiddler](http://www.telerik.com/fiddler) è un popolare strumento comunemente usato dagli sviluppatori a tale scopo. Per facilitare l'operato degli sviluppatori, Servizi mobili aggrega un debugger HTTP basato su Web (chiamato anche "client di prova") direttamente al servizio mobile, riducendo la necessità di strumenti esterni. Quando il servizio mobile è ospitato in locale, sarà disponibile a un URI simile a [http://localhost:59233](http://localhost:59233), mentre quando è ospitato nel cloud, l'URI sarà nel formato [http://todo-list.azure-mobile.net](http://todo-list.azure-mobile.net). I passaggi seguenti funzionano in maniera analoga, indipendentemente dalla posizione in cui è ospitato il servizio:

1. Per iniziare, aprire un progetto del server di Servizi mobili in **Visual Studio 2013 Update 2** o versione successiva. Se non è disponibile alcun progetto, è possibile crearne uno selezionando **File**, **Nuovo**, **Progetto**, quindi il nodo **Cloud** e infine il modello **Servizi mobili di Microsoft Azure**.
2. Premere **F5** per avviare la compilazione e l'esecuzione del progetto. Nella pagina iniziale, selezionare **Prova**.

    >[AZURE.NOTE]
    Se il servizio è ospitato in locale, fare clic sul collegamento per essere reindirizzati alla pagina successiva. Se invece il servizio è ospitato nel cloud, verrà richiesto un set di credenziali, al fine di garantire che gli utenti non autenticati non possano accedere alle informazioni sull'API e i payload. Per poter visualizzare la pagina è necessario eseguire l'accesso lasciando il **nome utente vuoto** e usando la **chiave dell'applicazione** come password. La chiave dell'applicazione è disponibile nel portale di Azure classico passando alla scheda **Dashboard** relativa al proprio servizio mobile e selezionando **Gestisci chiavi**.
    >
    > ![Prompt di autenticazione per accedere alla pagina della guida][HelpPageAuth]

3. La pagina visualizzata (definita "pagina della guida") contiene un elenco di tutte le API HTTP rese disponibili dal servizio mobile. Selezionare una delle API (se si è iniziato a usare il modello di progetto di Servizi mobili in Visual Studio, vedere **GET tables/TodoItem**).

    ![Pagina della guida][HelpPage]

4. La pagina risultante contiene tutta la documentazione e gli esempi JSON della richiesta, oltre alla risposta prevista dall'API. Selezionare il pulsante **prova**.

    ![Pagina di prova per un'API][HelpPageApi]

5. Questo è il "client di prova", che consente di inviare una richiesta HTTP per provare l'API. Selezionare **Invia**.

    ![Client di prova][TestClient]

6. Verrà visualizzata la risposta HTTP proveniente dal servizio mobile direttamente nella finestra del browser.

    ![Client di prova con risposta HTTP][TestClientResponse]

Ora è possibile esplorare le diverse API HTTP esposte dal servizio mobile con la comodità del proprio Web browser.

<a name="RuntimeDebugging"></a>
## Debug del runtime

Una delle principali funzionalità del back-end .NET è la capacità di eseguire il debug del codice di servizio a livello locale, ma anche di eseguire il debug del codice eseguito dinamicamente nell'ambiente cloud. A tale scopo, seguire questa procedura:

1. Aprire il progetto del servizio mobile di cui si vuole eseguire il debug in **Visual Studio 2013 Update 2** o versione successiva.
2. Configurare il caricamento del simbolo. Passare al menu **Debug** e selezionare **Opzioni e impostazioni**. Assicurarsi che l'opzione **Abilita Just My Code** sia deselezionata e che l'opzione **Abilita il supporto del server di origine** sia selezionata.

    ![Configurare il caricamento del simbolo][SymbolLoading]

3. Selezionare il nodo **Simboli** a sinistra e aggiungere un riferimento al server [SymbolSource] usando l'URI [http://srv.symbolsource.org/pdb/Public](http://srv.symbolsource.org/pdb/Public). I simboli per il back-end .NET di Servizi mobili sono resi disponibili in ogni nuova versione.

    ![Configurare il server dei simboli][SymbolServer]

4. Impostare un punto di interruzione nel frammento di codice di cui si vuole eseguire il debug. Ad esempio, impostare un punto di interruzione nel metodo **GetAllTodoItems()** di **TodoItemController** presente nel modello di progetto di Servizi mobili in Visual Studio.
5. Eseguire il debug del servizio in locale premendo **F5**. Il primo carico potrebbe essere lento in quanto Visual Studio scarica i simboli relativi al back-end .NET di Servizi mobili.
6. Come descritto nella sezione precedente relativa al debug HTTP, usare il cliente di prova per inviare una richiesta HTTP al metodo in cui è stato impostato il punto di interruzione. Ad esempio, è possibile inviare una richiesta al metodo **GetAllTodoItems()** selezionando **GET tables/TodoItem** nella pagina della guida, quindi selezionando **prova** e quindi **invia**.
7. Visual Studio dovrebbe interrompersi in corrispondenza del punto di interruzione impostato e nella finestra **Stack di chiamate** in Visual Studio dovrebbe essere disponibile un'analisi dello stack completa con il codice sorgente.

    ![Raggiungimento di un punto di interruzione][Breakpoint]

8. È ora possibile pubblicare il servizio in Azure, in modo che sia possibile eseguire il debug proprio come in precedenza. Pubblicare il progetto facendo clic con il pulsante destro del mouse su di esso in **Esplora soluzioni**, quindi scegliere **Pubblica**.
9. Nella scheda **Impostazioni della **procedura guidata di pubblicazione, selezionare la configurazione **Debug**. Ciò garantisce che i simboli di debug pertinenti siano pubblicati assieme al codice.

    ![Debug di pubblicazione][PublishDebug]

10. Dopo aver pubblicato correttamente il servizio, aprire **Esplora server** ed espandere i nodi **Azure** e **Servizi mobili**. Se necessario, eseguire l'accesso.
11. Fare clic con il pulsante destro del mouse sul servizio mobile appena pubblicato e selezionare **Collega debugger**.

    ![Collegamento del debugger][AttachDebugger]

12. Proprio come già fatto al passaggio 6, inviare una richiesta HTTP per richiamare il metodo in cui è stato impostato il punto di interruzione. Stavolta, però, usare la pagina della guida e il client di prova per il servizio mobile ospitato su Azure.
13. Visual Studio si interromperà nel punto di interruzione.

È ora possibile accedere alla potenza completa del debugger di Visual Studio quando si sviluppa codice in locale e rispetto al servizio mobile dinamico in Azure.

<a name="Logs"></a>
## Analisi dei log di diagnostica

Quando gestisce le richieste inviate dai clienti, il servizio mobile genera una serie di utili informazioni diagnostiche, acquisendo anche eventuali eccezioni riscontrate. Oltre a questo, è possibile instrumentare il codice del controller con log aggiuntivi sfruttando la proprietà [**Log**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx) disponibile nella proprietà [**Services**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx) di ogni [**TableController**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx).

Quando si esegue il debug in locale, i log saranno visualizzati nella finestra di **output** in Visual Studio.

![Log nella finestra di output in Visual Studio][LogsOutputWindow]

Dopo aver pubblicato il servizio in Azure, i log relativi all'istanza di servizio in esecuzione nel cloud saranno disponibili facendo clic con il pulsante destro del mouse sul servizio mobile nella finestra **Esplora server** di Visual Studio, quindi selezionando **Visualizza log**.

![Log in Esplora server di Visual Studio][LogsServerExplorer]

Gli stessi log sono disponibili anche sul portale di Azure classico, nella scheda **Log** relativa al proprio servizio mobile.

![Log nel portale di Azure classico][LogsPortal]

<a name="AssemblyResolution"></a>
## Debug della risoluzione dell'assembly cloud

Quando viene pubblicato su Azure, il servizio mobile viene caricato dall'ambiente host di Servizi mobili, che garantisce aggiornamenti e patch uniformi della pipeline HTTP che ospita il codice del controller. Ciò include tutti gli assembly a cui fanno riferimento i pacchetti [NuGet del back-end .NET](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22): il team aggiorna costantemente il servizio in modo da usare le versioni più recenti degli assembly.

Talvolta è possibile introdurre conflitti di controllo delle versioni facendo riferimento a *diverse versioni principali* di assembly obbligatori (sono consentite diverse versioni *minori*). Ciò accade di frequente quando NuGet richiede di eseguire l'aggiornamento alla versione più recente di uno dei pacchetti usati dal back-end .NET di Servizi mobili.

>[AZURE.NOTE] Servizi mobili è attualmente compatibile solo con ASP.NET 5.1, mentre ASP.NET 5.2 non è attualmente supportato. L'aggiornamento dei pacchetti NuGet ASP.NET alla versione 5.2.* può generare un errore dopo la distribuzione.

Se si effettua l'aggiornamento di uno qualsiasi di questi pacchetti, quando si pubblica il servizio aggiornato su Azure verrà visualizzata una pagina di avviso che indica il conflitto:

![Pagina della guida indicante il conflitto di caricamento dell'assembly][HelpConflict]

Verrà anche registrato nei log di servizio un messaggio di eccezione simile al seguente:

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

Questo problema è facilmente risolvibile: basta ripristinare una versione supportata dell'assembly obbligatorio e ripubblicare il servizio.

<a name="EFMigrations"></a>
## Risoluzione dei problemi relativi alle migrazioni di Entity Framework

Quando si usa il back-end .NET di Servizi mobili con un database SQL, Entity Framework (EF) viene usato come tecnologia di accesso ai dati che consente di eseguire query sul database e mantenere oggetti al suo interno. Uno degli aspetti importanti che EF gestisce per conto dello sviluppatore è il modo in cui le colonne del database (noto anche come *schema*) cambiano in base alle classi del modello specificate nella modifica del codice. Questo processo è noto come [Migrazioni Code First](http://msdn.microsoft.com/data/jj591621).

Le migrazioni possono essere complesse e richiedere di mantenere lo stato del database sincronizzato con il modello EF per ottenere un esito positivo. Per istruzioni su come gestire le migrazioni con il servizio mobile e i possibili errori che possono verificarsi, vedere [Come modificare un modello di dati in un servizio mobile back-end .NET](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

<!-- IMAGES -->

[HelpPageAuth]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
[HelpPage]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
[HelpPageApi]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
[TestClient]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
[TestClientResponse]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
[SymbolLoading]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
[SymbolServer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
[Breakpoint]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
[PublishDebug]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
[AttachDebugger]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
[LogsOutputWindow]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
[LogsServerExplorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
[LogsPortal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
[HelpConflict]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png


<!-- Links -->
[SymbolSource]: http://symbolsource.org

<!---HONumber=AcomDC_0211_2016-->