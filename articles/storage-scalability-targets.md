<properties 
   pageTitle="Obiettivi di scalabilità e prestazioni di Archiviazione di Azure | Azure"
   description="Scopri gli obiettivi di scalabilità e prestazioni per un account di archiviazione di Azure, incluse la capacità, la velocità di richiesta e la larghezza di banda in entrata e in uscita. Comprendere obiettivi di prestazioni per le partizioni all'interno di ciascun servizio Archiviazione di Azure."
   services="storage"
   documentationCenter="na"
   authors="tamram"
   manager="na"
   editor="na" /> <tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="04/24/2015"
   ms.author="tamram" />

# Obiettivi di scalabilità e prestazioni per Archiviazione di Azure

In questo argomento vengono descritti gli obiettivi di scalabilità e prestazioni per Archiviazione di Microsoft Azure. Per un riepilogo degli altri limiti di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md).

>[AZURE.NOTE]Tutti gli account di archiviazione vengono eseguiti sulla nuova topologia di rete flat e supportano gli obiettivi di scalabilità e prestazioni descritti di seguito, indipendentemente dal momento in cui sono stati creati. Per ulteriori informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere il blog del team di Archiviazione di Azure.

<!-- -->

>[AZURE.IMPORTANT]Gli obiettivi di scalabilità e prestazioni elencati di seguito sono di fascia alta, ma possono essere conseguiti. In tutti i casi, la velocità e la larghezza di banda richieste e ottenute dall’account di archiviazione dipendono dalla dimensione degli oggetti archiviati, dai modelli di accesso utilizzati e dal tipo di carico di lavoro eseguito dall’applicazione. Assicurarsi di eseguire il test del servizio per determinare se le prestazioni soddisfano i requisiti. Se possibile, evitare picchi improvvisi nella frequenza di traffico e assicurarsi che questo sia ben distribuito tra le partizioni.

>Quando l'applicazione raggiunge il limite in termini di carico di lavoro che può essere gestito da una partizione, Archiviazione di Azure inizierà a restituire il codice di errore 503 (Server occupato) o 500 (Timeout operazione). In questo caso, l'applicazione deve utilizzare un criterio di backoff esponenziale per i nuovi tentativi. Il backoff esponenziale consente di ridurre il carico sulla partizione e di uniformare i picchi di traffico verso tale partizione.

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è necessario compilare l'applicazione in modo che sia possibile usare più account di archiviazione e partizionare i dati tra gli account. Per ogni sottoscrizione di Azure sono consentiti 100 account di archiviazione. Vedere i [dettagli sui prezzi di Archiviazione](http://azure.microsoft.com/pricing/details/storage/) per informazioni sui prezzi in base al volume.

## Obiettivi di scalabilità per gli account di archiviazione

Gli obiettivi di scalabilità per un account di archiviazione dipendono dall’area in cui è stato creato l’account. Nelle sezioni seguenti vengono illustrati gli obiettivi di scalabilità per le aree statunitensi e per quelle europee e asiatiche.

Per ulteriori informazioni sull’architettura e sugli obiettivi di scalabilità per gli account di archiviazione, vedere il [blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/04/windows-azure-s-flat-network-storage-and-2012-scalability-targets.aspx).

### Obiettivi di scalabilità per account di archiviazione standard nelle aree statunitensi

Nella tabella seguente vengono descritti gli obiettivi di scalabilità per gli account di archiviazione standard nelle aree statunitensi, in base al livello di ridondanza scelto:

<table>
<tr>
<th>Capacità account totale</th>
<th>Frequenza di richiesta totale (presupponendo oggetti di dimensione di 1 KB)</th>
<th>Larghezza di banda totale per un account di archiviazione con ridondanza geografica</th>
<th>Larghezza di banda totale per un account di archiviazione con ridondanza locale</th>
<tr>
<td>500 TB</td>
<td>Fino a 20.000 entità o messaggi al secondo</td>
<td><ul>
<li>*In entrata: fino a 10 gigabit al secondo</li>
<li>*In uscita: fino a 20 gigabit al secondo</li>
</ol>
</td>
<td><ul>
<li>*In entrata: fino a 20 gigabit al secondo</li>
<li>*In uscita: fino a 30 gigabit al secondo</li>
</tr>
</table>

*Si intendono in ingresso tutti i dati (richieste) inviati a un account di archiviazione. 

*Si intendono in uscita tutti i dati (risposte) ricevuti da un account di archiviazione. 

### Obiettivi di scalabilità per account di archiviazione standard nelle aree europee e asiatiche

Nella tabella seguente vengono descritti gli obiettivi di scalabilità per account di archiviazione nelle aree europee e asiatiche, in base al livello di ridondanza scelto.

<table>
<tr>
<th>Capacità account totale</th>
<th>Frequenza di richiesta totale (presupponendo oggetti di dimensione di 1 KB)</th>
<th>Larghezza di banda totale per un account di archiviazione con ridondanza geografica</th>
<th>Larghezza di banda totale per un account di archiviazione con ridondanza locale</th>
<tr>
<td>500 TB</td>
<td>Fino a 20.000 entità o messaggi al secondo</td>
<td><ul>
<li>*In entrata: fino a 5 gigabit al secondo</li>
<li>*In uscita: fino a 10 gigabit al secondo</li>
</ol>
</td>
<td><ul>
<li>*In entrata: fino a 10 gigabit al secondo</li>
<li>*In uscita: fino a 15 gigabit al secondo</li>
</tr>
</table>

*Si intendono in ingresso tutti i dati (richieste) inviati a un account di archiviazione. 

*Si intendono in uscita tutti i dati (risposte) ricevuti da un account di archiviazione. 

### Obiettivi di scalabilità per gli account di Archiviazione Premium

Nella tabella seguente vengono illustrati gli obiettivi di scalabilità per gli account di Archiviazione Premium disponibili nelle aree occidentali e orientali di fascia 2 degli Stati Uniti e in quelle occidentali europee:

<table>
<tr>
<th>Capacità account totale</th>
<th>Larghezza di banda totale per un account di Archiviazione Premium con ridondanza locale</th>
<tr>
<td><ul>
<li>Capacità disco: 35 TB</li>
<li>Capacità snapshot: 10 TB</li>
</td>
<td>Fino a 50 gigabit al secondo per dati in ingresso e in uscita</td>
</table>

*Si intendono in ingresso tutti i dati (richieste) inviati a un account di archiviazione. 

*Si intendono in uscita tutti i dati (risposte) ricevuti da un account di archiviazione.

Per informazioni sui dischi di Archiviazione Premium, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per i carichi di lavoro delle macchine virtuali Azure](storage-premium-storage-preview-portal.md).

## Partizioni in Archiviazione di Azure

Nella tabella seguente vengono descritti gli obiettivi di prestazioni per una singola partizione per ciascun servizio.

<table>
<tr>
<th>Velocità effettiva da raggiungere per BLOB singolo</th>
<th>Velocità effettiva da raggiungere per coda singola (messaggi di 1 KB)</th>
<th>Velocità effettiva da raggiungere per partizione di tabella singola (entità di 1 KB)</th>
<th>Velocità effettiva da raggiungere per singolo file</th><tr>
<td>Fino a 60 MB al secondo o fino 500 richieste al secondo</td>
<td>Fino a 2.000 messaggi al secondo</td>
<td>Fino a 2.000 entità al secondo</td>
<td>Fino a 60 MB al secondo</td>
</table>

Ogni oggetto che contiene i dati archiviati in Archiviazione di Azure (BLOB, messaggi, entità e file) appartiene a una partizione ed è identificato da una chiave di partizione. La partizione determina il modo in cui Archiviazione di Azure bilancia il carico di BLOB, messaggi, entità e i file tra i server per soddisfare le esigenze di traffico di tali oggetti. La chiave di partizione è univoca all'interno dell'account di archiviazione e viene utilizzata per individuare un BLOB, un messaggio o un’entità.

Per le tabelle, tutte le entità con lo stesso valore di chiave di partizione vengono raggruppate nella stessa partizione e vengono archiviate nello stesso server partizioni. Si tratta di un punto importante da considerare nella progettazione dell'applicazione. L'applicazione deve bilanciare i vantaggi di scalabilità della distribuzione delle entità in più partizioni con i vantaggi di accesso ai dati derivanti dal raggruppamento di entità in una singola partizione. Un vantaggio chiave derivante dal raggruppamento delle entità in partizioni consiste nella possibilità di eseguire operazioni atomiche tra le entità nella stessa partizione, in quanto una partizione è presente in un singolo server.

Le partizioni influiscono sul bilanciamento del carico e sulla scalabilità per ognuno dei servizi di archiviazione nei modi seguenti:

- **BLOB**: la chiave di partizione per un BLOB è il nome del contenitore + il nome del BLOB. Ciò significa che ogni BLOB ha una partizione specifica. Pertanto, i BLOB possono essere distribuiti tra più server allo scopo di aumentare l’accesso. Sebbene i BLOB possano essere raggruppati logicamente in contenitori di BLOB, non vi sono implicazioni sul partizionamento derivanti da questo raggruppamento.

- **Messaggi**: la chiave di partizione per un messaggio è il nome della coda, in modo che tutti i messaggi in una coda vengano raggruppati in una singola partizione e serviti da un singolo server. Code diverse possono essere elaborate da server differenti per bilanciare il carico per quante code un account di archiviazione possa disporre.

- **Entità**: la chiave di partizione per un'entità è il nome della tabella + la chiave di partizione, dove la chiave di partizione è il valore della proprietà richiesta **PartitionKey** definita dall’utente per l'entità.

	Le entità all'interno di una tabella possono avere gli stessi valori di chiave di partizione. In tal caso, vengono raggruppate nella stessa partizione. Un'applicazione può eseguire transazioni batch atomiche su entità all'interno della stessa partizione, poiché vengono servite dallo stesso server.

	Le entità che si trovano nella stessa tabella ma che appartengono a partizioni diverse, possono disporre di carico bilanciato in server differenti, rendendo possibile disporre di una tabella di grandi dimensioni con scalabilità maggiore.

## Vedere anche

- [Dettagli prezzi di archiviazione](http://azure.microsoft.com/pricing/details/storage/)
- [Archiviazione di Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md)


<!--HONumber=54-->