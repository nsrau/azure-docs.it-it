<properties urldisplayname="Team Foundation Service" headerexpose="" pageTitle="Profilatura di un servizio cloud in locale nell'emulatore di calcolo" metakeywords="" footerexpose="" description="" umbraconavihide="0" disquscomments="1" title="Testing the Performance of a Cloud Service Locally in the Azure Compute Emulator Using the Visual Studio Profiler" authors="kempb" manager="douge" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="12/3/2014" ms.author="kempb" />

# Test locale delle prestazioni di un servizio cloud nell'emulatore di calcolo di Azure mediante il profiler di Visual Studio

È possibile usare diversi strumenti e tecniche per il test delle prestazioni di servizi cloud.
Quando si pubblica un servizio cloud in Azure, è possibile impostare Visual Studio per la raccolta di dati di profilatura e quindi per l'analisi locale di tali dati, come illustrato nella pagina relativa alla [profilatura di un'applicazione Azure][1].
È anche possibile usare gli strumenti di diagnostica per tenere traccia di diversi contatori di prestazioni, come illustrato in [Uso dei contatori di prestazioni in Azure][2].
È inoltre consigliabile eseguire localmente la profilatura dell'applicazione nell'emulatore di calcolo prima di distribuirla nel cloud.

In questo articolo viene illustrato il metodo Campionamento CPU per la profilatura, che può essere eseguito localmente nell'emulatore. Si tratta di un metodo di profilatura non eccessivamente invasivo. Il profiler salva uno snapshot dello stack di chiamate in base a intervalli di campionamento specificati. I dati vengono raccolti per un determinato periodo di tempo e vengono visualizzati in un rapporto. Questo metodo di profilatura tende a indicare la posizione in cui viene eseguita la maggior parte del lavoro della CPU in un'applicazione a elevato uso di calcolo.  Ciò consente di focalizzare l'attenzione sul "percorso critico" in cui l'applicazione trascorre la maggior parte del tempo.


## Prerequisiti

È possibile eseguire il profiler localmente solo se si dispone di Visual Studio Premium o Visual Studio Ultimate. 

## Contenuto dell'articolo:

-   [Passaggio 1: Configurare Visual Studio per la profilatura][]

-   [Passaggio 2: Connettersi a un processo][]

-   [Passaggio 3: Visualizzare i rapporti relativi alle prestazioni][]

-   [Passaggio 4: Apportare modifiche e confrontare le prestazioni][]

-   [Risoluzione dei problemi][]

-   [Passaggi successivi][]

## <a name="step1"> </a> Passaggio 1: Configurare Visual Studio per la profilatura

Visual Studio include alcune opzioni di configurazione che possono risultare utili per la profilatura. Per rendere comprensibili i rapporti di profilatura, saranno necessari simboli (file con estensione pdb) per l'applicazione, oltre a simboli per le librerie di sistema. È necessario assicurarsi di fare riferimento ai server dei simboli disponibili. A tale scopo, dal menu **Strumenti** in Visual Studio scegliere **Opzioni**, quindi **Debug** e infine **Simboli**. Verificare che Server dei simboli Microsoft sia elencato in **Percorsi dei file di simboli (pdb)**.  È inoltre possibile fare riferimento a http://referencesource.microsoft.com/symbols, dove potrebbero essere disponibili file di simboli aggiuntivi.

![][4]

Se lo si desidera, è possibile semplificare i rapporti generati dal profiler impostando Just My Code. Se Just My Code è abilitato, gli stack di chiamate funzione vengono semplificati, in modo che le chiamate interamente interne alle librerie e a .NET Framework non vengano visualizzate nei rapporti. Scegliere **Opzioni** dal menu **Strumenti**. Espandere quindi il nodo **Strumenti per le prestazioni** e scegliere **Generale**. Selezionare la casella di controllo **Abilita Just My Code per i rapporti del profiler**.

![][17]

Queste istruzioni sono applicabili a un progetto esistente o a un nuovo progetto.  Se si crea un nuovo progetto per provare ad applicare le tecniche illustrate di seguito, scegliere un progetto **Servizi cloud di Azure** in C# e quindi selezionare un **Ruolo Web** e un **Ruolo di lavoro**.

![][5]

A scopo esemplificativo, aggiungere al progetto codice la cui esecuzione richiede
molto tempo e che illustri alcuni problemi ovvi relativi alle prestazioni. Aggiungere ad esempio il codice seguente a un progetto di tipo ruolo di lavoro:

	public class Concatenator
	{
	    public static string Concatenate(int number)
	    {
	        int count;
	        string s = "";
	        for (count = 0; count < number; count++)
	        {
	            s += "\n" + count.ToString();
	        }
	        return s;
	    }
	}

Chiamare tale codice dal metodo RunAsync nella classe del ruolo di lavoro derivata da RoleEntryPoint. (Ignorare l'avviso relativo al metodo eseguito in modalità sincrona).

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Compilare ed eseguire localmente il servizio cloud senza eseguire il debug (CTRL+F5) con la configurazione della soluzione impostata su **Release**. In tale modo, tutti i file e le cartelle verranno creati per l'esecuzione locale dell'applicazione e tutti gli emulatori verranno avviati. Avviare l'interfaccia utente dell'emulatore di calcolo dalla barra delle applicazioni per verificare che il ruolo di lavoro sia in esecuzione.

## <a name="step2"> </a> Passaggio 2: Connettersi a un processo

Invece di eseguire la profilatura dell'applicazione avviandola dall'IDE di Visual Studio 2010, è necessario connettere il profiler a un processo in esecuzione. 

A tale scopo, scegliere **Profiler** dal menu **Analizza**, quindi fare clic su **Connetti/Disconnetti**.

![][6]

Per un ruolo di lavoro individuare il processo WaWorkerHost.exe.

![][7]

Se la cartella del progetto si trova su un'unità di rete, il profiler richiederà di specificare un percorso diverso per il salvataggio dei rapporti di profilatura.

 È anche possibile connettersi a un ruolo Web mediante la connessione a WaIISHost.exe.
 Se l'applicazione include più processi di ruolo di lavoro, sarà necessario usare il valore processID per distinguerli. È possibile eseguire query relative ai valori processID a livello di codice, mediante l'accesso all'oggetto Process. Se, ad esempio, si aggiunge il codice seguente al metodo Run della classe derivata da RoleEntryPoint in un ruolo, sarà possibile esaminare il log nell'interfaccia utente dell'emulatore di calcolo per individuare i processi a cui connettersi.

	var process = System.Diagnostics.Process.GetCurrentProcess();
	var message = String.Format("Process ID: {0}", process.Id);
	Trace.WriteLine(message, "Information");

Per visualizzare il log, avviare l'interfaccia utente dell'emulatore di calcolo.

![][8]

Aprire la finestra della console del log del ruolo di lavoro nell'interfaccia utente dell'emulatore di calcolo, facendo clic sulla barra del titolo nella finestra della console. Nel log è possibile verificare l'ID dei processi.

![][9]

Dopo la connessione, eseguire la procedura nell'interfaccia utente dell'applicazione, se necessario, per riprodurre lo scenario.

Per interrompere la profilatura, scegliere il collegamento **Interrompi la profilatura**.

![][10]

## <a name="step3"> </a> Passaggio 3: Visualizzare i rapporti relativi alle prestazioni

Viene visualizzato il rapporto relativo alle prestazioni dell'applicazione.

A questo punto, l'esecuzione del profiler viene interrotta, i dati vengono salvati in un file con estensione vsp e viene visualizzato un report
che include un'analisi di tali dati.

![][11]


Se nel percorso critico è visibile il file String.wstrcpy, fare clic su Just My Code per modificare la visualizzazione, in modo da mostrare solo il codice utente.  Se è visualizzato String.Concat, provare a selezionare il pulsante Mostra tutto il codice.

Come si può notare, il metodo Concatenate e String.Concat richiedono una parte significativa del tempo di esecuzione.

![][12]

Se è stato aggiunto il codice di concatenazione di stringa disponibile in questo articolo, nell'Elenco attività dovrebbe essere visualizzato un avviso corrispondente. È anche possibile che venga visualizzato un avviso relativo a una quantità eccessiva di Garbage Collection, dovuta al numero di stringhe eliminate.

![][14]

## <a name="step4"> </a> Passaggio 4: Apportare modifiche e confrontare le prestazioni

È anche possibile confrontare le prestazioni prima e dopo la modifica del codice.  Arrestare il processo in esecuzione e modificare il codice per sostituire l'operazione di concatenazione di stringa usando StringBuilder:

	public static string Concatenate(int number)
	{
	    int count;
	    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
	    for (count = 0; count < number; count++)
	    {
	         builder.Append("\n" + count.ToString());
	    }
	    return builder.ToString();
	}

Eseguire di nuovo la verifica delle prestazioni e quindi confrontare le prestazioni. Se le esecuzioni si trovano nella stessa sessione, in Esplora prestazioni è possibile selezionare entrambi i report, aprire il menu di scelta rapida e quindi scegliere **Confronta rapporto di prestazioni**. Se si desidera effettuare un confronto con un'esecuzione in un'altra sessione relativa alle prestazioni, aprire il menu **Analizza** e quindi scegliere **Confronta rapporto di prestazioni**. Specificare entrambi i file nella casella della finestra di dialogo visualizzata.

![][15]

Nei rapporti vengono evidenziate le differenze tra le due esecuzioni.

![][16]

A questo punto sono state eseguite le operazioni preliminari con il profiler.

## <a name="troubleshooting"> </a> Risoluzione dei problemi

- Assicurarsi di eseguire la profilatura di una compilazione di rilascio e avviarla senza eseguire il debug.

- Se l'opzione Connetti/Disconnetti non è abilitata nel menu Profiler, eseguire la Creazione guidata sessione di prestazioni.

- Usare l'interfaccia utente dell'emulatore di calcolo per visualizzare lo stato dell'applicazione. 

- Se si verificano problemi di avvio delle applicazioni nell'emulatore o problemi di connessione del profiler, arrestare l'emulatore di calcolo e riavviarlo. Se il problema persiste, provare a riavviare il sistema. È possibile che questo problema si verifichi se si usa l'emulatore di calcolo per sospendere e rimuovere distribuzioni in esecuzione.

- Se sono stati usati comandi relativi alla profilatura dalla
riga di comando, in particolare le impostazioni globali, assicurarsi che sia stata effettuata la chiamata a VSPerfClrEnv /globaloff e che VsPerfMon.exe sia stato arrestato.

- Se durante il campionamento viene visualizzato il messaggio "PRF0025: Dati non raccolti", verificare che nel processo a cui ci si è connessi sia presente attività della CPU. È possibile che le applicazioni che non eseguono attività di calcolo non producano dati di campionamento.  È inoltre possibile che il processo sia stato chiuso prima dell'esecuzione del campionamento. Verificare che il metodo Run di un ruolo da sottoporre a profilatura non preveda la terminazione.

## <a name="nextSteps"> </a> Passaggi successivi

La strumentazione dei file binari di Azure nell'emulatore non è supportata nel profiler di Visual Studio 2010. Se tuttavia si desidera testare l'allocazione della memoria, è possibile scegliere tale opzione durante la profilatura. È inoltre possibile scegliere la profilatura della concorrenza, che consente di determinare se i thread sprecano tempo nel tentativo di ottenere blocchi, oppure la profilatura di interazioni tra livelli, che consente di tenere traccia dei problemi di prestazioni durante l'interazione tra livelli di un'applicazione, nella maggior parte dei casi tra il livello dati e il ruolo di lavoro.  È possibile visualizzare le query di database generate dall'applicazione e usare i dati di profilatura per ottimizzare l'uso del database. Per informazioni sulla profilatura dell'interazione tra livelli, vedere [Procedura dettagliata: Uso del profiler relativo alle interazioni tra livelli in Visual Studio Team System 2010][3].


[Passaggio 1: Configurare Visual Studio per la profilatura]: #step1
[Passaggio 2: Connettersi a un processo]: #step2
[Passaggio 3: Visualizzare i rapporti relativi alle prestazioni]: #step3
[Passaggio 4: Apportare modifiche e confrontare le prestazioni]: #step4
[Risoluzione dei problemi]: #troubleshooting
[Passaggi successivi]: #nextSteps

[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png

<!--HONumber=35.1-->
