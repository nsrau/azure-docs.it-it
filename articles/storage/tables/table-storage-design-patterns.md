---
title: Modelli di progettazione tabella di Archiviazione di Azure | Microsoft Docs
description: Usare i modelli per le soluzioni di servizio tabelle di Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: b06f5a66566c250eef608ddccc551aaebe24ef74
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522799"
---
# <a name="table-design-patterns"></a>Modelli di progettazione tabella
Questo articolo descrive alcuni modelli adatti all'uso con le soluzioni di servizio tabelle. Fornisce inoltre informazioni su come risolvere alcuni dei problemi e dei compromessi illustrati negli altri articoli sulla progettazione dell'archiviazione tabelle. Il diagramma seguente contiene un riepilogo delle relazioni tra i diversi modelli:  

![per cercare i dati correlati](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


La mappa dei modelli nella figura precedente evidenzia alcune relazioni tra i modelli (blu) e gli anti-modelli (arancione) documentati in questa guida. Esistono molti altri modelli utili. Ad esempio, uno degli scenari chiave per il servizio tabelle è l'uso del [modello di vista materializzata](https://msdn.microsoft.com/library/azure/dn589782.aspx) dal modello [Command Query Responsibility Segregation](https://msdn.microsoft.com/library/azure/jj554200.aspx) (CQRS).  

## <a name="intra-partition-secondary-index-pattern"></a>Modello per indice secondario intrapartizione
Archivia più copie di ogni entità usando valori **RowKey** diversi (nella stessa partizione) per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi. Gli aggiornamenti tra copie possono essere mantenuti coerenti usando transazioni EGT.  

### <a name="context-and-problem"></a>Contesto e problema
Il servizio tabelle indicizza automaticamente le entità usando i valori **PartitionKey** e **RowKey**. Questo consente a un'applicazione client di recuperare un'entità in modo efficiente mediante questi valori. Ad esempio, usando la struttura della tabella riportata di seguito, un'applicazione client può usare una query di tipo punto per recuperare una singola entità dipendente attraverso il nome del reparto e l'ID del dipendente (i valori **PartitionKey** e **RowKey**). Un client può anche recuperare entità ordinate per ID dipendente in ogni reparto.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Se si desidera poter trovare un'entità dipendente anche in base al valore di un'altra proprietà, ad esempio l'indirizzo di posta elettronica, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza. Il motivo è che il servizio tabelle non fornisce indici secondari. Inoltre, non esiste un'opzione per richiedere un elenco di dipendenti ordinato in modo diverso rispetto all'ordine **RowKey** .  

### <a name="solution"></a>Soluzione
Per ovviare alla mancanza di indici secondari, è possibile archiviare più copie di ogni entità usando per ogni copia un valore **RowKey** diverso. Se si archivia un'entità con le strutture riportate di seguito, è possibile recuperare in modo efficiente entità dipendente in base all'ID dipendente o all'indirizzo di posta elettronica. I valori di prefisso per **RowKey**, "empid_" e "email_", consentono di eseguire query per un singolo dipendente o un intervallo di dipendenti usando un intervallo di indirizzi e-mail o ID dipendente.  

![Entità dipendente](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

I due criteri di filtro seguenti (uno che ricerca per ID dipendente e uno che ricerca per indirizzo di posta elettronica) specificano entrambi query di tipo punto:  

* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'empid_000223)  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'email_jonesj@contoso.com')  

Se si esegue una query su un intervallo di entità dipendente, è possibile specificare un intervallo ordinato per ID dipendente o un intervallo ordinato per indirizzo di posta elettronica eseguendo la query sulle entità con il prefisso appropriato in **RowKey**.  

* Per trovare tutti i dipendenti nel reparto vendite con un ID dipendente compreso tra 000100 e 000199 usare: $filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000100') and (RowKey le 'empid_000199')  
* Per trovare tutti i dipendenti del reparto vendite con un indirizzo di posta elettronica che inizia con la lettera "a" utilizzare: $filter = (PartitionKey eq "Sales") e (RowKey ge 'email_a') e (RowKey It'email_b')  
  
  Si noti che la sintassi di filtro usata negli esempi precedenti proviene dall'API REST del servizio tabelle. Per altre informazioni, vedere [Query Entities](http://msdn.microsoft.com/library/azure/dd179421.aspx) (Entità query).  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* L'uso dell'archiviazione tabelle è relativamente economico, pertanto l'aumento dei costi dovuto all'archiviazione di dati duplicati non dovrebbe rappresentare una preoccupazione. È però consigliabile valutare sempre il costo del progetto in base ai requisiti di archiviazione previsti e aggiungere entità duplicate solo per supportare le query che verranno eseguite dall'applicazione client.  
* Poiché le entità di indice secondario vengono archiviate nella stessa partizione delle entità originali, è necessario assicurarsi di non superare gli obiettivi di scalabilità delle singole partizioni.  
* Per mantenere la coerenza tra entità duplicate è possibile usare transazioni ETG, che consentono di aggiornare le due copie dell'entità in modo atomico. A questo scopo è necessario archiviare tutte le copie di un'entità nella stessa partizione. Per altre informazioni, vedere la sezione [Transazioni di gruppi di entità](table-storage-design.md#entity-group-transactions).  
* Il valore usato per **RowKey** deve essere univoco per ogni entità. Provare a usare valori di chiave composti.  
* Il riempimento dei valori numerici in **RowKey** (ad esempio l'ID dipendente 000223) rende possibile l'ordinamento e il filtro corretto in base ai limiti superiori e inferiori.  
* Non è necessario duplicare tutte le proprietà dell'entità. Se ad esempio le query che eseguono la ricerca di entità usando l'indirizzo di posta elettronica in **RowKey** non hanno mai bisogno dell'età del dipendente, queste entità potrebbero avere la struttura seguente:

   ![Struttura di entità dipendente](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* In genere è preferibile archiviare dati duplicati e assicurarsi che sia possibile recuperare tutti i dati necessari con una singola query anziché usando una query per individuare un'entità e una seconda per cercare i dati richiesti.  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando l'applicazione client deve recuperare le entità usando una serie di chiavi diverse, quando il client deve recuperare entità con criteri di ordinamento diversi e nei casi in cui è possibile identificare ogni entità attraverso una varietà di valori univoci. È però necessario assicurarsi che durante l'esecuzione di ricerche di entità con valori **RowKey** diversi non vengano superati i limiti di scalabilità della partizione.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per indice secondario interpartizione](#inter-partition-secondary-index-pattern)
* [Modello per chiave composta](#compound-key-pattern)
* [Transazioni dei gruppi di entità](#entity-group-transactions)
* [Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Modello per indice secondario intrapartizione
Archivia più copie di ogni entità usando valori **RowKey** diversi in partizioni separate o in tabelle separate per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi.  

### <a name="context-and-problem"></a>Contesto e problema
Il servizio tabelle indicizza automaticamente le entità usando i valori **PartitionKey** e **RowKey**. Questo consente a un'applicazione client di recuperare un'entità in modo efficiente mediante questi valori. Ad esempio, usando la struttura della tabella riportata di seguito, un'applicazione client può usare una query di tipo punto per recuperare una singola entità dipendente attraverso il nome del reparto e l'ID del dipendente (i valori **PartitionKey** e **RowKey**). Un client può anche recuperare entità ordinate per ID dipendente in ogni reparto.  

![ID dipendente](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Se si desidera poter trovare un'entità dipendente anche in base al valore di un'altra proprietà, ad esempio l'indirizzo di posta elettronica, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza. Il motivo è che il servizio tabelle non fornisce indici secondari. Inoltre, non esiste un'opzione per richiedere un elenco di dipendenti ordinato in modo diverso rispetto all'ordine **RowKey** .  

Si prevede un volume molto elevato di transazioni su queste entità e si vuole ridurre al minimo il rischio che il servizio tabelle esegua la limitazione del client.  

### <a name="solution"></a>Soluzione
Per ovviare alla mancanza di indici secondari, è possibile archiviare più copie di ogni entità usando per ogni copia valori **PartitionKey** e **RowKey** diversi. Se si archivia un'entità con le strutture riportate di seguito, è possibile recuperare in modo efficiente entità dipendente in base all'ID dipendente o all'indirizzo di posta elettronica. I valori di prefisso per **PartitionKey**, "empid_" e "email_", consentono di identificare l'indice da usare per una query.  

![Indice primario e indice secondario](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


I due criteri di filtro seguenti (uno che ricerca per ID dipendente e uno che ricerca per indirizzo di posta elettronica) specificano entrambi query di tipo punto:  

* $filter=(PartitionKey 'empid_Sales') e (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq 'jonesj@contoso.com')  

Se si esegue una query su un intervallo di entità dipendente, è possibile specificare un intervallo ordinato per ID dipendente o un intervallo ordinato per indirizzo di posta elettronica eseguendo la query sulle entità con il prefisso appropriato in **RowKey**.  

* Per trovare tutti i dipendenti del reparto vendite con un ID dipendente nell'intervallo compreso tra **000100** e **000199** ordinati in base all'ID dipendente, usare: $filter=(PartitionKey eq 'empid_Sales') and (RowKey ge '000100') and (RowKey le '000199')  
* Per trovare tutti i dipendenti del reparto vendite con un indirizzo di posta elettronica che inizia con 'a' ordinato in base all’indirizzo di posta elettronica utilizzare: $filter = (PartitionKey eq ' email_Sales') e (RowKey ge 'a') e (RowKey lt "b")  

Si noti che la sintassi di filtro usata negli esempi precedenti proviene dall'API REST del servizio tabelle. Per altre informazioni, vedere [Query Entities](http://msdn.microsoft.com/library/azure/dd179421.aspx) (Entità query).  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Per mantenere la coerenza finale tra le entità duplicate, è possibile usare il [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) per gestire le entità di indice primario e secondario.  
* L'uso dell'archiviazione tabelle è relativamente economico, pertanto l'aumento dei costi dovuto all'archiviazione di dati duplicati non dovrebbe rappresentare una preoccupazione. È però consigliabile valutare sempre il costo del progetto in base ai requisiti di archiviazione previsti e aggiungere entità duplicate solo per supportare le query che verranno eseguite dall'applicazione client.  
* Il valore usato per **RowKey** deve essere univoco per ogni entità. Provare a usare valori di chiave composti.  
* Il riempimento dei valori numerici in **RowKey** (ad esempio l'ID dipendente 000223) rende possibile l'ordinamento e il filtro corretto in base ai limiti superiori e inferiori.  
* Non è necessario duplicare tutte le proprietà dell'entità. Se ad esempio le query che eseguono la ricerca di entità usando l'indirizzo di posta elettronica in **RowKey** non hanno mai bisogno dell'età del dipendente, queste entità potrebbero avere la struttura seguente:
  
   ![Entità dipendente (indice secondario)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* In genere è preferibile archiviare dati duplicati e assicurarsi che sia possibile recuperare tutti i dati necessari con una singola query anziché usando una query per individuare un'entità mediante l'indice secondario e un'altra per cercare i dati richiesti nell'indice primario.  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando l'applicazione client deve recuperare le entità usando una serie di chiavi diverse, quando il client deve recuperare entità con criteri di ordinamento diversi e nei casi in cui è possibile identificare ogni entità attraverso una varietà di valori univoci. Usare questo modello quando si vuole evitare il superamento dei limiti di scalabilità della partizione durante l'esecuzione di ricerche di entità con i diversi valori **RowKey** .  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Modello per indice secondario intrapartizione](#intra-partition-secondary-index-pattern)  
* [Modello per chiave composta](#compound-key-pattern)  
* [Transazioni dei gruppi di entità](#entity-group-transactions)  
* [Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Modello per transazioni con coerenza finale
abilita un comportamento di coerenza finale tra i limiti della partizione o i limiti del sistema di archiviazione usando le code di Azure.  

### <a name="context-and-problem"></a>Contesto e problema
Le transazioni ETG consentono l'esecuzione di transazioni atomiche tra più entità che condividono la stessa chiave di partizione. Per motivi di scalabilità e prestazioni, si può scegliere di archiviare le entità con requisiti di coerenza in partizioni separate o in un sistema di archiviazione separato: in questo caso, non è possibile usare le transazioni ETG per mantenere la coerenza. Ad esempio, potrebbe essere necessario mantenere la coerenza finale tra:  

* Entità archiviate in due partizioni diverse nella stessa tabella, in tabelle diverse o in account di archiviazione diversi.  
* Un'entità archiviata nel servizio tabelle e un BLOB archiviato nel servizio BLOB.  
* Un'entità archiviata nel servizio tabelle e un file in un file system.  
* Un'entità archiviata nel servizio tabelle, ma indicizzata mediante Ricerca di Azure.  

### <a name="solution"></a>Soluzione
Usando le code di Azure, è possibile implementare una soluzione che offre coerenza finale tra due o più partizioni o sistemi di archiviazione.
Per illustrare questo approccio, si supponga di avere l'esigenza di archiviare le entità relative ai dipendenti precedenti. Queste entità sono raramente oggetto di query e devono essere escluse da tutte le attività associate ai dipendenti correnti. Per implementare questo requisito è necessario archiviare i dipendenti attivi nella tabella dei dipendenti **Correnti** e i dipendenti precedenti nella tabella dei dipendenti **Archiviati**. Per archiviare un dipendente è necessario eliminare l'entità dalla tabella dei dipendenti **Correnti** e aggiungerla a quella dei dipendenti **Archiviati**, ma non è possibile usare una transazione ETG per eseguire queste due operazioni. Per evitare il rischio che, a causa di un errore, un'entità venga visualizzata in entrambe le tabelle o in nessuna di esse, l'operazione di archiviazione deve garantire la coerenza finale. Il diagramma seguente illustra in sequenza i passaggi di questa operazione. Nel testo che segue sono disponibili maggiori dettagli per i percorsi di eccezione.  

![Soluzione per le code di Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Un client avvia l'operazione di archiviazione inserendo un messaggio in una coda di Azure, in questo esempio per l'archiviazione del dipendente 456. Un ruolo di lavoro esegue il polling della coda per individuare i nuovi messaggi. Quando ne trova uno, legge il messaggio e lascia una copia nascosta nella coda. Successivamente, il ruolo di lavoro recupera una copia dell'entità dalla tabella dei dipendenti **Correnti**, inserisce una copia nella tabella dei dipendenti **Archiviati** e quindi elimina l'originale dalla tabella dei dipendenti **Correnti**. Infine, se nei passaggi precedenti non si sono verificati errori, il ruolo di lavoro elimina il messaggio nascosto dalla coda.  

In questo esempio, il passaggio 4 inserisce il dipendente nella tabella dei dipendenti **Archiviati** . Potrebbe aggiungere il dipendente a un BLOB nel servizio BLOB o un file in un file system.  

### <a name="recovering-from-failures"></a>Ripristino da errori
È importante che le operazioni nei passaggi **4** e **5** siano *idempotenti* nei casi in cui il ruolo di lavoro deve riavviare l'operazione di archiviazione. Se si sta usando il servizio tabelle, per il passaggio **4** è consigliabile usare un'operazione "insert or replace"; per il passaggio **5** è consigliabile usare un'operazione "delete if exists" nella libreria client in uso. Se si sta usando un altro sistema di archiviazione, è consigliabile usare un'operazione idempotente appropriata.  

Se il ruolo di lavoro non completa mai il passaggio **6**, dopo un timeout il messaggio ricompare nella coda, pronto per una nuova elaborazione da parte del ruolo di lavoro. Il ruolo di lavoro può controllare quante volte un messaggio nella coda è stato letto e, se necessario, contrassegnarlo come messaggio non elaborabile da analizzare inviandolo a una coda separata. Per altre informazioni sulla lettura dei messaggi in coda e la verifica del numero di rimozioni dalla coda, vedere [Get Messages](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alcuni errori del servizio tabelle e del servizio di accodamento sono temporanei e l'applicazione client deve includere la logica di ripetizione dei tentativi appropriata per gestirli.  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Questa soluzione non prevede l'isolamento delle transazioni. Ad esempio, un client potrebbe leggere le tabelle dei dipendenti **Correnti** e **Archiviati** mentre il ruolo di lavoro è tra i passaggi **4** e **5** e ottenere una vista incoerente dei dati. Si noti che alla fine i dati saranno coerenti.  
* È necessario assicurarsi che i passaggi 4 e 5 siano idempotenti per garantire la coerenza finale.  
* È possibile ridimensionare la soluzione usando più code e istanze del ruolo di lavoro.  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando si desidera garantire la coerenza finale tra entità esistenti in tabelle o partizioni diverse. È possibile estendere il modello per garantire la coerenza finale per le operazioni tra il servizio tabelle e il servizio BLOB e altre origini dati di archiviazione non Azure, quali database o file system.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni dei gruppi di entità](#entity-group-transactions)  
* [Unione o sostituzione](#merge-or-replace)  

> [!NOTE]
> Se l'isolamento delle transazioni è importante per la soluzione, è consigliabile riprogettare le tabelle per consentire l'uso delle transazioni ETG.  
> 
> 

## <a name="index-entities-pattern"></a>Modello per entità di indice
mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  

### <a name="context-and-problem"></a>Contesto e problema
Il servizio tabelle indicizza automaticamente le entità usando i valori **PartitionKey** e **RowKey**. Consente a un'applicazione client di recuperare un'entità in modo efficiente mediante una query di tipo punto. Ad esempio, usando la struttura della tabella riportata di seguito, un'applicazione client può recuperare in modo efficiente una singola entità dipendente usando il nome del reparto e l'ID del dipendente (i valori **PartitionKey** e **RowKey**).  

![Entità dipendente](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Se si desidera poter recuperare un elenco di entità dipendente anche in base al valore di un'altra proprietà non univoca, ad esempio il cognome, è necessario usare un'analisi della partizione meno efficiente per trovare una corrispondenza piuttosto che usare un indice per la ricerca diretta. Il motivo è che il servizio tabelle non fornisce indici secondari.  

### <a name="solution"></a>Soluzione
Per attivare la ricerca per cognome con la struttura delle entità illustrata in precedenza, è necessario gestire elenchi di ID dipendente. Per recuperare le entità dipendente con un determinato cognome, ad esempio Jones, è necessario innanzitutto individuare l'elenco di ID relativi ai dipendenti con il cognome Jones e quindi recuperare tali entità dipendente. Per l'archiviazione dell'elenco di ID dipendente sono disponibili tre opzioni principali:  

* Usare l'archiviazione BLOB.  
* Creare entità di indice nella stessa partizione delle entità dipendente.  
* Creare entità di indice in una tabella o una partizione separata.  

<u>Opzione 1: usare l'archiviazione BLOB</u>  

Per la prima opzione è necessario creare un BLOB per ogni cognome univoco e archiviare in ogni BLOB un elenco dei valori **PartitionKey** (reparto) e **RowKey** (ID dipendente) per i dipendenti con questo cognome. Quando si aggiunge o elimina un dipendente, è necessario verificare la coerenza finale tra il contenuto del BLOB pertinente e le entità dipendente.  

<u>Opzione 2:</u> creare entità di indice nella stessa partizione  

Per la seconda opzione, usare entità di indice che archiviano i dati seguenti:  

![Entità di indice dipendente](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

La proprietà **EmployeeIDs** contiene un elenco di ID dipendente per i dipendenti con il cognome archiviato in **RowKey**.  

I passaggi seguenti illustrano il processo da seguire per aggiungere un nuovo dipendente se si usa la seconda opzione. In questo esempio si aggiunge al reparto vendite un dipendente con ID 000152 e cognome Jones:  

1. Recuperare l'entità di indice con il valore **PartitionKey** "Sales" e il valore **RowKey** "Jones". Salvare il valore ETag dell'entità per usarlo nel passaggio 2.  
2. Creare una transazione del gruppo di entità (cioè un'operazione batch) che inserisca la nuova entità dipendente (con valore **PartitionKey** "Sales" e valore **RowKey** "000152") e aggiorni l'entità di indice (con valore **PartitionKey** "Sales" e valore **RowKey** "Jones") aggiungendo il nuovo ID dipendente all'elenco nel campo EmployeeIDs. Per informazioni sulle transazioni di gruppi di entità, vedere la sezione [Transazioni di gruppi di entità](#entity-group-transactions).  
3. Se la transazione del gruppo di entità ha esito negativo a causa di un errore di concorrenza ottimistica (un altro utente ha appena modificato l'entità di indice), è necessario ricominciare dal passaggio 1.  

Se si usa la seconda opzione, è possibile adottare un approccio simile per l'eliminazione di un dipendente. Modificare il cognome del dipendente è un'operazione leggermente più complessa, in quanto è necessario eseguire una transazione del gruppo di entità che aggiorna tre entità: l'entità dipendente, l'entità di indice per il cognome precedente e l'entità di indice per il nuovo cognome. È necessario recuperare ogni entità prima di apportare qualsiasi modifica, per recuperare i valori ETag da usare in seguito per eseguire gli aggiornamenti usando la concorrenza ottimistica.  

I passaggi seguenti illustrano il processo da seguire per cercare tutti i dipendenti di un reparto con un determinato cognome se si usa la seconda opzione. In questo esempio si cercano tutti i dipendenti del reparto vendite il cui cognome è Jones:  

1. Recuperare l'entità di indice con il valore **PartitionKey** "Sales" e il valore **RowKey** "Jones".  
2. Analizzare l'elenco di ID dipendente nel campo EmployeeIDs.  
3. Se sono necessarie informazioni aggiuntive su ognuno dei dipendenti (ad esempio gli indirizzi e-mail), recuperare ognuna delle entità dipendente usando il valore **PartitionKey** "Sales" e i valori **RowKey** dall'elenco dei dipendenti ottenuti nel passaggio 2.  

<u>Opzione 3:</u> creare entità di indice in una tabella o una partizione separata  

Per la terza opzione, usare entità di indice che archiviano i dati seguenti:  

![Entità di indice dipendente in una partizione distinta](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


La proprietà **EmployeeIDs** contiene un elenco di ID dipendente per i dipendenti con il cognome archiviato in **RowKey**.  

Con la terza opzione non è possibile usare transazioni ETG per mantenere la coerenza, in quanto le entità di indice si trovano in una partizione separata rispetto alle entità dipendente. È necessario assicurarsi della coerenza finale tra le entità di indice e le entità dipendente.  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Questa soluzione richiede almeno due query per recuperare le entità corrispondenti: una sulle entità di indice per ottenere l'elenco di valori **RowKey** e altre query per il recupero di ogni entità dell'elenco.  
* Poiché una singola entità ha una dimensione massima di 1 MB, le opzioni 2 e 3 della soluzione presuppongono che l'elenco di ID dipendente per qualsiasi cognome non sia mai più grande di 1 MB. Se è probabile che l'elenco di ID dipendente abbia dimensioni superiori a 1 MB, usare l'opzione 1 e archiviare i dati di indice nell'archiviazione BLOB.  
* Se si usa l'opzione 2 (uso di transazioni EGT per gestire l'aggiunta e l'eliminazione dei dipendenti e la modifica del cognome di un dipendente), è necessario valutare se il volume delle transazioni raggiungerà i limiti di scalabilità in una determinata partizione. In tal caso, è opportuno considerare una soluzione con coerenza finale (opzione 1 o 3) che gestisca le richieste di aggiornamento mediante code e consenta di archiviare le entità di indice in una partizione separata rispetto alle entità dipendente.  
* L'opzione 2 di questa soluzione presuppone che si vogliano effettuare ricerche in base al cognome all'interno di un reparto, ad esempio recuperare un elenco di dipendenti del reparto vendite il cui cognome è Jones. Se si desidera poter cercare tutti i dipendenti il cui cognome è Jones nell'intera organizzazione, usare l'opzione 1 o l'opzione 3.
* È possibile implementare una soluzione basata su code che garantisca coerenza finale. Per altri dettagli, vedere [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern).  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando si desidera cercare un set di entità che condividono un valore di proprietà comune, ad esempio tutti i dipendenti con il cognome Jones.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Transazioni dei gruppi di entità](#entity-group-transactions)  
* [Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Modello di denormalizzazione
combina i dati correlati in una singola entità per consentire di recuperare tutti i dati necessari con un sola query di tipo punto.  

### <a name="context-and-problem"></a>Contesto e problema
In un database relazionale, in genere i dati vengono normalizzati per rimuovere i risultati duplicati nelle query che recuperano dati da più tabelle. Se si normalizzano i dati nelle tabelle di Azure, è necessario eseguire più round trip dal client al server per recuperare i dati correlati. Con la struttura della tabella mostrata di seguito, ad esempio, per recuperare i dettagli per un reparto sono necessari due round trip: uno per recuperare l'entità reparto che include l'ID del manager e una seconda richiesta per recuperare i dettagli sul manager in un'entità dipendente.  

![Entità reparto ed entità dipendente](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Soluzione
Anziché archiviare i dati in due entità separate, denormalizzare i dati e conservare una copia dei dettagli sul manager nell'entità reparto. Ad esempio:   

![Entità reparto](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Archiviando le entità reparto con queste proprietà, è possibile recuperare tutti i dettagli necessari su un reparto mediante una query di tipo punto.  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Archiviare alcuni dati due volte comporta un aumento dei costi. Il miglioramento delle prestazioni (risultante dal minor numero di richieste al servizio di archiviazione) in genere compensa l'incremento marginale dei costi di archiviazione, che per altro è parzialmente compensato dalla riduzione del numero di transazioni necessarie per recuperare i dettagli relativi a un reparto.  
* È necessario mantenere la coerenza tra le due entità in cui sono archiviate le informazioni sui manager. Il problema della coerenza può essere gestito usando transazioni ETG per aggiornare più entità in una singola transazione atomica: in questo caso, l'entità reparto e l'entità dipendente per il responsabile del reparto vengono archiviate nella stessa partizione.  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario cercare spesso informazioni correlate. Questo modello riduce il numero di query che il client deve eseguire per recuperare i dati necessari.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Transazioni dei gruppi di entità](#entity-group-transactions)  
* [Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Modello per chiave composta
Usa valori **RowKey** composti per consentire a un client di cercare dati correlati con una sola query di tipo punto.  

### <a name="context-and-problem"></a>Contesto e problema
In un database relazionale è piuttosto normale usare join nelle query per restituire dati correlati al client in una singola query. Ad esempio, si può usare l'ID dipendente per cercare un elenco di entità correlate che contengono i dati relativi alle prestazioni e alle valutazioni per tale dipendente.  

Si supponga di archiviare le entità dipendente nel servizio tabelle usando la struttura seguente:  

![Struttura di entità dipendente](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

È inoltre necessario archiviare i dati cronologici relativi alle valutazioni e alle prestazioni per ogni anno che il dipendente ha lavorato presso l'organizzazione, nonché poter accedere a queste informazioni in base all'anno. Una possibilità consiste nel creare un'altra tabella di archiviazione delle entità con la struttura seguente:  

![Struttura di entità dipendente alternativa](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Si noti che con questo approccio è possibile decidere di duplicare alcune informazioni (ad esempio nome e cognome) nella nuova entità, in modo da poter recuperare i dati con una singola richiesta. Non è tuttavia possibile mantenere la coerenza assoluta, in quanto non si può usare una transazione EGT per aggiornare le entità in modo atomico.  

### <a name="solution"></a>Soluzione
Archiviare un nuovo tipo di entità nella tabella originale usando entità con la struttura seguente:  

![Soluzione per la struttura di entità dipendente](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Si noti che ora il valore di **RowKey** è una chiave composta costituita dall'ID dipendente e dall'anno dei dati di valutazione, che consente di recuperare le prestazioni e le valutazioni del dipendente con una singola richiesta per una singola entità.  

L'esempio seguente illustra come recuperare tutti i dati di valutazione per uno specifico dipendente (ad esempio il dipendente 000123 del reparto vendite):  

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000123') and (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* È consigliabile usare un carattere separatore appropriato che semplifichi l'analisi del valore **RowKey**, ad esempio **000123_2012**.  
* Inoltre, si sta archiviando l'entità nella stessa partizione di altre entità che contengono dati correlati per lo stesso dipendente, dunque è possibile usare transazioni EGT per mantenere la coerenza assoluta.
* Per determinare se questo modello è appropriato, considerare la frequenza con cui si eseguiranno query sui dati.  Ad esempio, se si accederà raramente ai dati di valutazione e spesso ai dati principali sul dipendente, è consigliabile conservarli come entità separate.  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario archiviare una o più entità correlate su cui si eseguono query frequenti.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni dei gruppi di entità](#entity-group-transactions)  
* [Uso di tipi di entità eterogenei](#working-with-heterogeneous-entity-types)  
* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Modello della parte finale del log
recupera le *e* ntità aggiunte più di recente a una partizione in base a un valore **RowKey** che usa un ordinamento inverso di data e ora.  

### <a name="context-and-problem"></a>Contesto e problema
Un requisito comune è poter recuperare le entità create più di recente, ad esempio le ultime dieci note di rimborso spese inviate da un dipendente. Le query sulle tabelle supportano un'operazione di query **$top** per restituire le prime *n* entità di un set. Non esiste un'operazione di query equivalente per la restituzione delle ultime n entità di un set.  

### <a name="solution"></a>Soluzione
Archiviare le entità usando un valore **RowKey** che usa un ordinamento inverso di data e ora, in modo che la voce più recente sia sempre la prima della tabella.  

Ad esempio, per poter recuperare le ultime dieci note di rimborso spese inviate da un dipendente, è possibile usare un valore di tick inverso derivato dal valore di data/ora corrente. L'esempio di codice C# seguente illustra un modo per creare un valore "invertedTicks" appropriato per un valore **RowKey** che ordina dal più recente al meno recente:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Per tornare al valore di data e ora, usare il codice seguente:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

La query sulla tabella ha un aspetto simile al seguente:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* È necessario aggiungere zeri iniziali al valore di tick inverso per assicurarsi che il valore di stringa venga ordinato come previsto.  
* È necessario tenere presenti gli obiettivi di scalabilità a livello di partizione. Fare attenzione a non creare partizioni critiche.  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando si desidera accedere alle entità in ordine di data/ora inverso o quando è necessario accedere alle entità aggiunte più di recente.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Anti-modello prepend/append](#prepend-append-anti-pattern)  
* [Recupero di entità](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Modello di eliminazione volume elevato
Abilitare l'eliminazione di un volume elevato di entità mediante l'archiviazione di tutte le entità per l'eliminazione simultanea nella relativa tabella separata; per eliminare le entità, eliminare la tabella.  

### <a name="context-and-problem"></a>Contesto e problema
Molte applicazioni eliminano vecchi dati non più necessari a un'applicazione client o che l'applicazione ha archiviato in un altro supporto di archiviazione. In genere questi dati vengono identificati da una data; è presente un requisito che prevede l'eliminazione dei record di tutte le richieste di accesso risalenti a oltre 60 giorni prima.  

Una possibile progettazione consiste nell'usare la data e l'ora della richiesta di accesso in **RowKey**:  

![Data e ora del tentativo di accesso](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Questo approccio evita hotspot di partizione perché l'applicazione può inserire ed eliminare entità di accesso per ogni utente in una partizione separata, ma può rivelarsi dispendioso in termini di denaro e tempo se si dispone di un numero elevato di entità perché è necessario innanzitutto eseguire un'analisi di tabella per identificare tutte le entità da eliminare e successivamente eliminare ogni entità precedente. Si noti che è possibile ridurre il numero di round trip al server necessari per eliminare le entità precedenti raggruppando più richieste di eliminazione nelle transazioni EGT.  

### <a name="solution"></a>Soluzione
Usare una tabella separata per ogni giorno di tentativi di accesso. È possibile usare la progettazione di entità riportata sopra per evitare hotspot durante l'inserimento di entità; l'eliminazione di entità comporterà semplicemente l'eliminazione di una tabella al giorno (una singola operazione di archiviazione) invece della ricerca ed eliminazione di centinaia o migliaia di singole entità ogni giorno.  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* La progettazione supporta altre modalità di uso dei dati da parte dell'applicazione, come la ricerca di entità specifiche, il collegamento con altri dati o la generazione di informazioni aggregate?  
* La progettazione consente di evitare hotspot durante l'inserimento di nuove entità?  
* Se si vuole riutilizzare lo stesso nome di tabella dopo l'eliminazione, prevedere un ritardo. È consigliabile usare sempre nomi di tabella univoci.  
* Prevedere una limitazione delle richieste quando si usa per la prima volta una nuova tabella mentre il servizio tabelle apprende i modelli di accesso e le partizioni vengono distribuite in nodi. È necessario considerare la frequenza con cui è necessario creare nuove tabelle.  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando si dispone di un volume elevato di entità che è necessario eliminare contemporaneamente.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni dei gruppi di entità](#entity-group-transactions)
* [Modifica di entità](#modifying-entities)  

## <a name="data-series-pattern"></a>Modello di serie di dati
Archiviare serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  

### <a name="context-and-problem"></a>Contesto e problema
Spesso un'applicazione archivia una serie di dati richiesti di frequente per recuperarli tutti simultaneamente. L'applicazione potrebbe, ad esempio, registrare il numero di messaggi immediati che ogni dipendente invia ogni ora e quindi usare queste informazioni per tracciare il numero di messaggi inviati da ogni utente nelle 24 ore precedenti. Una progettazione potrebbe essere l'archiviazione di 24 entità per ogni dipendente:  

![Archiviare 24 entità per ogni dipendente](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Con questa progettazione è possibile individuare e aggiornare l'entità da aggiornare per ogni dipendente ogni volta che l'applicazione deve aggiornare il valore del numero di messaggi. Tuttavia, per recuperare le informazioni allo scopo di tracciare un grafico dell'attività per le 24 ore precedenti, è necessario recuperare 24 entità.  

### <a name="solution"></a>Soluzione
Usare la progettazione seguente con una proprietà separata per archiviare il numero di messaggi per ogni ora:  

![Entità statistiche messaggio](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Con questa progettazione è possibile usare un'operazione di unione per aggiornare il numero di messaggi per un dipendente per un'ora specifica. A questo punto, è possibile recuperare tutte le informazioni necessarie per tracciare il grafico usando una richiesta per una singola entità.  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Se la serie di dati completa non rientra in una singola entità (un'entità può contenere fino a 252 proprietà), usare un archivio dati alternativo, ad esempio un BLOB.  
* Se sono presenti più client che aggiornano un'entità contemporaneamente, sarà necessario usare il **ETag** per implementare la concorrenza ottimistica. Se si dispone di molti client, potrebbe verificarsi un conflitto elevato.  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario aggiornare e recuperare una serie di dati associata a una singola entità.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello di entità di grandi dimensioni](#large-entities-pattern)  
* [Unione o sostituzione](#merge-or-replace)  
* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) (se si archiviano le serie di dati in un BLOB)  

## <a name="wide-entities-pattern"></a>Modello di entità di grandi dimensioni
Usare più entità fisiche per archiviare entità logiche con più di 252 proprietà.  

### <a name="context-and-problem"></a>Contesto e problema
Una singola entità può avere più di 252 proprietà (escludendo le proprietà di sistema obbligatorie) e non è possibile memorizzare più di 1 MB di dati in totale. In un database relazionale è in genere possibile aggirare gli eventuali limiti sulle dimensioni di una riga aggiungendo una nuova tabella e applicando una relazione 1 a 1 tra di esse.  

### <a name="solution"></a>Soluzione
Usando il servizio tabelle, è possibile archiviare più entità per rappresentare un singolo oggetto aziendale di grandi dimensioni con più di 252 proprietà. Ad esempio, se si vuole archiviare un conteggio del numero di messaggi immediati inviati da ogni dipendente negli ultimi 365 giorni, è possibile usare la progettazione seguente che si avvale di due entità con schemi diversi:  

![Più entità](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Per apportare una modifica che richiede l'aggiornamento di entrambe le entità per mantenerle sincronizzate tra loro, è possibile usare una transazione EGT. Diversamente, è possibile usare una singola operazione di unione per aggiornare il numero di messaggi per un giorno specifico. Per recuperare tutti i dati per un singolo dipendente, è necessario recuperare entrambe le entità, operazione che è possibile eseguire con due richieste efficienti che usano un valore **PartitionKey** e un valore **RowKey**.  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Il recupero di un'entità logica completa richiede almeno due transazioni di archiviazione, una per recuperare ogni entità fisica.  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario archiviare entità le cui dimensioni o il cui numero di proprietà superano i limiti per una singola entità nel servizio tabelle.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Transazioni dei gruppi di entità](#entity-group-transactions)
* [Unione o sostituzione](#merge-or-replace)

## <a name="large-entities-pattern"></a>Modello di entità di grandi dimensioni
Usare l'archiviazione BLOB per archiviare valori di proprietà di grandi dimensioni.  

### <a name="context-and-problem"></a>Contesto e problema
Una singola entità non può memorizzare più di 1 MB di dati in totale. Se una o più proprietà archiviano valori che causano il superamento delle dimensioni totali dell'entità, non è possibile archiviare l'intera entità nel servizio tabelle.  

### <a name="solution"></a>Soluzione
Se l'entità supera le dimensioni di 1 MB perché una o più proprietà contengono una grande quantità di dati, è possibile archiviare i dati nel servizio BLOB e quindi archiviare l'indirizzo del BLOB in una proprietà nell'entità. Ad esempio, è possibile archiviare la foto di un dipendente nell'archiviazione BLOB e archiviare un collegamento a foto nella proprietà **Photo** dell'entità del dipendente:  

![Proprietà Photo](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* Per mantenere la coerenza finale tra l'entità nel servizio tabelle e i dati nel servizio BLOB, usare il [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern) per mantenere le identità.
* Il recupero di un'entità completa richiede almeno due transazioni di archiviazione: una per recuperare l'entità e un'altra per recuperare i dati BLOB.  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Usare questo modello quando è necessario archiviare entità le cui dimensioni superano i limiti per una singola entità nel servizio tabelle.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per transazioni con coerenza finale](#eventually-consistent-transactions-pattern)  
* [Modello di entità di grandi dimensioni](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Anti-modello prepend/append
Quando si dispone di un volume elevato di inserimenti, aumentare la scalabilità suddividendoli tra più partizioni.  

### <a name="context-and-problem"></a>Contesto e problema
L'anteposizione o l'aggiunta di entità alle entità archiviate determina in genere l'aggiunta da parte dell'applicazione di nuove entità alla prima o ultima partizione di una sequenza di partizioni. In questo caso, tutti gli inserimenti in un determinato momento vengono eseguiti nella stessa partizione, creando un hotspot che impedisce al servizio tabelle di bilanciare il carico degli inserimenti tra più nodi e causando il possibile raggiungimento degli obiettivi di scalabilità per partizione da parte dell'applicazione. Se ad esempio si dispone di un'applicazione che registra l'accesso alla rete e alle risorse da parte dei dipendenti, la struttura dell'entità mostrata sotto potrebbe determinare la trasformazione della partizione dell'ora corrente in un hotspot se il volume delle transazioni raggiunge l'obiettivo di scalabilità per una singola partizione:  

![Struttura di entità](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Soluzione
La struttura di un'entità alternativa seguente evita gli hotspot in qualsiasi partizione specifica quando l'applicazione effettua la registrazione di eventi:  

![Struttura di entità alternativa](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Si noti come in questo esempio entrambi i valori **PartitionKey** e **RowKey** siano chiavi composte. Il valore **PartitionKey** usa sia l'ID reparto che l'ID dipendente per distribuire la registrazione in più partizioni.  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come implementare questo modello, considerare quanto segue:  

* La struttura chiave alternativa che evita la creazione di partizioni critiche negli inserimenti supporta in modo efficiente le query effettuate dall'applicazione client?  
* Il volume delle transazioni previste è indicativo della probabilità che si raggiungano gli obiettivi di scalabilità per una singola partizione e si sia limitati dal servizio di archiviazione?  

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello
Evitare l'anti-modello prepend/append quando il volume delle transazioni determinerà probabilmente una limitazione da parte del servizio di archiviazione quando si accede a una partizione critica.  

### <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
Per l'implementazione di questo modello possono risultare utili i modelli e le informazioni aggiuntive seguenti:  

* [Modello per chiave composta](#compound-key-pattern)  
* [Modello della parte finale del log](#log-tail-pattern)  
* [Modifica di entità](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Anti-modello dei dati di log
In genere è necessario usare il servizio BLOB invece del servizio tabelle per archiviare i dati di log.  

### <a name="context-and-problem"></a>Contesto e problema
Un caso di utilizzo comune per i dati di log è il recupero di una selezione di voci di log per un intervallo specifico di data/ora. Ad esempio, per trovare tutti i messaggi di errore e critici registrati dall'applicazione tra le ore 15.04 e le ore 15.06 in una data specifica senza usare la data e l'ora del messaggio del log per determinare la partizione in cui sono state salvate le entità, verrà creata una partizione critica perché in qualsiasi momento tutte le entità del log condividono lo stesso valore **PartitionKey**. Vedere la sezione [Anti-modello prepend/append](#prepend-append-anti-pattern). Ad esempio, lo schema di entità seguente per un messaggio di log determina una partizione critica perché l'applicazione scrive tutti i messaggi di log nella partizione per la data e l'ora correnti:  

![Entità messaggio di log](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

In questo esempio, il valore **RowKey** include la data e l'ora del messaggio di log per garantire che i messaggi di log vengano archiviati in ordine di data/ora e include un ID del messaggio nel caso in cui più messaggi di log condividano la stessa data e la stessa ora.  

Un altro approccio prevede l'uso di un valore **PartitionKey** per assicurarsi che l'applicazione scriva i messaggi in un intervallo di partizioni. Ad esempio, se l'origine del messaggio di log consente di distribuire i messaggi in più partizioni, è possibile usare lo schema di entità seguente:  

![Entità messaggio di log alternativa](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Tuttavia, il problema con questo schema risiede nel fatto che per recuperare tutti i messaggi di log per un intervallo di tempo specifico è necessario cercare ogni partizione nella tabella.

### <a name="solution"></a>Soluzione
Nella sezione precedente è stato preso in esame il problema associato ai tentativi di usare il servizio tabelle per archiviare le voci di log e sono state proposte due progettazioni, entrambe insoddisfacenti. Una soluzione ha determinato una partizione critica che comporta il rischio di prestazioni insufficienti della scrittura dei messaggi di log; l'altra soluzione ha determinato prestazioni insufficienti delle query a causa del requisito di analizzare ogni partizione della tabella per recuperare i messaggi di log per un intervallo di tempo specifico. L'archiviazione BLOB offre una soluzione migliore per questo tipo di scenario ed è in questo modo che Analisi archiviazione di Azure archivia i dati di log raccolti.  

Questa sezione illustra come Analisi archiviazione archivia i dati di log nell'archiviazione BLOB per esemplificare questo approccio all'archiviazione dei dati per la quale vengono in genere eseguite query per intervallo.  

Analisi archiviazione archivia i messaggi di log in un formato delimitato in più BLOB. Il formato delimitato semplifica l'analisi dei dati nel messaggio di log da parte di un'applicazione client.  

Analisi archiviazione usa una convenzione di denominazione per i BLOB che consente di localizzare uno BLOB o più BLOB che contengono i messaggi di log per i quali si sta effettuando la ricerca. Ad esempio, un BLOB denominato "queue/2014/07/31/1800/000001.log" contiene messaggi di log correlati al servizio di accodamento per l'ora che inizia alle 18.00 del 31 luglio 2014. "000001" indica che si tratta del primo file di log per il periodo. Analisi archiviazione registra inoltre i timestamp del primo e dell'ultimo messaggio di log archiviati nel file come parte dei metadati del BLOB. L'API per l'archiviazione BLOB consente di individuare i BLOB in un contenitore in base a un prefisso del nome: per individuare tutti i BLOB contenenti i dati di log della coda per l'ora che inizia alle 18.00, è possibile usare il prefisso "queue/2014/07/31/1800".  

Analisi archiviazione esegue il buffer dei messaggi di log e quindi aggiorna periodicamente il BLOB appropriato o ne crea uno nuovo con il batch di voci di log più recente. Ciò riduce il numero di scritture che deve eseguire nel servizio BLOB.  

Se si implementa una soluzione simile nella propria applicazione, è necessario considerare come gestire il compromesso tra affidabilità (scrittura di ogni voce di log nell'archiviazione BLOB quando questa si verifica) e il costo e LA scalabilità (buffering degli aggiornamenti dell'applicazione e relativa scrittura nell'archiviazione BLOB in blocchi).  

### <a name="issues-and-considerations"></a>Considerazioni e problemi
Prima di decidere come archiviare i dati di log, considerare quanto segue:  

* Se si crea una progettazione di tabella che consente di evitare potenziali partizioni critiche, è possibile che non si possa accedere ai dati di log in modo efficiente.  
* Per elaborare i dati di log, spesso un client deve caricare molti record.  
* Nonostante i dati di log siano spesso strutturati, l'archiviazione BLOB può essere una soluzione migliore.  

## <a name="implementation-considerations"></a>Considerazioni sull'implementazione
Questa sezione illustra alcune considerazioni da tenere presente quando si implementano i modelli descritti nelle sezioni precedenti. Nella maggior parte di questa sezione vengono usati esempi scritti in C# che usano la libreria client di archiviazione (versione 4.3.0 al momento della stesura di questo documento).  

## <a name="retrieving-entities"></a>Recupero di entità
Come descritto nella sezione [Progettazione per le query](#design-for-querying), la query più efficiente è una query di tipo punto. Tuttavia, in alcuni scenari potrebbe essere necessario recuperare più entità. Questa sezione descrive alcuni approcci comuni al recupero di entità mediante la libreria client di archiviazione.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Esecuzione di una query di tipo punto mediante la libreria client di archiviazione
Il modo più semplice per eseguire una query di tipo punto consiste nell'usare l'operazione di tabella **Retrieve** come illustrato nel frammento di codice C# seguente che recupera un'entità con un **PartitionKey** di valore "Sales" e un **RowKey** di valore "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Si noti come in questo esempio l'entità recuperata prevista sia di tipo **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Recupero di più entità usando LINQ
È possibile recuperare più entità usando LINQ con la libreria client di archiviazione e specificando una query con una clausola **where** . Per evitare un'analisi di tabella, è consigliabile includere sempre il valore **PartitionKey** nella clausola where e, se possibile, il valore **RowKey** per evitare analisi di tabelle e partizioni. Il servizio tabelle supporta un set limitato di operatori di confronto (maggiore di, maggiore di o uguale a, minore di, minore di o uguale a e non uguale a) da usare per determinare la clausola where. Il frammento di codice C# seguente consente di trovare tutti i dipendenti il cui cognome inizia con la lettera "B" (presupponendo che il valore **RowKey** archivi il cognome) del reparto vendite (supponendo che il valore **PartitionKey** archivi il nome del reparto):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Si noti come la query specifichi sia un valore **RowKey** che un valore **PartitionKey** per garantire prestazioni migliori.  

L'esempio di codice seguente illustra la funzionalità equivalente usando l'API fluent (per altre informazioni sulle API fluent in generale, vedere l'articolo relativo alle [procedure consigliate per la progettazione di un’API fluent](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> L'esempio annida più metodi **CombineFilters** per includere le tre condizioni di filtro.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Recupero di un numero elevato di entità da una query
Una query ottimale restituisce una singola entità in base a un valore **PartitionKey** e a un valore **RowKey**. In alcuni scenari, tuttavia, potrebbe essere presente il requisito di restituire molte entità dalla stessa partizione o anche da più partizioni.  

È sempre necessario eseguire test completi delle prestazioni dell'applicazione in tali scenari.  

Una query sul servizio tabelle può restituire un massimo di 1.000 entità contemporaneamente e può essere eseguita per un massimo di cinque secondi. Se il set di risultati contiene più di 1.000 entità, nel caso in cui la query non venga completata entro cinque secondi, o se la query supera il limite della partizione, il servizio tabelle restituisce un token di continuazione per consentire all'applicazione client di richiedere il successivo set di entità. Per altre informazioni sul funzionamento dei token di continuazione, vedere [Timeout e paginazione delle query](http://msdn.microsoft.com/library/azure/dd135718.aspx).  

La libreria client di archiviazione può gestire automaticamente i token di continuazione per l'utente mentre restituisce entità dal servizio tabelle. L'esempio di codice C# seguente che usa la libreria client di archiviazione gestisce automaticamente i token di continuazione se il servizio tabelle li restituisce in una risposta:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Il codice C# seguente gestisce i token di continuazione in modo esplicito:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Usando i token di continuazione in modo esplicito è possibile controllare quando l'applicazione recupera il successivo segmento di dati. Ad esempio, se l'applicazione client consente agli utenti di spostarsi tra le entità archiviate in una tabella, un utente può decidere di non spostarsi tra tutte le entità recuperate dalla query in modo che l'applicazione usi solo un token di continuazione per recuperare il segmento successivo quando l'utente ha terminato il paging di tutte le entità nel segmento corrente. Questo approccio offre diversi vantaggi:  

* Consente di limitare la quantità di dati da recuperare dal servizio tabelle e da spostare tramite la rete.  
* Consente di eseguire operazioni di I/O asincrone in .NET.  
* Consente di serializzare il token di continuazione in un archivio permanente in modo da poter proseguire in caso di arresto anomalo dell'applicazione.  

> [!NOTE]
> Un token di continuazione in genere restituisce un segmento contenente al massimo 1.000 entità. Ciò avviene anche se si limita il numero di voci restituite da una query usando **Take** per restituire le prime n entità che corrispondono ai criteri di ricerca: il servizio tabelle può restituire un segmento contenente meno di n entità con un token di continuazione per consentire il recupero delle entità rimanenti.  
> 
> 

Il codice C# seguente illustra come modificare il numero di entità restituite all'interno di un segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Proiezione lato server
Una singola entità può avere fino a 255 proprietà e dimensioni fino a 1 MB. Quando si eseguono query sulla tabella e si recuperano entità, potrebbero non essere necessarie tutte le proprietà ed è possibile evitare di trasferire dati inutilmente (in modo da ridurre la latenza e i costi). È possibile usare la proiezione lato server per trasferire solo le proprietà necessarie. L'esempio seguente recupera solo la proprietà **Email** (insieme a **PartitionKey**, **RowKey**, **Timestamp** ed **ETag**) dalle entità selezionate dalla query.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Si noti come il valore **RowKey** è disponibile anche se non è stato incluso nell'elenco delle proprietà da recuperare.  

## <a name="modifying-entities"></a>Modifica di entità
La libreria client di archiviazione consente di modificare le entità archiviate nel servizio tabelle mediante l'inserimento, l'eliminazione e l'aggiornamento di entità. È possibile usare le transazioni EGT per eseguire in batch più operazioni di inserimento, aggiornamento ed eliminazione insieme allo scopo di ridurre il numero di round trip necessari e migliorare le prestazioni della soluzione.  

Si noti che le eccezioni generate quando la libreria client di archiviazione esegue una transazione EGT in genere includono l'indice dell'entità che ha causato l'esito negativo del batch. Ciò è utile quando si esegue il debug di codice che usa le transazioni EGT.  

È inoltre opportuno considerare l'influenza della progettazione sul modo in cui l'applicazione gestisce le operazioni di concorrenza e aggiornamento.  

### <a name="managing-concurrency"></a>Gestione della concorrenza
Per impostazione predefinita, il servizio tabelle implementa controlli di concorrenza ottimistica a livello di singole entità per le operazioni **Insert**, **Merge** e **Delete**, sebbene sia possibile per un client forzare il servizio tabelle in modo da ignorare questi controlli. Per altre informazioni sulla gestione della concorrenza nel servizio tabelle, vedere [Gestione della concorrenza in Archiviazione di Microsoft Azure](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Unione o sostituzione
Il metodo **Replace** della classe **TableOperation** sostituisce sempre l'entità completa nel servizio tabelle. Se non si include una proprietà nella richiesta quando tale proprietà è presente nell'entità archiviata, la richiesta rimuove la proprietà dall'entità archiviata. A meno che non si voglia rimuovere una proprietà in modo esplicito da un'entità archiviata, è necessario includere ogni proprietà nella richiesta.  

È possibile usare il metodo **Merge** della classe **TableOperation** per ridurre la quantità di dati inviati al servizio tabelle quando si vuole aggiornare un'entità. Il metodo **Merge** sostituisce le eventuali proprietà nell'entità archiviata con i valori di proprietà dell'entità inclusa nella richiesta, ma lascia invariate le proprietà nell'entità archiviata che non sono incluse nella richiesta. Ciò è utile se si dispone di entità di grandi dimensioni e si desidera solo aggiornare un numero limitato di proprietà in una richiesta.  

> [!NOTE]
> I metodi **Replace** e **Merge** hanno esito negativo se l'entità non esiste. In alternativa, se l'entità non esiste, è possibile usare i metodi **InsertOrReplace** e **InsertOrMerge** per creare una nuova entità.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Uso di tipi di entità eterogenei
Il servizio tabelle è un archivio di tabelle *senza schema*. Ciò significa che una singola tabella può archiviare entità di più tipi, offrendo una grande flessibilità di progettazione. L'esempio seguente illustra una tabella che archivia entità dipendente ed entità reparto:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Si noti che ogni entità deve disporre comunque dei valori **PartitionKey**, **RowKey** e **Timestamp**, ma può avere qualsiasi set di proprietà. Inoltre, non esiste alcuna indicazione relativa al tipo di un'entità, a meno che non si scelga di memorizzare le informazioni in una posizione. Esistono due opzioni per identificare il tipo di entità:  

* Anteporre il tipo di entità per il valore **RowKey** (o eventualmente il valore **PartitionKey**). Ad esempio, **EMPLOYEE_000123** o **DEPARTMENT_SALES** come valori **RowKey**.  
* Usare una proprietà separata per registrare il tipo di entità come illustrato nella tabella seguente.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>department</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Age</th>
<th>Email</th>
</tr>
<tr>
<td>Employee</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

La prima opzione che precede l'entità per il valore **RowKey**è utile se sussiste la possibilità che due entità di tipi diversi abbiano lo stesso valore di chiave. Inoltre, raggruppa entità dello stesso tipo insieme nella partizione.  

Le tecniche descritte in questa sezione sono particolarmente rilevanti per la discussione [Relazioni di ereditarietà](table-storage-design-modeling.md#inheritance-relationships) trattata all'inizio di questa Guida nell'articolo [Modellazione di relazioni](table-storage-design-modeling.md).  

> [!NOTE]
> È necessario considerare l'inclusione di un numero di versione nel valore del tipo di entità per consentire alle applicazioni client di sviluppare oggetti POCO e usare versioni diverse.  
> 
> 

La restante parte di questa sezione descrive alcune delle funzionalità della libreria client di archiviazione che semplificano l'uso di più tipi di entità nella stessa tabella.  

### <a name="retrieving-heterogeneous-entity-types"></a>Recupero di tipi di entità eterogenei
Se si usa la libreria client di archiviazione, sono disponibili tre opzioni per l'uso di più tipi di entità.  

Se si conosce il tipo dell'entità archiviata con valori **RowKey** e **PartitionKey** specifici, è possibile specificare il tipo di entità quando si recupera l'entità, come illustrato nei due esempi precedenti in cui vengono recuperate entità di tipo **EmployeeEntity**: [Esecuzione di una query di tipo punto mediante la libreria client di archiviazione](#executing-a-point-query-using-the-storage-client-library) e [Recupero di più entità usando LINQ](#retrieving-multiple-entities-using-linq).  

La seconda opzione prevede l'uso del tipo **DynamicTableEntity** (un contenitore di proprietà) anziché un tipo di entità POCO concreto (questa opzione può anche migliorare le prestazioni perché non richiede la serializzazione e la deserializzazione dell'entità nei tipi .NET). Il codice C# seguente può recuperare più entità di tipo diverso dalla tabella, ma restituisce tutte le entità come istanze **DynamicTableEntity** . Usa quindi la proprietà **EntityType** per determinare il tipo di ogni entità:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Si noti che per recuperare le altre proprietà è necessario usare il metodo **TryGetValue** sulla proprietà **Properties** della classe **DynamicTableEntity**.  

Una terza opzione prevede l'uso combinato del tipo **DynamicTableEntity** e di un'istanza **EntityResolver**. Ciò consente di risolvere a più tipi POCO nella stessa query. In questo esempio il delegato **EntityResolver** usa la proprietà **EntityType** per distinguere i due tipi di entità restituite dalla query. Il metodo **Resolve** usa il delegato **resolver** per risolvere le istanze **DynamicTableEntity** alle istanze **TableEntity**.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Modifica dei tipi di entità eterogenei
Per eliminare un'entità non è necessario conoscerne il tipo, che è comunque sempre noto quando l'entità viene inserita. Tuttavia, è possibile usare il tipo **DynamicTableEntity** per aggiornare un'entità senza conoscerne il tipo e senza usare una classe di entità POCO. L'esempio di codice seguente consente di recuperare una singola entità e controlla che la proprietà **EmployeeCount** esista prima di aggiornarla.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

## <a name="controlling-access-with-shared-access-signatures"></a>Controllo dell'accesso con le firme di accesso condiviso
È possibile usare i token di firma di accesso condiviso (SAS) per consentire alle applicazioni client di modificare le entità di tabella, ed eseguire query sulle stesse, senza la necessità di includere la chiave dell'account di archiviazione nel codice. In genere, l'uso di SAS nell'applicazione comporta tre vantaggi principali:  

* Non è necessario distribuire la chiave dell'account di archiviazione in una piattaforma non sicura (ad esempio un dispositivo mobile) per consentire a tale dispositivo di accedere e modificare le entità nel servizio tabelle.  
* È possibile scaricare una parte del lavoro eseguito dai ruoli Web e di lavoro nella gestione delle entità per i dispositivi client, ad esempio computer e dispositivi mobili degli utenti finali.  
* È possibile assegnare un set vincolato e limitato nel tempo di autorizzazioni a un client (ad esempio, l'accesso di sola lettura a risorse specifiche).  

Per altre informazioni sull'uso di token di firma di accesso condiviso con il servizio tabelle, vedere [Uso delle firme di accesso condiviso](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Tuttavia, è comunque necessario generare i token delle firme di accesso condiviso che consentono a un'applicazione client di accedere alle entità nel servizio tabelle: questa operazione deve essere eseguita in un ambiente che dispone di un accesso sicuro alle chiavi dell'account di archiviazione. In genere, è possibile usare un ruolo Web o di lavoro per generare i token delle firme di accesso condiviso e distribuirli alle applicazioni client che richiedono l'accesso alle entità. Poiché la generazione e la distribuzione dei token delle firme di accesso condiviso ai client comportano comunque un sovraccarico, è consigliabile valutare il modo migliore di ridurre tale sovraccarico, soprattutto in scenari con volumi elevati.  

È possibile generare un token delle firme di accesso condiviso che concede l'accesso a un sottoinsieme delle entità in una tabella. Per impostazione predefinita, viene creato un token della firma di accesso condiviso per un'intera tabella, ma è anche possibile specificare che il token della firma di accesso condiviso conceda l'accesso a un intervallo di valori **PartitionKey** o a un intervallo di valori **PartitionKey** e **RowKey**. Si potrebbe scegliere di generare token SAS per i singoli utenti del sistema in modo che il token SAS di ogni utente consenta di accedere solo alle proprie entità nel servizio tabelle.  

## <a name="asynchronous-and-parallel-operations"></a>Operazioni asincrone e parallele
A condizione che le richieste vengano distribuite in più partizioni, è possibile migliorare la velocità effettiva e la velocità di risposta del client usando le query parallele o asincrone.
Ad esempio, si potrebbero avere due o più istanze del ruolo di lavoro che accedono alle tabelle in parallelo. Si potrebbero avere singoli ruoli di lavoro responsabili di specifici set di partizioni o semplicemente avere più istanze del ruolo di lavoro, ciascuna in grado di accedere a tutte le partizioni in una tabella.  

All'interno di un'istanza del client, è possibile migliorare la velocità effettiva effettuando operazioni di archiviazione in modo asincrono. La libreria client di archiviazione semplifica la scrittura di query e modifiche asincrone. Ad esempio, è possibile iniziare con il metodo sincrono che recupera tutte le entità in una partizione come mostrato nel codice C# seguente:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

È possibile modificare facilmente questo codice affinché la query venga eseguita in modo asincrono come segue:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

In questo esempio asincrono è possibile visualizzare le modifiche seguenti dalla versione sincrona:  

* La firma del metodo include ora il modificatore **async** e restituisce un'istanza **Task**.  
* Invece di chiamare il metodo **ExecuteSegmented** per recuperare i risultati, il metodo ora chiama il metodo **ExecuteSegmentedAsync** e usa il modificatore **await** per recuperare i risultati in modo asincrono.  

L'applicazione client può chiamare questo metodo più volte (con valori diversi per il parametro **department** ) e ogni query verrà eseguita su un thread separato.  

Si noti che non è presente alcuna versione asincrona del metodo **Execute** nella classe **TableQuery** perché l'interfaccia **IEnumerable** non supporta l'enumerazione asincrona.  

È inoltre possibile inserire, aggiornare ed eliminare entità in modo asincrono. Nell'esempio C# seguente viene illustrato un metodo semplice e sincrono per inserire o sostituire un'entità dipendente:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

È possibile modificare facilmente questo codice affinché l'aggiornamento venga eseguito in modo asincrono come segue:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

In questo esempio asincrono è possibile visualizzare le modifiche seguenti dalla versione sincrona:  

* La firma del metodo include ora il modificatore **async** e restituisce un'istanza **Task**.  
* Invece di chiamare il metodo **Execute** per aggiornare l'entità, il metodo ora chiama il metodo **ExecuteAsync** e usa il modificatore **await** per recuperare i risultati in modo asincrono.  

L'applicazione client può chiamare più metodi asincroni come questo e ogni chiamata al metodo verrà eseguita su un thread separato.  

## <a name="next-steps"></a>Passaggi successivi

- [Modellazione di relazioni](table-storage-design-modeling.md)
- [Progettazione per le query](table-storage-design-for-query.md)
- [Crittografia dei dati di tabella](table-storage-design-encrypt-data.md)
- [Progettazione per la modifica dei dati](table-storage-design-for-modification.md)