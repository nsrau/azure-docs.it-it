<properties title="Search Service: workflow for developers" pageTitle="Search Service: workflow for developers" description="Search Service: workflow for developers" metaKeywords="" services="" solutions="" documentationCenter="" authors="heidist" videoId="" scriptId="" />

# Ricerca di Azure: flusso di lavoro per la distribuzione

[WACOM.INCLUDE [Questo articolo usa il portale di anteprima di Azure][]]

Questo articolo contiene una guida di orientamento e alcune procedure consigliate per la creazione e la manutenzione del servizio di ricerca con i relativi indici.

Si suppone che sia già stato effettuato il provisioning del servizio. Per iniziare, vedere [Configurare il servizio di ricerca nel portale di anteprima di Azure][].

-   [Passaggio 1: Creare l'indice][]
-   [Passaggio 2: Aggiungere i documenti][]
-   [Passaggio 3: Eseguire una query su un indice][]
-   [Passaggio 4: Aggiornare o eliminare gli indici e i documenti][]
-   [Considerazioni sulla progettazione delle risorse di archiviazione][]

## Passaggio 1: Creare l'indice

Le query (almeno le query non di sistema) sono mirate a un indice di ricerca contenente dati e attributi di ricerca. In questo passaggio verrà definito lo schema dell'indice in formato JSON ed eseguita una richiesta PUT HTTPS per creare quest'indice nel servizio.

Gli indici sono generalmente codificati nel proprio ambiente di sviluppo locale. Non sono disponibili strumenti o editor incorporati per la definizione dell'indice. Per altre informazioni sulla creazione dell'indice, vedere [Creare l'indice (API di Ricerca di Azure][] su MSDN.

## Passaggio 2: Aggiungere i documenti

Dopo aver creato l'indice di ricerca, sarà possibile aggiungere documenti all'indice eseguendo la richiesta POST in formato JSON. Ogni documento deve prevedere una chiave univoca e una raccolta di campi contenente dati disponibili e non per la ricerca. I dati del documento vengono rappresentati come un insieme di coppie chiave/valore.

È consigliabile aggiungere i documenti in batch per migliorare la velocità effettiva. È possibile creare batch contenenti fino a 10.000 documenti, supponendo che le dimensioni medie di un documento siano di ricerca 1 o 2 KB.

Per la richiesta POST c'è un codice di stato generale. I codici di stato sono HTTP 200 (Success) o HTTP 207 (Multi-Status) se esiste una combinazione di documenti elaborati correttamente o non elaborati. Oltre al codice di stato per la richiesta POST, la Ricerca di Azure prevede un campo di stato per ogni documento. In un caricamento in batch è necessario ottenere lo stato di ogni documento che indichi se l'inserimento è riuscito o no. Il campo di stato fornisce tali informazioni. Sarà impostato su false se non è stato possibile caricare il documento.

Con un carico pesante non è raro riscontrare alcuni errori di caricamento. Qualora ciò dovesse verificarsi, il codice di stato generale è 207, che indica un esito positivo parziale, mentre i documenti per i quali l'indicizzazione ha avuto esito negativo saranno contrassegnati dalla proprietà "status" impostata su false.

> [WACOM.NOTE] Quando il servizio riceve i documenti, questi vengono messi in coda per l'indicizzazione e potrebbero non essere immediatamente inclusi nei risultati della ricerca. Quando non è presente un carico pesante, i documenti vengono generalmente indicizzati entro pochi secondi.

## Passaggio 3: Eseguire una query su un indice

Dopo aver indicizzato i documenti è possibile eseguire le query di ricerca. Si può interrogare un indice per volta, usando OData o una semplice sintassi di query:

-   [Sintassi dell'espressione OData per la Ricerca di Azure][]
-   [Semplice sintassi di query nella Ricerca di Azure][]

## Passaggio 4: Aggiornare o eliminare gli indici e i documenti

È facoltativamente possibile apportare modifiche allo schema dell'indice di ricerca, aggiornare o eliminare i documenti all'interno dell'indice ed eliminare gli indici.

Quando si aggiorna un indice è possibile combinare più azioni (inserimento, unione, eliminazione) nello stesso batch, eliminando la necessità di eseguire più round trip. Attualmente, la Ricerca di Azure non supporta gli aggiornamenti parziali (HTTP PATCH), perciò per aggiornare un indice sarà necessario inviare nuovamente la relativa definizione.

## Considerazioni sulla progettazione delle risorse di archiviazione

Molte applicazioni di ricerca usano vari formati di archiviazione per diverse esigenze applicative. Le risorse di archiviazione interne offerte dalla Ricerca di Azure consentono di archiviare indici e documenti. L'analisi dei testi dipende dalla disponibilità di tutti i campi per la ricerca e degli attributi associati.

Non tutti i campi di un documento sono disponibili per la ricerca. Ad esempio, se l'applicazione è un catalogo online per musica o video, è consigliabile archiviare i file binari in BLOB o in altre risorse di archiviazione. Gli stessi file binari non sono disponibili per la ricerca, quindi non è necessario salvarli in modo permanente nell'archiviazione della Ricerca di Azure. È opportuno archiviare file immagine, video e audio in altri servizi o percorsi, con un campo nel documento che contenga l'URL al percorso del file.

Per altre informazioni sulla creazione di indici o documenti, vedere [API REST del servizio di ricerca di Azure][].

<!--Anchors--> 
<!--Image references--> 
<!--Link references-->

  [Questo articolo usa il portale di anteprima di Azure]: ../includes/preview-portal-note.md
  [Configurare il servizio di ricerca nel portale di anteprima di Azure]: ../search-configure/
  [Passaggio 1: Creare l'indice]: #sub-1
  [Passaggio 2: Aggiungere i documenti]: #sub-2
  [Passaggio 3: Eseguire una query su un indice]: #sub-3
  [Passaggio 4: Aggiornare o eliminare gli indici e i documenti]: #sub-4
  [Considerazioni sulla progettazione delle risorse di archiviazione]: #sub-5
  [Creare l'indice (API di Ricerca di Azure]: http://msdn.microsoft.com/en-us/library/dn798941.aspx
  [Sintassi dell'espressione OData per la Ricerca di Azure]: http://msdn.microsoft.com/en-us/library/dn798921.aspx
  [Semplice sintassi di query nella Ricerca di Azure]: http://msdn.microsoft.com/en-us/library/dn798920.aspx
  [API REST del servizio di ricerca di Azure]: http://msdn.microsoft.com/en-us/library/dn798935.aspx
