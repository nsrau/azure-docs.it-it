<properties title="Splitting and Merging with Elastic Scale" pageTitle="Suddivisione e unione con Scalabilità elastica" description="Illustra come gestire sottopartizioni e spostare dati tramite un servizio self-hosted usando API Elastic Scale." metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

# Suddivisione e unione con Scalabilità elastica

Le applicazioni basate sul database SQL di Azure si trovano in difficoltà quando le esigenze relative ai dati o all'elaborazione superano le capacità di una singola unità di scala del database SQL di Azure. Ciò può ad esempio verificarsi per applicazioni che ottengono una straordinaria diffusione o in cui un set specifico di tenant supera i limiti di un singolo database SQL di Azure. Il **servizio di suddivisione e unione** di Scalabilità elastica riduce notevolmente queste difficoltà. 

Questa descrizione del servizio di suddivisione e unione esamina la riduzione e l'aumento delle dimensioni tramite la modifica del numero di database di Azure e il bilanciamento della distribuzione di **shardlet** tra i database. Per le definizioni dei termini, vedere il [Glossario di Scalabilità elastica](./sql-database-elastic-scale-glossary.md). 

Considerate le possibilità di scelta attuali tra le edizioni del database SQL di Azure, è anche possibile gestire la capacità aumentando o riducendo la capacità di un singolo database SQL di Azure. L'aspetto della gestione della capacità elastica relativo alla riduzione o all'aumento delle dimensioni non è affrontato dalla suddivisione e unione. Per informazioni, vedere invece [Elasticità di partizionamento di Scalabilità elastica](./sql-database-elastic-scale-elasticity.md). 

## Scenari per la suddivisione/unione 
Le applicazioni devono essere sufficientemente flessibili per superare i limiti di un singolo database SQL di Azure, come illustrato dagli scenari seguenti: 

* **Aumento della capacità - Suddivisione di intervalli**: La possibilità di aumentare la capacità aggregata a livello di dati permette di risolvere le esigenze crescenti relative alla capacità. In questo scenario l'applicazione fornisce la capacità aggiuntiva tramite il partizionamento orizzontale dei dati e la distribuzione dei dati in un numero incrementale di database, fino alla soddisfazione delle esigenze relative alla capacità. La funzionalità di suddivisione del servizio di suddivisione/unione di Scalabilità elastica è ideale per questo scenario. 

* **Riduzione della capacità - Unione di intervalli**: Le fluttuazioni della capacità dipendono dalla natura stagionale di un'attività professionale. Questo scenario evidenzia la necessità di una riduzione semplice a poche unità di scala durante la fase di rallentamento dell'attività professionale. La funzionalità di unione del servizio di suddivisione/unione di Scalabilità elastica permette di soddisfare questo requisito. 

* **Gestione di hotspot - Spostamento di shardlet**: A causa della presenza di più tenant per ogni database, l'allocazione di shardlet alle partizioni può provocare colli di bottiglia di capacità in alcune partizioni. Sarà quindi necessario riallocare gli shardlet o spostare gli shardlet occupati in partizioni nuove o meno usate. 

Le attività di elaborazione nel servizio relative a queste capacità saranno definite in questo documento come richieste di **suddivisione/unione/spostamento**. 


Figura 1: Panoramica concettuale del servizio di suddivisione/unione


![Overview][1] 


**Nota**:  non tutti gli scenari di **Aumento della capacità** richiedono il servizio di suddivisione/unione. Se ad esempio si creano periodicamente nuove partizioni nell'ambiente per archiviare nuovi dati con valori crescenti di chiave di partizionamento orizzontale, sarà possibile usare le API del client di Gestione mappe partizioni per indirizzare i nuovi intervalli di dati alle partizioni appena create. Il servizio di suddivisione/unione è necessario solo se occorre anche spostare i dati esistenti.

## Concetti e funzionalità principali

**Servizi ospitati presso il cliente**: Il servizio di suddivisione/unione viene fornito come servizio ospitato presso il cliente. È necessario distribuire e ospitare il servizio nella sottoscrizione di Microsoft Azure. Il pacchetto scaricato da NuGet include un modello di configurazione da completare con le informazioni specifiche per la distribuzione. Vedere l'[esercitazione relativa al servizio di suddivisione/unione](./sql-database-elastic-scale-configure-deploy-split-and-merge.md) per informazioni dettagliate. Poiché il servizio è in esecuzione nella sottoscrizione di Azure, sarà possibile controllare e configurare la maggior parte degli aspetti relativi alla sicurezza del servizio. Il modello predefinito include le opzioni necessarie per configurare SSL, l'autenticazione client basata su certificato, la protezione DoS e le restrizioni IP. Altre informazioni sugli aspetti relativi alla sicurezza sono disponibili nel documento seguente, relativo alle [configurazioni della sicurezza per la scalabilità elastica](./sql-database-elastic-scale-configure-security.md).

Il servizio distribuito predefinito viene eseguito con un ruolo di lavoro e un ruolo Web. Ogni ruolo usa la dimensione di VM A1 in Servizi cloud di Azure. Benché non sia possibile modificare queste impostazioni durante la distribuzione del pacchetto, è possibile modificarle dopo una distribuzione corretta nel servizio cloud in esecuzione, tramite il portale di Azure. Si noti che per motivi tecnici il ruolo di lavoro deve essere configurato solo per un'istanza. 

**Integrazione delle mappe partizioni**: il servizio di suddivisione/unione interagisce con la mappa partizioni dell'applicazione. Quando si usa il servizio di suddivisione/unione per suddividere o unire intervalli o per spostare shardlet tra le diverse partizioni, il servizio manterrà automaticamente aggiornata la mappa partizioni. Per ottenere questo risultato, il servizio si connette al database di gestione delle mappe partizioni dell'applicazione e gestisce gli intervalli e i mapping durante lo svolgimento delle richieste di suddivisione/unione/spostamento. Ciò garantisce che la mappa partizioni presenti sempre una visualizzazione aggiornata durante l'esecuzione delle operazioni di suddivisione/unione. Le operazioni di suddivisione, unione e spostamento di shardlet vengono implementate tramite lo spostamento di un batch di shardlet dalla partizione di origine alla partizione di destinazione. Durante l'operazione di spostamento di shardlet, gli shardlet inclusi nel batch corrente verranno contrassegnati come offline nella mappa partizioni e non saranno disponibili per connessioni di routing dipendenti dai dati tramite l'API **OpenConnectionForKey**. 

**Connessioni a shardlet coerenti**: All'avvio dello spostamento di dati per un nuovo batch di shardlet, eventuali connessioni di routing dipendenti dai dati e fornite dalla mappa partizioni per la partizione in cui sono archiviati gli shardlet verranno terminate e le connessioni successive dalle API della mappa partizioni a questi shardlet verranno bloccate durante lo spostamento dei dati, in modo da evitare incoerenze. Verranno terminate anche le connessioni ad altri shardlet nella stessa partizione, ma queste connessioni avranno esito positivo immediato al tentativo successivo. Al termine dello spostamento del batch, gli shardlet verranno contrassegnati di nuovo come online per la partizione di destinazione e i dati di origine verranno rimossi dalla partizione di origine. Il servizio esegue questi passaggi per ogni batch, fino al completamento dello spostamento di tutti gli shardlet. Si verificheranno quindi alcune operazioni di interruzione delle connessioni durante il completamento dell'operazione di suddivisione/unione/spostamento.   

**Gestione della disponibilità di shardlet**: la limitazione dell'interruzione delle connessioni al batch attuale di shardlet, come illustrato in precedenza, limita anche l'ambito di non disponibilità a un unico batch di shardlet alla volta. Questo approccio è preferibile a un approccio in cui la partizione completa rimane offline per tutti gli shardlet corrispondenti durante l'esecuzione di un'operazione di suddivisione/unione. La dimensione di un batch, definita come il numero di shardlet distinti da muovere in un determinato momento, è un parametro di configurazione. Può essere definita per ogni operazione di suddivisione/unione, in base alle esigenze di disponibilità e prestazioni dell'applicazione. Si noti che l'intervallo bloccato nella mappa partizioni potrebbe avere dimensioni superiori rispetto a quelle del batch specificato. Ciò è dovuto al fatto che il servizio definisce una dimensione di intervallo in modo che il numero effettivo di valori di chiave di partizionamento orizzontale nei dati corrisponda in modo approssimativo alla dimensione del batch. È importante ricordare questo aspetto, in particolare per le chiavi di partizionamento orizzontale popolate in modo sparse. 

**Archiviazione di metadati**: Il servizio di suddivisione/unione usa un database per la gestione del proprio stato e per la creazione di log durante l'elaborazione delle richieste. L'utente crea questo database nella propria sottoscrizione e fornisce la stringa di connessione corrispondente nel file di configurazione per la distribuzione del servizio. Gli amministratori dell'organizzazione dell'utente possono anche connettersi a questo database per esaminare lo stato della richiesta e per analizzare le informazioni dettagliate su potenziali errori.

**Riconoscimento del partizionamento orizzontale**: Il servizio di suddivisione/unione riconosce le differenze tra (1) tabelle partizionate, (2) tabelle di riferimento e (3) tabelle normali. La semantica di un'operazione di suddivisione/unione/spostamento dipende dal tipo di tabella usata e viene definita nel modo seguente: 

* **Tabelle partizionate**: le operazioni di suddivisione/unione/spostamento spostano gli shardlet dalla partizione di origine a quella di destinazione. Dopo il completamento corretto della richiesta complessiva, questi shardlet non saranno più presenti nell'origine. Si noti che le tabelle di destinazione devono esistere nella partizione di destinazione e non devono includere dati nell'intervallo di destinazione prima dell'elaborazione dell'operazione. 

-    **Tabelle di riferimento**: per le tabelle di riferimento, le operazioni di suddivisione, unione e spostamento copiano i dati dalla partizione di origine a quella di destinazione. Si noti tuttavia che nella partizione di destinazione per una determinata tabella non vengono applicate modifiche se questa tabella nella partizione di destinazione include già alcune righe. Per permettere l'elaborazione dell'operazione di copia di tabelle di riferimento, è necessario che la tabella sia vuota.

-    **Altre tabelle**: nell'origine o nella destinazione di un'operazione di suddivisione/unione possono essere presenti altre tabelle. Il servizio di suddivisione/unione ignora tali tabelle per eventuali operazioni di spostamento o copia di dati. Si noti, tuttavia, che in caso di vincoli queste tabelle possono interferire con le operazioni.

Le informazioni relative al confronto tra tabelle di riferimento e tabelle partizionate vengono fornite dalle API **SchemaInfo** nella mappa partizioni. L'esempio seguente illustra l'uso di queste API in un determinato oggetto smm del gestore delle mappe partizioni: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Le tabelle 'region' e 'nation' sono definite come tabelle di riferimento e verranno copiate tramite le operazioni di suddivisione/unione/spostamento. Le tabelle 'customer' e 'orders' sono invece definite come tabelle partizionate. C_CUSTKEY e O_CUSTKEY fungono da chiave di partizionamento orizzontale. 

**Integrità referenziale**: Il servizio di suddivisione/unione analizza le dipendenze tra le tabelle e usa relazioni di tipo chiave esterna-chiave primaria per la gestione temporanea delle operazioni per lo spostamento di tabelle di riferimento e shardlet. Le tabelle di riferimento vengono in genere copiate per prime in ordine di dipendenza, quindi vengono copiati gli shardlet, in base al rispettivo ordine di dipendenza in ogni batch. Ciò è necessario per permettere il rispetto dei vincoli di chiave esterna-chiave primaria nella partizione di destinazione all'arrivo di nuovi dati. 

**Coerenza della mappa partizioni ed eventuale completamento**: In caso di errori, il servizio di suddivisione/unione riprende le operazioni dopo eventuali interruzioni e cerca di completare eventuali richieste in corso. È tuttavia possibile che si verifichino situazioni irreversibili, ad esempio nel caso in cui la partizione di destinazione vada persa o sia danneggiata in modo irreparabile. In queste circostanze è possibile che alcuni shardlet che dovevano essere spostati si trovino ancora nella partizione di origine. Il servizio assicura che i mapping di shardlet vengano aggiornati solo dopo il completamento corretto della copia dei dati necessari nella destinazione. Gli shardlet vengono eliminati dall'origine solo dopo la copia di tutti i rispettivi dati nella destinazione e dopo l'aggiornamento corretto dei mapping corrispondenti. L'operazione di eliminazione si verifica in background, mentre l'intervallo risulta già online nella partizione di destinazione. Il servizio di suddivisione/unione assicura sempre la correttezza dei mapping archiviati nella mappa partizioni.

## Recupero dei file binari del servizio

I file binari per il servizio di suddivisione/unione vengono forniti tramite [Nuget](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Vedere l'[esercitazione dettagliata sulla suddivisione/unione](./sql-database-elastic-scale-configure-deploy-split-and-merge.md) per altre informazioni sul download dei file binari.

## Interfaccia utente del servizio di suddivisione/unione

Oltre al ruolo di lavoro, il pacchetto del servizio di suddivisione/unione include anche il ruolo Web che può essere usato per inviare richieste di suddivisione/unione in modo interattivo. I componenti principali dell'interfaccia utente sono i seguenti:

-    Tipo di operazione: il tipo di operazione è un pulsante di opzione che controlla il tipo di operazione eseguita dal servizio per questa richiesta. È possibile scegliere tra gli scenari di suddivisione, unione e spostamento illustrati in Concetti e funzionalità principali. È anche possibile annullare un'operazione inviata in precedenza.

-    Mappa partizioni: la sezione successiva dei parametri della richiesta include informazioni sulla mappa partizioni e sul database che la ospita. È in particolare necessario specificare il nome del server di database SQL di Azure e del database che ospita la mappa partizioni, le credenziali per la connessione al database di mappe partizioni e infine il nome della mappa partizioni. L'operazione accetta attualmente solo un singolo set di credenziali. È necessario che le credenziali abbiano autorizzazioni sufficienti per apportare le modifiche alla mappa partizioni oltre che i dati utente nelle partizioni.

-    Intervallo di origine (suddivisione/unione): per un'operazione di suddivisione e unione è necessario che una richiesta includa la chiave inferiore e superiore dell'intervallo di origine nella partizione di origine. È attualmente necessario specificare le chiavi nel modo esatto in cui sono indicate nei mapping nella mappa partizioni. Per recuperare i mapping attuali in una determinata mappa partizioni, è possibile usare lo script GetMappings.ps1 di PowerShell.

-    Chiave di suddivisione e comportamento (suddivisione): per le operazioni di suddivisioni è necessario definire anche il punto in cui si vuole suddividere l'intervallo di origine. Per ottenere questo risultato, si specifica la chiave di partizionamento orizzontale nel punto in cui si deve verificare la suddivisione. Usare quindi il pulsante di opzione per indicare se si vuole spostare la parte inferiore dell'intervallo (escludendo la chiave di suddivisione) oppure se si vuole spostare la parte superiore (inclusa la chiave di suddivisione).

-    Shardlet di origine (spostamento): molte operazioni si differenziano dalle operazioni di suddivisione o unione, poiché non richiedono un intervallo per la descrizione dell'origine. Un'origine per lo spostamento viene identificata semplicemente dal valore della chiave di partizionamento orizzontale che si prevede di spostare.

-    Partizione di destinazione (suddivisione): dopo avere specificato le informazioni nell'origine dell'operazione di suddivisione, sarà necessario indicare la destinazione della copia dei dati specificando il nome del server del database SQL di Azure e del database per la destinazione.

-    Intervallo di destinazione (unione): le operazioni di unione invece spostano gli shardlet in una partizione esistente. Per identificare la partizione esistente, è necessario specificare i limiti dell'intervallo esistente con cui si vuole ottenere l'unione.

-    Dimensioni batch: le dimensioni del batch controllano il numero di shardlet che saranno offline contemporaneamente durante lo spostamento dei dati. Si tratta di un valore Integer. È possibile usare valori ridotti se si preferisce evitare periodi di inattività prolungati per gli shardlet. I valori più elevati comporteranno un incremento del tempo per cui un determinato shardlet risulterà offline, ma potrebbero migliorare le prestazioni.

-    ID operazione (annullamento): se è in corso un'operazione che non è più necessaria, sarà possibile annullarla specificando l'ID operazione corrispondente in questo campo. L'ID operazione può essere recuperato dalla tabella di stato dell'operazione (vedere la Sezione 8.1) o dall'output nel Web browser in cui è stata inviata la richiesta.


## Requisiti e limitazioni 

L'implementazione corrente del servizio di suddivisione/unione deve rispettare i requisiti e le limitazioni seguenti: 

* È attualmente necessario che le partizioni esistano e siano registrate nella mappa partizioni prima dell'esecuzione di un'operazione di suddivisione/unione in tali partizioni. 

* Il servizio di suddivisione/unione non crea attualmente in modo automatico tabelle o altri oggetti di database come parte delle operazioni correlate. È quindi necessario che lo schema per tutte le tabelle partizionate e le tabelle di riferimento esista nella partizione di destinazione prima dell'esecuzione di qualsiasi operazione di suddivisione/unione/spostamento. È in particolare necessario che le tabelle partizionate siano vuote nell'intervallo in cui l'operazione di suddivisione/unione/spostamento deve aggiungere shardlet. In caso contrario, l'operazione non riuscirà a eseguire la verifica di coerenza nella partizione di destinazione. Si noti anche che i dati di riferimento vengono copiati solo se la tabella di riferimento è vuota e che non sono previste garanzie a livello di coerenza rispetto ad altre operazioni di scrittura concorrenti nelle tabelle di riferimento. È consigliabile che durante l'esecuzione di operazioni di suddivisione/unione non siano in esecuzione altre operazioni di scrittura che modificano le tabelle di riferimento.

* Il servizio si basa attualmente sull'identità di riga definita da un indice univoco o da una chiave che include la chiave di partizionamento orizzontale per migliorare le prestazioni e l'affidabilità per shardlet di grandi dimensioni. Ciò permette al servizio di spostare i dati con un livello di granularità maggiore rispetto al solo valore di chiave di partizionamento orizzontale, contribuendo quindi a ridurre la quantità massima di spazio di log e i blocchi necessari durante l'operazione. È consigliabile creare un indice univoco o una chiave primaria che include la chiave di partizionamento orizzontale in una determinata tabella se si vuole usare quella tabella con le richieste di suddivisione/unione/spostamento. Per motivi di prestazioni, è consigliabile che la chiave di partizionamento orizzontale costituisca la colonna iniziale nella chiave o nell'indice.

* Durante l'elaborazione delle richieste è possibile che alcuni dati di shardlet siano presenti sia nella partizione di origine che nella partizione di destinazione. Ciò è attualmente necessario per la protezione da errori durante lo spostamento di shardlet. Come illustrato in precedenza, l'integrazione del servizio di suddivisione/unione con la mappa partizioni di Scalabilità elastica assicura che le connessioni tramite le API di routing dipendenti dai dati che usano il metodo **OpenConnectionForKey** sulla mappa partizioni non rilevino alcuno stato intermedio incoerente. Quando ci si connette alle partizioni di origine o di destinazione senza usare il metodo **OpenConnectionForKey**, tuttavia, è possibile che stati intermedi incoerenti risultino visibili durante l'esecuzione di richieste di suddivisione/unione/spostamento. È possibile che queste connessioni mostrino risultati parziali o duplicati, in base all'intervallo o alla partizione sottostante per la connessione. Questa limitazione include attualmente le connessioni effettuate dalle query su più partizioni di Scalabilità elastica.

* Il servizio di suddivisione/unione non supporta attualmente più istanze del ruolo per il ruolo di lavoro. Ciò impedisce alle configurazioni a disponibilità elevata in Azure di usare domini di errore o di aggiornamento, che dipendono dalla possibilità di eseguire più istanze del ruolo. Il database di metadati per il servizio di suddivisione/unione non deve essere inoltre condiviso tra istanze diverse. Ad esempio, un'istanza del servizio di suddivisione/unione in esecuzione in gestione temporanea deve fare riferimento a un database di metadati diverso rispetto all'istanza in esecuzione in produzione.
 

## Fatturazione 

Poiché il servizio di suddivisione/unione viene eseguito come servizio cloud nella sottoscrizione di Microsoft Azure, alle istanze di suddivisione/unione verranno applicati gli addebiti normali per i servizi cloud. Se non si eseguono spesso operazioni di suddivisione/unione/spostamento, è consigliabile eliminare il servizio cloud di suddivisione/unione, in modo da ridurre i costi per le istanze dei servizi cloud in esecuzione o distribuite. È possibile eseguire di nuovo la distribuzione e avviare la configurazione pronta per l'esecuzione quando occorre eseguire operazioni di suddivisione/unione. 
  
## Monitoraggio 
### Tabelle di stato 

Il servizio di suddivisione/unione fornisce la tabella **RequestStatus** nel database archivio di metadati per il monitoraggio delle richieste completate e in corso. La tabella include una riga per ogni richiesta di suddivisione/unione inviata a questa istanza del servizio di suddivisione/unione. Fornisce le informazioni seguenti per ogni richiesta:

* **Timestamp**: ora e data di inizio della richiesta.

* **OperationId**: GUID che identifica in modo univoco la richiesta. Questa richiesta può essere usata anche per annullare l'operazione durante l'esecuzione.

* **Status**: stato attuale della richiesta. per le richieste in corso indica anche la fase attuale dell'esecuzione della richiesta.

* **CancelRequest**: flag che indica se la richiesta è stata annullata.

* **Progress**: stima della percentuale di completamento dell'operazione. Un valore pari a 50 indica che la percentuale di completamento dell'operazione è pari a circa il 50%.

* **Details**: valore XML che fornisce un report di stato più dettagliato. Il report di stato viene aggiornato periodicamente durante la copia di set di righe dall'origine alla destinazione. In caso di errori o eccezioni, questa colonna include anche informazioni più dettagliate sull'errore.


### Diagnostica Azure 

Il modello di servizio per il servizio di suddivisione/unione è preconfigurato per l'uso dell'archiviazione di Diagnostica Azure per una registrazione dettagliata aggiuntiva e per l'archiviazione di informazioni di diagnostica. È possibile controllare la configurazione di Diagnostica Azure, ad esempio l'account di archiviazione e le credenziali, tramite il file di configurazione del servizio per il servizio di suddivisione/unione. La configurazione di Diagnostica Azure per il servizio segue le informazioni aggiuntive disponibili nella pagina relativa ai [dati fondamentali sul servizio cloud](http://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Include le definizioni per la registrazione di contatori delle prestazioni, per log IIS, registri eventi di Windows e registri eventi dell'applicazione di suddivisione/unione. È possibile accedere con facilità a questi log da Esplora server di Visual Studio, nella sezione dedicata ad Azure della struttura ad albero della finestra di esplorazione:

![Azure Diagnostics][2]   

La tabella WADLogsTable evidenziata nella figura precedente include gli eventi dettagliati del log applicazione del servizio di suddivisione/unione. Si noti, tuttavia, che la configurazione predefinita fornita come parte del pacchetto scaricato è destinata a una distribuzione in produzione. L'intervallo di estrazione dei log e dei contatori dalle istanze del servizio è quindi lungo (5 minuti). Per gli scenari di test e di sviluppo è possibile ridurre l'intervallo, modificando le impostazioni di diagnostica del ruolo Web o del ruolo di lavoro in base alle esigenze specifiche. Per ottenere questo risultato, fare clic con il pulsante destro del mouse sul ruolo in Esplora server di Visual Studio (vedere sopra), quindi modificare il periodo di trasferimento nella finestra di dialogo relativa alle impostazioni di configurazione di Diagnostica:

![Configuration][3]


Le diverse schede della finestra di dialogo controllano i diversi tipi di log, per ognuno dei quali è stata specificata un'impostazione relativa al periodo di trasferimento. 
La visibilità dei log e dei contatori in Diagnostica Azure sarà in genere necessaria per i team Microsoft in caso di errori della distribuzione del servizio di suddivisione/unione. È possibile usare strumenti quali [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/) per esportare i log di Diagnostica Azure in file con estensione CSV, che possono essere condivisi con facilità. 

## Prestazioni

Le prestazioni delle operazioni di suddivisione, unione e spostamento dipendono da diversi fattori. In generale i livelli di servizio più elevati ed efficienti del database SQL di Azure offrono prestazioni migliori. Allocazioni di IO, CPU e memoria più elevate per i livelli di servizio superiori risulteranno utili per le operazioni di copia ed eliminazione in blocco usate internamente dal servizio di suddivisione/unione. Potrebbe essere quindi utile aumentare il livello di servizio solo per un determinato set di database e per un periodo di tempo limitato e ben definito, in modo da completare rapidamente le operazioni di suddivisione/unione pianificate per gli intervalli ospitati nei database.

Si noti che il servizio esegue anche query di convalida come parte del normale funzionamento. Le query di convalida verificano, tra le altre cose, la presenza imprevista di dati nell'intervallo di destinazione e assicurano che eventuali operazioni di suddivisione/unione/spostamento vengano avviate con uno stato coerente. Queste query verranno eseguite in intervalli di chiavi di partizionamento orizzontale definiti dall'ambito dell'operazione di suddivisione/unione/spostamento e dalla dimensione del batch specificata come parte della definizione della richiesta. Le query vengono eseguite in modo ottimale quando è presente un indice che include la chiave di partizionamento orizzontale come colonna iniziale. 

Una proprietà di univocità con la chiave di partizionamento orizzontale come colonna iniziale permetterà inoltre al servizio di usare un approccio ottimizzato, che limita l'utilizzo di risorse a livello di spazio di log e di memoria. Questa proprietà di univocità è obbligatoria per lo spostamento di quantità elevate di dati, in genere superiori a 1 GB. 

## Procedure consigliate e risoluzione dei problemi 
-    Si consiglia di definire un tenant di test e di provare a eseguire le operazioni di suddivisione/unione/spostamento più importanti con il tenant di test in diverse partizioni. In questo modo sarà possibile assicurare che tutti metadati siano definiti correttamente nella mappa partizioni e che le operazioni non violino i vincoli o le chiavi esterne.
-    Assicurarsi che le dimensioni dei dati del tenant di test siano superiori alle dimensioni di dati massime del tenant più grande, in modo che non si verifichino problemi correlati alle dimensioni dei dati. In questo modo sarà anche possibile definire un limite massimo per il tempo necessario per lo spostamento di un singolo tenant. 
-    Per il servizio di suddivisione/unione è necessario potere rimuovere dati dalla partizione di origine dopo una copia riuscita dei dati nella destinazione. Esaminare attentamente lo schema per assicurarsi che siano consentite le eliminazioni. I trigger di eliminazione, ad esempio, possono impedire al servizio di eliminare i dati nell'origine e possono ostacolare la riuscita delle operazioni.
-    Assicurarsi che la chiave di partizionamento orizzontale sia la colonna iniziale nella definizione della chiave primaria o dell'indice univoco. In questo modo si assicureranno prestazioni ottimali per le query di convalida della suddivisione/unione e per le operazioni effettive di spostamento ed eliminazione dei dati, che vengono eseguite sempre negli intervalli delle chiavi di partizionamento orizzontale.
-    Per motivi relativi a prestazioni e costi, l'inserimento del servizio di suddivisione/unione nell'area e nel data center in cui risiedono i database costituisce in genere la soluzione ottimale. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

## Riferimenti 

* [Esercitazione relativa al servizio di suddivisione/unione](./sql-database-elastic-scale-configure-deploy-split-and-merge.md)

* [Considerazioni sulla sicurezza di Scalabilità elastica](./sql-database-elastic-scale-configure-security.md)  


<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-split-and-merge/diagnostics-config.png
