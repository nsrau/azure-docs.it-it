---
title: Guida alla risoluzione dei problemi delle prestazioni File di Azure
description: Risolvere i problemi di prestazioni noti con le condivisioni file di Azure. Individuare le possibili cause e le soluzioni alternative associate quando si verificano questi problemi.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 6739e5619a0dcaa940d38571c4a88c4f68971dfe
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009274"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Risolvere i problemi relativi alle prestazioni File di Azure

Questo articolo elenca alcuni problemi comuni relativi alle condivisioni file di Azure. Fornisce possibili cause e soluzioni alternative quando si verificano questi problemi.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Latenza elevata, velocità effettiva bassa e problemi di prestazioni generali

### <a name="cause-1-share-experiencing-throttling"></a>Cause 1: condivisione con limitazione delle richieste

La quota predefinita in una condivisione Premium è 100 GiB, che fornisce 100 IOPS Baseline (con un potenziale aumento fino a 300 per un'ora). Per ulteriori informazioni sul provisioning e la relativa relazione con IOPS, vedere la sezione relativa alle condivisioni sottoposte a [provisioning](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) nella Guida alla pianificazione.

Per verificare se la condivisione è stata limitata, è possibile sfruttare le metriche di Azure nel portale.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **tutti i servizi** e quindi cercare **metrica**.

1. Selezionare **Metriche**.

1. Selezionare l'account di archiviazione come risorsa.

1. Selezionare **file** come spazio dei nomi della metrica.

1. Selezionare **transazioni** come metrica.

1. Aggiungere un filtro per **responseType** e verificare se le richieste hanno un codice di risposta **SUCCESSWITHTHROTTLING** (per SMB) o **ClientThrottlingError** (per REST).

![Opzioni di metrica per le condivisioni file Premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> Per ricevere un avviso se una condivisione file è limitata, vedere [come creare un avviso se una condivisione file è limitata](#how-to-create-an-alert-if-a-file-share-is-throttled).

### <a name="solution"></a>Soluzione

- Aumentare la capacità con provisioning delle condivisioni specificando una quota superiore nella condivisione.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Cause 2: carico di lavoro elevato di metadati/spazio dei nomi

Se la maggior parte delle richieste è incentrata sui metadati, ad esempio CreateFile/OpenFile/CloseFile/QueryInfo/querydirectory, la latenza sarà peggiore rispetto alle operazioni di lettura/scrittura.

Per verificare se la maggior parte delle richieste sono incentrate sui metadati, è possibile usare gli stessi passaggi descritti in precedenza. Eccetto anziché aggiungere un filtro per **responseType**, aggiungere un filtro per **nome API**.

![Filtrare il nome API nelle metriche](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Soluzione alternativa

- Controllare se è possibile modificare l'applicazione per ridurre il numero di operazioni sui metadati.
- Aggiungere un disco rigido virtuale nella condivisione file e montare il disco rigido virtuale su SMB dal client per eseguire operazioni sui dati. Questo approccio funziona per gli scenari con un singolo writer e per più lettori e consente di eseguire le operazioni sui metadati in locale, offrendo prestazioni simili a quelle di una risorsa di archiviazione locale collegata direttamente.

### <a name="cause-3-single-threaded-application"></a>Motivo 3: applicazione a thread singolo

Se l'applicazione usata dal cliente è a thread singolo, questo può comportare un numero significativamente inferiore di IOPS/velocità effettiva rispetto al massimo possibile in base alle dimensioni della condivisione di cui è stato effettuato il provisioning.

### <a name="solution"></a>Soluzione

- Aumentare il parallelismo dell'applicazione aumentando il numero di thread.
- Passa alle applicazioni in cui è possibile il parallelismo. Per le operazioni di copia, ad esempio, i clienti possono usare AzCopy o RoboCopy da client Windows o il comando **parallelo** nei client Linux.

## <a name="very-high-latency-for-requests"></a>Latenza molto elevata per le richieste

### <a name="cause"></a>Causa

La macchina virtuale client potrebbe trovarsi in un'area diversa dalla condivisione file.

### <a name="solution"></a>Soluzione

- Eseguire l'applicazione da una macchina virtuale che si trova nella stessa area della condivisione file.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Il client non è in grado di raggiungere la velocità effettiva massima supportata dalla rete

Una delle possibili cause è la mancanza del supporto multicanale SMB. Attualmente, le condivisioni file di Azure supportano solo un canale singolo, pertanto esiste una sola connessione dalla macchina virtuale client al server. Questa singola connessione è ancorata a un singolo core nella macchina virtuale client, quindi la velocità effettiva massima ottenibile da una macchina virtuale è vincolata da un singolo core.

### <a name="workaround"></a>Soluzione alternativa

- Ottenere una VM con un core più grande può contribuire a migliorare la velocità effettiva.
- L'esecuzione dell'applicazione client da più macchine virtuali aumenterà la velocità effettiva.

- Usare le API REST laddove possibile.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>La velocità effettiva nei client Linux è significativamente inferiore rispetto ai client Windows.

### <a name="cause"></a>Causa

Si tratta di un problema noto relativo all'implementazione del client SMB in Linux.

### <a name="workaround"></a>Soluzione alternativa

- Distribuire il carico tra più macchine virtuali.
- Nella stessa VM usare più punti di montaggio con l'opzione **nosharesock** e distribuire il carico tra questi punti di montaggio.
- In Linux provare a montare con l'opzione **nostrictsync** per evitare di forzare lo SCARICAmento SMB per ogni chiamata **fsync** . Per File di Azure, questa opzione non interferisce con la coerenza dei dati, ma può comportare metadati di file non aggiornati nell'elenco di directory (comando**ls-l** ). L'esecuzione di query direttamente sui metadati del file (comando**Stat** ) restituirà i metadati del file più aggiornati.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Latenze elevate per carichi di lavoro intensivi dei metadati che coinvolgono numerose operazioni di apertura/chiusura.

### <a name="cause"></a>Causa

Mancanza di supporto per i lease di directory.

### <a name="workaround"></a>Soluzione alternativa

- Se possibile, evitare un handle di apertura/chiusura eccessivo nella stessa directory entro un breve periodo di tempo.
- Per le macchine virtuali Linux, aumentare il timeout della cache voce di directory specificando **actimeo = \<sec> ** come opzione di montaggio. Per impostazione predefinita, si tratta di un secondo, quindi un valore maggiore, ad esempio tre o cinque, potrebbe essere utile.
- Per le macchine virtuali Linux, aggiornare il kernel a 4,20 o versione successiva.

## <a name="low-iops-on-centosrhel"></a>IOPS Bassi su CentOS/RHEL

### <a name="cause"></a>Causa

La profondità IO maggiore di uno non è supportata in CentOS/RHEL.

### <a name="workaround"></a>Soluzione alternativa

- Eseguire l'aggiornamento a CentOS 8/RHEL 8.
- Passare a Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Rallentamento della copia del file da e verso File di Azure in Linux

Se si sta verificando un rallentamento della copia dei file da e verso File di Azure, vedere la sezione relativa alla [copia dei file lenti da e verso file di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) nella Guida alla risoluzione dei problemi di Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Modello di dente jittery/SAW per IOPS

### <a name="cause"></a>Causa

L'applicazione client supera costantemente il IOPS Baseline. Attualmente non è disponibile il lato servizio per il caricamento della richiesta, pertanto se il client supera il numero di operazioni di base IOPS, il servizio verrà limitato. Questa limitazione può comportare il verificarsi di un modello di IOPS per il client. In questo caso, il numero medio di operazioni di i/o al secondo eseguite dal client potrebbe essere inferiore al valore di IOPS Baseline.

### <a name="workaround"></a>Soluzione alternativa

- Ridurre il carico delle richieste dall'applicazione client, in modo che la condivisione non venga limitata.
- Aumentare la quota della condivisione in modo che la condivisione non venga limitata.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chiamate DirectoryOpen/DirectoryClose eccessive

### <a name="cause"></a>Causa

Se il numero di chiamate DirectoryOpen/DirectoryClose è tra le chiamate API principali e non si prevede che il client debba effettuare molte chiamate, potrebbe trattarsi di un problema con l'antivirus installato nella macchina virtuale client di Azure.

### <a name="workaround"></a>Soluzione alternativa

- Una correzione per questo problema è disponibile nell' [aggiornamento della piattaforma di aprile per Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>La creazione del file è più lenta del previsto

### <a name="cause"></a>Causa

I carichi di lavoro che si basano sulla creazione di un numero elevato di file non vedranno una differenza sostanziale tra le prestazioni delle condivisioni file Premium e delle condivisioni file standard.

### <a name="workaround"></a>Soluzione alternativa

- No.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Rallentamento delle prestazioni da Windows 8.1 o Server 2012 R2

### <a name="cause"></a>Causa

Maggiore della latenza prevista per l'accesso File di Azure per carichi di lavoro con utilizzo intensivo IO.

### <a name="workaround"></a>Soluzione alternativa

- Installare l' [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponibile.

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Come creare un avviso se una condivisione file è limitata

1. Passare all' **account di archiviazione** nell' **portale di Azure**.
2. Nella sezione Monitoraggio fare clic su **avvisi** e quindi su **+ nuova regola di avviso**.
3. Fare clic su **Modifica risorsa**, selezionare il **tipo di risorsa file** per l'account di archiviazione e quindi fare clic su **fine**. Ad esempio, se il nome dell'account di archiviazione è contoso, selezionare la risorsa Contoso/file.
4. Fare clic su **Seleziona condizione** per aggiungere una condizione.
5. Viene visualizzato un elenco di segnali supportati per l'account di archiviazione, selezionare la metrica **transazioni** .
6. Nel pannello **Configura logica** per i segnali fare clic sull'elenco a discesa **nome dimensione** e selezionare **tipo di risposta**.
7. Fare clic sull'elenco a discesa **valori dimensione** e selezionare **SUCCESSWITHTHROTTLING** (per SMB) o **ClientThrottlingError** (per REST).

  > [!NOTE]
  > Se il valore della dimensione SuccessWithThrottling o ClientThrottlingError non è elencato, significa che la risorsa non è stata limitata. Per aggiungere il valore della dimensione, fare clic su **Aggiungi valore personalizzato** accanto all'elenco a discesa **valori dimensione** , digitare **SuccessWithThrottling** o **ClientThrottlingError**, fare clic su **OK** , quindi ripetere il passaggio #7.

8. Fare clic sull'elenco a discesa **nome dimensione** e selezionare **condivisione file**.
9. Fare clic sull'elenco a discesa **valori dimensione** e selezionare le condivisioni file per le quali si desidera ricevere un avviso.

  > [!NOTE]
  > Se la condivisione file è una condivisione file standard, selezionare **tutti i valori correnti e futuri**. Nell'elenco a discesa valori dimensione non verranno elencate le condivisioni file perché le metriche per condivisione non sono disponibili per le condivisioni file standard. Gli avvisi di limitazione per le condivisioni file standard verranno attivati se una condivisione file all'interno dell'account di archiviazione è limitata e l'avviso non identificherà quale condivisione file è stata limitata. Poiché le metriche per condivisione non sono disponibili per le condivisioni file standard, è consigliabile disporre di una condivisione file per ogni account di archiviazione.

10. Definire i **parametri di avviso** (valore soglia, operatore, granularità aggregazione e frequenza di valutazione) e fare clic su **fine**.

  > [!TIP]
  > Se si utilizza una soglia statica, il grafico delle metriche consente di determinare un valore soglia ragionevole se la condivisione file è attualmente in fase di limitazione. Se si utilizza una soglia dinamica, nel grafico delle metriche verranno visualizzate le soglie calcolate in base ai dati recenti.

11. Fare clic su **Seleziona gruppo di azioni** per aggiungere un **gruppo di azioni** (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
12. Specificare i **Dettagli dell'avviso** , ad esempio il nome, la **Descrizione** e la **gravità**della **regola di avviso**.
13. Fare clic su **Crea regola di avviso** per creare l'avviso.

Per altre informazioni sulla configurazione degli avvisi in monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Vedere anche
* [Risolvere i problemi relativi a File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Risolvere i problemi relativi a File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Domande frequenti su File di Azure](storage-files-faq.md)
