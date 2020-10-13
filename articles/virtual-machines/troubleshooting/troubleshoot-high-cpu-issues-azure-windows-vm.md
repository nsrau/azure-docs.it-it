---
title: Risolvere i problemi di CPU elevata per le macchine virtuali Windows di Azure
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: ffac5ac4d1a8143590e1d72aaafc8a02d6ab04ca
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977256"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>Risolvere i problemi di CPU elevata per le macchine virtuali Windows di Azure

## <a name="summary"></a>Summary

I problemi di prestazioni si verificano in diversi sistemi operativi o applicazioni. ogni problema richiede un approccio univoco per la risoluzione dei problemi. La maggior parte di questi problemi riguarda CPU, memoria, rete e input/output (I/O) come posizioni chiave in cui si verifica il problema. Ognuna di queste aree genera sintomi diversi (a volte simultaneamente) e richiede una diagnosi e una soluzione diverse.

Questo articolo illustra i problemi di utilizzo della CPU elevata che si verificano nelle macchine virtuali di Azure che eseguono il sistema operativo Windows.

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Problemi di CPU elevata in macchine virtuali Windows di Azure

A parte i problemi di i/O e di latenza di rete, la risoluzione dei problemi di CPU e memoria richiede gli stessi strumenti e passaggi dei server locali. Uno degli strumenti supportati in genere da Microsoft è PerfInsights (disponibile sia per Windows che per Linux). PerfInsights può fornire una diagnosi delle procedure consigliate per le macchine virtuali di Azure in un report intuitivo. PerfInsights è anche uno strumento wrapper che consente di raccogliere dati PerfMon, Xperf e Netmon, a seconda dei flag selezionati nello strumento.

La maggior parte degli strumenti esistenti per la risoluzione dei problemi relativi alle prestazioni, ad esempio PerfMon o procmon, usati per i server locali funziona in macchine virtuali Windows di Azure. Tuttavia, PerfInsights è progettato in modo esplicito per le macchine virtuali di Azure per fornire altre informazioni, tra cui le procedure consigliate di Azure, le procedure consigliate di SQL, I grafici di latenza di I/O ad alta risoluzione, le schede CPU e memoria e così via.

Se viene eseguito come User-Mode o in modalità kernel, qualsiasi thread di un processo attivo richiede cicli della CPU per eseguire il codice da cui viene compilato. Molti problemi sono direttamente correlati al carico di lavoro. Il tipo di carico di lavoro esistente sul server comporta l'utilizzo delle risorse, inclusa la CPU.

#### <a name="common-factors"></a>Fattori comuni

I fattori seguenti sono comuni in una situazione di CPU elevata:

- Una modifica o una distribuzione recente del codice applicabile principalmente ad app quali Internet Information Services (IIS), Microsoft SharePoint, Microsoft SQL Server o applicazioni di terze parti.

- Aggiornamento recente che potrebbe essere correlato a un aggiornamento a livello di sistema operativo o a aggiornamenti cumulativi a livello di applicazione e correzioni.

- Una modifica di query o indici obsoleti. Le applicazioni del livello dati SQL Server e Oracle hanno anche un'ottimizzazione del piano di query come un altro fattore. Le modifiche ai dati o la mancanza di indici appropriati possono causare un maggiore utilizzo di risorse di calcolo da diverse query.

- Specifico della VM di Azure. Esistono alcuni processi, ad esempio RDAgent, e processi specifici dell'estensione, ad esempio l'agente di monitoraggio, l'agente MMA o il client di sicurezza, che possono causare un utilizzo elevato della CPU. Questi processi devono essere visualizzati dal punto di vista della configurazione o dei problemi noti.

## <a name="troubleshoot-the-issue"></a>Risolvere il problema

Questo articolo è incentrato sull'isolamento del processo problematico. Ulteriori analisi saranno specifiche per il processo che determina un utilizzo elevato della CPU.

Se, ad esempio, il processo è SQL Server (sqlservr.exe), i passaggi successivi saranno quelli per analizzare la query che usa il maggior numero di cicli della CPU in un periodo di tempo specifico.

### <a name="scope-the-issue"></a>Definire l'ambito del problema

Di seguito sono riportate alcune domande da porre per la risoluzione del problema:

- Esiste un modello per il problema? Ad esempio, il problema relativo alla CPU elevata si verifica in un determinato momento ogni giorno, settimana o mese? In tal caso, è possibile correlare questo problema a un processo, un report o un account di accesso utente?

- Il problema relativo alla CPU elevata è stato avviato dopo una modifica recente del codice? Si è applicato un aggiornamento in Windows o in un'applicazione?

- Il problema relativo alla CPU elevata si è avviato dopo una modifica nel carico di lavoro, ad esempio un aumento del numero di utenti, un afflusso di dati più elevato o un numero maggiore di report?

- Per Azure, il problema con CPU elevato inizia in una delle condizioni seguenti?

  - Dopo una ridistribuzione o un riavvio recente
  - Quando uno SKU o un tipo di macchina virtuale è stato modificato
  - Quando è stata aggiunta una nuova estensione
  - Dopo aver apportato modifiche al servizio di bilanciamento del carico

### <a name="azure-caveats"></a>Avvertenze di Azure

Conoscere il carico di lavoro. Quando si seleziona una macchina virtuale, è possibile sottovalutare i conteggi della CPU virtuale (vCPU) quando si esamina il costo complessivo mensile di hosting. Se il carico di lavoro è a elevato utilizzo di calcolo, la selezione di uno SKU di VM più piccolo con uno o due vCPU può causare problemi del carico di lavoro. Testare configurazioni diverse per il carico di lavoro per determinare la migliore funzionalità di calcolo richiesta.

Sono disponibili alcune serie di macchine virtuali, ad esempio serie B (modalità di espansione), consigliate per il controllo di qualità (QA) e i test. L'uso di queste serie nell'ambiente di produzione limita la capacità di elaborazione dopo l'esaurimento dei crediti della CPU.

Per le applicazioni note, ad esempio SQL Server, Oracle, RDS (Servizi Desktop remoto), desktop virtuale di Windows, IIS o SharePoint, sono disponibili articoli sulle procedure consigliate di Azure che includono suggerimenti per la configurazione minima per questi carichi di lavoro.

### <a name="ongoing-high-cpu-issues"></a>Problemi di elevata CPU in corso

Se il problema si verifica in questo momento, è l'opportunità migliore di acquisire la traccia del processo per determinare la causa del problema. Per individuare il processo, è possibile usare gli strumenti esistenti utilizzati per i server Windows locali. Gli strumenti seguenti sono consigliati dal supporto di Azure per le macchine virtuali di Azure.

### <a name="perfinsights"></a>PerfInsights

PerfInsights è lo strumento consigliato dal supporto di Azure per i problemi di prestazioni delle macchine virtuali. È progettato per coprire le procedure consigliate e le schede di analisi dedicate per CPU, memoria e grafici I/O ad alta risoluzione. È possibile eseguirlo OnDemand tramite il portale di Azure o dall'interno della macchina virtuale. È possibile condividere i dati con il team di supporto di Azure.

#### <a name="run-perfinsights"></a>Esegui PerfInsights

PerfInsights è disponibile per il sistema operativo [Windows](./how-to-use-perfinsights.md) e [Linux](./how-to-use-perfinsights-linux.md) . Per Windows, di seguito sono riportate le opzioni.

#### <a name="run-and-analyze-reports-through-azure-portal"></a>Eseguire e analizzare i report tramite portale di Azure

Quando viene [installato tramite il portale di Azure](./performance-diagnostics.md), installa effettivamente un'estensione nella macchina virtuale. Gli utenti possono anche installare PerfInsights come estensione passando direttamente al pannello [estensioni nel pannello della macchina virtuale](./performance-diagnostics-vm-extension.md)e quindi scegliendo un'opzione di diagnostica delle prestazioni.

#### <a name="azure-portal-option-1"></a>Opzione portale di Azure 1

Esplorare il pannello della macchina virtuale e selezionare l'opzione **diagnostica delle prestazioni** . Verrà richiesto di installare l'opzione (USA le estensioni) nella macchina virtuale per cui è stata selezionata.

  ![Installare la diagnostica delle prestazioni](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Opzione portale di Azure 2

Sfogliare per **diagnosticare e risolvere i problemi** nel pannello della macchina virtuale e cercare **problemi di prestazioni delle macchine virtuali**.

  ![Risolvere i problemi di prestazioni delle macchine virtuali](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

Se si seleziona **risoluzione dei problemi**, viene caricata la schermata di installazione di PerfInsights.

Se si seleziona **Installa**, l'installazione fornisce diverse opzioni di raccolta.

  ![Analisi delle prestazioni](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

Le opzioni numerate nello screenshot sono correlate ai commenti seguenti:

1. Per l'opzione di **CPU elevata**, selezionare **analisi prestazioni** o **Avanzate**.

2. Quando si aggiungono i sintomi, questi verranno aggiunti al report, che consente di condividere le informazioni con il supporto di Azure.

3. Consente di selezionare la durata della raccolta dei dati. Per l'opzione High-CPU selezionare almeno 15 minuti o più. In modalità portale di Azure è possibile raccogliere fino a 15 minuti di dati. Per periodi più lunghi di raccolta, è necessario eseguire il programma come un eseguibile all'interno della macchina virtuale.

4. Se viene richiesto dal supporto tecnico di Azure per raccogliere questi dati, è possibile aggiungere qui il numero di ticket. Questo campo è facoltativo.

5. Selezionare questo campo per accettare il contratto di licenza con l'utente finale (EULA).

6. Selezionare questo campo se si intende rendere disponibile questo report al team di supporto di Azure in questo caso.

Il report viene archiviato in uno degli account di archiviazione nella sottoscrizione. È disponibile per la visualizzazione e il download in un secondo momento.

#### <a name="run-perfinsights-from-within-the-vm"></a>Eseguire PerfInsights dall'interno della macchina virtuale

Questo metodo può essere utilizzato se si intende eseguire PerfInsights per periodi di tempo più lunghi. L' [articolo PerfInsights](./how-to-use-perfinsights.md#how-do-i-run-perfinsights) fornisce una procedura dettagliata per i diversi comandi e flag necessari per eseguire PerfInsights come file eseguibile. Ai fini dell'utilizzo elevato della CPU, è necessario disporre di una delle modalità seguenti:

- Scenario avanzato

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- Scenario lento (prestazioni) della macchina virtuale

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

L'output del comando si troverà nella stessa cartella in cui è stato salvato il file eseguibile PerfInsights.

#### <a name="what-to-look-for-in-the-report"></a>Cosa cercare nel report

Dopo aver eseguito il report, il percorso del contenuto dipende dal fatto che sia stato eseguito tramite il portale di Azure o come file eseguibile. Per entrambe le opzioni, accedere alla cartella dei log generata oppure scaricare (se portale di Azure) localmente per l'analisi.

### <a name="run-through-the-azure-portal"></a>Eseguire l'portale di Azure

  ![Diagnostica delle prestazioni a elevato effetto](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![Scarica report](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>Eseguire dall'interno della macchina virtuale

La struttura di cartelle dovrebbe essere simile alle immagini seguenti:

  ![Seleziona output](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![Struttura di cartelle](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. Qualsiasi raccolta aggiuntiva, ad esempio PerfMon, Xperf, Netmon, log SMB, log eventi e così via, è reperibile nella cartella di output.

1. Report effettivo insieme a analisi e consigli.

1. Per entrambe le prestazioni (VMslow) e Advanced, il report raccoglie le informazioni **PerfMon** per la durata dell'esecuzione di PerfInsights.

1. I registri eventi mostrano una rapida panoramica dei dettagli utili sull'arresto anomalo a livello di sistema o del processo.

#### <a name="where-to-start"></a>Dove iniziare

Aprire il report PerfInsights. La scheda **risultati** registra eventuali outlier in termini di utilizzo delle risorse. Se sono presenti istanze di utilizzo elevato della CPU, la scheda **risultati** lo suddividerà come un effetto elevato o con un effetto medio.

  ![Risorse a livello di effetto](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

Analogamente all'esempio precedente, PerfInsights è stato eseguito per 30 minuti. Per metà del tempo, il processo evidenziato stava esaurendo la CPU sul lato superiore. Se lo stesso processo era in esecuzione durante l'intervallo di tempo di raccolta, il livello di effetto sarebbe stato modificato in **alto**.

Se si espande l'evento **risultati** , verranno visualizzati diversi dettagli chiave. La scheda elenca i processi in ordine decrescente, in base all'utilizzo **medio della CPU** e indica se il processo è stato correlato al sistema, a un'app di proprietà di Microsoft (SQL, IIS) o a un processo di terze parti.

#### <a name="more-details"></a>Altre informazioni

In **CPU** è disponibile un sottoscheda dedicato che può essere usato per l'analisi dettagliata dei modelli, per core o per processo.

La scheda **principali consumer di CPU** dispone di due sezioni separate di interesse ed è possibile visualizzare le statistiche per processore qui. La progettazione dell'applicazione è spesso Single-Threaded o si blocca a un singolo processore. In questo scenario uno o alcuni core vengono eseguiti al 100%, mentre gli altri core vengono eseguiti a livelli previsti. Questi scenari sono più complessi perché la CPU media sul server sembra essere eseguita come previsto, ma i processi aggiunti in core con utilizzo elevato saranno più lenti del previsto.

  ![utilizzo CPU elevato](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

La seconda sezione (ugualmente importante) è la maggior **parte dei consumer di CPU con esecuzione prolungata**. In questa sezione vengono illustrati i dettagli del processo e il relativo modello di utilizzo della CPU. L'elenco è ordinato in presenza di consumer di CPU medi elevati nella parte superiore.

  ![Consumer di CPU di Tom con esecuzione prolungata](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

Queste due schede saranno sufficienti per impostare il percorso per i passaggi successivi per la risoluzione dei problemi. A seconda del processo che determina la condizione di CPU elevata, sarà necessario risolvere le domande poste in precedenza. I processi come SQL Server (sqlservr.exe) o IIS (w3wp.exe) richiedono un drill-down specifico per le modifiche alla query o al codice che causano questa condizione. Per i processi di sistema, ad esempio WMI o Lsass.exe, è necessario seguire un percorso diverso.

Per i processi correlati alle macchine virtuali di Azure, ad esempio i file eseguibili di estensione RDAgent, OMS e di monitoraggio, potrebbe essere necessario correggere una nuova build o una nuova versione ottenendo assistenza dal team di supporto di Azure.

### <a name="perfmon"></a>PerfMon

**PerfMon** è uno degli strumenti più recenti per la risoluzione di un problema di risorse in Windows Server. Non fornisce un report chiaro con suggerimenti o risultati. Al contrario, è necessario che l'utente esplori i dati raccolti e usi un filtro specifico nelle diverse categorie di contatori.

PerfInsights raccoglie PerfMon come log aggiuntivo per VMSlow e scenari avanzati. Tuttavia, Perfmon può essere raccolto in modo indipendente e presenta questi vantaggi aggiuntivi:

- Può essere raccolta in remoto.

- È possibile pianificarlo tramite le **attività**.

- Può essere raccolto per periodi di tempo più lunghi o in modalità continua con la funzionalità di rollup.

Si consideri lo stesso esempio illustrato in PerfInsights per vedere come PerfMon Visualizza questi dati. Le categorie di contatori obbligatorie sono le seguenti:

- Informazioni sul processore >% tempo processore > _Total

- Elabora >% ProcessorTime > tutte le istanze

#### <a name="where-to-start"></a>Dove iniziare

I nomi dei file di output di PerfMon hanno un' `.blg` estensione. È possibile raccogliere questi file in modo indipendente o usando PerfInsights. Per questa discussione, si userà la PerfMon `.blg` inclusa nei dati PerfInsights e raccolti in base all'esempio precedente.

In PerfMon non sono disponibili report predefiniti per l'utente. Sono disponibili visualizzazioni diverse che modificano il tipo di grafico, ma il processo di filtraggio (o il lavoro richiesto per identificare i processi colpevoli) è manuale.

> [!NOTE]
> Lo [strumento PAL](https://github.com/clinthuffman/PAL) può utilizzare `.blg` file e generare report dettagliati.

Per iniziare, selezionare la categoria **Aggiungi contatori** .

1. In **contatori disponibili**selezionare il contatore **% ProcessorTime** nella categoria **informazioni processore** .

1. Selezionare **_Total**, che fornisce le statistiche di tutti i core combinati.

1. Selezionare **Aggiungi**. La finestra Mostra **% ProcessorTime** in  **contatori aggiunti**.

  ![Aggiungi tempo processore](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

Dopo il caricamento dei contatori, verranno visualizzati i grafici di tendenza riga nell'intervallo di tempo della raccolta. È possibile selezionare o deselezionare i contatori. Fino ad ora è stato aggiunto un solo contatore.

  ![Impostazioni di performance monitor](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

Ogni contatore avrà valori **medi**, **minimi**e **massimi** . Concentrarsi sui valori **medi** e **massimi** , in quanto il valore medio può variare a seconda della durata della raccolta dei dati. Se l'attività di CPU elevata è stata considerata per 10 minuti mentre la raccolta complessiva era 40 minuti, i valori medi saranno molto inferiori.

Il grafico tendenza precedente mostra che il **processore totale** è stato compreso quasi il 80% per circa 15 minuti.

#### <a name="identify-the-process"></a>Identificare il processo

È stato rilevato che il server ha un consumo di CPU elevato per un periodo di tempo specificato, ma il driver non è ancora stato identificato. A differenza di PerfInsights, è necessario cercare manualmente il processo colpevole in questo caso.

Per questa attività è necessario cancellare o rimuovere i contatori **% ProcessorTime** aggiunti in precedenza, quindi aggiungere una nuova categoria:

- Elabora >% ProcessorTime > tutte le istanze

Questa categoria caricherà i contatori per tutti i processi in esecuzione in quel momento.

  ![Aggiungere contatori](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

In un computer di produzione tipico, centinaia o processi possono essere in esecuzione. Quindi, potrebbe essere necessario un po' di tempo per cancellare ogni contatore che sembra avere un grafico di tendenza basso o piatto.

Per accelerare questo processo, usare la visualizzazione **istogramma** e modificare il tipo di visualizzazione da **riga** a **istogramma**, che fornirà un grafico a barre. Si noterà che è più semplice scegliere i processi in cui si verifica un utilizzo elevato della CPU durante la fase di raccolta.

Poiché è sempre presente una barra per il **totale**, concentrarsi sulle barre che mostrano una velocità di esaurimento elevata. È possibile eliminare le altre barre per pulire la visualizzazione. Tornare quindi alla visualizzazione **riga** .

  ![Indicatori di performance monitor](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

È ora più facile rilevare il processo responsabile. Per impostazione predefinita, i valori **Max** e **min** sono multipli del numero di core del server o dei thread del processo.

  ![Risultati di performance monitor](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

L'elenco degli strumenti disponibili non termina in PerfInsights per PerfMon. Si ha accesso ad altri strumenti, ad esempio **ProcessMonitor** (procmon) o **Xperf**. Sono disponibili molti strumenti di terze parti da usare in modo necessario.

### <a name="azure-monitoring-tools"></a>Strumenti di monitoraggio di Azure

Le macchine virtuali di Azure hanno metriche affidabili che includono informazioni di base, ad esempio CPU, I/O di rete e byte I/O. Per le metriche avanzate, ad esempio monitoraggio di Azure, sarà necessario effettuare solo alcune selezioni per configurare e usare un account di archiviazione specificato.

#### <a name="basic-default-counters"></a>Contatori di base (predefinito)

  ![Metriche del grafico](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>Abilitazione di monitoraggio di Azure

Dopo aver abilitato le metriche di monitoraggio di Azure, il software installa un'estensione nella macchina virtuale e quindi inizia a raccogliere metriche granulari, che includono contatori Perfmon.

  ![Account di archiviazione di diagnostica](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

Le categorie di contatori di **base** sono impostate come **predefinite**. Tuttavia, è anche possibile impostare una raccolta **personalizzata** .

  ![Contatori delle prestazioni](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

Una volta abilitate le impostazioni, è possibile visualizzare questi contatori **Guest** nella sezione **metrica** . È anche possibile impostare **avvisi** (compresi i messaggi di posta elettronica) se le metriche raggiungono una determinata soglia.

  ![Spazio dei nomi metrica](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

Per altre informazioni su come usare monitoraggio di Azure per gestire le VM di Azure, vedere [monitoraggio di macchine virtuali di Azure con monitoraggio di Azure](../../azure-monitor/insights/monitor-vm-azure.md).

### <a name="reactive-troubleshooting"></a>Risoluzione dei problemi reattivo

Se il problema si è già verificato, è necessario individuare la causa del problema della CPU elevata. La posizione reattiva può essere complessa. La modalità di raccolta dati non sarà utile perché il problema si è già verificato.

Se questo problema si verificava una sola volta, potrebbe essere difficile determinare l'app che lo ha causato. Se la macchina virtuale di Azure è stata configurata per l'uso di OMS o di un altro rilevamento della diagnostica, è comunque possibile ottenere informazioni su ciò che ha causato il problema.

Se si sta usando un modello ripetuto, raccogliere i dati durante il periodo di tempo in cui il problema probabilmente verrà eseguito successivamente.

PerfInsights non dispone ancora di una funzionalità di **esecuzione pianificata** . Tuttavia, Perfmon può essere eseguito e pianificato tramite la riga di comando.

### <a name="logman-command"></a>Logman (comando)

Il comando **logman create counter** viene usato per eseguire la raccolta PerfMon attraverso la riga di comando, per pianificarla tramite **Gestione attività**o per eseguirla in modalità remota.

**Esempio** (include la modalità di raccolta remota)

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

Logman.exe possono anche essere avviate da un computer di macchine virtuali di Azure peer nella stessa VNET.

Per ulteriori informazioni su questi parametri, vedere [logman create counter](/windows-server/administration/windows-commands/logman-create-counter).

Dopo la raccolta dei dati PerfMon mentre si verifica il problema, i passaggi rimanenti per analizzare i dati sono gli stessi descritti in precedenza.

## <a name="conclusion"></a>Conclusione

Per eventuali problemi di prestazioni, la comprensione del carico di lavoro è fondamentale per la risoluzione del problema. Le opzioni in SKU di VM diversi e diverse opzioni di archiviazione su disco devono essere valutate tenendo conto del carico di lavoro di produzione. Il processo di test delle soluzioni in macchine virtuali diverse può aiutare a prendere la decisione migliore.

Poiché le operazioni utente e la quantità di dati variano, è sempre possibile usare un buffer per le funzionalità di calcolo, rete e I/O della macchina virtuale. A questo punto, qualsiasi cambiamento improvviso nel carico di lavoro non ha un effetto eccezionale.

Se si prevede un aumento del carico di lavoro presto, passare a uno SKU superiore con una potenza di elaborazione maggiore. Se il carico di lavoro sarà a elevato utilizzo di calcolo, scegliere le SKU della macchina virtuale in maniera saggia.