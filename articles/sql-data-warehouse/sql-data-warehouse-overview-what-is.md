<properties
   pageTitle="Che cos'è SQL Data Warehouse di Azure | Microsoft Azure"
   description="Database distribuito di livello aziendale, in grado di elaborare volumi di dati relazionali e non relazionali anche nell'ordine di petabyte. Si tratta del primo data warehouse basato sul cloud del settore con possibilità di aumento, riduzione e sospensione in pochi secondi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/26/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;"/>


# Che cos'è SQL Data Warehouse di Azure?

SQL Data Warehouse è un database ridimensionabile basato sul cloud in grado di elaborare volumi massivi di dati sia relazionali che non relazionali. Basato sull'architettura di elaborazione parallela massiva (MPP, Massively Parallel Processing), SQL Data Warehouse può gestire il carico di lavoro dell'organizzazione.

SQL Data Warehouse:

- Combina il comprovato database relazionale di SQL Server con le funzionalità di ridimensionamento del cloud di Azure. È possibile aumentare, ridurre, sospendere o riprendere il calcolo in pochi secondi. In questo modo si possono ridurre i costi aumentando l'uso della CPU quando occorre e riducendo l'utilizzo negli orari non di punta.
- Sfrutta la piattaforma Azure. È facile da distribuire, può essere mantenuta senza problemi ed è completamente a tolleranza di errore grazie ai backup automatici. 
- È complementare all'ecosistema di SQL Server. È possibile sviluppare con il familiare linguaggio T-SQL e gli strumenti di SQL Server.

Di seguito sono disponibili altre informazioni sulle funzionalità principali di SQL Data Warehouse.

## Ottimizzato

### Architettura di elaborazione parallela massiva (MPP)

SQL Data Warehouse usa l'architettura di elaborazione parallela massiva (MPP) di Microsoft progettata per eseguire alcuni dei più grandi data warehouse locali del mondo.

Attualmente, l'architettura MPP distribuisce i dati tra 60 unità di elaborazione e di archiviazione in modalità di condivisione "shared-nothing". I dati vengono archiviati nei BLOB di archiviazione di Azure ridondanti con replica geografica e collegati ai nodi di calcolo per l'esecuzione di query. Con questa architettura è possibile adottare un approccio di tipo "dividi e conquista" per l'esecuzione di query T-SQL complesse. Durante l'elaborazione, il nodo di controllo analizza la query e quindi ogni nodo di calcolo ottiene la relativa parte dei dati in parallelo.

Combinando l'architettura MPP e le funzionalità di archiviazione di Azure, SQL Data Warehouse può:

- Aumentare o ridurre lo spazio di archiviazione indipendente dal calcolo.
- Aumentare o ridurre il calcolo senza spostare i dati. 
- Sospendere la capacità di calcolo mantenendo intatti i dati.
- Riprendere la capacità di calcolo con pochissimo preavviso.

L'architettura è descritta in dettaglio di seguito.

![Architettura di SQL Data Warehouse][1]


- **Nodo di controllo :** "controlla" il sistema. È il front-end che interagisce con tutte le applicazioni e le connessioni. In SQL Data Warehouse il nodo di controllo si basa sul database SQL e quando si stabilisce la connessione ha lo stesso aspetto. In background il nodo di controllo coordina lo spostamento e il calcolo di tutti i dati e i calcoli richiesti per l'esecuzione di query parallele sui dati distribuiti. Quando si invia una query TSQL a SQL Data Warehouse, il nodo di controllo la trasforma in query distinte che saranno eseguite su ogni nodo di calcolo in parallelo.

- **Nodi di calcolo:** forniscono le potenzialità necessarie per SQL Data Warehouse. Sono database SQL che elaborano i passi di query e gestiscono i dati. Quando si aggiungono dati, SQL Data Warehouse distribuisce le righe usando i nodi di calcolo. I nodi di calcolo sono anche i processi di lavoro che eseguono le query parallele sui dati. Dopo l'elaborazione, restituiscono i risultati al nodo del controllo. Per completare la query, il nodo controllo aggrega i risultati e restituisce il risultato finale.


- **Archiviazione**: i dati vengono archiviati nei BLOB di Archiviazione di Azure. Quando i nodi di calcolo interagiscono con i dati, scrivono e leggono direttamente da e nell'archiviazione BLOB. Poiché l'archiviazione di Azure si espande in modo trasparente e senza limiti, SQL Data Warehouse può fare la stessa cosa. Poiché elaborazione e archiviazione sono indipendenti, SQL Data Warehouse può ridimensionare automaticamente le risorse di archiviazione separatamente dal ridimensionamento delle risorse di calcolo e viceversa. Archiviazione di Azure è anche completamente a tolleranza di errore e semplifica il processo di backup e ripristino.
   

- **Data Movement Service** (DMS): è la tecnologia Microsoft per lo spostamento dei dati tra i nodi. DMS fornisce ai nodi di calcolo l'accesso ai dati necessario per eseguire join e aggregazioni. DMS non è un servizio di Azure. È un servizio di Windows che viene eseguito insieme al database SQL in tutti i nodi. Poiché DMS viene eseguito in background, l'utente non interagisce direttamente con esso. Tuttavia, quando si esaminano i piani di query, si noterà che includono alcune operazioni DMS, perché lo spostamento dei dati è necessario in qualche forma o formato per eseguire ogni query in parallelo.


### Prestazioni delle query ottimizzate

Oltre alla strategia di "divisione e conquista", l'approccio MPP è supportato da numerose ottimizzazioni delle prestazioni specifiche per il data warehousing, ad esempio:

- Un'utilità di ottimizzazione delle query distribuita e un set di statistiche complesse su tutti i dati. Usando le informazioni sulla distribuzione e sulle dimensioni dei dati, il servizio è in grado di ottimizzare le query in base a una valutazione del costo di operazioni di query distribuite specifiche.

- Tecniche e algoritmi avanzati integrati nel processo di spostamento dei dati per spostare in modo efficiente i dati tra le diverse risorse di calcolo, secondo le esigenze, per eseguire la query. Queste operazioni di spostamento dei dati sono integrate e tutte le ottimizzazioni di Data Movement Service vengono eseguite automaticamente.

- Indice columnstore cluster per impostazione predefinita. Usando un'archiviazione basata su colonne, SQL Data Warehouse raggiunge una compressione cinque volte superiore all'archiviazione tradizionale basata su righe e prestazioni delle query fino a dieci volte maggiori. Query di analisi che devono eseguire l'analisi di un numero elevato di righe forniscono prestazioni eccezionali sugli indici columnstore.

## Scalabile

L'architettura di SQL Data Warehouse introduce risorse di archiviazione e calcolo separate in grado di permettere il ridimensionamento di ogni archiviazione in mondo indipendente. La struttura di distribuzione semplice e rapida del database SQL consente avere a disposizione risorse di calcolo aggiuntive con pochissimo preavviso. L'uso dei BLOB di archiviazione di Azure è complementare a queste funzionalità. L'uso dei BLOB offre non solo un'archiviazione stabile e replicata, ma anche l'infrastruttura per un'espansione senza problemi e a basso costo. Usando questa combinazione di archiviazione con scalabilità cloud e funzionalità di calcolo di Azure, SQL Data Warehouse consente di pagare per un'archiviazione con le prestazioni di query necessarie, solo quando è necessario. La modifica della quantità di risorse di calcolo è semplice, basta spostare a sinistra o a destra un dispositivo di scorrimento nel portale di Azure, ma può anche essere pianificata con T-SQL e PowerShell.

Oltre alla possibilità di controllare completamente la quantità del calcolo in modo indipendente dall'archiviazione, SQL Data Warehouse consente di sospendere completamente il data warehouse. Conservando lo spazio di archiviazione, tutto il calcolo viene rilasciato nel pool principale di Azure, riducendo immediatamente i costi. Quando è necessario, basta riprendere semplicemente il calcolo e i dati e il calcolo saranno disponibili per il carico di lavoro.

L'utilizzo del calcolo in SQL Data Warehouse viene misurato tramite Unità Data Warehouse (DWU). Le DWU sono una misura della potenza sottostante del data warehouse e sono progettate per assicurarsi di avere una quantità di prestazioni standard associata al warehouse in qualsiasi momento. In particolare, si usano le DWU per assicurarsi di poter eseguire queste operazioni:

- Ridimensionare il data warehouse in modo efficace senza doversi preoccupare dei componenti hardware o software sottostanti.

- Comprendere le prestazioni che verrà visualizzate a livello di DWU prima di modificare le dimensioni del data warehouse.

- Modificare o spostare i componenti hardware e software dell'istanza sottostante senza compromettere le prestazioni del carico di lavoro.

- Apportare modifiche all'architettura sottostante del servizio senza influire sulle prestazioni del carico di lavoro.

- Migliorare rapidamente le prestazioni in SQL Data Warehouse, avendo la certezza che ciò avvenga in modo scalabile e con un impatto uniforme sul sistema.

### Unità Data Warehouse

In particolare, vengono esaminate le Unità Data Warehouse come misura di tre metriche precise ritenute strettamente correlate alle prestazioni del carico di lavoro di data warehousing. L'obiettivo è che, per la disponibilità generale, queste metriche chiave del carico di lavoro vengano ridimensionate in modo lineare con le DWU scelte per il data warehouse.

**Analisi/aggregazione:** questa metrica del carico di lavoro accetta una query di data warehousing query standard che esegue l'analisi di un numero elevato di righe e quindi esegue un'aggregazione complessa. Questa è un'operazione con utilizzo intensivo di I/O e CPU.

**Carico:** questa metrica consente di inserire dati nel servizio. I carichi vengono completati con PolyBase che carica un set di dati rappresentativo da un BLOB di archiviazione di Azure. Questa metrica è progettata per evidenziare gli aspetti relativi a rete e CPU del servizio.

**CREATE TABLE AS SELECT (CTAS):** CTAS misura la possibilità di creare una copia di una tabella. Ciò comporta la lettura dei dati dall'archiviazione, la relativa distribuzione tra tutti i nodi del dispositivo e la riscrittura in un'archiviazione. È un'operazione con utilizzo intensivo della CPU e della rete.

### Quando è necessario scalare

In generale, è necessario che le DWU siano semplici. Quando sono necessari risultati più rapidi, aumentare le DWUs e pagare il costo di prestazioni più elevate. Quando è richiesta una minore quantità di potenza di calcolo, diminuire le DWU e pagare solo per le prestazioni necessarie Di seguito alcuni casi in cui si può pensare di modificare il numero di DWU:

- Quando non è necessario eseguire query, ad esempio la sera o nei week-end, è possibile sospendere le risorse di calcolo per annullare tutte le query in esecuzione e rimuovere tutte le DWU allocate al data warehouse.

- Quando si esegue un'operazione di caricamento o trasformazione di quantità di dati elevate, è possibile procedere al ridimensionamento in modo che i dati siano disponibili più rapidamente.

- Per comprendere il valore di DWU ideale, provare ad aumentarlo e diminuirlo ed eseguire alcune query dopo il caricamento dei dati. Poiché il ridimensionamento è rapido, è possibile provare diversi livelli di prestazioni senza eseguire il commit a più di un'ora.

> [AZURE.NOTE] Si noti che a causa dell'architettura o di SQL Data Warehouse potrebbe non risultare evidente il ridimensionamento delle prestazioni con volumi di dati limitati. È consigliabile iniziare con volumi di dati minimi di 1 TB per ottenere risultati dei test delle prestazioni accurati.

## Integrato

SQL Data Warehouse si basa sul comprovato motore di database relazionale di SQL Server e include molte delle funzionalità che ci si aspetta da un data warehouse aziendale. Se già si conosce Transact-SQL, sarà facile trasferire le proprie conoscenze in SQL Data Warehouse. Per gli utenti avanzati e per chi non ha molta familiarità, gli esempi disponibili nella documentazione saranno utili per iniziare. In generale, è possibile considerare il modo in cui sono costruiti gli elementi del linguaggio di SQL Data Warehouse come segue:

- SQL Data Warehouse usa la sintassi Transact-SQL (TSQL) di SQL Server per molte operazioni e supporta un'ampia gamma di costrutti SQL tradizionali, ad esempio stored procedure, funzioni definite dall'utente, partizionamento delle tabelle, indici e regole di confronto.

- SQL Data Warehouse include anche una serie di funzionalità di SQL Server all'avanguardia, inclusi gli indici columnstore cluster, l'integrazione di PolyBase e il controllo dei dati (tra cui la valutazione delle minacce).

- Poiché SQL Data Warehouse è ancora in fase di sviluppo, alcuni elementi del linguaggio TSQL meno comuni per i carichi di lavoro di data warehousing o più recenti in SQL Server potrebbero non essere disponibili al momento. Per alte informazioni correlate a questo argomento, vedere la documentazione sulla migrazione.

Grazie a Transact-SQL e alle funzionalità in comune tra SQL Server, SQL Data Warehouse, il database SQL e Analytics Platform System, è possibile sviluppare una soluzione adatta alle esigenze dei dati di cui si dispone. È possibile decidere dove mantenere i dati in base ai requisiti di prestazioni, sicurezza e scalabilità e quindi trasferirli secondo le necessità tra i diversi sistemi.

Oltre ad adottare la superficie di attacco TSQL di SQL Server, SQL Data Warehouse si integra anche con molti degli strumenti di SQL Server con cui gli utenti potrebbero avere familiarità. In particolare, è stata concentrata l'attenzione sull'integrazione con SQL Data Warehouse di alcune categorie di strumenti, inclusi:

**Strumenti tradizionali di SQL Server:** con SQL Data Warehouse è disponibile l'integrazione completa con SQL Server Analysis Services, Integration Services e Reporting Services.

**Strumenti basati sul cloud:** SQL Data Warehouse può essere usato insieme a numerosi nuovi strumenti di Azure e ha una stretta integrazione con Data factory, Analisi di flusso, Machine Learning e Power BI di Azure.

**Strumenti di terze parti:** numerosi provider di strumenti di terze parti hanno certificato l'integrazione dei propri strumenti con SQL Data Warehouse. Vedere l'elenco completo.

## Ibrido

L'uso di SQL Data Warehouse con PolyBase offre agli utenti capacità senza precedenti per spostare dati nel proprio ecosistema, consentendo di configurare scenari ibridi avanzati con origini dati non relazionali e locali.

Polybase è facile da usare e consente di sfruttare i dati provenienti da origini diverse eseguendo gli stessi comandi T-SQL familiari. Polybase consente di eseguire query su dati non relazionali presenti nell'archivio BLOB di Azure come se si trattasse di una normale tabella. Usare Polybase per eseguire query su dati non relazionali o per importare dati non relazionali in SQL Data Warehouse.

- PolyBase usa tabelle esterne per accedere ai dati non relazionali. Le definizioni di tabella vengono archiviate in SQL Data Warehouse e sono accessibili a SQL e agli strumenti come lo sono i normali dati relazionali.

- Polybase è agnostico nella relativa integrazione, quindi espone le stesse caratteristiche e funzionalità a tutte le origini supportate. I dati letti da Polybase possono essere in vari formati, inclusi i file delimitati o i file ORC.

- PolyBase può essere usato per accedere all'archiviazione BLOB che viene usata come risorsa di archiviazione anche per un cluster HDInsight, consentendo un livello di accesso all'avanguardia agli stessi dati con strumenti relazionali e non relazionali.


## Passaggi successivi

Dopo avere appreso alcune nozioni su SQL Data Warehouse, per iniziare, vedere [Carico di lavoro del data warehouse], l'articolo relativo al [provisioning] e [Caricare i dati di esempio].

>[AZURE.NOTE] Si desidera migliorare questo articolo. Se si sceglie di rispondere "no" alla domanda "È stato utile questo articolo?", includere un suggerimento breve su cosa manca o su come migliorare l'articolo. Grazie in anticipo!!

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Carico di lavoro del data warehouse]: ./sql-data-warehouse-overview-workload.md
[Caricare i dati di esempio]: ./sql-data-warehouse-get-started-load-samples.md
[provisioning]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->