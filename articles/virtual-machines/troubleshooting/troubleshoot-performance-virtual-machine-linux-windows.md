---
title: Risoluzione dei problemi di prestazioni generici per macchine virtuali di Azure che eseguono Linux o Windows
description: Questo articolo descrive la risoluzione dei problemi di prestazioni generiche della macchina virtuale (VM) tramite il monitoraggio e l'osservazione dei colli di bottiglia e fornisce la possibile correzione per i problemi che possono verificarsi.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: fc8cc4834997033203376cd33670cc907e2911e7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170288"
---
# <a name="generic-performance-troubleshooting-for-azure-virtual-machine-running-linux-or-windows"></a>Risoluzione dei problemi di prestazioni generici per macchine virtuali di Azure che eseguono Linux o Windows

Questo articolo descrive la risoluzione dei problemi di prestazioni generiche della macchina virtuale (VM) tramite il monitoraggio e l'osservazione dei colli di bottiglia e fornisce la possibile correzione per i problemi che possono verificarsi.

## <a name="enabling-monitoring"></a>Abilitazione del monitoraggio

### <a name="azure-iaas-virtual-machine-monitoring"></a>Monitoraggio delle macchine virtuali IAAS di Azure

Per monitorare la macchina virtuale Guest, usare il monitoraggio delle VM di Azure, che segnalerà l'utente a determinate condizioni di risorse di alto livello. Per verificare se la diagnostica della macchina virtuale è abilitata, vedere [Panoramica dei log delle risorse di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview#collecting-resource-logs). Se viene visualizzato quanto segue, probabilmente la diagnostica non è abilitata:

![Il monitoraggio non è abilitato](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Abilitare la diagnostica delle macchine virtuali tramite Microsoft portale di Azure

Per abilitare la diagnostica della macchina virtuale, passare alla macchina virtuale, fare clic su **Impostazioni**e quindi fare clic su **diagnostica**.

![Fare clic su impostazioni, quindi su diagnostica](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)
 
### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Abilitare la diagnostica dell'account di archiviazione tramite portale di Azure

Per prima cosa, identificare l'account di archiviazione (o gli account) usato dalla VM selezionando la macchina virtuale. Fare clic su **Impostazioni**e quindi su **dischi**:

![Fare clic su impostazioni, quindi su dischi](media/troubleshoot-performance-virtual-machine-linux-windows/3-storage-disks-disks-selection.png)

Nel portale passare all'account di archiviazione (o agli account) per la macchina virtuale ed eseguire i passaggi seguenti:

![Selezionare le metriche BLOB](media/troubleshoot-performance-virtual-machine-linux-windows/4-select-blob-metrics.png)
 
1. Selezionare **tutte le impostazioni**.
2. Attivare la diagnostica.
3. Selezionare *metrica *BLOB** * e impostare conservazione su **30** giorni.
4. Salvare le modifiche.

## <a name="observing-bottlenecks"></a>Osservazione di colli di bottiglia

### <a name="accessing-the-monitoring"></a>Accesso al monitoraggio

Selezionare la macchina virtuale di Azure che si vuole analizzare e selezionare **monitoraggio**.

![Seleziona monitoraggio](media/troubleshoot-performance-virtual-machine-linux-windows/5-observe-monitoring.png)
 
### <a name="timelines-of-observation"></a>Sequenze temporali di osservazione

Per individuare eventuali colli di bottiglia delle risorse, esaminare i dati. Se la ricerca del computer è stata eseguita correttamente, ma è stato segnalato che le prestazioni sono diminuite recentemente, esaminare un intervallo di tempo di dati che includa i dati delle metriche delle prestazioni prima che il report venga modificato, durante e dopo il problema.

### <a name="check-for-cpu-bottleneck"></a>Verificare il collo di bottiglia della CPU

![Verificare il collo di bottiglia della CPU](media/troubleshoot-performance-virtual-machine-linux-windows/6-cpu-bottleneck-time-range.png)

1. Modificare il grafico.
2. Impostare l'intervallo di tempo.
3. È quindi necessario aggiungere il contatore: Percentuale CPU sistema operativo guest
4. Salvare.

### <a name="cpu-observe-trends"></a>Tendenze di osservazione CPU

Quando si esaminano i problemi di prestazioni, tenere presenti le tendenze e comprendere se influiscono sull'utente. Nelle sezioni successive verranno usati i grafici di monitoraggio del portale per mostrare le tendenze. Possono inoltre essere utili per fare riferimento a differenze tra i comportamenti delle risorse nello stesso periodo di tempo. Per personalizzare i grafici, fare clic su [piattaforma dati di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform).

Spiking: la chiodatura potrebbe essere correlata a un'attività pianificata o a un evento noto. Se è possibile identificare l'attività, determinare se l'attività viene eseguita al livello di prestazioni necessario. Se le prestazioni sono accettabili, potrebbe non essere necessario aumentare le risorse.

Picco e costante: indica spesso un nuovo carico di lavoro. Se non si tratta di un carico di lavoro riconosciuto, abilitare il monitoraggio nella VM per individuare il processo o i processi che causa il comportamento. Una volta riconosciuto il processo, determinare se il consumo maggiore è stato causato da codice inefficiente o da un consumo normale. Se consumo normale, decidere se il processo funziona a livello di prestazioni necessario.

Costante: determinare se la macchina virtuale è sempre eseguita a questo livello o se è stata eseguita solo a tale livello poiché la diagnostica è stata abilitata. In tal caso, identificare il processo o i processi che provocano il problema e prendere in considerazione l'aggiunta di tale risorsa.

Costante aumento: un aumento costante del consumo è spesso un codice inefficiente o un processo che richiede un maggiore carico di lavoro dell'utente.

### <a name="high-cpu-utilization-remediation"></a>Monitoraggio e aggiornamento CPU elevato

Se l'applicazione o il processo non è in esecuzione con il livello di prestazioni corretto e si sta visualizzando la costante 95% + utilizzo CPU, è possibile eseguire una delle attività seguenti:

* Per il sollievo immediato, aumentare le dimensioni della macchina virtuale con un numero maggiore di core
* Informazioni sul problema: individuare l'applicazione o il processo e risolvere i problemi di conseguenza.

Se la macchina virtuale è stata aumentata e la CPU è ancora in esecuzione 95%, determinare se questa impostazione offre prestazioni migliori o una velocità effettiva delle applicazioni superiore a un livello accettabile. In caso contrario, risolvere i problemi relativi a application\process. singoli

## <a name="check-for-memory-bottleneck"></a>Verifica il collo di bottiglia della memoria

Per visualizzare le metriche:

1. Aggiungere una sezione.
2. Aggiungere un riquadro.
3. Aprire la raccolta.
4. Selezionare l'utilizzo della memoria e trascinare. Quando il riquadro è ancorato, fare clic con il pulsante destro del mouse e selezionare **6x4**.

### <a name="memory-observe-trends"></a>Tendenze di osservazione della memoria

L'utilizzo della memoria Mostra la quantità di memoria utilizzata con la macchina virtuale. Comprendere la tendenza e se viene eseguito il mapping al momento in cui si verificano i problemi. È necessario avere sempre più di 100 MB di memoria disponibile.

Picco e costante/costante consumo costante: un utilizzo elevato della memoria potrebbe non essere la conseguenza di prestazioni non corrette, perché alcune applicazioni come i motori di database relazionali allocano una grande quantità di memoria e questo utilizzo potrebbe non essere significativo. Tuttavia, se sono presenti più applicazioni affamate di memoria, è possibile che si verifichi un calo delle prestazioni dalla contesa di memoria che causa il troncamento e il paging/swapping su disco. Questo scarso rendimento è spesso una delle cause evidenti dell'effetto sulle prestazioni dell'applicazione.

Aumento del consumo costante: un'applicazione possibile "riscaldamento", questo consumo è comune tra l'avvio dei motori di database. Tuttavia, potrebbe anche essere un segno di una perdita di memoria in un'applicazione. Identificare l'applicazione e comprendere se il comportamento è previsto.

Utilizzo file di paging o di scambio: controllare se si sta utilizzando il file di paging di Windows (che si trova nel file di scambio D: \) o Linux (che si trova su `/dev/sdb`). Se non è presente alcun elemento in questi volumi, ad eccezione di questi file, verificare la presenza di letture/scritture elevate su tali dischi. Questo problema è indicativo di condizioni di memoria insufficiente.

### <a name="high-memory-utilization-remediation"></a>Correzione dell'utilizzo elevato della memoria

Per risolvere un utilizzo elevato della memoria, eseguire una delle attività seguenti:

* Per l'utilizzo immediato o per il file di paging o di scambio, aumentare le dimensioni della macchina virtuale con una quantità maggiore di memoria e quindi monitorare.
* Informazioni sul problema: individuare applicazioni/processi e risolvere i problemi per l'identificazione di applicazioni di memoria con utilizzo elevato.
* Se si conosce l'applicazione, vedere se l'allocazione di memoria può essere limitata.

Se dopo l'aggiornamento a una macchina virtuale di dimensioni maggiori si scopre che è ancora presente un aumento costante costante fino al 100%, identificare l'applicazione, il processo e la risoluzione dei problemi.

## <a name="check-for-disk-bottleneck"></a>Verifica il collo di bottiglia del disco

Per controllare il sottosistema di archiviazione per la macchina virtuale, controllare la diagnostica a livello di macchina virtuale di Azure usando i contatori nella diagnostica della macchina virtuale e anche la diagnostica dell'account di archiviazione.

Si noti che non sono presenti contatori per gli account di archiviazione Premium e con ridondanza della zona. Per i problemi correlati a questi contatori, generare un caso di supporto.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Visualizzazione della diagnostica dell'account di archiviazione nel monitoraggio

Per usare gli elementi seguenti, passare all'account di archiviazione per la macchina virtuale nel portale:

![Visualizzazione della diagnostica dell'account di archiviazione nel monitoraggio](media/troubleshoot-performance-virtual-machine-linux-windows/7-virtual-machine-storage-account.png)

1. Modificare il grafico di monitoraggio.
2. Impostare l'intervallo di tempo.
3. Aggiungere i contatori descritti nei passaggi seguenti.
4. Salvare le modifiche.

### <a name="disk-observe-trends-standard-storage-only"></a>Tendenze di osservazione del disco (solo archiviazione standard)

Per identificare i problemi relativi all'archiviazione, esaminare le metriche delle prestazioni della diagnostica dell'account di archiviazione e della diagnostica della macchina virtuale.

Per ogni controllo riportato di seguito, cercare le tendenze principali quando si verificano problemi nell'intervallo di tempo del problema.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Controllare la disponibilità di archiviazione di Azure-aggiungere la metrica dell'account di archiviazione: disponibilità

Se viene visualizzato un calo della disponibilità, potrebbe essersi verificato un problema con la piattaforma, controllare lo [stato di Azure](https://azure.microsoft.com/status/). Se non viene visualizzato alcun problema, generare una nuova richiesta di supporto.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Verificare il timeout di archiviazione di Azure: aggiungere le metriche dell'account di archiviazione:

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

I valori nelle metriche * TimeOutError indicano che un'operazione di i/o ha impiegato troppo tempo e si è verificato un timeout. L'esecuzione dei passaggi successivi consente di identificare le possibili cause.

Valore averageserverlatency aumenta allo stesso tempo in TimeOutErrors può essere un problema di piattaforma. Genera una nuova richiesta di supporto in questa situazione.

AverageE2ELatency rappresenta la latenza del client. Verificare il modo in cui vengono eseguite le operazioni di IOPS dall'applicazione. Cercare un aumento o una metrica TotalRequests sempre elevata. Questa metrica rappresenta IOPS. Se si sta iniziando a raggiungere i limiti dell'account di archiviazione o del disco rigido virtuale singolo, è possibile che la latenza sia correlata alla limitazione.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Verificare la limitazione di archiviazione di Azure: aggiungere le metriche dell'account di archiviazione: ThrottlingError

I valori per la limitazione indicano che la limitazione è a livello di account di archiviazione, vale a dire che l'utente ha raggiunto il limite di IOPS per l'account. È possibile determinare se si sta raggiungendo la soglia di IOPs controllando la metrica **TotalRequests**.

Si noti che ogni disco rigido virtuale ha un limite di 500 IOPS o 60 Mbit, ma è associato al limite cumulativo di 20000 IOPS per account di archiviazione.

Con questa metrica non è possibile stabilire quale BLOB causa la limitazione delle richieste e quali sono le conseguenze. Tuttavia, si raggiungono i limiti di IOPS o di ingresso/uscita dell'account di archiviazione.

Per determinare se si sta raggiungendo il limite di IOPS, passare alla diagnostica dell'account di archiviazione e controllare TotalRequests, cercando di vedere se si sta per raggiungere 20000 TotalRequests. Identificare una modifica nel modello, se il limite viene visualizzato per la prima volta o se questo limite si verifica a un certo momento.

#### <a name="references"></a>Riferimenti

* [Obiettivi di scalabilità per i dischi delle macchine virtuali](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)

La larghezza di banda dell'account di archiviazione viene misurata in base alle metriche dell'account di archiviazione: TotalIngress e TotalEgress. Sono disponibili soglie diverse per la larghezza di banda a seconda del tipo di ridondanza e delle aree.

* [Obiettivi di scalabilità per BLOB, code, tabelle e file](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-blobs-queues-tables-and-files)

Controllare TotalIngress e TotalEgress in base ai limiti di ingresso e uscita per il tipo di ridondanza e l'area dell'account di archiviazione.

Controllare i limiti di velocità effettiva dei dischi rigidi virtuali collegati alla macchina virtuale. Aggiungere il disco della metrica VM lettura e scrittura.

Ogni disco rigido virtuale può supportare fino a 60 MB/s (IOPS non sono esposti per ogni disco rigido virtuale). Esaminare i dati per verificare se si stanno raggiungendo i limiti della velocità effettiva combinata di MB dei dischi rigidi virtuali a livello di macchina virtuale usando la lettura e la scrittura su disco, quindi ottimizzare la configurazione dell'archiviazione delle macchine virtuali per ridimensionare i limiti del disco rigido virtuale precedenti.

### <a name="high-disk-utilizationlatency-remediation"></a>Monitoraggio e aggiornamento latenza elevata del disco

Ridurre la latenza del client e ottimizzare i/o delle VM per scalare i limiti precedenti

* [Ottimizzazione di IO per Windows in Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Ottimizzazione di IO per Linux in Azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Riduzione della limitazione

Se si raggiungono i limiti superiori degli account di archiviazione, bilanciare nuovamente i dischi rigidi virtuali tra gli account di archiviazione. Vedere [obiettivi di scalabilità e prestazioni per archiviazione di Azure](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/).

### <a name="increase-throughput-and-reduce-latency"></a>Aumentare la velocità effettiva e ridurre la latenza

Se si ha un'applicazione sensibile alla latenza e si richiede una velocità effettiva elevata, eseguire la migrazione dei dischi rigidi virtuali in archiviazione Premium di Azure usando la VM serie DS e GS.

Questi articoli illustrano gli scenari specifici:

* [Migrazione ad Archiviazione Premium di Azure](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Usare archiviazione Premium di Azure con SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/forums/).

In alternativa, archiviare un evento imprevisto del supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
