---
title: Risolvere i problemi relativi alle prestazioni delle macchine virtuali di Azure in Linux o WindowsTroubleshoot Azure virtual machine performance on Linux or Windows
description: Questo articolo descrive la risoluzione dei problemi di prestazioni generiche della macchina virtuale (VM) tramite il monitoraggio e l'osservazione dei colli di bottiglia e fornisce possibili correzioni per i problemi che possono verificarsi.
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
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772619"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Risolvere i problemi relativi alle prestazioni delle macchine virtuali di Azure in Linux o WindowsTroubleshoot Azure virtual machine performance on Linux or Windows

Questo articolo descrive la risoluzione dei problemi di prestazioni generiche della macchina virtuale (VM) tramite il monitoraggio e l'osservazione dei colli di bottiglia e fornisce possibili correzioni per i problemi che possono verificarsi. Oltre al monitoraggio, è anche possibile usare Perfinsights che può fornire un report con consigli sulle procedure consigliate e colli di bottiglia chiave intorno a IO/CPU/Memory. Perfinsights è disponibile sia per le macchine virtuali Windows che per le macchine virtuali Linux in Azure.Perfinsights is available for both [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) and [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) VM's in Azure.

Questo articolo illustra l'uso del monitoraggio per diagnosticare i colli di bottiglia delle prestazioni.

## <a name="enabling-monitoring"></a>Abilitazione del monitoraggio

### <a name="azure-iaas-virtual-machine-monitoring"></a>Monitoraggio delle macchine virtuali IAAS di AzureAzure IAAS virtual machine monitoring

Per monitorare la macchina virtuale guest, usare il monitoraggio della macchina virtuale di Azure, che avviserà l'utente di determinate condizioni di risorse di alto livello. Per verificare se la diagnostica della macchina virtuale è abilitata, vedere [Panoramica dei log delle risorse](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs)di Azure.To check whether you have the VM diagnostics enabled, see Azure Resource logs overview. Se viene visualizzato quanto segue, molto probabilmente la diagnostica non è abilitata:

![Il monitoraggio non è abilitato](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Abilitare la diagnostica delle macchine virtuali tramite il portale di Microsoft AzureEnable VM diagnostics through microsoft Azure portal

Per abilitare la diagnostica delle macchine virtuali:To enable VM diagnostics:

1. Passare alla macchina virtualeGo to the VM
2. Fare clic su **Impostazioni di diagnostica**
3. Selezionare l'account di archiviazione e fare clic su **Abilita monitoraggio a livello di guest.**

   ![Fare clic su Impostazioni, quindi su Diagnostica](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

È possibile controllare l'account di archiviazione usato per l'impostazione di Diagnostica dalla scheda **Agente** in **Impostazioni di diagnostica**.

![Controllare l'account di archiviazioneCheck storage account](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Abilitare la diagnostica dell'account di archiviazione tramite il portale di AzureEnable storage account diagnostics through Azure portal

L'archiviazione è un livello molto importante quando si intende analizzare le prestazioni di I/O per una macchina virtuale in Azure.Storage is a very important tier when we intend to analyze IO performance for a Virtual Machine in Azure. Per le metriche correlate all'archiviazione è necessario abilitare la diagnostica come passaggio aggiuntivo. Questo potrebbe anche essere abilitato, se vogliamo solo analizzare i contatori correlati all'archiviazione.

1. Identificare l'account di archiviazione (o gli account) usato dalla macchina virtuale selezionando la macchina virtuale. Fare clic su **Impostazioni**, quindi su **Dischi**:

   ![Fare clic su Impostazioni, quindi su Dischi](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. Nel portale passare all'account di archiviazione o agli account per la macchina virtuale e eseguire i passaggi seguenti:In the portal, go to the storage account (or accounts) for the VM and work through the following steps:

   1. Fare clic su Panoramica per l'account di archiviazione trovato con il passaggio precedente.
   2. Verranno visualizzate le metriche predefinite. 

    ![Metriche predefinite](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Fare clic su una delle metriche, che mostrerà un altro pannello con più opzioni per configurare e aggiungere metriche.

   ![Aggiungi Metriche](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

Per configurare queste opzioni:

1.  Selezionare **Metriche**.
2.  Selezionare la **risorsa** (account di archiviazione).
3.  Selezionare lo **spazio dei nomi**
4.  Selezionare **Metrica**.
5.  Selezionare il tipo di **aggregazione**
6.  È possibile aggiungere questa visualizzazione nel dashboard.

## <a name="observing-bottlenecks"></a>Osservare i colli di bottiglia

Dopo aver eseguito il processo di configurazione iniziale per le metriche necessarie e dopo aver abilitato la diagnostica per la macchina virtuale e l'account di archiviazione correlato, è possibile passare alla fase di analisi.

### <a name="accessing-the-monitoring"></a>Accesso al monitoraggio

Selezionare la macchina virtuale di Azure che si vuole analizzare e selezionare **Monitoraggio**.

![Seleziona monitoraggio](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Tempestività di osservazione

Per identificare se sono presenti colli di bottiglia delle risorse, esaminare i dati. Se il computer ha rilevato che il computer è stato eseguito correttamente, ma è stato segnalato che le prestazioni sono state recentemente ridotte, esaminare un intervallo di tempo di dati che include i dati delle metriche delle prestazioni prima della modifica del segnalato, durante e dopo il problema.

### <a name="check-for-cpu-bottleneck"></a>Verificare il collo di bottiglia della CPU

![Verificare la presenza di colli di bottiglia della CPU](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Modificare il grafico.
2. Impostare l'intervallo di tempo.
3. È quindi necessario aggiungere nel contatore: CPU Percentuale sistema operativo guest
4. Salvare.

### <a name="cpu-observe-trends"></a>La CPU osserva le tendenze

Quando si esaminano i problemi di prestazioni, essere consapevoli delle tendenze e capire se influenzano l'utente. Nelle sezioni successive verranno utilizzati i grafici di monitoraggio dal portale per mostrare le tendenze. Possono anche essere utili per fare riferimento incrociato i comportamenti delle risorse di differenza nello stesso periodo di tempo. Per personalizzare i grafici, fare clic su [Piattaforma dati di Monitoraggio di Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)

Spiking – Spiking potrebbe essere correlato a un'attività pianificata /evento noto. Se è possibile identificare l'attività, determinare se l'attività viene eseguita al livello di prestazioni richiesto. Se le prestazioni sono accettabili, potrebbe non essere necessario aumentare le risorse.

Spike up e Constant: spesso indica un nuovo carico di lavoro. Se non si tratta di un carico di lavoro riconosciuto, abilitare il monitoraggio nella macchina virtuale per individuare il processo (o i processi) che causa il comportamento. Una volta riconosciuto il processo, determinare se l'aumento del consumo è causato da codice inefficiente o dal consumo normale. Se il consumo normale, decidere se il processo funziona al livello di prestazioni richiesto.

Costante: determinare se la macchina virtuale è sempre stata eseguita a questo livello o se è stata eseguita solo a tale livello da quando la diagnostica è stata abilitata. In tal caso, identificare il processo (o i processi) che causa il problema e prendere in considerazione l'aggiunta di un'altra risorsa.

Aumento costante: un aumento costante del consumo è spesso codice inefficiente o un processo che assume più carico di lavoro dell'utente.

### <a name="high-cpu-utilization-remediation"></a>Correzione dell'utilizzo elevato della CPU

Se l'applicazione o il processo non è in esecuzione al livello di prestazioni corretto e viene costante l'utilizzo della CPU pari al 95%, è possibile eseguire una delle attività seguenti:

* Per un sollievo immediato - Aumentare le dimensioni della macchina virtuale a una dimensione con più core
* Comprendere il problema: individuare l'applicazione/processo e risolvere i problemi di conseguenza.

Se la macchina virtuale è stata aumentata e la CPU è ancora in esecuzione 95%, determinare se questa impostazione offre prestazioni migliori o una maggiore velocità effettiva dell'applicazione a un livello accettabile. In caso contrario, risolvere i problemi relativi alla singola applicazione e al processo.

È possibile utilizzare Perfinsights per [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) o [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) per analizzare quale processo determina l'utilizzo della CPU. 

## <a name="check-for-memory-bottleneck"></a>Verificare il collo di bottiglia della memoria

Per visualizzare le metriche:

1. Aggiungere una sezione.
2. Aggiungere un riquadro.
3. Aprire la Galleria.
4. Selezionare utilizzo memoria e trascinare. Quando il riquadro è ancorato, fare clic con il pulsante destro del mouse e selezionare **6x4**.

### <a name="memory-observe-trends"></a>La memoria osserva le tendenze

L'utilizzo della memoria mostra la quantità di memoria utilizzata con la macchina virtuale. Comprendere la tendenza e se si esegue il mapping al momento in cui si vedono i problemi. Si dovrebbe sempre avere più di 100 MB di memoria disponibile.

Spike e consumo costante costante/costante: l'utilizzo elevato della memoria potrebbe non essere la causa di prestazioni non ottimali, poiché alcune applicazioni, ad esempio i motori di database relazionali, allocano una grande quantità di memoria e questo utilizzo potrebbe non essere significativo. Tuttavia, se sono presenti più applicazioni affamate di memoria, è possibile riscontrare una riduzione delle prestazioni da conflitti di memoria che causano il taglio e il paging/scambio su disco. Queste prestazioni ridotte sono spesso una causa evidente dell'impatto sulle prestazioni dell'applicazione.

Consumo in costante aumento – Una possibile applicazione 'riscaldamento', questo consumo è comune tra i motori di database di avvio. Tuttavia, potrebbe anche essere un'indicazione di una perdita di memoria in un'applicazione. Identificare l'applicazione e capire se il comportamento è previsto.

Utilizzo della pagina o dello scambio di file: verificare\) se si utilizza il `/dev/sdb`file di paging di Windows (in d: o nel file di scambio Linux (che si trova su ) vengono utilizzati in modo intensivo. Se non si dispone di alcun elemento su questi volumi ad eccezione di questi file, verificare la presenza di letture/scritture elevate su tali dischi. Questo problema è indicativo di condizioni di memoria insufficiente.

### <a name="high-memory-utilization-remediation"></a>Correzione dell'utilizzo elevato della memoriaHigh memory utilization remediation

Per risolvere l'utilizzo elevato della memoria, eseguire una delle attività seguenti:

* Per un sollievo immediato o Per uso immediato di pagine o scambio di file : aumentare le dimensioni della macchina virtuale a uno con più memoria, quindi monitorare.
* Comprendere il problema: individuare le applicazioni/processi e risolvere i problemi relativi all'identificazione delle applicazioni con memoria ad alto consumo.
* Se si conosce l'applicazione, verificare se l'allocazione di memoria può essere limitata.

Se dopo l'aggiornamento a una macchina virtuale di grandi dimensioni, si scopre che si dispone ancora di un costante aumento costante fino al 100%, identificare l'applicazione/processo e risolvere i problemi.

È possibile utilizzare Perfinsights per [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) o [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) per analizzare quale processo sta determinando il consumo di memoria. 

## <a name="check-for-disk-bottleneck"></a>Verificare la presenza di colli di bottiglia del disco

Per controllare il sottosistema di archiviazione per la macchina virtuale, controllare la diagnostica a livello di macchina virtuale di Azure usando i contatori in Diagnostica macchina virtuale e anche Diagnostica account di archiviazione.

Per la risoluzione dei problemi specifici della macchina virtuale, è possibile usare Perfinsights per Windows o Linux , che potrebbe aiutare ad analizzare quale processo sta guidando le informazioni di I/O.For within VM specific troubleshooting, you can use Perfinsights for [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) or [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux), which could help to analyze which process is driving the IO's. 

Si noti che non sono disponibili contatori per gli account di archiviazione ridondanti e Premium. Per problemi correlati a questi contatori, generare un caso di supporto.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Visualizzazione della diagnostica dell'account di archiviazione nel monitoraggioViewing storage account diagnostics in monitoring

Per usare gli elementi seguenti, passare all'account di archiviazione per la macchina virtuale nel portale:To work on the below items, go into the storage account for the VM in the portal:

![Visualizzazione della diagnostica dell'account di archiviazione nel monitoraggioViewing Storage Account Diagnostics in Monitoring](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Modificare il grafico di monitoraggio.
2. Impostare l'intervallo di tempo.
3. Aggiungere i contatori descritti nei passaggi seguenti.
4. Salvare le modifiche.

### <a name="disk-observe-trends-standard-storage-only"></a>Disco osservare le tendenze (solo archiviazione standard)

Per identificare i problemi relativi all'archiviazione, esaminare le metriche delle prestazioni da Diagnostica account di archiviazione e Diagnostica macchina virtuale.

Per ogni controllo riportato di seguito, cercare le tendenze chiave quando si verificano i problemi entro l'intervallo di tempo del problema.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Verificare la disponibilità di Archiviazione di Azure: aggiungere la metrica dell'account di archiviazione: disponibilitàCheck Azure storage availability – Add the storage account metric: availability

Se si verifica un calo della disponibilità, potrebbe essersi verificato un problema con la piattaforma, controllare [lo stato](https://azure.microsoft.com/status/)di Azure . Se non viene visualizzato alcun problema, generare una nuova richiesta di supporto.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Controllare il timeout di archiviazione di Azure: aggiungere le metriche dell'account di archiviazione:Check for Azure storage timeout - Add the storage account metrics:

* ClientTimeOutError (Errore ClientTimeOut)ClientTimeOutError
* ServerTimeOutError (Errore server)
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

I valori nelle metriche di TimeOutError indicano che un'operazione di I/O ha richiesto troppo tempo e si è verificato il timeout. L'elaborazione dei passaggi successivi aiuterà a identificare le possibili cause.

AverageServerLatency aumenta contemporaneamente a TimeOutErrors potrebbe essere un problema della piattaforma. Sollevare una nuova richiesta di supporto in questa situazione.

AverageE2ELatency rappresenta la latenza del client. Verificare la modalità di esecuzione delle operazioni di I/O al secondo da parte dell'applicazione. Cercare una metrica TotalRequests aumento o costantemente elevato. Questa metrica rappresenta le operazioni di I/O al secondo. Se si sta iniziando a raggiungere i limiti dell'account di archiviazione o del singolo disco rigido virtuale, la latenza potrebbe essere correlata alla limitazione delle richieste.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Controllare la limitazione dell'archiviazione di Azure - Aggiungere le metriche dell'account di archiviazione: ThrottlingErrorCheck for Azure storage throttling - Add the storage account metrics: ThrottlingError

I valori per la limitazione delle richieste indicano che si sta limitando a livello di account di archiviazione, il che significa che il limite di operazioni di I/O al secondo dell'account viene limitato. È possibile determinare se si sta raggiungendo la soglia di IOPs controllando la metrica **TotalRequests**.

Si noti che ogni disco rigido virtuale ha un limite di 500 IOPS o 60 MBit, ma è vincolato dal limite cumulativo di 20000 IOPS per account di archiviazione.

Con questa metrica, non è possibile stabilire quale BLOB causa la limitazione delle richieste e quali ne sono interessati. Tuttavia, si sta raggiungendo i limiti di IOPS o Ingress/Egress dell'account di archiviazione.

Per identificare se si sta raggiungendo il limite di operazioni di I/O al secondo, passare alla diagnostica dell'account di archiviazione e controllare TotalRequests, cercando di vedere se ci si sta avvicinando 20 mila TotalRequests. Identificare una modifica nel modello, se viene visualizzato il limite per la prima volta o se questo limite si verifica in un determinato momento.

Con le nuove offerte di disco in Archiviazione standard, i limiti di IOPS e velocità effettiva potrebbero differire, ma il limite cumulativo dell'account di archiviazione Standard è 20000 IOPS (l'archiviazione Premium ha limiti diversi a livello di account o di disco). Ulteriori informazioni sulle diverse offerte di dischi di archiviazione standard e sui limiti per disco:

* [Obiettivi di scalabilità e prestazioni per i dischi delle macchine virtuali in Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)

#### <a name="references"></a>Riferimenti

* [Obiettivi di scalabilità e prestazioni per gli account di archiviazione BLOB di pagine premiumScalability and performance targets for premium page blob storage accounts](../../storage/blobs/scalability-targets-premium-page-blobs.md)

La larghezza di banda dell'account di archiviazione viene misurata dalle metriche dell'account di archiviazione: TotalIngress e TotalEgress. Sono disponibili soglie diverse per la larghezza di banda a seconda del tipo di ridondanza e delle aree.

* [Obiettivi di scalabilità e prestazioni per gli account di archiviazione standard](../../storage/common/scalability-targets-standard-account.md)

Controllare TotalIngress e TotalEgress rispetto ai limiti di ingresso e uscita per il tipo e l'area di ridondanza dell'account di archiviazione.

Controllare i limiti di velocità effettiva dei dischi rigidi virtuali collegati alla macchina virtuale. Aggiungere le metriche della macchina virtuale Lettura e scrittura del disco.

Le nuove offerte di dischi nell'archiviazione standard hanno sono diversi limiti di IOPS e velocità effettiva (le operazioni di I/O al secondo non sono esposte per disco rigido virtuale). Esaminare i dati per verificare se si stanno raggiungendo i limiti della velocità effettiva combinata MB dei disco rigido virtuale a livello di macchina virtuale usando Lettura disco e Scrittura, quindi ottimizzare la configurazione di archiviazione della macchina virtuale per scalare i limiti del disco rigido virtuale passato. Ulteriori informazioni sulle diverse offerte di dischi di archiviazione standard e sui limiti per disco:

* [Obiettivi di scalabilità e prestazioni per i dischi delle macchine virtuali in Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)

### <a name="high-disk-utilizationlatency-remediation"></a>Utilizzo elevato del disco/correzione della latenzaHigh disk utilization/latency remediation

Ridurre la latenza dei client e ottimizzare l'I/O delle macchine virtuali per scalare i limiti del disco rigido virtuale oltre

* [Ottimizzazione di I/O per Windows in AzureOptimizing IO for Windows in Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Ottimizzazione di I/O per Linux in AzureOptimizing IO for Linux in Azure](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>Ridurre la limitazione delle richieste

Se si raggiungono i limiti superiori degli account di archiviazione, ribilanciare i dischi rigidi virtuali tra gli account di archiviazione. Fare riferimento a Obiettivi di [scalabilità e prestazioni di Archiviazione di Azure.](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/)

### <a name="increase-throughput-and-reduce-latency"></a>Aumentare la velocità effettiva e ridurre la latenza

Se si dispone di un'applicazione sensibile alla latenza e si richiede una velocità effettiva elevata, eseguire la migrazione dei dischi rigidi virtuali all'archiviazione di Azure Premium usando la macchina virtuale della serie DS e GS.

Questi articoli illustrano gli scenari specifici:These articles discuss the specific scenarios:

* [Migrazione ad Archiviazione Premium di Azure](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Usare Archiviazione Premium di Azure con SQL ServerUse Azure Premium Storage with SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni in qualsiasi momento di questo articolo, contattare gli esperti di Azure nei [forum MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

In alternativa, archiviare un incidente di supporto di Azure.Alternatively, file an Azure support incident. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
