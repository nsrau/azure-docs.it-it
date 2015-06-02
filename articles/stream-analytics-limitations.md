<properties 
	pageTitle="Limitazioni nella versione di anteprima di Analisi dei flussi | Azure" 
	description="Informazioni sulle limitazioni nella versione di anteprima pubblica applicate ai processi di Analisi dei flussi di Azure" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/05/2015"
	ms.author="jgao"/>

# Limitazioni e problemi noti della versione di anteprima di Analisi dei flussi di Azure

In questo documento vengono descritti le limitazioni e i problemi noti di [Analisi dei flussi di Azure][stream.analytics.documentation] durante la versione di anteprima. Nella maggior parte dei casi i limiti vengono applicati con l'obiettivo di ottenere i commenti preliminari dell'utente oppure in base ai vincoli imposti dalla capacità disponibile. 
<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## Limitazioni

### Disponibilità internazionale
Il provisioning di processi di Analisi dei flussi nella versione di anteprima può essere eseguito solo nelle aree geografiche degli Stati Uniti centrali e dell'Europa occidentale.

### Scalabilità 
**Quota di unità di streaming**

A causa dei limiti di capacità correnti, viene applicata una quota pari a 12 unità di streaming per ogni area in una sottoscrizione. Per altre informazioni, vedere [Ridimensionare i processi di Analisi dei flussi di Azure][stream.analytics.scale.jobs]. Se l’azienda ha necessità di ridurre questi limiti, potrà contattare il [Supporto tecnico Microsoft][microsoft.support] che si impegnerà a soddisfare la richiesta entro i vincoli dell’offerta pubblica.

**Utilizzo di unità di streaming**

In questa versione di anteprima, il numero di unità di streaming fornite per un processo potrebbe talvolta superare la quantità selezionata o fatturata. Inoltre, non verranno applicati limiti alle unità di streaming stesse, vale a dire che le prestazioni osservate potrebbero risultare superiori a quelle garantite, in base alla disponibilità delle risorse di calcolo.

**Chiave di partizione**

Durante l'applicazione della scalabilità orizzontale della query mediante **Partition By**, il campo per il partizionamento deve essere **PartitionId**. Il partizionamento in altri campi definiti dall'utente verrà reso disponibile in una versione futura. Per informazioni dettagliate sul ridimensionamento del proprio processo, vedere [Ridimensionare i processi di Analisi dei flussi di Azure][stream.analytics.scale.jobs].

### Input



**Codifica dei caratteri**

Per le origini di input CSV e JSON, UTF-8 è l'unico formato di codifica supportato.


### Complessità delle query
Il numero massimo di funzioni di aggregazione supportate in una definizione di query del processo di Analisi dei flussi è sette.

### Numero di query
Il numero massimo di query supportate per una determinata origine di input è 5.


### Gestione del ciclo di vita

**Aggiornamento dei processi**

Analisi dei flussi non supporta al momento modifiche di definizione o configurazione per processi in corso di esecuzione. Per modificare input, output, query, dimensioni o configurazione di un processo in esecuzione, è necessario innanzitutto arrestare il processo stesso.

**Arresto e riavvio dei processi**

In seguito all'arresto di un processo, non vengono conservate informazioni relative al suo stato di avanzamento e non è pertanto possibile configurare un processo riavviato in modo da farlo riprendere dal punto in cui era stato interrotto. Si tratta di un limite che verrà risolto in una versione futura. Per le procedure consigliate per l'avvio e l'arresto dei processi, vedere [Guida per gli sviluppatori di Analisi dei flussi di Azure][stream.analytics.developer.guide].

### Monitoraggio
Alcune metriche relative all'utilizzo e alle prestazioni del processo, quali la latenza, non sono disponibili nella versione di anteprima. La versione di anteprima, inoltre, quantifica la velocità effettiva del processo solo in base al conteggio degli eventi e non in base alle dimensioni.

## Note sulla versione e problemi noti

Nella sezione seguente è riportato un elenco dei problemi noti relativi ad Analisi dei flussi di Azure. Tale sezione verrà modificata man mano che si rimuovono elementi dall'elenco, si riscontrano nuovi problemi o se ne rilevano altri esistenti.


### Necessarie autorizzazioni con privilegi elevati per hub eventi
Analisi dei flussi richiede, al momento, un criterio di accesso condiviso con autorizzazioni di gestione per origini di input e destinazioni di output provenienti da hub eventi.

### Ritardo nella configurazione dell'account di archiviazione di Azure
Quando si crea per la prima volta un processo di Analisi dei flussi in un’area, verrà richiesto di creare un nuovo account di archiviazione, o di specificarne uno esistente, per il monitoraggio dei processi di Analisi dei flussi in tale area. A causa della latenza nella configurazione del monitoraggio, la creazione di un altro processo di Analisi dei flussi nella stessa area entro 30 minuti determinerà la visualizzazione della richiesta di specificare un secondo account di archiviazione anziché la visualizzazione di quello appena configurato nel menu a discesa relativo all'**account di archiviazione per il monitoraggio**. Per evitare la creazione di account di archiviazione non necessari, dopo aver creato per la prima volta un processo in una determinata area, attendere 30 minuti prima di eseguire il provisioning di altri processi nella stessa area.

### I processi devono utilizzare ciascuno il proprio gruppo di consumer di hub di eventi
Ogni input del processo di analisi del flusso deve essere configurato in modo tale da disporre del proprio gruppo di utenti dell'Hub eventi. Quando un processo contiene self-join o più output, alcuni input potrebbero essere letti da più di un lettore. Ciò determina il superamento del limite di 5 lettori per gruppo di utenti in un singolo gruppo di utenti. In questo caso, la query dovrà essere suddivisa in più query e i risultati intermedi devono essere instradati tramite gli Hub eventi aggiuntivi. Si noti che è presente anche un limite di 20 gruppi di utenti per Hub eventi. Per informazioni dettagliate, vedere [Guida alla programmazione di Hub eventi][azure.event.hubs.developer.guide].

### Aggiungere input/output - Hub eventi 
La terza pagina delle finestre di dialogo **Aggiungi input** e **Aggiungi output** per le origini degli hub eventi presenta un elenco a discesa denominato **Hub eventi** che contiene un elenco di spazi dei nomi del bus di servizio nella sottoscrizione corrente nonché un’opzione per connettersi a un hub eventi in una sottoscrizione diversa. Per connettersi a un hub eventi nella stessa sottoscrizione, selezionare il relativo spazio dei nomi del bus di servizio. Per connettersi a un hub eventi esterno alla sottoscrizione, scegliere **Usa hub eventi di un’altra sottoscrizione**.


### Non è possibile fare riferimento allo stesso passaggio della query più di una volta
In questa versione di anteprima non è possibile fare riferimento più volte a un passaggio di sottoquery specifico definito usando la parola chiave **WITH**. Uno scenario comune su cui ciò potrebbe influire negativamente è un self-join che usa degli alias dello stesso passaggio. Per risolvere questo problema, creare due passaggi distinti con la stessa sottoquery e nomi diversi.

### Le conversioni di tipi non supportati generano valori null
Qualsiasi valore di evento con conversioni di tipi non supportate nella sezione relativa ai tipi di dati del documento delle [informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure][stream.analytics.query.language.reference] genererà un valore null. In questa versione di anteprima non è disponibile alcun sistema di registrazione degli errori per queste eccezioni di conversione.

### Problema relativo a memoria insufficiente
I processi di Analisi dei flussi con un'elevata tolleranza per eventi non ordinati e/o query complesse, con relativa conservazione di grandi quantità di stati, possono causare l'esaurimento della memoria, il che a sua volta causerebbe il riavvio del processo. Le operazioni di avvio e di arresto saranno visibili nei log delle operazioni del processo. Per evitare questo comportamento, ridimensionare la query orizzontalmente occupando più partizioni. In una versione futura questa limitazione verrà risolta riducendo le prestazioni dei processi interessati anziché causandone il riavvio.

### Nessun output restituito nei risultati di una query partizionata in presenza di una partizione vuota dell'hub eventi
Quando si esegue una query partizionata con una sottoquery non partizionata come secondo passaggio, se una delle partizioni dell'hub eventi nell'input è completamente vuota, la query non genererà risultati. Verrà riportato un errore nei log delle operazioni relative al processo. Per evitare questo problema, assicurarsi che tutte le partizioni dell'hub eventi dispongano di eventi in ingresso in ogni momento.

### Limitazione del volume di eventi del database SQL
Quando si usa un database SQL come destinazione di output, un volume troppo elevato di dati di output può causare il timeout del processo di Analisi dei flussi. Per risolvere il problema, ridurre il volume di output tramite operatori di join o aggregati oppure scegliere Archiviazione BLOB di Azure o Hub eventi come destinazione di output.

### Input di BLOB di grandi dimensioni non supportato
L'uso di file di grandi dimensioni dall'archiviazione BLOB può causare l'arresto anomalo dei processi di Analisi dei flussi. Per evitare questo problema, mantenere ogni BLOB entro i 10 MB di dimensione.

### Gli spazi vuoti nelle intestazioni di colonna generano voci di output null
Analisi dei flussi non taglia gli spazi vuoti nelle intestazioni di colonna. L’aggiunta di spazi vuoti all'inizio o alla fine di un nome di colonna comporterà voci null nell'output del processo.


## Supporto
Per ulteriore supporto, vedere il [forum dedicato ad Analisi dei flussi di Azure](stream-analytics-forum.md).


## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](stream-analytics-query-language-reference.md)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](stream-analytics-rest-api-reference.md) 

<!--Anchors-->
[Limitations]: #Limitations
[Release notes and known issues]: #Release-notes-and-known-issues
[Next steps]: #next-steps

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->

[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.limitations]: stream-analytics-limitations.md


[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


[microsoft.support]: http://support.microsoft.com/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!--HONumber=54-->