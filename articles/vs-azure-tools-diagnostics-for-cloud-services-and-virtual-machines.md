<properties
   pageTitle="Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure | Microsoft Azure"
   description="Illustra come configurare le informazioni di diagnostica per il debug di servizi cloud e delle macchine virtuali di Azure in Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/30/2016"
   ms.author="tarcher" />

# Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure

Per risolvere gli errori di un servizio cloud di Azure o di una macchina virtuale di Azure, è possibile configurare Diagnostica di Azure con maggiore semplicità tramite Visual Studio. Diagnostica di Azure acquisisce i dati di sistema e i dati di registrazione nelle macchine virtuali e nelle istanze di macchine virtuali che eseguono il servizio cloud e trasferisce i dati all'account di archiviazione scelto. Per altre informazioni sulla registrazione di diagnostica in Azure, vedere [Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure](./app-service-web/web-sites-enable-diagnostic-log.md).

Questo argomento illustra come abilitare e configurare la Diagnostica di Azure in Visual Studio, prima e dopo la distribuzione, oltre che nelle macchine virtuali di Azure. Illustra anche come selezionare i tipi di informazioni di diagnostica da raccogliere e come visualizzare le informazioni dopo la raccolta.

È possibile configurare la Diagnostica di Azure nei modi seguenti:

- È possibile modificare le impostazioni di configurazione della diagnostica tramite la finestra di dialogo **Configurazione di diagnostica** in Visual Studio. Le impostazioni vengono salvate in un file denominato diagnostics.wadcfgx (diagnostics.wadcfg in Azure SDK 2.4 o versioni precedenti). In alternativa, è possibile modificare direttamente il file di configurazione. Se si aggiorna manualmente il file, le modifiche alla configurazione verranno applicate alla successiva distribuzione del servizio cloud in Azure alla successiva esecuzione del servizio nell'emulatore.

- Usare **Cloud Explorer** o **Esplora server** in Visual Studio per modificare le impostazioni di diagnostica per un servizio cloud o una macchina virtuale in esecuzione.

## Modifiche alla diagnostica di Azure 2.6

Sono state apportate le modifiche seguenti ai progetti Azure SDK 2.6 in Visual Studio. Queste modifiche sono applicabili anche alle versioni successive di Azure SDK.

- L'emulatore locale supporta ora la diagnostica. È quindi possibile raccogliere dati di diagnostica e assicurare che l'applicazione crei le tracce corrette durante lo sviluppo e il testing in Visual Studio. La stringa di connessione `UseDevelopmentStorage=true` abilita la raccolta di dati di diagnostica durante l'esecuzione del progetto servizio cloud in Visual Studio mediante l'emulatore di archiviazione di Azure. Tutti i dati di diagnostica vengono raccolti nell'account di archiviazione (archivio di sviluppo).

- La stringa di connessione dell'account di archiviazione di diagnostica (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) viene archiviata ancora una volta nel file di configurazione (.cscfg) del servizio. In Azure SDK 2.5 l'account di archiviazione di diagnostica viene specificato nel file diagnostics.wadcfgx.

Il funzionamento della stringa di connessione presenta alcune differenze sostanziali tra Azure SDK 2.4 e versioni precedenti e Azure SDK 2.6 e versioni successive.

- In Azure SDK 2.4 e versioni precedenti la stringa di connessione viene usata come runtime dal plug-in di diagnostica per ottenere le informazioni sull'account di archiviazione per il trasferimento dei log di diagnostica.

- In Azure SDK 2.6 e versioni successive la stringa di connessione di diagnostica viene usata da Visual Studio per configurare l'estensione di diagnostica con le informazioni appropriate sull'account di archiviazione durante la pubblicazione. La stringa di connessione consente di definire diversi account di archiviazione per diverse configurazioni del servizio, che verranno usate da Visual Studio durante la pubblicazione. Poiché, tuttavia, il plug-in di diagnostica non è più disponibile (dopo Azure SDK 2.5), il solo file .cscfg non è in grado di abilitare l'estensione di diagnostica. È necessario abilitare l'estensione separatamente tramite strumenti quali Visual Studio o PowerShell.

- Per semplificare il processo di configurazione dell'estensione di diagnostica con PowerShell, l'output del pacchetto da Visual Studio contiene anche il codice XML di configurazione pubblica per l'estensione di diagnostica per ogni ruolo. Visual Studio usa la stringa di connessione di diagnostica per popolare le informazioni sull'account di archiviazione presenti nella configurazione pubblica. I file di configurazione pubblica vengono creati nella cartella Extensions e seguono il modello PaaSDiagnostics.<RoleName>.PubConfig.xml. Eventuali distribuzioni basate su PowerShell possono usare questo modello per il mapping di ogni configurazione a un ruolo.

- La stringa di connessione nel file .cscfg viene usata anche dal portale di Azure per accedere ai dati di diagnostica, per consentirne la visualizzazione nella scheda **Monitoraggio**. La stringa di connessione è necessaria per configurare il servizio, in modo da visualizzare i dati dettagliati del monitoraggio nel portale.

## Migrazione di progetti in Azure SDK 2.6 e versioni successive

Quando si esegue la migrazione da Azure SDK 2.5 ad Azure SDK 2.6 o versioni successivi, eventuali account di archiviazione di diagnostica specificati nel file con estensione wadcfgx rimarranno in tale posizione. Per sfruttare i vantaggi derivanti dalla flessibilità che consente di usare diversi account di archiviazione per diverse configurazioni di archiviazione, sarà necessario aggiungere manualmente la stringa di connessione al progetto. Se si esegue la migrazione di un progetto da Azure SDK 2.4 o versioni precedenti ad Azure SDK 2.6, le stringhe di connessione di diagnostica verranno conservate. Occorre tuttavia notare le modifiche relative al modo in cui le stringhe di connessione vengono gestite in Azure SDK 2.6, come illustrato nella sezione precedente.

### Modalità di determinazione dell'account di archiviazione di diagnostica da parte di Visual Studio

- Se nel file .cscfg viene specificata una stringa di connessione di diagnostica, Visual Studio la userà per configurare l'estensione di diagnostica durante la pubblicazione e durante la generazione dei file XML di configurazione pubblica nel corso della creazione dei pacchetti.

- Se nel file .cscfg non viene specificata alcuna stringa di connessione di diagnostica, Visual Studio userà di nuovo l'account di archiviazione specificato nel file con estensione wadcfgx per configurare l'estensione di diagnostica durante la pubblicazione e durante la generazione dei file XML di configurazione pubblica nel corso della creazione dei pacchetti.

- La stringa di connessione di diagnostica nel file .cscfg ha la priorità rispetto all'account di archiviazione specificato nel file con estensione wadcfgx. Se nel file .cscfg viene specificata una stringa di connessione di diagnostica, Visual Studio userà tale stringa e ignorerà l'account di archiviazione specificato nel file con estensione wadcfgx.

### Funzione della casella di controllo "Aggiorna stringhe di connessione risorsa di archiviazione sviluppo…"

La casella di controllo **Aggiorna le stringhe di connessione dell'archivio di sviluppo per la diagnostica e la memorizzazione nella cache con le credenziali dell'account di archiviazione di Microsoft Azure quando si esegue la pubblicazione in Microsoft Azure** offre un modo semplice per aggiornare eventuali stringhe di connessione dell'account di archiviazione con l'account di archiviazione di Azure specificato durante la pubblicazione.

Ad esempio, si supponga di selezionare questa casella di controllo e che la stringa di connessione di diagnostica specifichi `UseDevelopmentStorage=true`. Quando si pubblica il progetto in Azure, Visual Studio aggiornerà automaticamente la stringa di connessione di diagnostica con l'account di archiviazione specificato nella Pubblicazione guidata. Se, tuttavia, un account di archiviazione effettivo è stato specificato come stringa di connessione di diagnostica, verrà usato invece tale account.

## Differenze della funzionalità di diagnostica tra Azure SDK 2.4 e versioni precedenti e Azure SDK 2.5 e versioni successive

Se si aggiorna il progetto da Azure SDK 2.4 ad Azure SDK 2.5 o versioni successive, è consigliabile ricordare le differenze seguenti a livello di funzionalità di diagnostica.

- **Le API di configurazione sono deprecate**: la configurazione a livello di codice della diagnostica è disponibile in Azure SDK 2.4 o versioni precedenti, ma è deprecata in Azure SDK 2.5 e versioni successive. Se la configurazione di diagnostica è attualmente definita nel codice, sarà necessario riconfigurare completamente tali impostazioni nel progetto sottoposto a migrazione, in modo da mantenere il funzionamento della diagnostica. Il file di configurazione della diagnostica per Azure SDK 2.4 è diagnostics.wadcfg, mentre è diagnostics.wadcfgx per Azure SDK 2.5 e versioni successive.

- **La diagnostica per le applicazioni del servizio cloud può essere configurata solo a livello di ruolo, non a livello di istanza.**

- **Ogni volta che si distribuisce l'app, le configurazioni di diagnostica vengono aggiornate**: ciò può provocare problemi di parità se si modifica la configurazione di diagnostica da Esplora server e quindi si ridistribuisce l'app.

- **In Azure SDK 2.5 e versioni successive i dump di arresto anomalo del sistema sono configurati nel file di configurazione, non nel codice**: se i dump di arresto anomalo del sistema sono configurati nel codice, sarà necessario trasferire manualmente la configurazione dal codice al file di configurazione, perché i dump di arresto anomalo del sistema non vengono trasferiti durante la migrazione in Azure SDK 2.6.

## Abilitare la diagnostica nei progetti di servizi cloud prima della distribuzione

In Visual Studio è possibile scegliere di raccogliere i dati di diagnostica per i ruoli eseguiti in Azure quando si esegue il servizio nell'emulatore prima di distribuirlo. Tutte le modifiche apportate alle impostazioni di diagnostica in Visual Studio vengono salvate nel file di configurazione diagnostics.wadcfgx. Queste impostazioni di configurazione specificano l'account di archiviazione in cui i dati di diagnostica vengono salvati quando si distribuisce il servizio cloud.

### Per abilitare la diagnostica in Visual Studio prima della distribuzione

1. Dal menu di scelta rapida per il ruolo desiderato scegliere **Proprietà**, quindi selezionare la scheda **Configurazione** nella finestra **Proprietà** del ruolo.

1. Nella sezione **Diagnostica** verificare che la casella di controllo **Abilita diagnostica** sia selezionata.

    ![Accesso all'opzione Abilita diagnostica](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)

1. Scegliere il pulsante con i puntini di sospensione (…) per specificare l'account di archiviazione in cui si vuole che vengano archiviati i dati di diagnostica. L'account di archiviazione scelto corrisponderà al percorso di archiviazione dei dati di diagnostica.

    ![Specificare l'account di archiviazione da usare](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)

1. Nella finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione** specificare se si vuole effettuare la connessione usando l'emulatore di archiviazione di Azure, una sottoscrizione di Azure o credenziali immessi manualmente.

    ![Finestra di dialogo Account di archiviazione](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)

  - Se si sceglie l'opzione Emulatore di archiviazione di Microsoft Azure, la stringa di connessione sarò impostata su UseDevelopmentStorage=true.

  - Se si sceglie l'opzione relativa alla propria sottoscrizione, sarà possibile selezionare la sottoscrizione di Azure e il nome di account da usare. Per gestire le sottoscrizioni di Azure, è possibile usare il pulsante Gestisci account.

  - Se si sceglie l'opzione Credenziali immesse manualmente, verrà richiesta l'immissione del nome e della chiave dell'account di Azure da usare.

1. Fare clic su **Configura** per visualizzare la finestra di dialogo **Configurazione di diagnostica**. Ogni scheda, ad eccezione di **Generale** e **Directory log**, rappresenta un'origine di dati di diagnostica che è possibile raccogliere. La scheda predefinita, **Generale**, offre le opzioni di raccolta di dati di diagnostica seguenti: **Solo errori**, **Tutte le informazioni** e **Personalizza piano**. L'opzione predefinita, **Solo errori**, richiede la quantità minima di spazio di archiviazione, perché non trasferisce messaggi di avviso o di traccia. L'opzione Tutte le informazioni trasferisce la quantità più elevata di informazioni ed è quindi l'opzione più costosa in termini di risorse di archiviazione.

    ![Abilitare la diagnostica e la configurazione di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

1. Per questo esempio selezionare l'opzione **Personalizza piano**, in modo da personalizzare i dati raccolti.

1. La casella **Quota disco in MB** specifica la quantità di spazio da allocare nell'account di archiviazione per i dati di diagnostica. Se si vuole, è possibile cambiare il valore predefinito.

1. In ogni scheda dei dati di diagnostica da raccogliere selezionare la rispettiva casella di controllo **Abilita il trasferimento di <log type>**. Ad esempio, per raccogliere log applicazioni, selezionare la casella di controllo **Abilita il trasferimento di log applicazioni** nella scheda **Log applicazioni**. Specificare anche eventuali altre informazioni richieste da ogni tipo di dati di diagnostica. Per informazioni di configurazione su ogni scheda, vedere la sezione **Configurare le origini dati di diagnostica** più avanti in questo argomento.

1. Dopo l'abilitazione della raccolta di tutti i dati di diagnostica desiderati, fare clic su **OK**.

1. Eseguire il progetto di servizio cloud di Azure in Visual Studio come di consueto. Quando si usa l'applicazione, le informazioni di log abilitate vengono salvate nell'account di archiviazione di Azure specificato.

## Abilitare la diagnostica nelle macchine virtuali di Azure

In Visual Studio è possibile scegliere di raccogliere i dati di diagnostica per le macchine virtuali di Azure.

### Per abilitare la diagnostica nelle macchine virtuali di Azure

1. In **Esplora server** scegliere il nodo di Azure e quindi connettersi alla sottoscrizione di Azure, se non si è già connessi.

1. Espandere il nodo **Macchine virtuali**. È possibile creare una nuova macchina virtuale o selezionare una macchina virtuale già esistente.

1. Dal menu di scelta rapida per la macchina virtuale desiderata scegliere **Configura**. Verrà visualizzata la finestra di dialogo di configurazione della macchina virtuale.

    ![Configurazione di una macchina virtuale di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)

1. Se non è già installata, aggiungere l'estensione Microsoft Monitoring Agent Diagnostics. Questa estensione consente di raccogliere dati di diagnostica per la macchina virtuale di Azure. Nell'elenco Estensioni installate scegliere Microsoft Monitoring Agent Diagnostics dal menu a discesa Seleziona un'estensione disponibile.

    ![Installazione di un'estensione di macchina virtuale di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)

    >[AZURE.NOTE] Sono disponibili altre estensioni di diagnostica per le macchine virtuali. Per altre informazioni, vedere Estensioni VM e funzionalità di Azure.

1. Scegliere il pulsante **Aggiungi** per aggiungere l'estensione e visualizzare la rispettiva finestra di dialogo **Configurazione di diagnostica**.

1. Fare clic su **Configura** per specificare un account di archiviazione e quindi fare clic su **OK**.

    Ogni scheda, ad eccezione di **Generale** e **Directory log** rappresenta un'origine di dati di diagnostica che è possibile raccogliere.

    ![Abilitare la diagnostica e la configurazione di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

    La scheda predefinita, **Generale**, offre le opzioni di raccolta di dati di diagnostica seguenti: **Solo errori**, **Tutte le informazioni** e **Personalizza piano**. L'opzione predefinita, **Solo errori**, richiede la quantità minima di spazio di archiviazione, perché non trasferisce messaggi di avviso o di traccia. L'opzione **Tutte le informazioni** trasferisce la quantità più elevata di informazioni ed è quindi l'opzione più costosa in termini di risorse di archiviazione.

1. Per questo esempio selezionare l'opzione **Personalizza piano**, in modo da personalizzare i dati raccolti.

1. La casella **Quota disco in MB** specifica la quantità di spazio da allocare nell'account di archiviazione per i dati di diagnostica. Se si vuole, è possibile cambiare il valore predefinito.

1. In ogni scheda dei dati di diagnostica da raccogliere selezionare la rispettiva casella di controllo **Abilita il trasferimento di <log type>**.

    Ad esempio, per raccogliere i log applicazioni, selezionare la casella di controllo **Abilita il trasferimento di log applicazioni** nella scheda **Log applicazioni**. Specificare anche eventuali altre informazioni richieste da ogni tipo di dati di diagnostica. Per informazioni di configurazione su ogni scheda, vedere la sezione **Configurare le origini dati di diagnostica** più avanti in questo argomento.

1. Dopo l'abilitazione della raccolta di tutti i dati di diagnostica desiderati, fare clic su **OK**.

1. Salvare il progetto aggiornato.

    Nella finestra **Log attività di Microsoft Azure** verrà visualizzato un messaggio relativo al completamento dell'aggiornamento della macchina virtuale.

## Configurare le origini dati di diagnostica

Dopo l'abilitazione della raccolta di dati di diagnostica, è possibile scegliere esattamente le origini dati da raccogliere e le informazioni raccolte. L'elenco seguente include le schede della finestra di dialogo **Configurazione di diagnostica** e informazioni sul significato di ogni opzione di configurazione.

### Log applicazioni

I **Log applicazioni** includono informazioni di diagnostica prodotte da un'applicazione Web. Se, ad esempio, si vogliono acquisire log applicazioni, selezionare la casella di controllo **Abilita il trasferimento di log applicazioni**. È possibile aumentare o ridurre il numero di minuti per il trasferimento dei log applicazioni all'account di archiviazione cambiando il valore **Periodo di trasferimento (min)**. È anche possibile cambiare la quantità di informazioni acquisite nel log, impostando il valore Livello log. Ad esempio, è possibile scegliere **Dettagliato** per ottenere più informazioni oppure **Critico** per acquisire solo gli errori critici. Se è disponibile un provider di diagnostica specifico che genera log applicazioni, sarà possibile acquisirli aggiungendo il GUID alla casella **GUID provider**.

  ![Log applicazioni](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  Per altre informazioni sui log applicazioni, vedere [Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure](./app-service-web/web-sites-enable-diagnostic-log.md).

### Registri eventi di Windows

Per acquisire log eventi di Windows, selezionare la casella di controllo **Abilita il trasferimento dei log eventi di Windows**. È possibile aumentare o ridurre il numero di minuti per il trasferimento dei log eventi all'account di archiviazione cambiando il valore **Periodo di trasferimento (min)**. Selezionare le caselle di controllo per i tipi di eventi da tracciare.

  ![Log eventi](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Se si usa Azure SDK 2.6 o versioni successive e si vuole specificare un'origine dati personalizzata, immetterla nella casella di testo **<Data source name>**, quindi scegliere il pulsante **Aggiungi** accanto alla casella. L'origine dati viene aggiunta al file diagnostics.cfcfg.

Se si usa Azure SDK 2.5 e si vuole specificare un'origine dati personalizzata, sarà possibile aggiungerla alla sezione `WindowsEventLog` del file diagnostics.wadcfgx, come illustrato nell'esempio seguente.

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### Contatori delle prestazioni

Le informazioni sui contatori delle prestazioni possono semplificare l'individuazione dei colli di bottiglia e l'ottimizzazione delle prestazioni del sistema e delle applicazioni. Per altre informazioni, vedere [Creare e usare contatori di prestazioni in un'applicazione Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx). Per acquisire contatori delle prestazioni, selezionare la casella di controllo **Abilita il trasferimento di contatori delle prestazioni**. È possibile aumentare o ridurre il numero di minuti per il trasferimento dei log eventi all'account di archiviazione modificando il valore **Periodo di trasferimento (min)**. Selezionare le caselle di controllo per i contatori delle prestazioni da tracciare.

  ![Contatori delle prestazioni](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Per tenere traccia di un contatore delle prestazioni non incluso nell'elenco, immetterlo usando la sintassi suggerita, quindi scegliere **Aggiungi**. Il sistema operativo della macchina virtuale determina i contatori delle prestazioni di cui è possibile tenere traccia. Per altre informazioni sulla sintassi, vedere [Specificare un percorso del contatore](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### Log infrastruttura

Per acquisire log infrastruttura, che includono informazioni sull'infrastruttura di diagnostica di Azure, il modulo RemoteAccess e il modulo RemoteForwarder, selezionare la casella di controllo **Abilita il trasferimento dei log dell'infrastruttura di diagnostica**. È possibile aumentare o ridurre il numero di minuti per il trasferimento dei log all'account di archiviazione modificando il valore Periodo di trasferimento (min).

  ![Log dell'infrastruttura di diagnostica](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  Per altre informazioni, vedere [Raccogliere dati di registrazione usando Diagnostica Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### Directory di log

Per acquisire directory di log, che includono i dati raccolti dalle directory di log per richieste di Internet Information Services (IIS), richieste non riuscite o cartelle scelte, selezionare la casella di controllo **Abilita il trasferimento di directory di log**. È possibile aumentare o ridurre il numero di minuti per il trasferimento dei log all'account di archiviazione modificando il valore **Periodo di trasferimento (min)**.

È possibile selezionare le caselle dei log da raccogliere, ad esempio **Log IIS** e **Log delle richieste non riuscite**. Vengono forniti i nomi dei contenitori di archiviazione predefiniti, ma è possibile modificarli, se si vuole.

È anche possibile acquisire log da qualsiasi cartella. È sufficiente specificare il percorso nella sezione **Log da directory assoluta** e quindi scegliere **Aggiungi directory**. I log verranno acquisiti nei contenitori specificati.

  ![Directory di log](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### Log ETW

Se si usa [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803(v=vs.85).aspx) (ETW) e si vogliono acquisire log ETW, selezionare la casella di controllo **Abilita il trasferimento di log ETW**. È possibile aumentare o ridurre il numero di minuti per il trasferimento dei log all'account di archiviazione modificando il valore **Periodo di trasferimento (min)**.

Gli eventi vengono acquisiti da origini eventi e manifesti evento specificati. Per specificare un'origine eventi, immettere un nome nella sezione **Origini evento**, quindi scegliere **Aggiungi origine evento**. È analogamente possibile specificare un manifesto evento nella sezione **Manifesti evento** e quindi scegliere **Aggiungi manifesto evento**.

  ![Log ETW](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  Il framework ETW è supportato in ASP.NET tramite le classi nello spazio dei nomi [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110). Lo spazio dei nomi Microsoft.WindowsAzure.Diagnostics, che eredita da ed estende le classi [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) standard, consente l'uso di [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) come framework di registrazione nell'ambiente Azure. Per altre informazioni, vedere [Controllare la registrazione e la traccia in Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [Abilitazione di Diagnostica in servizi cloud e macchine virtuali di Azure](./cloud-services/cloud-services-dotnet-diagnostics.md).

### Dump di arresto anomalo del sistema

Per acquisire informazioni sulle situazioni in cui si verifica un arresto anomalo di un'istanza del ruolo, selezionare la casella di controllo **Abilita il trasferimento di dump di arresto anomalo del sistema**. Poiché la maggior parte delle eccezioni è gestita da ASP.NET, questo è in genere utile solo per i ruoli di lavoro. È possibile aumentare o ridurre la percentuale di spazio di archiviazione dedicata ai dump di arresto anomalo del sistema modificando il valore **Quota directory (%)**. È possibile cambiare il contenitore di archiviazione in cui vengono archiviati i dump di arresto anomalo del sistema e stabilire se si vuole acquisire un dump **Completo** o **Ridotto**.

Vengono elencati i processi attualmente tracciati. Selezionare le caselle di controllo per i processi da acquisire. Per aggiungere un altro processo all'elenco, immettere il nome del processo e quindi scegliere **Aggiungi processo**.

  ![Dump di arresto anomalo del sistema](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  Per altre informazioni, vedere [Controllare la registrazione e la traccia in Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [Diagnostica di Microsoft Azure - Parte 4: Personalizzare i componenti di registrazione e modifiche a Diagnostica di Azure 1.3](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## Visualizzare i dati di diagnostica

Dopo la raccolta dei dati di diagnostica per un servizio cloud o una macchina virtuale, sarà possibile visualizzarli.

### Per visualizzare i dati di diagnostica del servizio cloud

1. Distribuire il servizio cloud come di consueto, quindi eseguirlo.

1. È possibile visualizzare i dati di diagnostica in un report generato da Visual Studio o le tabelle dell'account di archiviazione. Per visualizzare i dati in un report, aprire **Cloud Explorer** o **Esplora server**, aprire il menu di scelta rapida del nodo per il ruolo desiderato, quindi scegliere **Visualizza dati di diagnostica**.

    ![Visualizza dati di diagnostica](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)

    Verrà visualizzato un report che mostra i dati disponibili.

    ![Report Diagnostica di Microsoft Azure in Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)

    Se i dati più recenti non vengono visualizzati, potrebbe essere necessario attendere il termine del periodo di trasferimento.

    Scegliere il collegamento **Aggiorna** per aggiornare immediatamente i dati oppure scegliere un intervallo dall'elenco a discesa **Aggiornamento automatico** per aggiornare automaticamente i dati. Per esportare i dati di errore, scegliere **Esporta in CSV** per creare un file con valori delimitati da virgole che può essere aperto in un foglio di calcolo.

    In **Cloud Explorer** o **Esplora server** aprire l'account di archiviazione associato alla distribuzione.

1. Aprire le tabelle di diagnostica nel visualizzatore di tabelle e quindi esaminare i dati raccolti. Per i log IIS e i log personalizzati sarà possibile aprire un contenitore BLOB. Esaminando la tabella seguente, sarà possibile trovare la tabella o il contenitore BLOB che include i dati desiderati. Oltre ai dati per tale file di log, le voci della tabella contengono EventTickCount, DeploymentId, Role e RoleInstance per semplificare l'identificazione della macchina virtuale e del ruolo che hanno generato i dati e quando.

    |Dati di diagnostica|Descrizione|Località|
    |---|---|---|
    |Log applicazioni|Log generati dal codice chiamando i metodi della classe System.Diagnostics.Trace.|WADLogsTable|
    |Log eventi|Questi dati sono tratti dai log eventi di Windows nelle macchine virtuali. Windows archivia le informazioni in questi log, ma le applicazioni e i servizi li usano anche per segnalare errori o registrare informazioni.|WADWindowsEventLogsTable|
    |Contatori delle prestazioni|È possibile raccogliere dati relativi a qualsiasi contatore delle prestazioni disponibile nella macchina virtuale. Il sistema operativo fornisce contatori delle prestazioni, che includono molte statistiche, ad esempio relative all'utilizzo della memoria e al tempo processore.|WADPerformanceCountersTable|
    |Log infrastruttura|Questi log sono generati dall'infrastruttura di diagnostica stessa.|WADDiagnosticInfrastructureLogsTable|
    |Log di IIS|Questi log registrano le richieste Web. Se il servizio cloud riceve una quantità significativa di traffico, questi log possono essere abbastanza lunghi. È quindi consigliabile raccogliere e archiviare questi dati solo quando necessario.|I log relativi alle richieste non riuscite sono disponibili nel contenitore BLOB in wad-iis-failedreqlogs in un percorso corrispondente a tale distribuzione, ruolo e istanza. I log completi sono disponibili in wad-iis-logfiles. Le voci per ogni file vengono inserite nella tabella WADDirectories.|
    |Dump di arresto anomalo del sistema|Queste informazioni forniscono immagini binarie del processo del servizio cloud, in genere un ruolo di lavoro.|Contenitore BLOB wad-crush-dumps|
    |File di log personalizzati|Registra i dati definiti dall'utente.|È possibile specificare nel codice il percorso dei file di log personalizzati nell'account di archiviazione. Ad esempio, è possibile specificare un contenitore BLOB personalizzato.|

1. Se i dati di qualsiasi tipo vengono troncati, è possibile provare ad aumentare il buffer per tale tipo di dati o ridurre l'intervallo tra i trasferimenti di dati dalla macchina virtuale all'account di archiviazione.

1. (Facoltativo) Ripulire occasionalmente i dati dall'account di archiviazione per ridurre i costi di archiviazione complessivi.

1. Quando si esegue una distribuzione completa, il file diagnostics.cscfg (con estensione wadcfgx per Azure SDK 2.5) viene aggiornato in Azure e il servizio cloud rileva eventuali modifiche apportate alla configurazione di diagnostica. Se, invece, si aggiorna una distribuzione esistente, il file .cscfg non verrà aggiornato in Azure. È comunque possibile modificare le impostazioni di diagnostica, tuttavia, eseguendo i passaggi illustrati nella sezione successiva. Per altre informazioni sull'esecuzione di una distribuzione completa e sull'aggiornamento di una distribuzione esistente, vedere [Procedura guidata Pubblica l'applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md).

### Per visualizzare i dati di diagnostica della macchina virtuale

1. Dal menu di scelta rapida per la macchina virtuale scegliere **Visualizza dati di diagnostica**.

    ![Visualizzare i dati di diagnostica nella macchina virtuale di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)

    Verrà visualizzata la finestra **Riepilogo diagnostica**.

    ![Riepilogo di diagnostica della macchina virtuale di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)

    Se i dati più recenti non vengono visualizzati, potrebbe essere necessario attendere il termine del periodo di trasferimento.

    Scegliere il collegamento **Aggiorna** per aggiornare immediatamente i dati oppure scegliere un intervallo dall'elenco a discesa **Aggiornamento automatico** per aggiornare automaticamente i dati. Per esportare i dati di errore, scegliere **Esporta in CSV** per creare un file con valori delimitati da virgole che può essere aperto in un foglio di calcolo.

## Configurare la diagnostica del servizio cloud dopo la distribuzione

Se si sta esaminando un problema relativo a un servizio cloud già in esecuzione, è possibile che si voglia raccogliere dati non specificati prima della distribuzione originale del ruolo. In questo caso è possibile iniziare a raccogliere i dati usando le impostazioni in Esplora server. È possibile configurare la diagnostica per un'istanza singola o per tutte le istanze di un ruolo, in base all'apertura della finestra di dialogo Configurazione di diagnostica dal menu di scelta rapida per l'istanza o per il ruolo. Se si configura il nodo del ruolo, eventuali modifiche verranno applicate a tutte le istanze. Se si configura il nodo dell'istanza, eventuali modifiche verranno applicate solo a tale istanza.

### Per configurare la diagnostica per un servizio cloud in esecuzione

1. In Esplora server espandere il nodo **Servizi cloud**, quindi espandere i nodi per trovare il ruolo o l'istanza da esaminare o entrambi.

    ![Configurazione di Diagnostica](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)

1. Dal menu di scelta rapida per un nodo di istanza o un nodo di ruolo scegliere **Aggiorna impostazioni diagnostica**, quindi scegliere le impostazioni di diagnostica da raccogliere.

    Per informazioni sulle impostazioni di configurazione, vedere **Configurare le origini dati di diagnostica** in questo argomento. Per informazioni su come visualizzare i dati di diagnostica, vedere **Visualizzare i dati di diagnostica** in questo argomento.

    Se si modifica la raccolta di dati in **Esplora server**, tali modifiche rimarranno validi fino a una nuova distribuzione completa del servizio cloud. Se si usano le impostazioni di pubblicazione predefinite, le modifiche non verranno sovrascritte, perché l'impostazione di pubblicazione predefinita prevede l'aggiornamento della distribuzione esistente, invece di eseguire una ridistribuzione completa. Per assicurare che le impostazioni vengano cancellate durante la distribuzione, passare alla scheda **Impostazioni avanzate** nella Pubblicazione guidata e deselezionare la casella di controllo **Aggiornamento distribuzione**. Quando si esegue la nuova distribuzione con tale casella di controllo deselezionata, verranno ripristinate le impostazioni del file con estensione wadcfgx (o wadcfg) definite tramite l'editor delle proprietà per il ruolo specifico. Se si aggiorna la distribuzione, Azure manterrà le impostazioni precedenti.

## Risolvere i problemi del servizio cloud di Azure

Se si verificano problemi con i progetti del servizio cloud, ad esempio un ruolo bloccato con stato "occupato", che viene riciclato ripetutamente o genera un errore di server interno, è possibile usare alcuni strumenti e tecniche per diagnosticare e risolvere questi problemi. Per esempi specifici di problemi comuni e soluzioni, oltre a una panoramica dei concetti e degli strumenti usati per diagnosticare e risolvere tali errori, vedere il post di blog relativo ai [Dati di diagnostica di calcolo delle soluzioni PaaS per Azure](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## Domande e risposte

**Quali sono le dimensioni del buffer e quanto dovrebbe essere grande?**

In ogni istanza della macchina virtuale le quote limitano la quantità di dati di diagnostica che possono essere archiviati nel file system locale. È anche possibile specificare una dimensione del buffer per ogni tipo di dato di diagnostica disponibile. Tale dimensione del buffer funge da quota individuale per quel tipo di dati. Verificando la parte inferiore della finestra di dialogo, è possibile determinare la quota complessiva e la quantità di memoria rimanente. Se si specificano buffer più grandi o più tipi di dati, ci si avvicinerà alla quota complessiva. È possibile cambiare la quota complessiva, modificando il file di configurazione diagnostics.wadcfg/.wadcfgx. I dati di diagnostica vengono archiviati nello stesso file system usato per i dati dell'applicazione, quindi se l'applicazione usa una quantità elevata di spazio su disco, è consigliabile non aumentare la quota di diagnostica complessiva.

**che cos'è il periodo di trasferimento e quanto deve essere lungo?**

Il periodo di trasferimento corrisponde alla quantità di tempo trascorsa tra acquisizioni dei dati. Dopo ogni periodo di trasferimento, i dati vengono spostati dal file system locale su una macchina virtuale a tabelle nell'account di archiviazione. Se la quantità di dati raccolta supera la quota prima del termine di un periodo di trasferimento, i dati precedenti verranno eliminati. Se si perdono dati perché i dati superano la dimensione del buffer o la quota complessiva, è consigliabile ridurre il periodo di trasferimento.

**Che fuso orario viene usato per i timestamp?**

I timestamp usano il fuso orario del data center che ospita il servizio cloud. Vengono usate le tre colonne di timestamp seguenti nelle tabelle di log.

  - **PreciseTimeStamp** è il timestamp ETW dell'evento, ovvero l'ora di registrazione dell'evento dal client.

  - **TIMESTAMP** è PreciseTimeStamp arrotondato per difetto al limite di frequenza di caricamento. Se la frequenza di caricamento è pari a 5 minuti e l'ora dell'evento è 00:17:12, TIMESTAMP sarà quindi pari a 00:15:00.

  - **Timestamp** è il timestamp in corrispondenza del quale l'entità è stata creata nella tabella di Azure.

**Come si gestiscono i costi durante la raccolta delle informazioni di diagnostica?**

Le impostazioni predefinite (**Livello log** impostato su **Errore** e **Periodo di trasferimento** impostato su **1 minuto**) sono progettate per ridurre al minimo i costi. I costi calcolati aumenteranno se si raccolgono più dati di diagnostica o si ridice il periodo di trasferimento. Non raccogliere una quantità di dati superiore a quanto necessario e non dimenticare di disabilitare la raccolta di dati quando non è più necessaria. È sempre possibile abilitarla di nuovo, anche in fase di esecuzione, come illustrato nella sezione precedente.

**Come si raccolgono log relativi alle richieste non riuscite da IIS?**

Per impostazione predefinita, IIS non raccoglie log relativi alle richieste non riuscite. È possibile configurare IIS in modo che li raccolga, se si modifica il file web.config per il ruolo Web.

**Non si ottengono informazioni di traccia dai metodi RoleEntryPoint quali OnStart. Qual è il problema?**

I metodi di RoleEntryPoint vengono chiamati nel contesto di WAIISHost.exe, non IIS. Le informazioni di configurazione nel file web.config che in genere abilitano la traccia non sono applicabili in questo caso. Per risolvere il problema, aggiungere un file con estensione config al progetto di ruolo Web e assegnare al file un nome corrispondente all'assembly di output contenente il codice RoleEntryPoint. Nel progetto di ruolo Web predefinito il nome del file con estensione config sarà WAIISHost.exe.config. Aggiungere quindi le righe seguenti al file:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Nella finestra **Proprietà** impostare la proprietà **Copia nella directory di output** su **Copia sempre**.

## Passaggi successivi

Per altre informazioni sulle registrazioni di diagnostica in Azure, vedere [Abilitazione di Diagnostica in servizi cloud e macchine virtuali di Azure](./cloud-services/cloud-services-dotnet-diagnostics.md) e [Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure](./app-service-web/web-sites-enable-diagnostic-log.md).

<!---HONumber=AcomDC_0413_2016-->