---
title: Domande frequenti sulla API Tabella in Azure Cosmos DB
description: Risposte alle domande frequenti sulla API Tabella in Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 981c6f145f0bf06fbe81234d473b9fbcd2235174
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614486"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Domande frequenti sulla API Tabella in Azure Cosmos DB

Il Azure Cosmos DB API Tabella è disponibile nella [portale di Azure](https://portal.azure.com) prima di tutto è necessario iscriversi per una sottoscrizione di Azure. Dopo aver effettuato l'iscrizione, è possibile aggiungere un account API Table di Azure Cosmos DB alla sottoscrizione di Azure e quindi aggiungere tabelle all'account. Per informazioni sui linguaggi supportati e sulle guide introduttive associate, vedere [Introduzione all'API Tabelle di Azure Cosmos DB](table-introduction.md).

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>API Tabella nell'archiviazione tabelle di Azure Azure Cosmos DB rispetto a

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Quali sono le differenze tra il comportamento dell'API Tabelle e quello dell'archiviazione tabelle di Azure?

Esistono alcune differenze a livello di comportamento che gli utenti di archiviazione tabelle di Azure che vogliono creare tabelle con l'API Tabelle di Azure Cosmos DB devono tenere in considerazione:

* L'API Tabelle di Azure Cosmos DB usa un modello di capacità riservata per assicurare prestazioni garantite, ma ciò comporta il pagamento della capacità non appena viene creata la tabella, anche se la capacità non viene usata. Archiviazione tabelle di Azure prevede solo il pagamento della capacità effettivamente usata. Questo è il motivo per cui l'API Tabella può offrire un contratto di servizio con 10 ms di lettura e 15 ms di scrittura al 99° percentile, mentre Archiviazione tabelle di Azure offre un contratto di servizio con 10 secondi. Con l'API Tabelle, tuttavia, sono previsti costi anche per le tabelle vuote senza richieste, in modo da assicurare che sia disponibile la capacità per la gestione di eventuali richieste in base al contratto di servizio offerto da Azure Cosmos DB.

* I risultati della query restituiti dal API Tabella non sono ordinati in base all'ordine delle chiavi di partizione/chiavi di riga così come sono nell'archivio tabelle di Azure.

* Le chiavi di riga possono contenere solo fino a 255 byte.

* I batch possono avere solo fino a 2 MB.

* CORS attualmente non è supportato.

* I nomi di tabella nell'archiviazione tabelle di Azure non fanno distinzione tra maiuscole e minuscole, ma sono in Azure Cosmos DB API Tabella.

* Alcuni dei formati interni di Azure Cosmos DB per le informazioni di codifica, ad esempio i campi binari, non offrono attualmente l'efficienza auspicabile. Di conseguenza questo può causare limitazioni impreviste per le dimensioni dei dati. Ad esempio, attualmente non è possibile usare l'intero MB di un'entità tabella per archiviare i dati binari, perché la codifica incrementa le dimensioni dei dati.

* Il nome della proprietà dell'entità' ID ' non è attualmente supportato.

* TableQuery TakeCount non è limitato a 1000.

* Per quanto riguarda l'API REST, sono disponibili alcune opzioni relative a endpoint/query non supportate dall'API Tabella di Azure Cosmos DB:

  | Metodi REST | Opzione relativa a endpoint/query REST | URL della documentazione | Spiegazione |
  | ------------| ------------- | ---------- | ----------- |
  | GET, PUT | `/?restype=service@comp=properties`| [Set Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) (Operazione Set Table Service Properties) [Get Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) (Operazione Get Table Service Properties) | Questo endpoint viene usato per configurare le regole CORS, la configurazione di analisi di archiviazione e le impostazioni di registrazione. CORS non è attualmente supportato e l'analisi e la registrazione vengono gestite in modo diverso in Azure Cosmos DB rispetto ad archiviazione tabelle di Azure. |
  | OPTIONS | `/<table-resource-name>` | [Pre-flight CORS table request](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) (Operazione Pre-flight CORS table request) | Questa è una parte di CORS che non è attualmente supportata da Azure Cosmos DB. |
  | GET | `/?restype=service@comp=stats` | [Get Table Service Stats](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) (Operazione Get Table Service Stats) | Fornisce informazioni sulla velocità con cui i dati vengono replicati tra il server primario e i server secondari. Non è necessario in Cosmos DB perché la replica fa parte delle operazioni di scrittura. |
  | GET, PUT | `/mytable?comp=acl` | [Get Table ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) (Operazione Get Table ACL) e [Set Table ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) (Operazione Set Table ACL) | Ottiene e configura i criteri di accesso archiviati usati per gestire le firme di accesso condiviso. Anche se la firma di accesso condiviso è supportata, viene configurata e gestita in modo diverso. |

* Azure Cosmos DB API Tabella supporta solo il formato JSON, non ATOM.

* Nonostante Azure Cosmos DB supporti le firme di accesso condiviso, non supporta alcuni criteri, in particolare i criteri correlati alle operazioni di gestione, ad esempio il diritto di creare nuove tabelle.

* Per .NET SDK in particolare, alcune classi e alcuni metodi non sono attualmente supportati da Azure Cosmos DB.

  | Classe | Metodo non supportato |
  |-------|-------- |
  | CloudTableClient | \*ServiceProperties* |
  |                  | \*ServiceStats* |
  | CloudTable | SetPermissions* |
  |            | GetPermissions* |
  | TableServiceContext | * (questa classe è deprecata) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>Altre domande frequenti

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>È necessario un nuovo SDK per usare l'API Tabelle?

No, gli SDK di archiviazione esistenti dovrebbero funzionare ancora. È tuttavia consigliabile ottenere sempre gli SDK più recenti per assicurare il supporto migliore e in molti casi prestazioni superiori. Per un elenco dei linguaggi supportati, vedere [Introduzione all'API Tabelle di Azure Cosmos DB](table-introduction.md).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Quale stringa di connessione è necessario usare per connettersi all'API Tabelle?

La stringa di connessione è:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

È possibile ottenere la stringa di connessione nella pagina Stringa di connessione del portale di Azure.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Come si sostituiscono le impostazioni di configurazione per le opzioni relative alle richieste in .NET SDK per l'API Tabelle?

Alcune impostazioni vengono gestite nel metodo CreateCloudTableClient e altre tramite il file app.config nella sezione appSettings dell'applicazione client. Per informazioni sulle impostazioni di configurazione, vedere [Funzionalità di Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Sono stati introdotti cambiamenti per i clienti che usano gli SDK di archiviazione tabelle di Azure?

Nessuno. Non è stato introdotto alcun cambiamento per i clienti nuovi o esistenti che usano gli SDK di archiviazione tabelle di Azure esistenti.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Come si visualizzano i dati di tabella archiviati in Azure Cosmos DB da usare con l'API Tabella?

Per esplorare i dati si può usare il portale di Azure. È anche possibile usare il codice dell'API Tabelle o gli strumenti illustrati nella risposta successiva.

### <a name="which-tools-work-with-the-table-api"></a>Quali strumenti è possibile usare con l'API Tabelle?

È possibile usare [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

La nuova API Tabelle è supportata dagli strumenti sufficientemente flessibili da accettare una stringa di connessione nel formato specificato in precedenza. Per un elenco degli strumenti di tabella, vedere la pagina [Strumenti client di Archiviazione di Azure](../storage/common/storage-explorers.md).

### <a name="is-the-concurrency-on-operations-controlled"></a>La concorrenza nelle operazioni è controllata?

Sì. La concorrenza ottimistica è supportata tramite l'uso del meccanismo degli ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>È supportato il modello di query OData per le entità?

Sì. L'API Tabelle supporta query OData e query LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>È supportata la connessione affiancata tra archiviazione tabelle di Azure e l'API Tabelle di Azure Cosmos DB?

Sì. È possibile connettersi creando due istanze separate di CloudTableClient, ognuna delle quali punta a un URI specifico tramite la stringa di connessione.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Come si esegue la migrazione di un'applicazione di archiviazione tabelle di Azure esistente a questa offerta?

Sono supportati [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) e l'[Utilità di migrazione dati di Azure Cosmos DB](import-data.md).

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Come si espandono le dimensioni di archiviazione per questo servizio, se ad esempio *n* GB di dati iniziali crescono nel tempo fino a 1 TB?

Azure Cosmos DB è progettato per offrire spazio di archiviazione illimitato tramite scalabilità orizzontale. Il servizio può monitorare e aumentare efficacemente lo spazio di archiviazione.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Come si monitora l'offerta dell'API Tabelle?

È possibile usare il riquadro **Metriche** dell'API Tabelle per monitorare le richieste e l'utilizzo dello spazio di archiviazione.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Come si calcola la velocità effettiva necessaria?

È possibile usare lo strumento di stima della capacità per calcolare il valore di TableThroughput necessario per le operazioni. Per altre informazioni, vedere [Estimate Request Units and Data Storage](https://www.documentdb.com/capacityplanner) (Stimare le unità richiesta e l'archiviazione dei dati). In generale, è possibile rappresentare un'entità in formato JSON e specificare i numeri relativi alle operazioni.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>È possibile usare l'SDK dell'API Tabelle in locale con l'emulatore?

Attualmente non è possibile.

### <a name="can-my-existing-application-work-with-the-table-api"></a>È possibile usare un'applicazione esistente con l'API Tabelle?

Sì, è supportata la stessa API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>È necessario eseguire la migrazione delle applicazioni di Archiviazione tabelle di Azure esistenti all'SDK se non si vogliono usare le funzionalità dell'API Tabella?

No. È possibile creare e usare asset di archiviazione tabelle di Azure esistenti senza interruzioni di alcun tipo. Se non si usa l'API Tabella, tuttavia, non è possibile usufruire dell'indicizzazione automatica, dell'opzione di coerenza aggiuntiva o della distribuzione globale.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Come si aggiunge la replica dei dati nell'API Tabella in più aree di Azure?

Per aggiungere aree adatte all'applicazione, è possibile usare le [impostazioni di replica globali](tutorial-global-distribution-sql-api.md#portal) del portale Azure Cosmos DB. Per sviluppare un'applicazione distribuita a livello globale, è consigliabile anche aggiungere l'applicazione con il valore di PreferredLocation impostato sull'area locale, per garantire una bassa latenza di lettura.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Come si modifica l'area di scrittura primaria per l'account nell'API Tabelle?

È possibile usare il riquadro del portale relativo alla replica globale di Azure Cosmos DB per aggiungere un'area e quindi effettuare il failover nell'area necessaria. Per istruzioni, vedere l'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Come si configurano le aree di lettura preferite per una bassa latenza quando si distribuiscono i dati?

Per facilitare la lettura dalla località locale, usare la chiave PreferredLocation nel file app.config. Per le applicazioni esistenti, l'API Tabelle genera un errore in caso di impostazione di LocationMode. Rimuovere tale codice, perché l'API Tabelle acquisisce queste informazioni dal file app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Come funzionano i livelli di coerenza nell'API Tabelle?

Azure Cosmos DB offre compromessi ben ponderati tra coerenza, disponibilità e latenza. Azure Cosmos DB offre cinque livelli di coerenza per gli sviluppatori che usano l'API Tabelle, consentendo così di scegliere il modello di coerenza appropriato a livello di tabella ed effettuare singole richieste durante l'esecuzione di query sui dati. Quando un client si connette, può specificare un livello di coerenza. È possibile modificare il livello tramite l'argomento consistencyLevel di CreateCloudTableClient.

L'API Tabelle offre letture a bassa latenza con la "lettura delle proprie scritture", con la coerenza con obsolescenza associata come impostazione predefinita. Per altre informazioni, vedere l'articolo relativo ai [livelli di coerenza](consistency-levels.md).

Per impostazione predefinita, l'archivio tabelle di Azure offre coerenza assoluta all'interno di un'area e coerenza finale nelle località secondarie.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>L'API Tabelle di Azure Cosmos DB offre più livelli di coerenza rispetto all'archiviazione tabelle di Azure?

Sì. Per informazioni su come trarre vantaggio dalla natura distribuita di Azure Cosmos DB, vedere l'articolo relativo ai [livelli di coerenza](consistency-levels.md). Grazie alle garanzie associate ai livelli di coerenza, possono essere usati in tutta sicurezza.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando è abilitata la distribuzione globale, quanto tempo è necessario per replicare i dati?

Azure Cosmos DB esegue il commit permanente dei dati nell'area locale e ne effettua immediatamente il push in altre aree, nell'arco di millisecondi. Questa replica dipende solo dal tempo di round trip del data center. Per altre informazioni sulla funzionalità di distribuzione globale di Azure Cosmos DB, vedere l'articolo relativo ad [Azure Cosmos DB come servizio di database distribuito a livello globale in Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>È possibile modificare il livello di coerenza delle richieste di lettura?

Con Azure Cosmos DB, è possibile impostare il livello di coerenza a livello di contenitore (nella tabella). Usando .NET SDK, si può modificare il livello specificando il valore della chiave TableConsistencyLevel nel file app.config. I valori possibili sono: Strong, Bounded Staleness, Session, Consistent Prefix ed Eventual. Per altre informazioni, vedere [Livelli di coerenza dei dati ottimizzabili in Azure Cosmos DB](consistency-levels.md). Il concetto chiave è che non è possibile impostare un livello di coerenza delle richieste superiore rispetto all'impostazione della tabella. Ad esempio, non è possibile impostare il livello di coerenza Eventual per la tabella e il livello di coerenza Strong per le richieste.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Come viene gestito il failover dall'API Tabelle se un'area diventa inattiva?

L'API Tabelle sfrutta i vantaggi della piattaforma distribuita a livello globale di Azure Cosmos DB. Affinché l'applicazione possa tollerare i tempi di inattività del data center, abilitare almeno un'altra area per l'account nel portale di Azure Cosmos DB, come illustrato nell'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](high-availability.md). Si può impostare la priorità dell'area usando il portale. Vedere l'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](high-availability.md).

È possibile aggiungere il numero di aree desiderato per l'account e controllare in quale potrà essere effettuato il failover specificando una priorità di failover. Per usare il database, è necessario includere anche un'applicazione. Nel corso di questa operazione, i clienti non riscontrano tempi di inattività. A differenza degli altri SDK, l'[SDK client .NET più recente](table-sdk-dotnet.md) supporta l'homing automatico. ovvero può rilevare l'area inattiva ed effettuare automaticamente il failover nella nuova area.

### <a name="is-the-table-api-enabled-for-backups"></a>L'API Tabelle è abilitata per il backup?

Sì, l'API Tabelle sfrutta i vantaggi della piattaforma di Azure Cosmos DB per i backup. I backup vengono eseguiti automaticamente. Per altre informazioni, vedere [Backup online automatico e ripristino con Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>L'API Tabelle indicizza tutti gli attributi di un'entità per impostazione predefinita?

Sì. Per impostazione predefinita vengono indicizzati tutti gli attributi di un'entità. Per ulteriori informazioni, vedere [Azure Cosmos DB: criteri di indicizzazione](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Questo significa che non è necessario creare più di un indice per soddisfare le query?

Sì. L'API Tabelle di Azure Cosmos DB offre l'indicizzazione automatica di tutti gli attributi senza definizione di schema. Questa automazione consente agli sviluppatori di concentrarsi sull'applicazione anziché sulla creazione e sulla gestione degli indici. Per ulteriori informazioni, vedere [Azure Cosmos DB: criteri di indicizzazione](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>È possibile modificare i criteri di indicizzazione?

Sì. È possibile modificare i criteri di indicizzazione specificando la definizione di indice. È necessario eseguire correttamente la codifica e l'escape delle impostazioni.

Per gli SDK non .NET è possibile configurare i criteri di indicizzazione solo nel portale in **Esplora dati**. Passare alla tabella specifica da modificare, quindi selezionare **Scala e impostazioni**->Criteri di indicizzazione, apportare le modifiche desiderate e quindi scegliere **Salva**.

Da .NET SDK è possibile inviare i criteri nel file app.config:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Come piattaforma, Azure Cosmos DB offre numerose funzionalità, come l'ordinamento, le aggregazioni, la gerarchia e così via. Queste funzionalità verranno aggiunte all'API Table?

L'API Tabelle offre le stesse funzionalità di query dell'archiviazione tabelle di Azure. Azure Cosmos DB supporta anche l'ordinamento, le aggregazioni, le query geospaziali, la gerarchia e un'ampia gamma di funzioni predefinite. Per altre informazioni, vedere [Query SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Quando è consigliabile modificare TableThroughput per l'API Tabelle?

È consigliabile modificare TableThroughput quando si verifica una delle condizioni seguenti:

* Si esegue un'operazione di estrazione, trasformazione e caricamento (ETL) di dati o si vuole caricare una grande quantità di dati in breve tempo.
* È necessaria maggiore velocità effettiva del contenitore o di un set di contenitori nel back-end. Ad esempio, si rileva che la velocità effettiva usata è superiore alla velocità effettiva di cui è stato effettuato il provisioning e viene applicata la limitazione. Per altre informazioni, vedere [impostare la velocità effettiva per i contenitori di Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>È possibile aumentare o ridurre la velocità effettiva di una tabella dell'API Tabelle?

Sì, è possibile usare il riquadro relativo alla scalabilità del portale di Azure Cosmos DB per ridimensionare la velocità effettiva. Per altre informazioni, vedere l'articolo su come [impostare la velocità effettiva](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Per le tabelle appena sottoposte a provisioning viene impostato un valore predefinito di TableThroughput?

Sì. Se non si sostituisce il valore di TableThroughput in app.config e non si usa un contenitore già creato in Azure Cosmos DB, il servizio crea una tabella con velocità effettiva di 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>I prezzi per i clienti esistenti del servizio archiviazione tabelle di Azure sono cambiati?

Nessuno. Il prezzo per i clienti esistenti di Archiviazione tabelle di Azure non è cambiato.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Come viene calcolato il prezzo per l'API Tabelle?

Il prezzo dipende dal valore di TableThroughput allocato.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Come si gestisce l'eventuale limitazione della frequenza nelle tabelle dell'offerta dell'API Tabelle?

Se la frequenza delle richieste supera la capacità della velocità effettiva di cui è stato effettuato il provisioning per il contenitore sottostante o un set di contenitori, verrà restituito un errore e l'SDK ripeterà la chiamata applicando i criteri di ripetizione dei tentativi.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Perché è necessario scegliere una velocità effettiva oltre alla chiave di partizione e alla chiave di riga per sfruttare i vantaggi dell'offerta API Tabelle di Azure Cosmos DB?

Azure Cosmos DB imposta una velocità effettiva predefinita per il contenitore, se non se ne specifica una nel file app.config o tramite il portale.

Azure Cosmos DB offre garanzie di prestazioni e latenza, con limiti superiori nelle operazioni. Questa garanzia è possibile quando il motore può applicare la governance nelle operazioni del tenant. L'impostazione di TableThroughput assicura la velocità effettiva e la latenza garantite, perché la piattaforma riserva tale capacità e garantisce l'esito positivo delle operazioni.

È possibile usare la specifica della velocità effettiva modificandola in modo elastico per trarre vantaggio dalla stagionalità dell'applicazione, soddisfare le esigenze in termini di velocità effettiva e ridurre i costi.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Archiviazione tabelle di Azure è una soluzione economica se si eseguono raramente query, perché si paga solo per l'archiviazione dei dati. Con l'offerta relativa all'API Tabella di Azure Cosmos DB sembra che vengano effettuati addebiti anche se non si esegue alcuna transazione o non si archiviano dati. Per quale motivo?

Azure Cosmos DB è progettato come un sistema basato su contratti di servizio distribuito a livello globale con garanzie di disponibilità, latenza e velocità effettiva. La velocità effettiva riservata in Azure Cosmos DB è garantita, a differenza della velocità effettiva di altri sistemi. Azure Cosmos DB offre funzionalità aggiuntive che sono state richieste dai clienti, come gli indici secondari e la distribuzione globale.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Quando si inseriscono dati nell'archiviazione tabelle di Azure, non viene mai visualizzata una notifica di raggiungimento della quota che indica che la partizione è piena. Con l'API Tabelle viene visualizzato questo messaggio. Questa offerta impone limitazioni e la modifica dell'applicazione esistente?

Azure Cosmos DB è un sistema basato su contratti di servizio che offre scalabilità illimitata, con garanzie di latenza, velocità effettiva, disponibilità e coerenza. Per assicurarsi le prestazioni Premium garantite, verificare che l'indice e le dimensioni dei dati siano gestibili e scalabili. Il limite di 10 GB per il numero di entità o elementi per chiave di partizione ha lo scopo di assicurare prestazioni di ricerca e di query elevate. Per garantire la scalabilità dell'applicazione anche per Archiviazione di Azure, è consigliabile *non* creare una partizione critica archiviando tutte le informazioni in una partizione ed eseguendo query su di essa.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>La chiave di partizione e la chiave di riga sono ancora necessarie con l'API Tabelle?

Sì. Dato che la superficie di attacco dell'API Tabelle è simile a quella di Azure Table Storage SDK, la chiave di partizione offre un modo efficiente per distribuire i dati. La chiave di riga è univoca all'interno della partizione. La chiave di riga deve essere presente e non può essere Null come nell'SDK standard. La chiave di riga è lunga 255 byte, mentre la chiave di partizione ha una lunghezza di 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Quali sono i messaggi di errore per l'API Tabelle?

L'archiviazione tabelle di Azure e l'API Tabelle di Azure Cosmos DB usano gli stessi SDK, quindi la maggior parte degli errori sarà uguale.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Perché quando si prova a creare numerose tabelle una dopo l'altra nell'API Tabelle viene applicata la limitazione?

Azure Cosmos DB è un sistema basato su contratti di servizio che offre garanzie di latenza, velocità effettiva, disponibilità e coerenza. Dato che è un sistema con provisioning, riserva risorse per garantire tali requisiti. La rapida frequenza di creazione delle tabelle viene rilevata e limitata. È consigliabile esaminare la frequenza di creazione di tabelle e ridurla a meno di 5 al minuto. Tenere presente che l'API Tabelle è un sistema con provisioning. Il relativo pagamento ha inizio nel momento in cui ne viene effettuato il provisioning.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Come si forniscono commenti e suggerimenti sull'SDK o sui bug?

È possibile condividere commenti e suggerimenti in uno dei modi seguenti:

* [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow è ideale per domande relative alla programmazione. Assicurarsi che la domanda sia [pertinente](https://stackoverflow.com/help/on-topic) e [specificare il numero massimo possibile di dettagli, in modo che la domanda sia chiara e sia possibile fornire una risposta](https://stackoverflow.com/help/how-to-ask).

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app dell'API Tabella con .NET SDK e Azure Cosmos DB](create-table-dotnet.md)
* [Creare un'app Java per gestire i dati dell'API Tabella di Azure Cosmos DB](create-table-java.md)