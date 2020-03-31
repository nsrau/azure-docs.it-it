---
title: Guida alla risoluzione dei problemi relativi alle prestazioni di File di AzureAzure Files performance troubleshooting
description: Problemi noti di prestazioni con le condivisioni file di Azure e le soluzioni alternative associate.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598086"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Risolvere i problemi di prestazioni relativi ai file di AzureTroubleshoot Azure Files performance issues

Questo articolo elenca alcuni problemi comuni relativi alle condivisioni file di Azure.This article lists some common problems related to Azure file shares. Fornisce possibili cause e soluzioni alternative quando si verificano questi problemi.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Latenza elevata, bassa velocità effettiva e problemi di prestazioni generaliHigh latency, low throughput, and general performance issues

### <a name="cause-1-share-experiencing-throttling"></a>Causa 1: Condivisione in cui si verifica la limitazione delle richiesteCause 1: Share experiencing throttling

La quota predefinita in una quota premium è 100 GiB, che fornisce 100 IOPS di base (con un potenziale di burst fino a 300 per un'ora). Per altre informazioni sul provisioning e sulla relativa relazione con le operazioni di I/O al secondo, vedere la sezione Condivisioni di cui è stato [eseguito](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) il provisioning della guida alla pianificazione.

Per verificare se la condivisione viene limitata, è possibile sfruttare Metriche di Azure nel portale.

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Selezionare **Tutti i servizi** e quindi cercare **Metriche**.

1. Selezionare **Metriche**.

1. Selezionare l'account di archiviazione come risorsa.

1. Selezionare **File** come spazio dei nomi metrico.

1. Selezionare **Transazioni** come metrica.

1. Aggiungere un filtro per **ResponseType** e verificare se le richieste hanno un codice di risposta **SuccessWithThrottling** (per SMB) o **ClientThrottlingError** (per REST).

![Opzioni di metriche per fileshares premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Per ricevere un avviso se una condivisione file è limitata, vedere [Come creare un avviso se una condivisione file è limitata.](#how-to-create-an-alert-if-a-file-share-is-throttled)

### <a name="solution"></a>Soluzione

- Aumentare la capacità di provisioning della condivisione specificando una quota superiore nella condivisione.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Causa 2: metadati/spazio dei nomi carico di lavoro elevato

Se la maggior parte delle richieste sono incentrate sui metadati, ad esempio createfile/openfile/closefile/queryinfo/querydirectory, la latenza sarà peggiore rispetto alle operazioni di lettura/scrittura.

Per verificare se la maggior parte delle richieste sono incentrate sui metadati, è possibile utilizzare la stessa procedura descritta in precedenza. Ad eccezione di un filtro per **ResponseType**, aggiungere un filtro per **Nome API**.

![Filtrare per nome API nelle metricheFilter for API Name in your metrics](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Soluzione alternativa

- Verificare se l'applicazione può essere modificata per ridurre il numero di operazioni sui metadati.
- Aggiungere un disco rigido virtuale nella condivisione file e montare il disco rigido virtuale tramite SMB dal client per eseguire operazioni sui file sui dati. Questo approccio funziona per scenari di singoli writer e lettori multipli e consente alle operazioni sui metadati di essere locali, offrendo prestazioni simili a un'archiviazione collegata diretta locale.

### <a name="cause-3-single-threaded-application"></a>Causa 3: Applicazione a thread singolo

Se l'applicazione utilizzata dal cliente è a thread singolo, ciò può comportare una riduzione significativa delle operazioni di I/O al secondo/velocità effettiva rispetto al massimo possibile in base alle dimensioni della condivisione di cui è stato eseguito il provisioning.

### <a name="solution"></a>Soluzione

- Aumentare il parallelismo dell'applicazione aumentando il numero di thread.
- Passare ad applicazioni in cui è possibile il parallelismo. Ad esempio, per le operazioni di copia, i clienti potrebbero usare AzCopy o RoboCopy dai client Windows o il comando parallelo nei client Linux.For example, for copy operations, customers could use AzCopy or RoboCopy from Windows clients or the **parallel** command on Linux clients.

## <a name="very-high-latency-for-requests"></a>Latenza molto elevata per le richieste

### <a name="cause"></a>Causa

La macchina virtuale client potrebbe trovarsi in un'area diversa rispetto alla condivisione file.

### <a name="solution"></a>Soluzione

- Eseguire l'applicazione da una macchina virtuale che si trova nella stessa area della condivisione file.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Client che non è in grado di ottenere la velocità effettiva massima supportata dalla rete

Una possibile causa di ciò è una mancanza di supporto multicanale SMB. Attualmente, le condivisioni file di Azure supportano solo un singolo canale, pertanto è presente una sola connessione dalla macchina virtuale client al server. Questa singola connessione è ancorata a un singolo core nella macchina virtuale client, pertanto la velocità effettiva massima raggiungibile da una macchina virtuale è associata da un singolo core.

### <a name="workaround"></a>Soluzione alternativa

- Ottenere una macchina virtuale con un core più grande può contribuire a migliorare la velocità effettiva.
- L'esecuzione dell'applicazione client da più macchine virtuali aumenterà la velocità effettiva.

- Se possibile, usare le API REST.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>La velocità effettiva nei client Linux è significativamente inferiore rispetto ai client Windows.Throughput on Linux clients is significantly lower in when compared to Windows clients.

### <a name="cause"></a>Causa

Si tratta di un problema noto con l'implementazione del client SMB su Linux.

### <a name="workaround"></a>Soluzione alternativa

- Distribuire il carico tra più macchine virtuali.
- Nella stessa macchina virtuale usare più punti di montaggio senza l'opzione **nosharesock** e distribuire il carico tra questi punti di montaggio.
- Su Linux, prova a montare con **l'opzione nostrictsync** per evitare di forzare SMB a filo su ogni chiamata **fsync.** Per i file di Azure, questa opzione non interferisce con la coerenza dei dati, ma può causare metadati di file non aggiornati nell'elenco di directory (comando**ls -l).** L'esecuzione diretta di query sui metadati del file (comando**stat)** restituirà i metadati del file più aggiornati.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Latenze elevate per carichi di lavoro pesanti sui metadati che comportano operazioni di apertura/chiusura estese.

### <a name="cause"></a>Causa

Mancanza di supporto per i lease di directory.

### <a name="workaround"></a>Soluzione alternativa

- Se possibile, evitare un handle di apertura/chiusura eccessivo sulla stessa directory in un breve periodo di tempo.
- Per le macchine virtuali Linux, aumentare il timeout della cache delle voci della directory specificando **actimeo\<s sec>** come opzione di montaggio. Per impostazione predefinita, è un secondo, quindi un valore più grande come tre o cinque potrebbe aiutare.
- Per le macchine virtuali Linux, aggiornare il kernel a 4.20 o versione successiva.

## <a name="low-iops-on-centosrhel"></a>Basse operazioni di I/O al secondo su CentOS/RHEL

### <a name="cause"></a>Causa

La profondità di I/O maggiore di uno non è supportata in CentOS/RHEL.

### <a name="workaround"></a>Soluzione alternativa

- Aggiornamento a CentOS 8 / RHEL 8.
- Cambia in Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Rallentamento della copia del file da e verso File di Azure in Linux

Se si verifica la copia lenta dei file da e verso File di Azure, vedere la sezione Copia lenta dei file da e verso File di Azure in Linux nella guida alla risoluzione dei problemi di Linux.If you are experiencing slow file copying to and from Azure Files, take a look at the [Slow file copying to and from Azure Files in Linux Files section](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) in the Linux troubleshooting guide.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Modello di jittery/saw-tooth per IOPS

### <a name="cause"></a>Causa

L'applicazione client supera costantemente le operazioni di I/O al secondo di base. Attualmente, non vi è alcun asmoothing lato servizio del carico della richiesta, quindi se il client supera le operazioni di I/O al secondo di base, otterrà limitato dal servizio. Tale limitazione può causare il client sperimentando un modello di IOPS nervoso/saw-tooth. In questo caso, le operazioni di I/O al secondo medie raggiunte dal client potrebbero essere inferiori alle operazioni di I/O al secondo di base.

### <a name="workaround"></a>Soluzione alternativa

- Ridurre il carico delle richieste dall'applicazione client, in modo che la condivisione non venga limitata.
- Aumentare la quota della condivisione in modo che la condivisione non venga limitata.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chiamate eccessive di DirectoryOpen/DirectoryClose

### <a name="cause"></a>Causa

Se il numero di chiamate DirectoryOpen/DirectoryClose è tra le prime chiamate API e non si prevede che il client effettui un numero così numeroso di chiamate, potrebbe trattarsi di un problema con l'antivirus installato nella macchina virtuale del client Azure.

### <a name="workaround"></a>Soluzione alternativa

- Una correzione per questo problema è disponibile [nell'aggiornamento della piattaforma di aprile per Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>La creazione del file è più lenta del previsto

### <a name="cause"></a>Causa

I carichi di lavoro che si basano sulla creazione di un numero elevato di file non vedranno una differenza sostanziale tra le prestazioni delle condivisioni file premium e le condivisioni file standard.

### <a name="workaround"></a>Soluzione alternativa

- No.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Rallentamento delle prestazioni da Windows 8.1 o Server 2012 R2

### <a name="cause"></a>Causa

Latenza con accesso ai file di Azure per carichi di lavoro con utilizzo intensivo di I/O superiore al previsto.

### <a name="workaround"></a>Soluzione alternativa

- Installare [l'hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponibile .

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Come creare un avviso se una condivisione file è limitataHow to create an alert if a file share is throttled

1. Nel [portale di Azure](https://portal.azure.com)fare clic su **Monitor**. 

2. Fare clic su **Avvisi** e quindi su **Nuova regola di avviso**.

3. Fare clic su **Seleziona** per selezionare l'account **di archiviazione/risorsa file** contenente la condivisione file per cui si vuole avvisare e quindi fare clic su **Fine**. Ad esempio, se il nome dell'account di archiviazione è contoso, selezionare la risorsa contoso/file.

4. Fare clic su **Aggiungi** per aggiungere una condizione.

5. Verrà visualizzato un elenco di segnali supportati per l'account di archiviazione, selezionare la metrica **Transazioni.**

6. Nel pannello **Configura logica del segnale** passare alla dimensione Tipo di **risposta,** fare clic sull'elenco a discesa **Valori di** dimensione e selezionare **SuccessWithThrottling** (per SMB) o **ClientThrottlingError** (per REST). 

  > [!NOTE]
  > Se il valore della dimensione SuccessWithThrottling o ClientThrottlingError non è elencato, significa che la risorsa non è stata limitata.  Per aggiungere il valore **+** di dimensione, fare clic sul menu a discesa accanto a **Valori di dimensione,** digitare **SuccessWithThrottling** o **ClientThrottlingError**, fare clic **su OK** e quindi ripetere il passaggio #6.

7. Passare alla dimensione **Condivisione file,** fare clic sull'elenco a discesa **Valori dimensioni** e selezionare le condivisioni file per le quali si desidera avvisare. 

  > [!NOTE]
  > Se la condivisione file è una condivisione file standard, l'elenco a discesa dei valori di dimensione sarà vuoto perché le metriche per condivisione non sono disponibili per le condivisioni file standard. Gli avvisi di limitazione per le condivisioni file standard verranno attivati se una condivisione file all'interno dell'account di archiviazione viene limitata e l'avviso non identificherà quale condivisione file è stata limitata. Poiché le metriche per condivisione non sono disponibili per le condivisioni file standard, è consigliabile disporre di una condivisione file per ogni account di archiviazione. 

8. Definire i parametri di **avviso** (soglia, operatore, granularità di aggregazione e frequenza) utilizzati per valutare la regola di avviso della metrica e fare clic su **Fine**.

  > [!TIP]
  > Se si utilizza una soglia statica, il grafico delle metriche consente di determinare una soglia ragionevole se la condivisione file è attualmente in fase di limitazione. Se si utilizza una soglia dinamica, nel grafico delle metriche verranno visualizzate le soglie calcolate in base ai dati recenti.

9. Aggiungere un **gruppo** di azioni (e-mail, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.

10. Immettere **i dettagli dell'avviso,** ad esempio **Nome regola di avviso**, **Descrizione** e **Gravità**.

11. Fare clic su **Crea regola di avviso** per creare l'avviso.

Per altre informazioni sulla configurazione degli avvisi in Monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure.To]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)learn more about configuring alerts in Azure Monitor, see Overview of alerts in Microsoft Azure.
