---
title: Configurare la diagnostica per i servizi cloud e le macchine virtuali di Azure | Microsoft Docs
description: Informazioni su configurare la diagnostica per il debug dei servizi cloud e delle macchine virtuali di Azure in Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: ghogen
editor: 
ms.assetid: e70cd7b4-6298-43aa-adea-6fd618414c26
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: f00771d89749e7507d7f303f366fe63f537900ff
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Configurare la diagnostica per i servizi cloud e le macchine virtuali di Azure
Per risolvere gli errori di un servizio cloud o di una macchina virtuale di Azure, è possibile usare Visual Studio per configurare con maggiore facilità Diagnostica di Azure. Diagnostica di Azure acquisisce i dati di sistema e i dati di registrazione nelle macchine virtuali e nelle istanze di macchine virtuali che eseguono il servizio cloud. I dati di diagnostica vengono trasferiti all'account di archiviazione scelto. Per altre informazioni sulla registrazione diagnostica in Azure, vedere [Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure](app-service/web-sites-enable-diagnostic-log.md).

Questo articolo illustra come usare Visual Studio per attivare e configurare Diagnostica di Azure, prima e dopo la distribuzione. Sono disponibili informazioni sulle procedure per configurare Diagnostica di Azure nelle macchine virtuali di Azure, per selezionare i tipi di informazioni di diagnostica da raccogliere e per visualizzare le informazioni dopo la raccolta.

Per configurare Diagnostica di Azure, è possibile usare una delle opzioni seguenti:

* Modificare le impostazioni di diagnostica nella finestra di dialogo **Configurazione di diagnostica** in Visual Studio. Le impostazioni vengono salvate in un file denominato diagnostics.wadcfgx (il file è denominato diagnostics.wadcfg in Azure SDK 2.4 e versioni precedenti). È anche possibile modificare direttamente il file di configurazione. Se si aggiorna manualmente il file, le modifiche alla configurazione vengono applicate alla successiva distribuzione del servizio cloud in Azure o alla successiva esecuzione del servizio nell'emulatore.
* Usare Cloud Explorer o Esplora server in Visual Studio per modificare le impostazioni di diagnostica per un servizio cloud o una macchina virtuale in esecuzione.

## <a name="azure-sdk-26-diagnostics-changes"></a>Modifiche alla diagnostica in Azure SDK 2.6
Le modifiche seguenti riguardano i progetti Azure SDK 2.6 e successivi in Visual Studio:

* L'emulatore locale supporta ora la diagnostica. È quindi possibile raccogliere dati di diagnostica e assicurarsi che l'applicazione crei le tracce corrette durante lo sviluppo e il testing in Visual Studio. La stringa di connessione `UseDevelopmentStorage=true` attiva la raccolta di dati di diagnostica durante l'esecuzione del progetto servizio cloud in Visual Studio mediante l'emulatore di archiviazione di Azure. Tutti i dati di diagnostica vengono raccolti nell'account di archiviazione Archivio di sviluppo.
* La stringa di connessione dell'account di archiviazione per la diagnostica `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` è archiviata nel file di configurazione (con estensione cscfg) del servizio. In Azure SDK 2.5 l'account di archiviazione di diagnostica viene specificato nel file diagnostics.wadcfgx.

La stringa di connessione funziona in modo diverso per alcuni aspetti chiave in Azure SDK 2.6 e versioni successive rispetto ad Azure SDK 2.4 e versioni precedenti:

* In Azure SDK 2.4 e versioni precedenti la stringa di connessione viene usata come runtime dal plug-in di diagnostica per ottenere le informazioni sull'account di archiviazione per il trasferimento dei log di diagnostica.
* In Azure SDK 2.6 e versioni successive, Visual Studio usa la stringa di connessione di diagnostica per configurare l'estensione Diagnostica di Azure con le informazioni appropriate sull'account di archiviazione durante la pubblicazione. È possibile usare la stringa di connessione per definire diversi account di archiviazione per diverse configurazioni del servizio, usate da Visual Studio durante la pubblicazione. Poiché, tuttavia, il plug-in di diagnostica non è disponibile dopo Azure SDK 2.5, il solo file cscfg non consente di configurare l'estensione di diagnostica. È necessario configurare l'estensione separatamente tramite strumenti come Visual Studio o PowerShell.
* Per semplificare il processo di configurazione dell'estensione di diagnostica con PowerShell, l'output del pacchetto da Visual Studio include il codice XML di configurazione pubblica per l'estensione di diagnostica per ogni ruolo. Visual Studio usa la stringa di connessione di diagnostica per popolare le informazioni sull'account di archiviazione disponibili nella configurazione pubblica. I file di configurazione pubblica vengono creati nella cartella Extensions. Per i file di configurazione pubblica viene usato il modello di denominazione PaaSDiagnostics.&lt;nome ruolo\>.PubConfig.Xml. Eventuali distribuzioni basate su PowerShell possono usare questo modello per il mapping di ogni configurazione a un ruolo.
* Il [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) usa la stringa di connessione nel file con estensione cscfg per accedere ai dati di diagnostica. I dati vengono visualizzati nella scheda **Monitoraggio**. La stringa di connessione è necessaria per impostare il servizio in modo da visualizzare i dati dettagliati del monitoraggio nel portale.

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>Eseguire la migrazione di progetti in Azure SDK 2.6 e versioni successive
Quando si esegue la migrazione da Azure SDK 2.5 ad Azure SDK 2.6 o versioni successive, eventuali account di archiviazione di diagnostica specificati nel file con estensione wadcfgx rimarranno in tale file. Per sfruttare la flessibilità offerta dall'uso di account di archiviazione diversi per configurazioni di archiviazione diverse, aggiungere manualmente la stringa di connessione al progetto. Le stringhe di connessione di diagnostica vengono mantenute in seguito alla migrazione di un progetto da Azure SDK 2.4 o versioni precedenti ad Azure SDK 2.6. Tenere presente, tuttavia, le modifiche relative al modo in cui le stringhe di connessione vengono gestite in Azure SDK 2.6, come descritto nella sezione precedente.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Modalità di determinazione dell'account di archiviazione di diagnostica da parte di Visual Studio
* Se nel file cscfg viene specificata una stringa di connessione di diagnostica, Visual Studio la userà per configurare l'estensione di diagnostica durante la pubblicazione e durante la generazione dei file XML di configurazione pubblica nel corso della creazione dei pacchetti.
* Se nel file cscfg non viene specificata una stringa di connessione di diagnostica, Visual Studio userà di nuovo l'account di archiviazione specificato nel file wadcfgx per configurare l'estensione di diagnostica per la pubblicazione e per la generazione dei file XML di configurazione pubblica durante la creazione dei pacchetti.
* La stringa di connessione di diagnostica nel file .cscfg ha la priorità rispetto all'account di archiviazione specificato nel file con estensione wadcfgx. Se nel file cscfg viene specificata una stringa di connessione di diagnostica, Visual Studio usa tale stringa e ignora l'account di archiviazione specificato nel file wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>Qual è la funzione della casella di controllo "Aggiorna le stringhe di connessione dell'archivio di sviluppo..."?
La casella di controllo **Aggiorna le stringhe di connessione dell'archivio di sviluppo per la diagnostica e la memorizzazione nella cache con le credenziali dell'account di archiviazione di Microsoft Azure quando si esegue la pubblicazione in Microsoft Azure** offre un modo semplice per aggiornare eventuali stringhe di connessione dell'account di archiviazione con l'account di archiviazione di Azure specificato durante la pubblicazione.

Ad esempio, se si seleziona questa casella di controllo e la stringa di connessione di diagnostica specifica `UseDevelopmentStorage=true`, quando si pubblica il progetto in Azure, Visual Studio aggiorna automaticamente la stringa di connessione di diagnostica con l'account di archiviazione specificato nella pubblicazione guidata. Tuttavia, se viene specificato un account di archiviazione effettivo come stringa di connessione di diagnostica, verrà invece usato tale account.

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Differenze delle funzionalità di diagnostica tra Azure SDK 2.4 e versioni precedenti e Azure SDK 2.5 e versioni successive
Se si aggiorna il progetto da Azure SDK 2.4 e versioni precedenti ad Azure SDK 2.5 o versioni successive, tenere presente le differenze seguenti a livello di funzionalità di diagnostica:

* **Le API di configurazione sono deprecate**. La configurazione a livello di codice della diagnostica è disponibile in Azure SDK 2.4 e versioni precedenti, ma è deprecata in Azure SDK 2.5 e versioni successive. Se la configurazione di diagnostica è attualmente definita nel codice, è necessario riconfigurare completamente tali impostazioni nel progetto migrato per mantenere il funzionamento della diagnostica. Il file di configurazione di diagnostica per Azure SDK 2.4 è diagnostics.wadcfg. Il file di configurazione di diagnostica per Azure SDK 2.5 e versioni successive è diagnostics.wadcfgx.
* **La diagnostica per le applicazioni del servizio cloud può essere configurata solo a livello di ruolo**. In Azure SDK 2.5 e versioni successive, è possibile configurare la diagnostica per le applicazioni del servizio cloud a livello di istanza.
* **Ogni volta che si distribuisce l'app, viene aggiornata la configurazione della diagnostica**. Ciò può causare problemi di parità se si modifica la configurazione di diagnostica da Esplora server di Visual Studio e quindi si ridistribuisce l'app.
* **In Azure SDK 2.5 e versioni successive, i dump di arresto anomalo del sistema sono configurati nel file di configurazione di diagnostica e non nel codice**. In caso di dump di arresto anomalo del sistema configurati nel codice, è necessario trasferire manualmente la configurazione dal codice al file di configurazione. I dump di arresto anomalo del sistema non vengono trasferiti durante la migrazione ad Azure SDK 2.6.

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>Attivare la diagnostica nei progetti di servizi cloud prima di distribuirli
In Visual Studio è possibile raccogliere i dati di diagnostica per i ruoli eseguiti in Azure quando si esegue il servizio nell'emulatore prima della distribuzione. Tutte le modifiche apportate alle impostazioni di diagnostica in Visual Studio vengono salvate nel file di configurazione diagnostics.wadcfgx. Queste impostazioni specificano l'account di archiviazione in cui i dati di diagnostica vengono salvati quando si distribuisce il servizio cloud.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>Per attivare la diagnostica in Visual Studio prima della distribuzione

1. Dal menu di scelta rapida per il ruolo scegliere **Proprietà**. Nella finestra di dialogo **Proprietà** del ruolo selezionare la scheda **Configurazione**.
2. Nella sezione **Diagnostica** verificare che la casella di controllo **Abilita diagnostica** sia selezionata.
   
    ![Accedere all'opzione Abilita diagnostica](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. Per specificare l'account di archiviazione per i dati di diagnostica, selezionare il pulsante con i puntini di sospensione (...).
   
    ![Specificare l'account di archiviazione da usare](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. Nella finestra di dialogo **Crea stringa di connessione a risorsa di archiviazione** specificare se ci si vuole connettere usando l'emulatore di archiviazione di Azure, una sottoscrizione di Azure o credenziali immesse manualmente.
   
    ![Finestra di dialogo Account di archiviazione](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * Se si seleziona **Emulatore di archiviazione di Microsoft Azure** la stringa di connessione viene impostata su `UseDevelopmentStorage=true`.
   * Se si seleziona **Sottoscrizione**, è possibile selezionare la sottoscrizione di Azure che si vuole usare e immettere un nome di account. Per gestire le sottoscrizioni di Azure, selezionare **Gestisci account**.
   * Se si seleziona **Credenziali immesse manualmente**, immettere il nome e la chiave dell'account di Azure che si vuole usare.
5. Per visualizzare la finestra di dialogo **Configurazione di diagnostica**, selezionare **Configura**. Ad eccezione di **Generale** e **Directory log**, ogni scheda rappresenta un'origine di dati di diagnostica che è possibile raccogliere. La scheda predefinita, **Generale**, offre le opzioni di raccolta di dati di diagnostica seguenti: **Solo errori**, **Tutte le informazioni** e **Personalizza piano**. L'opzione predefinita, **Solo errori**, richiede la quantità minima di spazio di archiviazione, perché non trasferisce messaggi di avviso o di traccia. L'opzione **Tutte le informazioni** trasferisce la quantità più elevata di informazioni e usa la quantità maggiore di spazio di archiviazione, quindi è l'opzione più costosa.
   
    ![Abilitare la diagnostica e la configurazione di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. Per questo esempio selezionare l'opzione **Personalizza piano** , in modo da poter personalizzare i dati raccolti.
7. Nella casella **Quota disco in MB** è possibile specificare la quantità di spazio da allocare nell'account di archiviazione per i dati di diagnostica. È possibile modificare o accettare il valore predefinito.
8. In ogni scheda dei dati di diagnostica da raccogliere selezionare la casella di controllo **Abilita il trasferimento di \<tipo log\>**. Ad esempio, per raccogliere i log applicazioni, selezionare la casella di controllo **Abilita il trasferimento di log applicazioni** nella scheda **Log applicazioni**. Specificare anche eventuali altre informazioni richieste da ogni tipo di dati di diagnostica. Per informazioni sulla configurazione di ogni scheda, vedere la sezione **Configurare le origini dati di diagnostica** più avanti in questo articolo. 
9. Dopo aver abilitato la raccolta di tutti i dati di diagnostica desiderati, selezionare **OK**.
10. Eseguire il progetto di servizio cloud di Azure in Visual Studio come di consueto. Quando si usa l'applicazione, le informazioni di log abilitate vengono salvate nell'account di archiviazione di Azure specificato.

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>Attivare la diagnostica in macchine virtuali di Azure
In Visual Studio è possibile raccogliere i dati di diagnostica per le macchine virtuali di Azure.

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>Per attivare la diagnostica in macchine virtuali di Azure

1. In Esplora server selezionare il nodo di Azure e quindi connettersi alla sottoscrizione di Azure, se non si è già connessi.
2. Espandere il nodo **Macchine virtuali** . È possibile creare una nuova macchina virtuale o selezionare un nodo esistente.
3. Dal menu di scelta rapida per la macchina virtuale desiderata scegliere **Configura**. Verrà visualizzata la finestra di dialogo per la configurazione della macchina virtuale.
   
    ![Configurare una macchina virtuale di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Se non è già installata, aggiungere l'estensione Microsoft Monitoring Agent Diagnostics. Con questa estensione è possibile raccogliere dati di diagnostica per la macchina virtuale di Azure. Nell'elenco **Estensioni installate** selezionare **Microsoft Monitoring Agent Diagnostics** nella casella di riepilogo a discesa **Seleziona un'estensione disponibile**.
   
    ![Installare un'estensione di macchina virtuale di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > Sono disponibili altre estensioni di diagnostica per le macchine virtuali. Per altre informazioni, vedere [Estensioni e funzionalità della macchina virtuale per Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).
   > 
   > 
5. Per aggiungere l'estensione e visualizzare la rispettiva finestra di dialogo **Configurazione di diagnostica** selezionare **Aggiungi**.
6. Per specificare un account di archiviazione, selezionare **Configura** e quindi selezionare **OK**.
   
    Ogni scheda, ad eccezione di **Generale** e **Directory log** rappresenta un'origine di dati di diagnostica che è possibile raccogliere.
   
    ![Abilitare la diagnostica e la configurazione di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    La scheda predefinita, **Generale**, offre le opzioni di raccolta di dati di diagnostica seguenti: **Solo errori**, **Tutte le informazioni** e **Personalizza piano**. L'opzione predefinita, **Solo errori**, richiede la quantità minima di spazio di archiviazione, perché non trasferisce messaggi di avviso o di traccia. L'opzione **Tutte le informazioni** trasferisce la quantità più elevata di informazioni ed è quindi l'opzione più costosa in termini di risorse di archiviazione.
7. Per questo esempio selezionare l'opzione **Personalizza piano** , in modo da personalizzare i dati raccolti.
8. La casella **Quota disco in MB** specifica la quantità di spazio da allocare nell'account di archiviazione per i dati di diagnostica. Se si vuole, è possibile cambiare il valore predefinito.
9. In ogni scheda dei dati di diagnostica da raccogliere selezionare la rispettiva casella di controllo **Abilita il trasferimento di \<tipo log\>**.
   
    Ad esempio, per raccogliere i log applicazioni, selezionare la casella di controllo **Abilita il trasferimento di log applicazioni** nella scheda **Log applicazioni**. Specificare anche eventuali altre informazioni richieste per ogni tipo di dati di diagnostica. Per informazioni sulla configurazione di ogni scheda, vedere la sezione **Configurare le origini dati di diagnostica** più avanti in questo articolo.
10. Dopo l'abilitazione della raccolta di tutti i dati di diagnostica desiderati, selezionare **OK**.
11. Salvare il progetto aggiornato.
    
    Un messaggio nella finestra **Log attività di Microsoft Azure** indica che la macchina virtuale è stata aggiornata.

## <a name="set-up-diagnostics-data-sources"></a>Configurare le origini dati di diagnostica
Dopo aver abilitato la raccolta di dati di diagnostica, è possibile scegliere esattamente le origini dati da raccogliere e le informazioni raccolte. Nelle sezioni successive vengono descritte le schede della finestra di dialogo **Configurazione di diagnostica** con indicazione del significato di ogni opzione di configurazione.

### <a name="application-logs"></a>Log applicazioni
I log applicazioni includono informazioni di diagnostica prodotte da un'applicazione Web. Se, ad esempio, si vogliono acquisire log applicazioni, selezionare la casella di controllo **Abilita il trasferimento di log applicazioni** . Per aumentare o diminuire l'intervallo tra il trasferimento dei log applicazioni all'account di archiviazione, modificare il valore **Periodo di trasferimento (min)**. È anche possibile cambiare la quantità di informazioni acquisite nel log, impostando il valore **Livello log**. Ad esempio, selezionare **Dettagliato** per ottenere più informazioni oppure **Critico** per acquisire solo gli errori critici. Se è disponibile un provider di diagnostica specifico che genera log applicazioni, sarà possibile acquisirli aggiungendo il GUID nella casella **GUID provider**.

  ![Log applicazioni](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

Per altre informazioni sui log applicazioni, vedere [Abilitare la registrazione diagnostica per le app Web del Servizio app di Azure](app-service/web-sites-enable-diagnostic-log.md).

### <a name="windows-event-logs"></a>Registri eventi di Windows
Per acquisire registri eventi di Windows, selezionare la casella di controllo **Abilita il trasferimento dei log eventi di Windows**. Per aumentare o diminuire l'intervallo tra il trasferimento dei registri eventi all'account di archiviazione, modificare il valore **Periodo di trasferimento (min)**. Selezionare le caselle di controllo per i tipi di eventi da tracciare.

![Log eventi](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Se si usa Azure SDK 2.6 o versioni successive e si vuole specificare un'origine dati personalizzata, immetterla nella casella di testo **\<Nome origine dati\>** e quindi selezionare **Aggiungi**. L'origine dati viene aggiunta al file diagnostics.cfcfg.

Se si usa Azure SDK 2.5 e si vuole specificare un'origine dati personalizzata, è possibile aggiungerla alla sezione `WindowsEventLog` del file diagnostics.wadcfgx, come nell'esempio seguente:

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Contatori delle prestazioni
Le informazioni sui contatori delle prestazioni possono semplificare l'individuazione dei colli di bottiglia e l'ottimizzazione delle prestazioni del sistema e delle applicazioni. Per altre informazioni, vedere [Creare e usare contatori di prestazioni in un'applicazione Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx). Per acquisire contatori delle prestazioni, selezionare la casella di controllo **Abilita il trasferimento di contatori delle prestazioni**. Per aumentare o diminuire l'intervallo tra il trasferimento dei registri eventi all'account di archiviazione, modificare il valore **Periodo di trasferimento (min)**. Selezionare le caselle di controllo per i contatori delle prestazioni da tracciare.

![Contatori delle prestazioni](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Per tenere traccia di un contatore delle prestazioni non incluso nell'elenco, immetterlo usando la sintassi suggerita e quindi selezionare **Aggiungi**. Il sistema operativo della macchina virtuale determina i contatori delle prestazioni di cui è possibile tenere traccia. Per altre informazioni sulla sintassi, vedere [Specificare un percorso del contatore](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Log infrastruttura
I log dell'infrastruttura contengono informazioni sull'infrastruttura di diagnostica di Azure, il modulo RemoteAccess e il modulo RemoteForwarder. Per raccogliere informazioni sui log dell'infrastruttura, selezionare la casella di controllo **Abilita il trasferimento dei log dell'infrastruttura di diagnostica**. Per aumentare o diminuire l'intervallo tra il trasferimento dei log dell'infrastruttura all'account di archiviazione, modificare il valore **Periodo di trasferimento (min)**.

![Log dell'infrastruttura di diagnostica](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

Per altre informazioni, vedere [Raccogliere i dati di registrazione usando Diagnostica di Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="log-directories"></a>Directory log
Le directory di log contengono i dati raccolti dalle directory di log per le richieste di Internet Information Services (IIS), le richieste non riuscite o le cartelle scelte. Per acquisire le directory di log, selezionare la casella di controllo **Abilita il trasferimento di directory di log**. Per aumentare o diminuire l'intervallo tra il trasferimento dei log all'account di archiviazione, modificare il valore **Periodo di trasferimento (min)**.

Selezionare le caselle di controllo dei log da raccogliere, ad esempio **Log IIS** e **Log delle richieste non riuscite**. Vengono forniti i nomi dei contenitori di archiviazione predefiniti, ma è possibile modificarli.

È possibile acquisire log da qualsiasi cartella. Specificare il percorso nella sezione **Log da directory assoluta** e quindi selezionare **Aggiungi directory**. I log vengono acquisiti nei contenitori specificati.

![Directory log](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>Log ETW
Se si usa [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (ETW) e si vogliono acquisire log ETW, selezionare la casella di controllo **Abilita il trasferimento di log ETW**. Per aumentare o diminuire l'intervallo tra il trasferimento dei log all'account di archiviazione, modificare il valore **Periodo di trasferimento (min)**.

Gli eventi vengono acquisiti da origini eventi e manifesti evento specificati. Per specificare un'origine eventi, immettere un nome nella sezione **Origini evento** e quindi selezionare **Aggiungi origine evento**. In modo analogo, è possibile specificare un manifesto di evento nella sezione **Manifesti evento** e quindi selezionare **Aggiungi manifesto evento**.

![Log ETW](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

Il framework ETW è supportato in ASP.NET tramite le classi nello spazio dei nomi [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)). Lo spazio dei nomi Microsoft.WindowsAzure.Diagnostics, che eredita dalle classi standard [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) e le estende, consente l'uso di [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) come framework di registrazione nell'ambiente Azure. Per altre informazioni, vedere [Controllo della registrazione e della traccia in Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [Abilitazione di Diagnostica di Azure in servizi cloud di Azure](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Dump di arresto anomalo del sistema
Per acquisire informazioni sulle situazioni in cui si verifica un arresto anomalo di un'istanza del ruolo, selezionare la casella di controllo **Abilita il trasferimento di dump di arresto anomalo del sistema**. Poiché la maggior parte delle eccezioni è gestita da ASP.NET, questo è in genere utile solo per i ruoli di lavoro. Per aumentare o ridurre la percentuale di spazio di archiviazione dedicata ai dump di arresto anomalo del sistema, modificare il valore **Quota directory (%)**. È possibile cambiare il contenitore di archiviazione in cui vengono archiviati i dump di arresto anomalo del sistema e stabilire se si vuole acquisire un dump **Completo** o **Ridotto**.

I processi attualmente tracciati sono elencati nel prossimo screenshot. Selezionare le caselle di controllo per i processi da acquisire. Per aggiungere un altro processo all'elenco, immettere il nome del processo e quindi selezionare **Aggiungi processo**.

![Dump di arresto anomalo del sistema](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

Per altre informazioni, vedere [Controllo della registrazione e della traccia in Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [Diagnostica di Microsoft Azure - Parte 4: Personalizzare i componenti di registrazione e modifiche a Diagnostica di Azure 1.3](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## <a name="view-the-diagnostics-data"></a>Visualizzare i dati di diagnostica
Dopo la raccolta dei dati di diagnostica per un servizio cloud o una macchina virtuale, sarà possibile visualizzarli.

### <a name="to-view-cloud-service-diagnostics-data"></a>Per visualizzare i dati di diagnostica del servizio cloud
1. Distribuire il servizio cloud come di consueto e quindi eseguirlo.
2. È possibile visualizzare i dati di diagnostica in un report generato da Visual Studio o in tabelle nell'account di archiviazione. Per visualizzare i dati in un report, aprire Cloud Explorer o Esplora server, aprire il menu di scelta rapida del nodo per il ruolo desiderato e quindi scegliere **Visualizza dati di diagnostica**.
   
    ![Visualizza dati di diagnostica](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    Verrà visualizzato un report che mostra i dati disponibili.
   
    ![Report di Diagnostica di Microsoft Azure in Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Se i dati più recenti non vengono visualizzati, potrebbe essere necessario attendere il termine del periodo di trasferimento.
   
    Per aggiornare immediatamente i dati, selezionare il collegamento **Aggiorna**. Per attivare l'aggiornamento automatico dei dati, selezionare un intervallo nella casella di riepilogo a discesa **Aggiornamento automatico**. Per esportare i dati di errore, selezionare il pulsante **Esporta in CSV** per creare un file con valori delimitati da virgole che può essere aperto in un foglio di lavoro di Excel.
   
    In Cloud Explorer o Esplora server aprire l'account di archiviazione associato alla distribuzione.
3. Aprire le tabelle di diagnostica nel visualizzatore di tabelle e quindi esaminare i dati raccolti. Per i log IIS e i log personalizzati sarà possibile aprire un contenitore BLOB. La tabella seguente elenca le tabelle o i contenitori di BLOB che contengono i dati per i diversi file di log. Oltre ai dati per tale file di log, le voci della tabella contengono **EventTickCount**, **DeploymentId**, **Role** e **RoleInstance** per semplificare l'identificazione della macchina virtuale e del ruolo che hanno generato i dati e quando. 
   
   | Dati di diagnostica | DESCRIZIONE | Località |
   | --- | --- | --- |
   | Log applicazioni |Log generati dal codice chiamando i metodi della classe **System.Diagnostics.Trace**. |WADLogsTable |
   | Log eventi |Dati tratti dai registri eventi di Windows nelle macchine virtuali. Windows archivia le informazioni in questi log, ma le applicazioni e i servizi li usano anche per segnalare errori o registrare informazioni. |WADWindowsEventLogsTable |
   | Contatori delle prestazioni |È possibile raccogliere dati relativi a qualsiasi contatore delle prestazioni disponibile nella macchina virtuale. Il sistema operativo fornisce contatori delle prestazioni, che includono molte statistiche, come l'utilizzo della memoria e il tempo del processore. |WADPerformanceCountersTable |
   | Log infrastruttura |Log generati dall'infrastruttura di diagnostica stessa. |WADDiagnosticInfrastructureLogsTable |
   | Log di IIS |Log che registrano le richieste Web. Se il servizio cloud riceve una quantità significativa di traffico, questi log possono essere lunghi. È buona norma raccogliere e archiviare questi dati solo quando necessario. |I log relativi alle richieste non riuscite sono disponibili nel contenitore BLOB in wad-iis-failedreqlogs in un percorso corrispondente a tale distribuzione, ruolo e istanza. I log completi sono disponibili in wad-iis-logfiles. Le voci per ogni file vengono inserite nella tabella WADDirectories. |
   | Dump di arresto anomalo del sistema |Forniscono immagini binarie del processo del servizio cloud, in genere un ruolo di lavoro. |Contenitore BLOB wad-crush-dumps |
   | File di log personalizzati |Registra i dati definiti dall'utente. |È possibile specificare nel codice il percorso dei file di log personalizzati nell'account di archiviazione. Ad esempio, è possibile specificare un contenitore BLOB personalizzato. |
4. Se i dati di qualsiasi tipo vengono troncati, è possibile provare ad aumentare il buffer per tale tipo di dati o ridurre l'intervallo tra i trasferimenti di dati dalla macchina virtuale all'account di archiviazione.
5. (Facoltativo) Ripulire occasionalmente i dati dall'account di archiviazione per ridurre i costi di archiviazione complessivi.
6. Quando si esegue una distribuzione completa, il file diagnostics.cscfg (con estensione wadcfgx per Azure SDK 2.5) viene aggiornato in Azure e il servizio cloud rileva eventuali modifiche apportate alla configurazione di diagnostica. Se, invece, si aggiorna una distribuzione esistente, il file cscfg non verrà aggiornato in Azure. È comunque possibile modificare le impostazioni di diagnostica, tuttavia, eseguendo i passaggi illustrati nella sezione successiva. Per altre informazioni sull'esecuzione di una distribuzione completa e sull'aggiornamento di una distribuzione esistente, vedere [Procedura guidata Pubblica l'applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Per visualizzare i dati di diagnostica della macchina virtuale
1. Dal menu di scelta rapida per la macchina virtuale scegliere **Visualizza dati di diagnostica**.
   
    ![Visualizzare i dati di diagnostica in una macchina virtuale di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    Viene visualizzata la finestra di dialogo **Riepilogo diagnostica**.
   
    ![Riepilogo di diagnostica della macchina virtuale di Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Se i dati più recenti non vengono visualizzati, potrebbe essere necessario attendere il termine del periodo di trasferimento.
   
    Per aggiornare immediatamente i dati, selezionare il collegamento **Aggiorna**. Per attivare l'aggiornamento automatico dei dati, selezionare un intervallo nella casella di riepilogo a discesa **Aggiornamento automatico**. Per esportare i dati di errore, selezionare il pulsante **Esporta in CSV** per creare un file con valori delimitati da virgole che può essere aperto in un foglio di lavoro di Excel.

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>Configurare la diagnostica del servizio cloud dopo la distribuzione
Se si sta esaminando un problema relativo a un servizio cloud già in esecuzione, è possibile che si voglia raccogliere dati non specificati prima della distribuzione originale del ruolo. In questo caso è possibile iniziare a raccogliere i dati modificando le impostazioni in Esplora server. È possibile configurare la diagnostica per un'istanza singola o per tutte le istanze di un ruolo, a seconda che la finestra di dialogo **Configurazione di diagnostica** venga aperta dal menu di scelta rapida per l'istanza o per il ruolo. Se si configura il nodo del ruolo, eventuali modifiche verranno applicate a tutte le istanze. Se si configura il nodo dell'istanza, eventuali modifiche verranno applicate solo a tale istanza.

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>Per configurare la diagnostica per un servizio cloud in esecuzione
1. In Esplora server espandere il nodo **Servizi cloud** e quindi espandere l'elenco dei nodi per trovare il ruolo o l'istanza da esaminare o entrambi.
   
    ![Configurare la diagnostica](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. Dal menu di scelta rapida per un nodo di istanza o un nodo di ruolo scegliere **Aggiorna impostazioni diagnostica** e quindi selezionare le impostazioni di diagnostica da raccogliere.
   
    Per informazioni sulle impostazioni di configurazione, vedere la sezione **Configurare le origini dati di diagnostica** in questo articolo. Per informazioni su come visualizzare i dati di diagnostica, vedere la sezione **Visualizzare i dati di diagnostica** in questo articolo.
   
    Se si modifica la raccolta di dati in Esplora server, le modifiche rimangono valide fino a una nuova distribuzione completa del servizio cloud. Se si usano le impostazioni di pubblicazione predefinite, le modifiche non vengono sovrascritte. Le impostazioni di pubblicazione predefinite prevedono l'aggiornamento della distribuzione esistente, anziché una ridistribuzione completa. Per assicurarsi che le impostazioni vengano cancellate durante la distribuzione, passare alla scheda **Impostazioni avanzate** nella Pubblicazione guidata e deselezionare la casella di controllo **Aggiornamento distribuzione**. Quando si esegue la nuova distribuzione con tale casella di controllo deselezionata, verranno ripristinate le impostazioni del file con estensione wadcfgx (o wadcfg) definite tramite l'editor delle **Proprietà** per il ruolo specifico. Se si aggiorna la distribuzione, Azure mantiene le impostazioni precedenti.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Risolvere i problemi del servizio cloud di Azure
Se si verificano problemi con i progetti del servizio cloud, come un ruolo bloccato con stato "occupato", che viene riciclato ripetutamente o genera un errore di server interno, è possibile usare alcuni strumenti e tecniche per diagnosticare e risolvere il problema. Per esempi specifici di problemi comuni e soluzioni, oltre a una panoramica dei concetti e degli strumenti usati per diagnosticare e risolvere questi errori, vedere [Azure PaaS compute diagnostics data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) (Dati di diagnostica di calcolo delle soluzioni PaaS per Azure).

## <a name="q--a"></a>Domande e risposte
**Quali sono le dimensioni del buffer e quanto dovrebbe essere grande?**

In ogni istanza della macchina virtuale le quote limitano la quantità di dati di diagnostica che possono essere archiviati nel file system locale. È anche possibile specificare una dimensione del buffer per ogni tipo di dato di diagnostica disponibile. Tale dimensione del buffer funge da quota individuale per quel tipo di dati. Per determinare la quota complessiva e la quantità di memoria rimanente, vedere il tipo di dati di diagnostica nella parte inferiore della finestra di dialogo. Se si specificano buffer più grandi o più tipi di dati, ci si avvicinerà alla quota complessiva. È possibile cambiare la quota complessiva, modificando il file di configurazione diagnostics.wadcfg o wadcfgx. I dati di diagnostica vengono archiviati nello stesso file system dei dati dell'applicazione. Se l'applicazione usa una grande quantità di spazio su disco, non è necessario aumentare la quota di diagnostica complessiva.

**che cos'è il periodo di trasferimento e quanto deve essere lungo?**

Il periodo di trasferimento corrisponde alla quantità di tempo trascorsa tra acquisizioni dei dati. Dopo ogni periodo di trasferimento, i dati vengono spostati dal file system locale su una macchina virtuale a tabelle nell'account di archiviazione. Se la quantità di dati raccolta supera la quota prima del termine di un periodo di trasferimento, i dati precedenti verranno eliminati. Se si perdono dati perché i dati superano la dimensione del buffer o la quota complessiva, è possibile ridurre il periodo di trasferimento.

**Che fuso orario viene usato per i timestamp?**

I timestamp usano il fuso orario locale del data center che ospita il servizio cloud. Vengono usate le tre colonne di timestamp seguenti nelle tabelle di log:

* **PreciseTimeStamp**: timestamp ETW dell'evento, ovvero l'ora di registrazione dell'evento dal client.
* **TIMESTAMP**: valore per **PreciseTimeStamp** arrotondato per difetto al limite di frequenza di caricamento. Ad esempio, se la frequenza di caricamento è pari a 5 minuti e l'ora dell'evento è 00.17.12, TIMESTAMP è 00.15.00.
* **Timestamp**: timestamp in corrispondenza del quale l'entità è stata creata nella tabella di Azure.

**Come si gestiscono i costi durante la raccolta delle informazioni di diagnostica?**

Le impostazioni predefinite (**Livello log** impostato su **Errore** e **Periodo di trasferimento** impostato su **1 minuto**) sono progettate per ridurre al minimo i costi. I costi calcolati aumentano se si raccolgono più dati di diagnostica o si riduce il periodo di trasferimento. Non raccogliere una quantità di dati superiore a quanto necessario e non dimenticare di disabilitare la raccolta di dati quando non è più necessaria. È sempre possibile abilitarla di nuovo, anche in fase di esecuzione, come descritto in precedenza in questo articolo.

**Come si raccolgono log relativi alle richieste non riuscite da IIS?**

Per impostazione predefinita, IIS non raccoglie log relativi alle richieste non riuscite. È possibile configurare IIS per raccogliere i log di richieste non riuscite modificando il file web.config per il ruolo Web.

**Non si ottengono informazioni di traccia dai metodi RoleEntryPoint quali OnStart. Qual è il problema?**

I metodi di **RoleEntryPoint** vengono chiamati nel contesto di WAIISHost.exe, non in IIS. Le informazioni di configurazione nel file web.config che in genere abilitano la traccia non sono applicabili in questo caso. Per risolvere il problema, aggiungere un file con estensione config al progetto di ruolo Web e assegnare al file un nome corrispondente all'assembly di output contenente il codice **RoleEntryPoint**. Nel progetto di ruolo Web predefinito il nome del file con estensione config è WAIISHost.exe.config. Aggiungere le righe seguenti al file:

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

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle registrazioni di diagnostica in Azure, vedere [Abilitazione di Diagnostica di Azure in servizi cloud di Azure](cloud-services/cloud-services-dotnet-diagnostics.md) e [Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure](app-service/web-sites-enable-diagnostic-log.md).

