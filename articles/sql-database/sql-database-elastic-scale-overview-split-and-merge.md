<properties 
    pageTitle="Scalabilità tramite lo strumento di suddivisione-unione del database elastico" 
    description="Illustra come gestire partizioni e spostare dati tramite un servizio self-hosted usando API di database elastici." 
    services="sql-database" 
    documentationCenter="" 
    manager="jeffreyg" 
    authors="sidneyh"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/29/2015" 
    ms.author="sidneyh" />

# Scalabilità tramite lo strumento di suddivisione-unione del database elastico

Se si sceglie di non utilizzare il modello semplice di allocazione di database separati a ciascun shardlet (tenant), l'applicazione potrebbe richiedere la distribuzione flessibile dei dati tra database quando variano le esigenze relative alla capacità. Gli strumenti dei database elastici includono uno strumento di suddivisione-unione ospitato dal cliente per ribilanciare la distribuzione dei dati e la gestione di hotspot per le applicazioni partizionate. Si basa su una funzionalità sottostante per lo spostamento su richiesta di shardlet tra database diversi e si integra con la gestione delle mappe partizioni per mantenere mapping coerenti.

Lo strumento di suddivisione-unione gestisce la riduzione e l’aumento della scala; è possibile aggiungere o rimuovere database dal set di partizioni e utilizzare lo strumento di suddivisione-unione per ribilanciare la distribuzione di shardlet tra di essi. (Per le definizioni dei termini, vedere il [Glossario della scalabilità elastica](sql-database-elastic-scale-glossary.md)).

## Novità della suddivisione-unione

La versione 1.1.0 dello strumento suddivisione-unione consente di pulire automaticamente i metadati da richiesta completata. Un'opzione di configurazione controlla il periodo di conservazione di questi metadati prima che vengano rimossi.

La versione 1.0.0 dello strumento suddivisione-unione offre i miglioramenti seguenti: * le API .Net sono incluse per interfacciarsi con divisione-unione - il ruolo web è ora facoltativo * Data e ora sono ora supportati per le chiavi di partizionamento orizzontale * Le mappe di partizionamento di elenco sono ora supportate. * I limiti di intervallo nelle richieste possono corrispondere più facilmente con gli intervalli archiviati nella mappa di partizionamento. * Più istanze del ruolo di lavoro sono ora supportate per migliorare la disponibilità. * Le credenziali archiviate come parte dell'operazione di suddivisione-unione vengono ora crittografate in stato inattivo.

## Come eseguire l'aggiornamento

Per eseguire l'aggiornamento alla versione più recente della suddivisione-unione, seguire questa procedura:

1. Scaricare la versione più recente del pacchetto di suddivisione-unione da NuGet, come descritto in [Download dei pacchetti di suddivisione-unione](sql-database-elastic-scale-configure-deploy-split-and-merge.md#download-the-Split-Merge-packages).
2. Modificare il file di configurazione del servizio cloud per la distribuzione della suddivisione-unione, in modo da riflettere i nuovi parametri di configurazione. È richiesto un nuovo parametro con le informazioni relative al certificato usato per la crittografia. Per semplificare questa operazione, è possibile confrontare il file del nuovo modello di configurazione dal download con la configurazione esistente. Assicurarsi di aggiungere le impostazioni per "DataEncryptionPrimaryCertificateThumbprint" e "DataEncryptionPrimary" per il ruolo Web e il ruolo di lavoro.
3. Prima di distribuire l'aggiornamento in Azure, assicurarsi che tutte le operazioni di suddivisione-unione in esecuzione siano state completate. A tale scopo, è possibile eseguire una query nelle tabelle RequestStatus e PendingWorkflows nel database dei metadati di suddivisione-unione per le richieste in corso.
4. Aggiornare la distribuzione del servizio cloud esistente per il servizio di suddivisione-unione nella sottoscrizione di Azure con il nuovo pacchetto e con il file di configurazione del servizio aggiornato.

Non è necessario eseguire il provisioning di un nuovo database dei metadati per l’aggiornamento della suddivisione-unione. Il database dei metadati esistente verrà aggiornato automaticamente alla nuova versione.

## Scenari per la suddivisione-unione 
Le applicazioni devono essere sufficientemente flessibili per superare i limiti di un singolo database SQL di Azure, come illustrato dai seguenti scenari:

* **Capacità di aumento - Intervalli di suddivisione**: la possibilità di aumentare la capacità aggregata a livello di dati permette di soddisfare le esigenze crescenti relative alla capacità. In questo scenario l'applicazione fornisce la capacità aggiuntiva tramite il partizionamento orizzontale dei dati e la distribuzione dei dati in un numero incrementale di database, fino alla soddisfazione delle esigenze relative alla capacità. La funzionalità di suddivisione del servizio di suddivisione-unione della scalabilità elastica permette di far fronte a questo scenario. 

* **Capacità di riduzione – Intervalli di unione**: la capacità varia a causa della natura stagionale di un'azienda. Questo scenario evidenzia la necessità di una riduzione semplice a poche unità di scala durante la fase di rallentamento dell'attività professionale. La funzionalità di unione del servizio di suddivisione-unione della scalabilità elastica permette di soddisfare questo requisito.

* **Gestione di hotspot - Spostamento di shardlet**: a causa della presenza di più tenant per ogni database, l'allocazione di shardlet alle partizioni può provocare colli di bottiglia della capacità in alcune partizioni. Sarà quindi necessario riallocare gli shardlet o spostare gli shardlet occupati in partizioni nuove o meno usate.

Le attività di elaborazione nel servizio relative a tali capacità saranno definite in questo documento come richieste di **suddivisione/unione/spostamento**.


Figura 1: panoramica concettuale del servizio di suddivisione-unione


![Panoramica][1]


**Nota**: non tutti gli scenari di **Aumento della capacità** richiedono il servizio di suddivisione-unione. Se ad esempio si creano periodicamente nuove partizioni nell'ambiente per archiviare nuovi dati con valori crescenti di chiave di partizionamento orizzontale, sarà possibile usare le API del client di Gestione mappe partizioni per indirizzare i nuovi intervalli di dati alle partizioni appena create. Il servizio di suddivisione-unione è necessario solo se occorre spostare anche i dati esistenti.

## Concetti e funzionalità principali

**Servizi ospitati dal cliente**: la suddivisione-unione viene fornita come servizio ospitato dal cliente. È necessario distribuire e ospitare il servizio nella sottoscrizione di Microsoft Azure. Il pacchetto scaricato da NuGet include un modello di configurazione da completare con le informazioni specifiche per la distribuzione. Vedere l'[esercitazione relativa alla suddivisione-unione](sql-database-elastic-scale-configure-deploy-split-and-merge.md) per i dettagli. Poiché il servizio è in esecuzione nella sottoscrizione Azure, sarà possibile controllare e configurare la maggior parte degli aspetti relativi alla sicurezza del servizio. Il modello predefinito include le opzioni necessarie per configurare SSL, l'autenticazione client basata su certificato, la crittografia per le credenziali archiviate, la protezione DoS e le restrizioni IP. Altre informazioni sugli aspetti relativi alla sicurezza sono disponibili nel seguente documento relativo alla [configurazione di sicurezza della suddivisione-unione](sql-database-elastic-scale-split-merge-security-configuration.md).

Il servizio distribuito predefinito viene eseguito con un ruolo di lavoro e un ruolo Web. Ogni ruolo usa la dimensione di VM A1 in Servizi cloud di Azure. Benché non sia possibile modificare queste impostazioni durante la distribuzione del pacchetto, è possibile modificarle dopo una distribuzione corretta nel servizio cloud in esecuzione, tramite il portale di Azure. Si noti che per motivi tecnici il ruolo di lavoro deve essere configurato solo per un'istanza.

**Integrazione della mappa partizioni**: il servizio di suddivisione-unione interagisce con la mappa partizioni dell'applicazione. Quando si usa il servizio di suddivisione-unione per suddividere o unire intervalli o per spostare shardlet tra le diverse partizioni, il servizio mantiene automaticamente aggiornata la mappa partizioni. Per ottenere questo risultato, il servizio si connette al database di gestione delle mappe partizioni dell'applicazione e gestisce gli intervalli e i mapping durante lo svolgimento delle richieste di suddivisione/unione/spostamento. Ciò garantisce che la mappa partizioni presenti sempre una visualizzazione aggiornata durante l'esecuzione delle operazioni di suddivisione-unione. Le operazioni di suddivisione, unione e spostamento di shardlet vengono implementate tramite lo spostamento di un batch di shardlet dalla partizione di origine alla partizione di destinazione. Durante l'operazione di spostamento di shardlet, gli shardlet inclusi nel batch corrente vengono contrassegnati come offline nella mappa partizioni e non sono disponibili per connessioni di routing dipendenti dai dati tramite l'API **OpenConnectionForKey**.

**Connessioni di shardlet coerenti**: all'avvio dello spostamento di dati per un nuovo batch di shardlet, eventuali connessioni di routing dipendenti dai dati e fornite dalla mappa partizioni per la partizione in cui sono archiviati gli shardlet verranno terminate e le connessioni successive dalle API della mappa partizioni a questi shardlet verranno bloccate durante lo spostamento dei dati, in modo da evitare incoerenze. Verranno terminate anche le connessioni ad altri shardlet nella stessa partizione, ma queste connessioni avranno esito positivo immediato al tentativo successivo. Al termine dello spostamento del batch, gli shardlet verranno contrassegnati di nuovo come online per la partizione di destinazione e i dati di origine verranno rimossi dalla partizione di origine. Il servizio esegue questi passaggi per ogni batch, fino al completamento dello spostamento di tutti gli shardlet. Si verificheranno quindi alcune operazioni di interruzione delle connessioni durante il completamento dell'operazione di suddivisione/unione/spostamento.

**Gestione della disponibilità degli shardlet**: la limitazione dell'interruzione delle connessioni al batch attuale di shardlet, come illustrato in precedenza, limita anche l'ambito di non disponibilità a un unico batch di shardlet alla volta. Questo approccio è preferibile a un approccio in cui la partizione completa rimane offline per tutti gli shardlet corrispondenti durante l'esecuzione di un'operazione di suddivisione o unione. La dimensione di un batch, definita come il numero di shardlet distinti da muovere in un determinato momento, è un parametro di configurazione. Può essere definita per ogni operazione di suddivisione o unione, in base alle esigenze di disponibilità e prestazioni dell'applicazione. Si noti che l'intervallo bloccato nella mappa partizioni potrebbe avere dimensioni superiori rispetto a quelle del batch specificato. Ciò è dovuto al fatto che il servizio definisce una dimensione di intervallo in modo che il numero effettivo di valori di chiave di partizionamento orizzontale nei dati corrisponda in modo approssimativo alla dimensione del batch. È importante ricordare questo aspetto, in particolare per le chiavi di partizionamento orizzontale popolate in modo sparse.

**Archiviazione dei metadati**: il servizio di suddivisione-unione usa un database per la gestione del proprio stato e per la creazione di log durante l'elaborazione delle richieste. L'utente crea questo database nella propria sottoscrizione e fornisce la stringa di connessione corrispondente nel file di configurazione per la distribuzione del servizio. Gli amministratori dell'organizzazione dell'utente possono anche connettersi a questo database per esaminare lo stato della richiesta e per analizzare le informazioni dettagliate su potenziali errori.

**Condivisione del partizionamento orizzontale**: il servizio di suddivisione-unione fa distinzione tra (1) tabelle partizionate, (2) tabelle di riferimento e (3) tabelle normali. La semantica di un'operazione di suddivisione/unione/spostamento dipende dal tipo di tabella usata e viene definita nel seguente modo:

* **Tabelle partizionate**: le operazioni di suddivisione, unione e spostamento consentono di spostare gli shardlet dalla partizione di origine a quella di destinazione. Dopo il completamento corretto della richiesta complessiva, questi shardlet non saranno più presenti nell'origine. Si noti che le tabelle di destinazione devono esistere nella partizione di destinazione e non devono includere dati nell'intervallo di destinazione prima dell'elaborazione dell'operazione. 

* **Tabelle di riferimento**: per le tabelle di riferimento, le operazioni di suddivisione, unione e spostamento copiano i dati dalla partizione di origine a quella di destinazione. Si noti tuttavia che nella partizione di destinazione per una determinata tabella non vengono applicate modifiche se questa tabella nella partizione di destinazione include già alcune righe. Per permettere l'elaborazione dell'operazione di copia di tabelle di riferimento, è necessario che la tabella sia vuota.

* **Altre tabelle**: nell'origine o nella destinazione di un'operazione di suddivisione e unione possono essere presenti altre tabelle. Il servizio di suddivisione-unione ignora tali tabelle per eventuali operazioni di spostamento o copia dei dati. Si noti, tuttavia, che in caso di vincoli queste tabelle possono interferire con le operazioni.

Le informazioni relative al confronto tra tabelle di riferimento e tabelle partizionate vengono fornite dalle API **SchemaInfo** nella mappa partizioni. Il seguente esempio illustra l'uso di queste API in un determinato oggetto smm del gestore delle mappe partizioni:

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

Le tabelle "area" e "nazione" sono definite come tabelle di riferimento e vengono copiate con operazioni di suddivisione/unione/spostamento. "cliente" e "ordini" a loro volta sono definite come tabelle partizionate. C\_CUSTKEY e O\_CUSTKEY fungono da chiave di partizionamento orizzontale.

**Integrità referenziale**: il servizio di suddivisione-unione analizza le dipendenze tra le tabelle e usa relazioni di tipo chiave esterna-chiave primaria per la gestione temporanea delle operazioni per lo spostamento di tabelle di riferimento e shardlet. Le tabelle di riferimento vengono in genere copiate per prime in ordine di dipendenza, quindi vengono copiati gli shardlet, in base al rispettivo ordine di dipendenza in ogni batch. Ciò è necessario per permettere il rispetto dei vincoli di chiave esterna-chiave primaria nella partizione di destinazione all'arrivo di nuovi dati.

**Coerenza e completamento finale mappa partizioni**: in caso di errori, il servizio di suddivisione-unione riprende le operazioni dopo eventuali interruzioni e cerca di completare eventuali richieste in corso. È tuttavia possibile che si verifichino situazioni irreversibili, ad esempio nel caso in cui la partizione di destinazione vada persa o sia danneggiata in modo irreparabile. In queste circostanze è possibile che alcuni shardlet che dovevano essere spostati si trovino ancora nella partizione di origine. Il servizio assicura che i mapping di shardlet vengano aggiornati solo dopo il completamento corretto della copia dei dati necessari nella destinazione. Gli shardlet vengono eliminati dall'origine solo dopo la copia di tutti i rispettivi dati nella destinazione e dopo l'aggiornamento corretto dei mapping corrispondenti. L'operazione di eliminazione si verifica in background, mentre l'intervallo risulta già online nella partizione di destinazione. Il servizio di suddivisione-unione assicura sempre la correttezza dei mapping archiviati nella mappa partizioni.

## Recupero dei file binari del servizio

I file binari per il servizio di suddivisione-unione vengono forniti tramite [Nuget](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Vedere l’[esercitazione dettagliata sulla suddivisione-unione](sql-database-elastic-scale-configure-deploy-split-and-merge.md) per ulteriori informazioni su come scaricare i file binari.

## Interfaccia utente del servizio di suddivisione-unione

Oltre al ruolo di lavoro, il pacchetto del servizio di suddivisione-unione include anche il ruolo Web che può essere usato per inviare richieste di suddivisione-unione in modo interattivo. I componenti principali dell'interfaccia utente sono i seguenti:

-    Tipo di operazione: il tipo di operazione è un pulsante di opzione che controlla il tipo di operazione eseguita dal servizio per questa richiesta. È possibile scegliere tra gli scenari di suddivisione, unione e spostamento illustrati in Concetti e funzionalità principali. È anche possibile annullare un'operazione inviata in precedenza. È possibile usare richieste di suddivisione, unione e spostamento per le mappe partizioni di tipo intervallo. Le mappe partizioni di tipo elenco supportano solo operazioni di spostamento.

-    Mappa partizioni: la sezione successiva dei parametri della richiesta include informazioni sulla mappa partizioni e sul database che la ospita. È in particolare necessario specificare il nome del server di database SQL di Azure e del database che ospita la mappa partizioni, le credenziali per la connessione al database di mappe partizioni e infine il nome della mappa partizioni. L'operazione accetta attualmente solo un singolo set di credenziali. È necessario che le credenziali abbiano autorizzazioni sufficienti per apportare le modifiche alla mappa partizioni oltre che i dati utente nelle partizioni.

-    Intervallo di origine (suddivisione e unione): un'operazione di suddivisione e unione elabora un intervallo usando le relative chiavi superiore e inferiore. Per specificare un'operazione con un valore di chiave superiore non associato, selezionare la casella di controllo "Chiave superiore massima" e lasciare vuoto il campo della chiave superiore. Non è necessario che i valori dell'intervallo di chiavi specificati corrispondano esattamente a un mapping e ai relativi limiti nella mappa partizioni. Se non si specifica alcun limite dell'intervallo, il servizio dedurrà automaticamente l'intervallo più vicino. Per recuperare i mapping attuali in una determinata mappa partizioni, è possibile usare lo script GetMappings.ps1 di PowerShell.

-    Comportamento origine di suddivisione (suddivisione): per le operazioni di suddivisione, è necessario definire anche il punto in cui si vuole suddividere l'intervallo di origine. Per ottenere questo risultato, si specifica la chiave di partizionamento orizzontale nel punto in cui si deve verificare la suddivisione. Usare quindi il pulsante di opzione per indicare se si vuole spostare la parte inferiore dell'intervallo (escludendo la chiave di suddivisione) oppure se si vuole spostare la parte superiore (inclusa la chiave di suddivisione).

-    Shardlet di origine (spostamento): molte operazioni si differenziano dalle operazioni di suddivisione o unione, poiché non richiedono un intervallo per la descrizione dell'origine. Un'origine per lo spostamento viene identificata semplicemente dal valore della chiave di partizionamento orizzontale che si prevede di spostare.

-    Partizione di destinazione (suddivisione): dopo avere specificato le informazioni nell'origine dell'operazione di suddivisione, è necessario indicare la destinazione della copia dei dati specificando il nome del server del database SQL di Azure e del database per la destinazione.

-    Intervallo di destinazione (unione): le operazioni di unione invece spostano gli shardlet in una partizione esistente. Per identificare la partizione esistente, è necessario specificare i limiti dell'intervallo esistente con cui si vuole ottenere l'unione.

-    Dimensioni del batch: le dimensioni del batch controllano il numero di shardlet che saranno offline contemporaneamente durante lo spostamento dei dati. Si tratta di un valore Integer. È possibile usare valori ridotti se si preferisce evitare periodi di inattività prolungati per gli shardlet. I valori più elevati comporteranno un incremento del tempo per cui un determinato shardlet risulterà offline, ma potrebbero migliorare le prestazioni.

-    ID operazione (annullamento): se è in corso un'operazione che non è più necessaria, sarà possibile annullarla specificando l'ID operazione corrispondente in questo campo. L'ID operazione può essere recuperato dalla tabella di stato dell'operazione (vedere la Sezione 8.1) o dall'output nel Web browser in cui è stata inviata la richiesta.


## Requisiti e limitazioni 

L'implementazione corrente del servizio di suddivisione-unione deve rispettare i seguenti requisiti e limitazioni:

* È attualmente necessario che le partizioni esistano e siano registrate nella mappa partizioni prima dell'esecuzione di un'operazione di suddivisione-unione in tali partizioni. 

* Il servizio di suddivisione-unione attualmente non crea in modo automatico tabelle o altri oggetti di database come parte delle operazioni correlate. È quindi necessario che lo schema per tutte le tabelle partizionate e le tabelle di riferimento esista nella partizione di destinazione prima dell'esecuzione di qualsiasi operazione di suddivisione/unione/spostamento. È in particolare necessario che le tabelle partizionate siano vuote nell'intervallo in cui l'operazione di suddivisione/unione/spostamento deve aggiungere shardlet. In caso contrario, l'operazione non riuscirà a eseguire la verifica di coerenza nella partizione di destinazione. Si noti anche che i dati di riferimento vengono copiati solo se la tabella di riferimento è vuota e che non sono previste garanzie a livello di coerenza rispetto ad altre operazioni di scrittura concorrenti nelle tabelle di riferimento. Si consiglia questo: durante l'esecuzione di operazioni di suddivisione/unione, altre operazioni di scrittura non devono apportare modifiche alle tabelle di riferimento.

* Il servizio si basa attualmente sull'identità di riga definita da un indice univoco o da una chiave che include la chiave di partizionamento orizzontale per migliorare le prestazioni e l'affidabilità per shardlet di grandi dimensioni. Ciò permette al servizio di spostare i dati con un livello di granularità maggiore rispetto al solo valore di chiave di partizionamento orizzontale, contribuendo quindi a ridurre la quantità massima di spazio di log e i blocchi necessari durante l'operazione. È consigliabile creare un indice univoco o una chiave primaria che include la chiave di partizionamento orizzontale in una determinata tabella se si vuole usare quella tabella con le richieste di suddivisione/unione/spostamento. Per motivi di prestazioni, è consigliabile che la chiave di partizionamento orizzontale costituisca la colonna iniziale nella chiave o nell'indice.

* Durante l'elaborazione delle richieste è possibile che alcuni dati di shardlet siano presenti sia nella partizione di origine che nella partizione di destinazione. Ciò è attualmente necessario per la protezione da errori durante lo spostamento di shardlet. Come illustrato in precedenza, l'integrazione del servizio di suddivisione-unione con la mappa partizioni per la scalabilità elastica assicura che le connessioni tramite le API di routing dipendenti dai dati che usano il metodo **OpenConnectionForKey** sulla mappa partizioni non rilevino alcuno stato intermedio incoerente. Quando ci si connette alle partizioni di origine o di destinazione senza usare il metodo **OpenConnectionForKey**, è tuttavia possibile che stati intermedi incoerenti risultino visibili durante l'esecuzione di richieste di suddivisione/unione/spostamento. È possibile che queste connessioni mostrino risultati parziali o duplicati, in base all'intervallo o alla partizione sottostante per la connessione. Questa limitazione include attualmente le connessioni effettuate dalle query su più partizioni di Scalabilità elastica.

* Il database dei metadati per il servizio di suddivisione-unione non deve essere condiviso tra ruoli diversi. Ad esempio, un ruolo del servizio di suddivisione-unione in esecuzione in gestione temporanea deve fare riferimento a un database dei metadati diverso rispetto al ruolo di produzione.
 

## Fatturazione 

Il servizio di suddivisione-unione viene eseguito come servizio cloud nella sottoscrizione Microsoft Azure. Pertanto all'istanza del servizio vengono applicate tariffe per i servizi cloud. Se non si eseguono spesso operazioni di suddivisione/unione/spostamento, è consigliabile eliminare il servizio cloud di suddivisione-unione, in modo da ridurre i costi per le istanze dei servizi cloud in esecuzione o distribuite. È possibile eseguire di nuovo la distribuzione e avviare la configurazione pronta per l'esecuzione quando occorre eseguire operazioni di suddivisione-unione.
 
## Monitoraggio 
### Tabelle di stato 

Il servizio di suddivisione-unione fornisce la tabella **RequestStatus** nel database archivio di metadati per il monitoraggio delle richieste completate e in corso. La tabella include una riga per ogni richiesta di suddivisione-unione inviata a questa istanza del servizio di suddivisione-unione. Fornisce le seguenti informazioni per ogni richiesta:

* **Timestamp**: ora e data di inizio della richiesta.

* **OperationId**: GUID che identifica in modo univoco la richiesta. Questa richiesta può essere usata anche per annullare l'operazione durante l'esecuzione.

* **Status**: stato attuale della richiesta. per le richieste in corso indica anche la fase attuale dell'esecuzione della richiesta.

* **CancelRequest**: flag che indica se la richiesta è stata annullata.

* **Progress**: stima della percentuale di completamento dell'operazione. Un valore pari a 50 indica che la percentuale di completamento dell'operazione è pari a circa il 50%.

* **Details**: valore XML che fornisce un report di stato più dettagliato. Il report di stato viene aggiornato periodicamente durante la copia di set di righe dall'origine alla destinazione. In caso di errori o eccezioni, questa colonna include anche informazioni più dettagliate sull'errore.


### Diagnostica Azure

Il servizio di suddivisione-unione utilizza la diagnostica Azure basata su Azure SDK 2.5 per il monitoraggio e la diagnostica. È possibile controllare la configurazione della diagnostica come indicato di seguito: [Abilitazione della diagnostica nei servizi cloud e nelle macchine virtuali di Azure](../cloud-services-dotnet-diagnostics.md). Il pacchetto di download include due configurazioni della diagnostica: una per il ruolo Web e una per il ruolo di lavoro. Queste configurazioni della diagnostica per il servizio seguono le istruzioni fornite dai [Dati fondamentali dei servizi cloud di Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Essi includono le definizioni per la registrazione di contatori delle prestazioni, log IIS, registri eventi di Windows e registri eventi dell'applicazione di suddivisione-unione.

## Distribuzione della diagnostica 

Per abilitare il monitoraggio e la diagnostica utilizzando la configurazione della diagnostica per i ruoli Web e di lavoro forniti dal pacchetto NuGet, eseguire i seguenti comandi utilizzando Azure PowerShell:

    $storage_name = "<YourAzureStorageAccount>" 
    
    $key = "<YourAzureStorageAccountKey" 
    
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    
    
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 
    
    
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

È possibile trovare ulteriori informazioni su come configurare e distribuire le impostazioni di diagnostica qui: [Abilitazione della diagnostica nei servizi cloud e nelle macchine virtuali di Azure](../cloud-services-dotnet-diagnostics.md).

## Recupero della diagnostica 

È possibile accedere con facilità alla diagnostica da Esplora server di Visual Studio, nella sezione dedicata ad Azure della struttura ad albero della finestra di esplorazione. Aprire un'istanza di Visual Studio e nella barra dei menu fare clic su Visualizza, quindi su Esplora server. Fare clic sull'icona di Azure per connettersi alla sottoscrizione Azure. Passare ad Azure -> Archiviazione -> <your storage account> -> Tabelle -> WADLogsTable. Per altre informazioni, vedere [Esplorazione delle risorse di archiviazione con Esplora server](http://msdn.microsoft.com/library/azure/ff683677.aspx).

![WADLogsTable][2]

La tabella WADLogsTable evidenziata nella figura precedente include gli eventi dettagliati del log applicazione del servizio di suddivisione-unione. Si noti che la configurazione predefinita del pacchetto scaricato è destinata a una distribuzione in produzione. L'intervallo di estrazione dei log e dei contatori dalle istanze del servizio è quindi lungo (5 minuti). Per gli scenari di test e di sviluppo, ridurre l'intervallo, modificando le impostazioni di diagnostica del ruolo Web o del ruolo di lavoro in base alle esigenze specifiche. Fare clic con il pulsante destro del mouse sul ruolo in Esplora server di Visual Studio (vedere sopra), quindi modificare il periodo di trasferimento nella finestra di dialogo relativa alle impostazioni di configurazione di diagnostica:

![Configurazione][3]


## Prestazioni

In generale i livelli di servizio più elevati ed efficienti del database SQL di Azure offrono prestazioni migliori. Allocazioni di IO, CPU e memoria più elevate per i livelli di servizio superiori risulteranno utili per le operazioni di copia ed eliminazione in blocco usate internamente dal servizio di suddivisione-unione. Per questo motivo, aumentare il livello di servizio solo per tali database per un periodo di tempo limitato e definito.

Il servizio esegue anche query di convalida come parte del normale funzionamento. Le query di convalida verificano la presenza imprevista di dati nell'intervallo di destinazione e assicurano che eventuali operazioni di suddivisione/unione/spostamento vengano avviate con uno stato coerente. Queste query verranno eseguite in intervalli di chiavi di partizionamento orizzontale definiti dall'ambito dell'operazione e dalla dimensione del batch specificata come parte della definizione della richiesta. Le query vengono eseguite in modo ottimale quando è presente un indice che include la chiave di partizionamento orizzontale come colonna iniziale.

Una proprietà di univocità con la chiave di partizionamento orizzontale come colonna iniziale permetterà inoltre al servizio di usare un approccio ottimizzato, che limita l'utilizzo di risorse a livello di spazio di log e di memoria. Questa proprietà di univocità è obbligatoria per lo spostamento di quantità elevate di dati, in genere superiori a 1 GB.

## Procedure consigliate e risoluzione dei problemi 
-    Definire un tenant di test e di provare a eseguire le operazioni di suddivisione/unione/spostamento più importanti con il tenant di test in diverse partizioni. Assicurare che tutti metadati siano definiti correttamente nella mappa partizioni e che le operazioni non violino i vincoli o le chiavi esterne.
-    Assicurarsi che le dimensioni dei dati del tenant di test siano superiori alle dimensioni di dati massime del tenant più grande, in modo che non si verifichino problemi correlati alle dimensioni dei dati. In questo modo sarà possibile definire un limite massimo per il tempo necessario per lo spostamento di un singolo tenant. 
-    Assicurarsi che lo schema consenta le eliminazioni. Per il servizio di suddivisione-unione è necessario potere rimuovere dati dalla partizione di origine dopo una copia riuscita dei dati nella destinazione. I **trigger di eliminazione**, ad esempio, possono impedire al servizio di eliminare i dati nell'origine e possono ostacolare la riuscita delle operazioni.
-    La chiave di partizionamento orizzontale deve essere la colonna iniziale nella definizione della chiave primaria o dell'indice univoco. In questo modo si assicurano prestazioni ottimali per le query di convalida della suddivisione o unione e per le operazioni effettive di spostamento ed eliminazione dei dati, che vengono eseguite sempre negli intervalli delle chiavi di partizionamento orizzontale.
-    Collocare il servizio di suddivisione-unione nel centro dati e nell’area in cui si trovano i database. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## Riferimenti 

* [esercitazione relativa al servizio di suddivisione-unione](sql-database-elastic-scale-configure-deploy-split-and-merge.md)

* [Considerazioni sulla sicurezza di Scalabilità elastica](sql-database-elastic-scale-split-merge-security-configuration.md)


<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]: ./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]: ./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
 

<!---HONumber=Oct15_HO3-->