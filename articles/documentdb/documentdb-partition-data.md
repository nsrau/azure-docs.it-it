<properties      
    pageTitle="Partizionamento dei dati in DocumentDB | Azure"      
    description="Informazioni su come eseguire il partizionamento dei dati in DocumentDB e quando usare il partizionamento hash, per intervalli e basato su ricerca."          
    services="documentdb"      
    authors="arramac"      
    manager="johnmac"      
    editor="monicar"      
    documentationCenter=""/> <tags      
    ms.service="documentdb"      
    ms.workload="data-services"      
    ms.tgt_pltfrm="na"      
    ms.devlang="na"      
    ms.topic="article"      
    ms.date="05/28/2015"      
    ms.author="arramac"/>

# Partizionamento dei dati in DocumentDB

[Microsoft Azure DocumentDB](../../services/documentdb/) è progettato per permettere di ottenere prestazioni elevate e prevedibili e applicare facilmente la *scalabilità orizzontale* in base alla crescita dell'applicazione. DocumentDB viene usato per i servizi di produzione su vasta scala di Microsoft, quale l'archivio dati utente su cui si basano le app Web e per dispositivi mobili della famiglia di prodotti MSN.

Grazie al **partizionamento orizzontale** dei dati, è possibile ottenere una scalabilità quasi infinita in termini di archiviazione e produttività per l'applicazione DocumentDB. Gli account DocumentDB supportano la scalabilità lineare grazie all'uso di unità organizzabili in stack, note anche come **raccolte**. Il partizionamento ottimale dei dati tra le raccolte dipenderà dal formato dei dati e dai modelli di accesso.

Dopo la lettura di questo articolo, si sarà in grado di rispondere alle domande seguenti:

 - Cos'è il partizionamento hash, per intervalli e basato su ricerca?
 - Quando è perché è consigliabile usare ogni tecnica di partizionamento?
 - Come procedere alla compilazione di un'applicazione partizionata in Azure DocumentDB?

In questo articolo vengono illustrati alcuni concetti sul partizionamento orizzontale. Se si è pronti a scrivere codice che suddivide i dati in partizioni usando .NET DocumentDB SDK, fare riferimento a [Partizionamento dei dati con DocumentDB .NET SDK](documentdb-sharding.md).

## Raccolte = partizioni

Prima si approfondire il discorso sulle tecniche di partizionamento dei dati, è importante comprendere cos'è e cosa non è una raccolta. Come probabilmente si saprà già, una raccolta è un contenitore per i documenti JSON. Le raccolte in DocumentDB non sono semplicemente contenitori *logici*, ma anche contenitori *fisici*. Sono il limite della transazione per stored procedure e trigger e il punto di ingresso per query e operazioni CRUD. A ogni raccolta viene assegnata una quantità di velocità effettiva riservata che non viene condivisa con altre raccolte nello stesso account. È quindi possibile applicare la scalabilità orizzontale all'applicazione, sia in termini di archiviazione che di velocità effettiva, aggiungendo altre raccolte e quindi distribuendo i documenti tra di esse.

Le raccolte sono diverse dalle tabelle nei database relazionali. Alle raccolte non viene applicato uno schema. Pertanto è possibile archiviare diversi tipi di documenti con schemi diversi nella stessa raccolta. È tuttavia possibile scegliere di usare le raccolte per archiviare oggetti di un singolo tipo come si farebbe con le tabelle. Il modello migliore dipende solo dal modo in cui i dati vengono visualizzati insieme in query e transazioni.

## Partizionamento con DocumentDB

Le tecniche più comuni usate per il partizionamento dei dati con Azure DocumentDB sono *partizionamento per intervalli*, *partizionamento basato su ricerca* e *partizionamento hash*. In genere si definisce un solo nome di proprietà JSON all'interno del documento come chiave di partizione, ad esempio "timestamp" o "IDutente". In alcuni casi, potrebbe invece trattarsi di una proprietà JSON interna o di un nome di proprietà diverso per ogni tipo distinto di documento.

Di seguito vengono esaminate queste tecniche in maggiore dettaglio.

## Partizionamento per intervalli

Nel partizionamento per intervalli le partizioni vengono assegnate in base alla presenza della chiave di partizione in un determinato intervallo. Questo approccio viene in genere usato per il partizionamento con proprietà di tipo *time stamp*, (ad esempio, eventTime tra 1 febbraio 2015 e 2 febbraio 2015).

> [AZURE.TIP]È consigliabile usare il partizionamento per intervalli se le query sono limitate a valori di un intervallo specifico in relazione alla chiave di partizione.

## Partizionamento basato su ricerca

Nel partizionamento basato su ricerca le partizioni vengono assegnate in base a una mappa di ricerca che assegna valori di partizione discreti a partizioni specifiche, una tecnica nota anche come mappa partizioni. Questo approccio viene in genere usato per il partizionamento in base all'area (ad esempio, la partizione per la Scandinavia contiene Norvegia, Danimarca e Svezia).

> [AZURE.TIP]Il partizionamento basato su ricerca offre il massimo livello di controllo nella gestione di un'applicazione multi-tenant. È possibile assegnare più tenant a una singola raccolta, un singolo tenant a una singola raccolta o anche un singolo tenant a più raccolte.

## Partizionamento hash

Nel partizionamento hash le partizioni vengono assegnate in base al valore di una funzione hash, permettendo di distribuire uniformemente richieste e dati tra diverse partizioni. Questo approccio viene generalmente usato per il partizionamento dei dati prodotti o utilizzati da un numero elevato di client distinti e risulta utile per l'archiviazione di profili utente, elementi del catalogo e dati di telemetria IoT ("Internet of Things").

> [AZURE.TIP]È consigliabile usare il partizionamento hash ogni volta che sono presenti troppe entità per l'enumerazione tramite il partizionamento basato su ricerca (ad esempio, utenti o dispositivi) e la frequenza delle richieste è abbastanza uniforme tra le entità.

## Scelta della tecnica di partizionamento corretta

Quale tecnica di partizionamento è quindi adatta alle proprie esigenze ? Dipende dal tipo di dati e dai modelli di accesso comuni. La scelta della tecnica di partizionamento corretta in fase di progettazione permette di evitare il debito tecnico e di gestire la crescita delle dimensioni dei dati e dei volumi delle richieste.

- **Partizionamento per intervalli**: viene usato in genere nel contesto delle date, in quanto offre un meccanismo semplice e naturale per definire la durata delle partizioni in base al timestamp. È utile anche quando le query vengono generalmente vincolate a un intervallo di tempo, dal momento che viene allineato con i limiti del partizionamento. 
- **Partizionamento basato su ricerca**: permette di raggruppare e organizzare in modo naturale set di dati non ordinati e non correlati, ad esempio, raggruppare tenant per organizzazione o stati per area geografica. La ricerca offre inoltre un controllo dettagliato per la migrazione dei dati tra raccolte. 
- **Partizionamento hash**: è utile per il bilanciamento uniforme del carico delle richieste per rendere effettivo l’uso di risorse di archiviazione e velocità effettiva con provisioning. L'uso di algoritmi di *hashing coerente* consente di ridurre al minimo la quantità di dati da spostare quando si aggiunge o si rimuove una partizione.

Non è necessario scegliere una sola tecnica di partizionamento. A seconda dello scenario, può anche essere utile un *insieme* di queste tecniche. Ad esempio, se si archiviano i dati di telemetria di un veicolo, un buon approccio consiste nel partizionare i dati di telemetria del dispositivo per intervalli in base al timestamp per una migliore gestibilità delle partizioni, quindi creare una partizione secondaria basata sul numero di identificazione del veicolo per applicare la scalabilità orizzontale ai fini della velocità effettiva (partizionamento composito per intervalli-hash).

## Sviluppo di un'applicazione partizionata
Esistono tre aree di progettazione principali da esaminare quando si sviluppa un'applicazione partizionata in DocumentDB.

- Come instradare richieste di creazione e lettura (include le query) alle raccolte appropriate.
- Come rendere permanente e recuperare la configurazione della risoluzione della partizione, nota anche come mappa partizioni.
- Come si aggiungono o rimuovono le partizioni con l'aumentare del volume di dati e richieste.

Queste aree saranno ora esaminate in maggiore dettaglio.

## Instradamento di elementi creati e query

L'instradamento delle richieste di creazione di un documento è semplice per tutte e tre le tecniche descritte. Il documento viene creato nella partizione dal valore di hash, ricerca o intervallo corrispondente alla chiave di partizione.

Query e richieste di lettura dovrebbero, in genere, essere limitate a una singola chiave di partizione, in modo da effettuare il fan-out delle query solo alle partizioni corrispondenti. Per le query su tutti i dati sarebbe tuttavia necessario *effettuare il fan-out* della richiesta tra più partizioni e quindi unire i risultati. Tenere presente che per alcune query potrebbe essere necessario eseguire una logica personalizzata per unire i risultati, ad esempio si recuperano i primi N risultati.

## Gestione della mappa partizioni

È anche necessario decidere come archiviare la mappa partizioni, in che modo i client caricheranno e riceveranno gli aggiornamenti quando viene modificata e come viene condivisa tra più client. Se la mappa di partizione non cambia spesso, è possibile salvarla semplicemente nel file di configurazione dell'applicazione.

In caso contrario, è possibile archiviarla in qualsiasi archivio permanente. Un modello di progettazione comune osservato nell'ambiente di produzione consiste nel serializzare le mappe di partizione come JSON e archiviarle anche nelle raccolte di DocumentDB. I client possono quindi memorizzare la mappa nella cache per evitare round trip aggiuntivi e quindi eseguire periodicamente il polling delle modifiche. Se è possibile che i client modifichino la mappa partizioni, assicurarsi che vengano usati uno schema di denominazione coerente e la concorrenza ottimistica (eTag) per consentire aggiornamenti coerenti con la mappa partizioni.

## Aggiunta e rimozione di partizioni

Con DocumentDB è possibile aggiungere e rimuovere raccolte in qualsiasi momento e usarle per archiviare nuovi dati in ingresso o ribilanciare i dati disponibili nelle raccolte esistenti. Per il numero di raccolte, vedere la pagina relativa ai [limiti](documentdb-limits.md). È sempre possibile contattare Microsoft per aumentare tali limiti.

L'aggiunta e la rimozione di una nuova partizione con il partizionamento basato su ricerca o per intervalli sono operazioni semplici. Ad esempio, per aggiungere una nuova area geografica o un nuovo intervallo di tempo per i dati recenti, è sufficiente aggiungere le nuove partizioni alla mappa partizioni. La suddivisione di una partizione esistente in più partizioni oppure l'unione di due partizioni sono operazioni leggermente più complesse. È necessario

- Portare offline la partizione per le letture.
- Instradare le letture a entrambe le partizioni usando la configurazione di partizionamento precedente, nonché la nuova configurazione di partizionamento durante la migrazione. Si noti che le transazioni e garanzie del livello di coerenza non saranno disponibili fino al completamento della migrazione.

L'hashing è un'operazione relativamente più complessa per l'aggiunta e rimozione di partizioni. L'appplicazione di semplici tecniche di hashing causeranno una riproduzione casuale e richiederanno lo spostamento della maggior parte dei dati. L'uso di una tecnica di **hashing coerente** assicura lo spostamento solo di una frazione dei dati.

Un modo relativamente semplice per aggiungere nuove partizioni senza richiedere lo spostamento dei dati consiste nel "travasare" i dati a una nuova raccolta e quindi effettuare il fan-out delle richieste tra la raccolta precedente e quella nuova. È tuttavia consigliabile adottare questo approccio solo in rari casi (ad esempio, trasferire i dati nel caso di carichi di lavoro in momenti di picco e tenerli temporaneamente in attesa finché non possono essere spostati).

## Passaggi successivi
In questo articolo sono state introdotte alcune tecniche su come partizionare i dati con DocumentDB e quando usare una determinata tecnica o una combinazione di tecniche.

-   Quindi, fare riferimento a questo [articolo](documentdb-sharding.md) per informazioni su come eseguire il partizionamento dei dati usando il resolver della partizione con l’SDK di DocumentDB. 
-   Scaricare uno degli [SDK supportati](https://msdn.microsoft.com/library/azure/dn781482.aspx)
-   Per eventuali domande, contattare Microsoft tramite i [forum di supporto MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB).
   


 

<!---HONumber=July15_HO3-->