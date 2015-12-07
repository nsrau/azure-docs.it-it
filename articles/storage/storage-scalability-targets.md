<properties 
   pageTitle="Obiettivi di scalabilità e prestazioni di Archiviazione di Azure | Microsoft Azure"
   description="Scopri gli obiettivi di scalabilità e prestazioni di Archiviazione di Azure, incluse la capacità, la velocità di richiesta e la larghezza di banda in entrata e in uscita sia per gli account di archiviazione standard che premium. Comprendere obiettivi di prestazioni per le partizioni all'interno di ciascun servizio Archiviazione di Azure."
   services="storage"
   documentationCenter="na"
   authors="tamram"
   manager="na"
   editor="na" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="11/17/2015"
   ms.author="tamram" />

# Obiettivi di scalabilità e prestazioni per Archiviazione di Azure

## Panoramica

In questo argomento vengono descritti gli obiettivi di scalabilità e prestazioni per Archiviazione di Microsoft Azure. Per un riepilogo degli altri limiti di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

>[AZURE.NOTE]Tutti gli account di archiviazione vengono eseguiti sulla nuova topologia di rete flat e supportano gli obiettivi di scalabilità e prestazioni descritti di seguito, indipendentemente dal momento in cui sono stati creati. Per ulteriori informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

<!-- -->

>[AZURE.IMPORTANT]Gli obiettivi di scalabilità e prestazioni elencati di seguito sono di fascia alta, ma possono essere conseguiti. In tutti i casi, la velocità e la larghezza di banda richieste e ottenute dall’account di archiviazione dipendono dalla dimensione degli oggetti archiviati, dai modelli di accesso utilizzati e dal tipo di carico di lavoro eseguito dall’applicazione. Assicurarsi di eseguire il test del servizio per determinare se le prestazioni soddisfano i requisiti. Se possibile, evitare picchi improvvisi nella frequenza di traffico e assicurarsi che questo sia ben distribuito tra le partizioni.

>Quando l'applicazione raggiunge il limite in termini di carico di lavoro che può essere gestito da una partizione, Archiviazione di Azure inizierà a restituire il codice di errore 503 (Server occupato) o 500 (Timeout operazione). In questo caso, l'applicazione deve utilizzare un criterio di backoff esponenziale per i nuovi tentativi. Il backoff esponenziale consente di ridurre il carico sulla partizione e di uniformare i picchi di traffico verso tale partizione.

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è possibile compilare l'applicazione in modo che sia possibile usare più account di archiviazione e partizionare i dati tra gli account. Vedere i [dettagli sui prezzi di Archiviazione](http://azure.microsoft.com/pricing/details/storage/) per informazioni sui prezzi in base al volume.


## Obiettivi di scalabilità per BLOB, code, tabelle e file

[AZURE.INCLUDE [azure-storage-limits](../../includes/azure-storage-limits.md)]

## Obiettivi di scalabilità per i dischi della macchina virtuale 

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../../includes/azure-storage-limits-vm-disks.md)]

Vedere [Dimensioni della macchina virtuale](../virtual-machines/virtual-machines-size-specs.md) per altri dettagli.

### Account di archiviazione standard

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../../includes/azure-storage-limits-vm-disks-standard.md)]

### Account di archiviazione premium

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../../includes/azure-storage-limits-vm-disks-premium.md)]

## Obiettivi di scalabilità per Gestione risorse di Azure

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

## Partizioni in Archiviazione di Azure

Ogni oggetto che contiene i dati archiviati in Archiviazione di Azure (BLOB, messaggi, entità e file) appartiene a una partizione ed è identificato da una chiave di partizione. La partizione determina il modo in cui Archiviazione di Azure bilancia il carico di BLOB, messaggi, entità e i file tra i server per soddisfare le esigenze di traffico di tali oggetti. La chiave di partizione è univoca all'interno dell'account di archiviazione e viene utilizzata per individuare un BLOB, un messaggio o un’entità.

Nella tabella precedente [Obiettivi di scalabilità per gli account di archiviazione Standard](#scalability-targets-for-standard-storage-accounts) elenca gli obiettivi di prestazioni di una singola partizione per ogni servizio.

Le partizioni influiscono sul bilanciamento del carico e sulla scalabilità per ognuno dei servizi di archiviazione nei modi seguenti:

- **BLOB**: la chiave di partizione per un BLOB è il nome del contenitore + il nome del BLOB. Ciò significa che ogni BLOB ha una partizione specifica. Pertanto, i BLOB possono essere distribuiti tra più server allo scopo di aumentare l’accesso. Sebbene i BLOB possano essere raggruppati logicamente in contenitori di BLOB, non vi sono implicazioni sul partizionamento derivanti da questo raggruppamento.

- **File**: la chiave di partizione per un file è il nome dell'account + il nome della condivisione file. Ciò significa che tutti i file di una condivisione file sono presenti anche in una singola partizione.

- **Messaggi**: la chiave di partizione per un messaggio è il nome della coda, in modo che tutti i messaggi in una coda vengano raggruppati in una singola partizione e serviti da un singolo server. Code diverse possono essere elaborate da server differenti per bilanciare il carico per quante code un account di archiviazione possa disporre.

- **Entità**: la chiave di partizione per un'entità è il nome della tabella + la chiave di partizione, dove la chiave di partizione è il valore della proprietà richiesta **PartitionKey** definita dall’utente per l'entità.

	Tutte le entità con lo stesso valore di chiave di partizione vengono raggruppate nella stessa partizione e vengono archiviate nello stesso server partizioni. Si tratta di un punto importante da considerare nella progettazione dell'applicazione. L'applicazione deve bilanciare i vantaggi di scalabilità della distribuzione delle entità in più partizioni con i vantaggi di accesso ai dati derivanti dal raggruppamento di entità in una singola partizione.

	Un vantaggio chiave derivante dal raggruppamento di un set di entità in una tabella in una singola partizione consiste nella possibilità di eseguire operazioni batch atomiche tra le entità nella stessa partizione, in quanto una partizione è presente in un singolo server. Pertanto, se si desidera eseguire operazioni batch, considerare il raggruppamento di entità con la stessa chiave di partizione.

	Dall’alto lato, le entità che si trovano nella stessa tabella ma che appartengono a partizioni diverse, possono disporre di carico bilanciato in server differenti, rendendo possibile disporre di una tabella di grandi dimensioni con scalabilità maggiore.

## Vedere anche

- [Dettagli prezzi di archiviazione](http://azure.microsoft.com/pricing/details/storage/)
- [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)
- [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage-preview-portal/)
- [Replica di Archiviazione di Azure](storage-redundancy.md)
- [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](storage-performance-checklist.md)
- [Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

<!---HONumber=AcomDC_1125_2015-->