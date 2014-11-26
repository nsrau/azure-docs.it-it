<properties title="Manage your Search service on Microsoft Azure" pageTitle="Manage your Search service on Microsoft Azure" description="Manage your Search service on Microsoft Azure" metaKeywords="" services="" solutions="" documentationCenter="" authors="heidist" videoId="" scriptId="" />

# Gestire il servizio di ricerca in Microsoft Azure

[WACOM.INCLUDE [Questo articolo usa il portale di anteprima di Azure](../includes/preview-portal-note.md)]

Il servizio di ricerca di Azure è un servizio basato sul cloud nonché un'API basata su HTTP che è possibile usare in applicazioni di ricerca personalizzate. Il servizio di ricerca fornisce il motore per l'analisi del testo tramite ricerca full-text, funzionalità avanzate di ricerca, archiviazione e una sintassi dei comandi di query.

Questo articolo spiega come amministrare un servizio di ricerca in Microsoft Azure.

Come accennato, è necessario usare il portale di anteprima per le attività amministrative. Il servizio di ricerca di Azure non è disponibile in versioni precedenti del portale. Un'API di gestione sarà presto disponibile per lo scripting delle attività di gestione più comuni.

<!--TOC-->

-   [Aggiungere un servizio di ricerca alla sottoscrizione][Aggiungere un servizio di ricerca alla sottoscrizione]
-   [Attività amministrative][Attività amministrative]
-   [URL del servizio][URL del servizio]
-   [Gestire le chiavi API][Gestire le chiavi API]
-   [Monitorare l'utilizzo delle risorse][Monitorare l'utilizzo delle risorse]
-   [Aumentare o ridurre la quantità di risorse][Aumentare o ridurre la quantità di risorse]
-   [Avviare o arrestare il servizio][Avviare o arrestare il servizio]

## Aggiungere un servizio di ricerca alla sottoscrizione

Come amministratore si ha la facoltà di aggiungere un servizio di ricerca alla sottoscrizione di Azure usando il [portale di anteprima di Azure][portale di anteprima di Azure]. Solo gli amministratori possono aggiungere funzionalità a una sottoscrizione. Quando si configura il servizio, sono disponibili due livelli di prezzo tra cui scegliere.

Per gli abbonati esistenti è disponibile un servizio condiviso senza costi aggiuntivi, consigliato per scopi di apprendimento, test dei modelli di prova e piccoli progetti di sviluppo. Il servizio condiviso è limitato a 50 MB di spazio di archiviazione, tre indici e un numero massimo 10.000 documenti, anche qualora lo spazio di archiviazione usato da questi ultimi sia inferiore ai 50 MB consentiti. Non vi è garanzia alcuna rispetto alle prestazioni del servizio condiviso, pertanto, se si prevede di sviluppare un'applicazione di ricerca di produzione, è consigliabile valutare l'acquisto del servizio di ricerca standard.

Il servizio di ricerca standard è fatturabile perché fornisce accesso a risorse dedicate e a un'infrastruttura usata unicamente dalla propria sottoscrizione. Il servizio di ricerca standard viene allocato in bundle di partizioni definiti dall'utente (archiviazione) e repliche (carichi di lavoro del servizio) e viene tariffato per unità di ricerca. È possibile aumentare partizioni o repliche indipendentemente, aggiungendo le risorse necessarie.

Per pianificare la capacità e comprendere l'impatto di fatturazione, è consigliabile consultare le informazioni disponibili seguendo i collegamenti seguenti:

-   [Limitazioni e vincoli][Limitazioni e vincoli]
-   [Dettagli prezzi][Dettagli prezzi]

Quando si è pronti per l'iscrizione, vedere [Configurare il servizio di ricerca nel portale di anteprima di Azure][Configurare il servizio di ricerca nel portale di anteprima di Azure].

## Attività amministrative

Anche se per alcuni servizi sono ammessi i coamministratori, per un servizio di ricerca di Azure è ammesso un solo amministratore per sottoscrizione. Per eseguire le attività descritte in questa sezione è necessario essere un amministratore.
Oltre che per l'aggiunta di un servizio di ricerca alla sottoscrizione, un amministratore è responsabile per l'esecuzione delle attività seguenti:

-   Distribuzione dell'URL del servizio (definito durante il provisioning del servizio).
-   Gestione e distribuzione delle chiavi API.
-   Monitoraggio dell'utilizzo delle risorse
-   Aumento o riduzione (applicabile solo al servizio di ricerca standard)
-   Avvio o arresto del servizio

## URL del servizio

L'URL del servizio è definito come proprietà fissa quando si crea il servizio. Non sarà possibile modificarlo in seguito.

Gli sviluppatori che devono creare applicazioni di ricerca dovranno conoscere l'URL del servizio per le richieste HTTP. È possibile individuare rapidamente l'URL del servizio tramite il Dashboard servizi.

Per ottenere l'URL del servizio dal Dashboard servizi:

1.  Accedere al [portale di anteprima di Azure][portale di anteprima di Azure].
2.  Fare clic su **Sfoglia** | **Tutto** | **Servizi di ricerca**.
3.  Fare clic sul nome del servizio di ricerca per aprire il dashboard.
4.  Fare clic su **PROPRIETÀ** per aprire una pagina delle proprietà con effetto scorrimento. L'URL del servizio è visualizzato nella parte superiore della pagina. È possibile bloccare questa pagina per accedervi facilmente in seguito.

    ![][0]

Gli sviluppatori potrebbero richiedere anche la versione dell'API. Un requisito per la codifica di API del servizio di ricerca di Azure è la specifica della versione dell'API nella richiesta. Tale requisito esiste per consentire agli sviluppatori di continuare a usare una versione precedente e quindi passare a una versione successiva al momento opportuno.

La versione dell'API non è visualizzata nelle pagine del portale, pertanto è un'informazione che l'utente non può fornire. Per informazioni sulle versioni API attuali e precedenti, vedere [API REST del servizio di ricerca di Azure][API REST del servizio di ricerca di Azure].

<!---->

## Gestire le chiavi API

Gli sviluppatori che devono creare applicazioni di ricerca avranno bisogno di una chiave API per accedere al servizio di ricerca. Ogni richiesta HTTP indirizzata al servizio di ricerca necessiterà di una chiave API generata specificamente per il proprio servizio. Questa chiave API è l'unico meccanismo di autenticazione per l'URL del servizio di ricerca.

Per accedere al servizio di ricerca vengono usati due tipi di chiavi:

-   Amministrativa (valida per qualsiasi operazione)
-   Di query (valida solo per richieste di query)

Una chiave API amministrativa viene creata con il servizio. Vi sono una chiave primaria e una secondaria. Queste possono essere usate indifferentemente. Nessuna delle due prevede un livello di accesso maggiore o minore rispetto all'altra, il che può essere utile nel caso in cui si voglia eseguire il rollover delle chiavi. È possibile rigenerare qualsiasi delle due chiavi amministrative ma non è possibile aggiungere chiavi al totale delle chiavi amministrative disponibili. È consentito un massimo di due chiavi amministrative per ogni servizio di ricerca.

Le chiavi di query sono progettate per l'uso in applicazioni client che chiamano il servizio di ricerca direttamente. È possibile creare fino a 50 chiavi di query.

Per ottenere o rigenerare le chiavi API, aprire il Dashboard servizi. Fare clic su **CHIAVI** per aprire la pagina di gestione delle chiavi con effetto scorrimento. I comandi per rigenerare o creare le chiavi sono visualizzati nella parte superiore della pagina.

![][1]

<!---->

## Monitorare l'utilizzo delle risorse

In questa anteprima pubblica, il monitoraggio delle risorse è limitato alle informazioni visualizzate nel dashboard del servizio e ad alcune metriche che è possibile ottenere effettuando query sul servizio.

Nella sezione Utilizzo del Dashboard servizi è possibile determinare rapidamente se i livelli delle risorse di partizione sono adeguati per l'applicazione.

Usando l'API del servizio di ricerca, è possibile ottenere un conteggio dei documenti e degli indici. Esistono limiti rigidi associati a questi conteggi in base al livello di prezzo. Per dettagli, vedere [Limitazioni e vincoli][Limitazioni e vincoli].

-   [Ottieni statistiche indice][Ottieni statistiche indice]
-   [Conteggio documenti][Conteggio documenti]

> [WACOM.NOTE] Il comportamento della cache può determinare la dichiarazione di un limite più alto. Ad esempio, quando si usa il servizio condiviso, è possibile che venga visualizzato un conteggio documenti superiore al limite rigido di 10.000. Questa stima in eccesso è temporanea e verrà rilevata al successivo controllo di imposizione del limite.

<!---->

## Aumentare o ridurre la quantità di risorse

Ogni servizio di ricerca viene creato con un minimo di una replica e una partizione. Se si è effettuata l'iscrizione alle risorse dedicate usando il livello di prezzo Standard, fare clic sul riquadro **RIDIMENSIONA** nel dashboard del servizio per regolare il numero di partizioni e di repliche usate dal servizio.

Quando si aggiungono risorse, queste verranno usate dal servizio in modo automatico. Non sono necessarie ulteriori azioni da parte dell'utente, ma vi sarà un lieve ritardo prima che l'impatto delle nuove risorse sia apprezzabile. Possono essere necessari 15 o più minuti per il provisioning delle risorse aggiuntive.

![][2]

### Aggiungere repliche

L'aumento delle query al secondo o il raggiungimento della disponibilità elevata si ottengono mediante l'aggiunta di repliche. Ogni replica presenta una copia di un indice, pertanto l'aggiunta di un'ulteriore replica si traduce in un indice aggiuntivo che può essere usato per soddisfare le richieste di query. In linea generale, attualmente sono necessarie almeno 3 repliche per la disponibilità elevata.

Un servizio di ricerca con un numero superiore di repliche può eseguire il bilanciamento del carico delle richieste di query su un numero maggiore di indici. Per un determinato volume di query, la velocità effettiva delle query sarà maggiore in presenza di più copie dell'indice disponibili per soddisfare la richiesta. Se si verificano latenze delle query, ci si può aspettare un netto miglioramento delle prestazioni una volta portate online le repliche aggiuntive.

Sebbene la velocità effettiva delle query aumenti quando si aggiungono repliche, non viene effettivamente raddoppiata o triplicata in rapporto al numero di repliche aggiunte. Tutte le applicazioni di ricerca sono influenzate da fattori esterni che possono influire sulle prestazioni di query. Query complesse e latenza di rete sono due fattori che contribuiscono alle variazioni nei tempi di risposta alle query.

### Aggiungere partizioni

La maggior parte delle applicazioni di servizio mostrano una necessità insita di disporre di più repliche, più che di un numero maggiore di partizioni, poiché la maggior parte delle applicazioni che usano la ricerca può essere contenuta facilmente in una singola partizione che può supportare fino a 15 milioni di documenti.

Per quei casi in cui è richiesto un numero maggiore di documenti, è possibile aggiungere partizioni. Si noti che le partizioni vengono aggiunte in multipli di 12 (ossia 1, 2, 3, 4, 6 o 12). Si tratta di un artefatto del partizionamento orizzontale: un indice viene creato in 12 sottopartizioni, tutte archiviabili a propria volta in un'unica partizione o equamente distribuibili in 2, 3, 4, 6 o 12 partizioni (in quest'ultimo caso, una per partizione).

### Rimuovere repliche

Dopo periodi di alti volumi di query è possibile che si vogliano rimuovere repliche una volta che i carichi di query di ricerca si sono normalizzati (ad esempio al termine di un periodo di vendite prefestive).

Per fare ciò è sufficiente spostare il dispositivo di scorrimento relativo alle repliche su un numero inferiore. Non vi sono altre azioni richieste da parte dell'utente. La riduzione del numero di repliche comporta il rilascio di macchine virtuali nel data center. Le operazioni di query e di inserimento dati verranno ora elaborate su un numero minore di VM rispetto a prima. Il limite minimo è di una replica.

### Rimuovere partizioni

A differenza della rimozione di repliche, che non richiede operazioni aggiuntive da parte dell'utente, potrebbe essere richiesto lavoro aggiuntivo se si occupa più archiviazione di quanta se ne vuole ottenere dopo una riduzione. Ad esempio, se la soluzione usa tre partizioni, il tentativo di ridurle a una o due partizioni genererà un errore se si sta usando più spazio di archiviazione di quanto ne è disponibile nel numero di partizioni a cui si vuole arrivare. In questo caso la scelta è di eliminare indici o documenti all'interno di un indice associato per liberare spazio oppure di mantenere la configurazione attuale.

Non è disponibile un metodo di rilevamento che indichi quante sottopartizioni di indice sono archiviate su una partizione specifica. Ogni partizione fornisce approssimativamente 25 GB di spazio di archiviazione, pertanto sarà necessario ridurre l'archiviazione a una dimensione che possa essere contenuta nel numero disponibile di partizioni. Se si vuole tornare a una partizione, questa dovrà contenere tutte e 12 le sottopartizioni.

Per pianificare in anticipo le esigenze future, è possibile verificare l'archiviazione (usando [Ottieni statistiche indice][Ottieni statistiche indice]) per verificare quanta archiviazione è stata effettivamente usata.

<!---->

## Avviare o arrestare il servizio

È possibile avviare, arrestare o eliminare il servizio usando i comandi disponibili nel Dashboard servizi.

![][3]

L'arresto o l'avvio del servizio non determinano la sospensione della fatturazione. Per evitare qualsiasi ulteriore addebito relativo al servizio, è necessario che quest'ultimo venga eliminato. Quando il servizio viene rimosso, tutti i dati ad esso associati verranno eliminati.

<!--Anchors--> 
<!--Image references--> 
<!--Link references-->

  [Aggiungere un servizio di ricerca alla sottoscrizione]: #sub-1
  [Attività amministrative]: #sub-2
  [URL del servizio]: #sub-3
  [Gestire le chiavi API]: #sub-4
  [Monitorare l'utilizzo delle risorse]: #sub-5
  [Aumentare o ridurre la quantità di risorse]: #sub-6
  [Avviare o arrestare il servizio]: #sub-7
  [portale di anteprima di Azure]: https://portal.azure.com
  [Limitazioni e vincoli]: http://msdn.microsoft.com/it-it/library/dn798934.aspx
  [Dettagli prezzi]: http://go.microsoft.com/fwlink/p/?LinkdID=509792
  [Configurare il servizio di ricerca nel portale di anteprima di Azure]: ../search-configure/
  [0]: ./media/search-manage/Azure-Search-Manage-1-URL.png
  [API REST del servizio di ricerca di Azure]: http://go.microsoft.com/fwlink/p/?LinkdID=509922
  [1]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
  [Ottieni statistiche indice]: http://msdn.microsoft.com/it-it/library/dn798942.aspx
  [Conteggio documenti]: http://msdn.microsoft.com/it-it/library/dn798924.aspx
  [2]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png
  [3]: ./media/search-manage/Azure-Search-Manage-4-StartStop.png
