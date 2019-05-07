---
title: Prestazioni dei file di Azure Guida risoluzione dei problemi
description: Problemi di prestazioni con le condivisioni file di Azure premium (anteprima) e soluzioni alternative associate noti.
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 5ae0bb736a7cc0bbc38df5905abc5d8a71f60eb9
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190044"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Risolvere i problemi relativi alle prestazioni di file di Azure

Questo articolo elenca alcuni problemi comuni correlati a condivisioni file di Azure premium (anteprima). Possibili cause e soluzioni alternative fornisce quando si verificano questi problemi.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Una latenza elevata, bassa velocità effettiva e problemi generali relativi alle prestazioni

### <a name="cause-1-share-experiencing-throttling"></a>Causa 1: Condividere la limitazione delle richieste di cui si è verificato

La quota predefinita su una condivisione è 100 GiB, che offre IOPS della linea di base 100 (con un potenziale per il burst fino a 300 per un'ora). Per altre informazioni sul provisioning e la relativa relazione per IOPS, vedere la [effettuato il provisioning di condivisioni](storage-files-planning.md#provisioned-shares) sezione della Guida alla pianificazione.

Per verificare se la condivisione è limitata, è possibile sfruttare le metriche di Azure nel portale.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **tutti i servizi** e quindi cercare **metriche**.

1. Selezionare **Metriche**.

1. Selezionare l'account di archiviazione come risorsa.

1. Selezionare **File** come spazio dei nomi delle metriche.

1. Selezionare **transazioni** come metrica.

1. Aggiungere un filtro per **ResponseType** e verificare se le richieste hanno un codice di risposta **SuccessWithThrottling**.

![Opzioni di metriche per condivisioni file premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Soluzione

- Aumento condividono capacità con provisioning specificando una quota maggiore nella condivisione.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Causa 2: Carico di lavoro metadati/spazio dei nomi

Se la maggior parte delle richieste di metadati sugli elementi, (ad esempio createfile/openfile/closefile/queryinfo/querydirectory), la latenza è peggiore rispetto di operazioni di lettura/scrittura.

Per verificare che se la maggior parte delle richieste di metadati sugli elementi, è possibile usare gli stessi passaggi come illustrato in precedenza. Tranne che invece di aggiungere un filtro per **ResponseType**, aggiungere un filtro per **nome API**.

![Filtra per nome API nelle metriche](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Soluzione alternativa

- Controllare se l'applicazione può essere modificata per ridurre il numero di operazioni sui metadati.

### <a name="cause-3-single-threaded-application"></a>Causa 3: Applicazione a thread singolo

Se l'applicazione in uso da parte del cliente è a thread singolo, ciò può comportare significativamente inferiore IOPS/velocità effettiva rispetto al numero massimo consentito in base alle dimensioni di condivisione con provisioning.

### <a name="solution"></a>Soluzione

- Aumentare il parallelismo dell'applicazione, aumentando il numero di thread.
- Passare alle applicazioni in cui è possibile il parallelismo. Ad esempio, per le operazioni di copia, i clienti è stato possibile usare AzCopy o RoboCopy dal client di Windows o il **parallele** comando nei client Linux.

## <a name="very-high-latency-for-requests"></a>Latenza molto elevata per le richieste

### <a name="cause"></a>Causa

Il macchina virtuale client potrebbe trovarsi in un'area diversa rispetto alla condivisione di file premium.

### <a name="solution"></a>Soluzione

- Eseguire l'applicazione da una macchina virtuale che si trova nella stessa area della condivisione di file premium.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Client non è possibile ottenere la velocità effettiva massima supportata dalla rete

Una delle possibili cause di questo oggetto è una mancanza fo SMB multicanale supporto. Condivisioni file di Azure attualmente supportano solo singolo canale, pertanto non c'è una sola connessione dal client della macchina virtuale al server. Questa singola connessione è stata associata a un singolo core nella macchina virtuale, il client in modo che è associata la massima velocità effettiva ottenibile da una macchina virtuale da un singolo core.

### <a name="workaround"></a>Soluzione alternativa

- Recupero di una macchina virtuale con un core più grande può contribuire al miglioramento della velocità effettiva.
- Esecuzione dell'applicazione client da più macchine virtuali aumenta la velocità effettiva.
- Dove possibile, usare le API REST.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Velocità effettiva nei client Linux è notevolmente inferiore rispetto ai client di Windows.

### <a name="cause"></a>Causa

Si tratta di un problema noto con l'implementazione del client SMB in Linux.

### <a name="workaround"></a>Soluzione alternativa

- Distribuire il carico tra più macchine virtuali
- Nella stessa VM, usare più punti di montaggio **nosharesock** opzione e distribuito il carico in questi punti di montaggio.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Delle latenze elevate per i metadati carichi di lavoro che implicano operazioni di apertura e chiusura estese.

### <a name="cause"></a>Causa

Mancanza di supporto per i lease di directory.

### <a name="workaround"></a>Soluzione alternativa

- Se possibile, evitare un numero eccessivo handle di apertura/chiusura nella stessa directory entro un breve periodo di tempo.
- Per le macchine virtuali Linux, aumentare il timeout della cache voce di directory, specificando **actimeo =<sec>**  come opzione di montaggio. Per impostazione predefinita, è un secondo, in modo che può essere utile un valore maggiore, ad esempio tre o cinque.
- Per le macchine virtuali Linux, aggiornare il kernel a 4.20 o versione successiva.

## <a name="low-iops-on-centosrhel"></a>Bassa IOPS in CentOS/RHEL

### <a name="cause"></a>Causa

Profondità dei / o maggiore di uno non è supportata in CentOS/RHEL.

### <a name="workaround"></a>Soluzione alternativa

- Eseguire l'aggiornamento a 8 CentOS / RHEL 8.
- Impostare su Ubuntu.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Modello di instabilità/sega per IOPS

### <a name="cause"></a>Causa

Applicazione client supera spesso la linea di base di IOPS. Attualmente, non è disponibile alcun lato servizio anti-aliasing del carico delle richieste, pertanto, se il client supera linea di base di IOPS, sarà limitata dal servizio. Tale limitazione può comportare il client riscontra una serie di operazioni IOPS di instabilità/sega. In questo caso, Media IOPS ottenuta dal client potrebbe essere inferiore alla linea di base di IOPS.

### <a name="workaround"></a>Soluzione alternativa

- Ridurre il carico delle richieste dall'applicazione client, in modo che la condivisione non applicata la limitazione.
- Aumentare la quota della condivisione in modo che la condivisione non applicata la limitazione.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chiamate DirectoryOpen/DirectoryClose eccessivo

### <a name="cause"></a>Causa

Se il numero di chiamate DirectoryOpen/DirectoryClose è tra le prime chiamate alle API e non si prevede che il client esegua che molte chiamate, potrebbe essere un problema con l'antivirus installato nel client Azure della macchina virtuale.

### <a name="workaround"></a>Soluzione alternativa

- Una correzione per risolvere questo problema è disponibile nel [mese di aprile Platform Update per Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Creazione di file è più lenta del previsto

### <a name="cause"></a>Causa

I carichi di lavoro che si basano sulla creazione di un numero elevato di file non visualizzeranno una differenza sostanziale tra le prestazioni delle condivisioni file premium e le condivisioni di file standard.

### <a name="workaround"></a>Soluzione alternativa

- No.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Rallentamento delle prestazioni da Server 2012 R2 o Windows 8.1

### <a name="cause"></a>Causa

Latenza accesso ai file di Azure per carichi di lavoro con utilizzo intensivo dei / o prevista superiore.

### <a name="workaround"></a>Soluzione alternativa

- Installare il disponibile [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).