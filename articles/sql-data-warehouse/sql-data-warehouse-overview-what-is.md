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
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;"/>


# Che cos'è SQL Data Warehouse di Azure?

SQL Data Warehouse è un database distribuito di livello aziendale, in grado di elaborare volumi massivi di dati relazionali e non relazionali. Si tratta del primo data warehouse basato sul cloud del settore che combina le comprovate funzionalità SQL con la possibilità di aumentare, ridurre e sospendere le risorse in pochi secondi. SQL Data Warehouse è anche profondamente radicato in Azure e può quindi essere distribuito facilmente in secondi. Il servizio è anche completamente gestito ed evita le perdite di tempo richieste per l'applicazione di patch, la manutenzione e i backup. I backup automatici, incorporati di SQL Data Warehouse supportano la tolleranza di errore e ripristino self-service. Durante la creazione di SQL Data Warehouse gli sviluppatori si sono concentrati su alcuni attributi chiave, per garantire che sarebbe stato possibile sfruttare completamente i vantaggi offerti da Azure, e sulla creazione di un data warehouse che potesse soddisfare qualsiasi carico di lavoro aziendale.

## Ottimizzato

### Architettura di Data Warehouse

Fondamentalmente, SQL Data Warehouse viene eseguito usando l'architettura di elaborazione parallela massiva (MPP) di Microsoft, originariamente progettata per eseguire alcuni dei più grandi data warehouse aziendali locali. Questa architettura sfrutta i miglioramenti delle prestazioni di data warehousing predefiniti e consente anche di eseguire facilmente il ridimensionamendo di SQL Data Warehouse e la parallelizzazione del calcolo di query SQL complesse. L'architettura di SQL Data Warehouse è progettata anche per sfruttare la presenza in Azure. Combinando questi due aspetti, l'architettura si suddivide in 4 componenti chiave:

![Architettura di SQL Data Warehouse][1]

- **Nodo di controllo:** si stabilisce la connessione al nodo di controllo quando si usa SQL Data Warehouse con qualsiasi strumento di sviluppo, caricamento o business intelligence. In SQL Data Warehouse il nodo di controllo è un database SQL e quando si stabilisce la connessione ha l'aspetto di un normale database SQL. Tuttavia, in background coordina completamente lo spostamento e il calcolo dei dati eseguiti nel sistema. Quando viene inviato un comando al nodo di controllo, lo suddivide in un set di query che saranno passate ai nodi di calcolo del servizio.

- **Nodi di calcolo:** come il nodo di controllo, i nodi di calcolo di SQL Data Warehouse si basano sull'uso di database SQL. Il loro scopo è fornire la potenza di calcolo del servizio. Dietro le quinte, ogni volta che si caricano dati in SQL Data Warehouse, questi vengono distribuiti tra i nodi del servizio. Quindi, ogni volta che il nodo di controllo riceve un comando lo suddivide in parti per ogni nodo di calcolo e questi operano sui dati corrispondenti. Al termine del calcolo, i nodi di calcolo passano i risultati parziali al nodo di controllo, che quindi aggrega i risultati prima di restituire una risposta.

- **Archiviazione:** tutta l'archiviazione per SQL Data Warehouse si basa su BLOB di archiviazione Azure standard. Ciò significa che quando interagiscono con i dati, i nodi di calcolo scrivono e leggono direttamente nei BLOB. La capacità di archiviazione di Azure di espandersi in modo trasparente e quasi senza limiti consente di ridimensionare automaticamente l'archiviazione e di farlo separatamente dal calcolo. Archiviazione di Azure consente anche di memorizzare in modo permanente l'archiviazione durante il ridimensionamento o la sospensione, semplificare il processo di backup e ripristino e avere un'archiviazione più sicura e a tolleranza di errore.

- **Servizi di spostamento dati:** l'ultima parte che raggruppa tutti gli elementi in SQL Data Warehouse sono i servizi di spostamento dati. I servizi di spostamento dati consentono al nodo di controllo di comunicare e passare dati a tutti i nodi di calcolo. Consentono anche ai nodi di calcolo di passare dati tra di essi, fornendo l'accesso ai dati in altri nodi di calcolo, e di ottenere i dati necessari per completare i join e aggregazioni.

### Ottimizzazioni del motore

Questo approccio MPP consente a SQL Data Warehouse di adottare un approccio di tipo "dividi e domina", come descritto in precedenza per la risoluzione dei problemi relativi a grandi quantità di dati. Poiché i dati in SQL Data Warehouse vengono divisi e distribuiti tra i nodi di calcolo del servizio, ognuno di essi è in grado di operare sulla propria parte di dati in parallelo. Infine, i risultati vengono passati al nodo di controllo e aggregati prima di essere restituiti agli utenti. Questo approccio è supportato anche da numerose ottimizzazioni delle prestazioni specifici per il data warehousing:

- SQL Data Warehouse usa un Query Optimizer avanzato e un set di statistiche complesse su tutti i dati nel servizio per creare i piani di query. Usando le informazioni sulla distribuzione e sulle dimensioni dei dati, il servizio è in grado di ottimizzare le query distribuite in base a una valutazione del costo di operazioni di query specifiche.

- Oltre a creare piani di query ottimali, SQL Data Warehouse incorpora tecniche e algoritmi sofisticati in grado di spostare in modo efficiente i dati tra le diverse risorse di calcolo come necessario per eseguire la query. Queste operazioni sono integrate nei servizi di spostamento dati del data warehouse e le ottimizzazioni vengono eseguite automaticamente.

- L'inclusione di indici columnstore cluster nel dispositivo è essenziale anche per velocizzare le prestazioni delle query. Usando un'archiviazione basata su colonne, SQL Data Warehouse può raggiungere una compressione cinque volte superiore all'archiviazione tradizionale basata su righe e prestazioni delle query fino a dieci volte maggiori. Le query del data warehouse funzionano in modo ottimale sugli indici columnstore perché analizzano spesso l'intera tabella o un'intera partizione di una tabella, riducendo al minimo l'impatto dello spostamento dei dati per i passi di query.

## Scalabile

L'architettura di SQL Data Warehouse introduce risorse di archiviazione e calcolo separate in grado di permettere il ridimensionamento di ogni archiviazione in mondo indipendente. La struttura di distribuzione semplice e rapida del database SQL consente avere a disposizione risorse di calcolo aggiuntive con pochissimo preavviso. L'uso dei BLOB di archiviazione di Azure è complementare a queste funzionalità. I BLOB offrono non solo un'archiviazione stabile e replicata, ma forniscono anche l'infrastruttura per un'espansione senza problemi e a basso costo. Usando questa combinazione di archiviazione con scalabilità cloud e funzionalità di calcolo di Azure, SQL Data Warehouse consente di pagare per un'archiviazione con le prestazioni di query necessarie, solo quando è necessario. La modifica della quantità di risorse di calcolo è semplice, basta spostare a sinistra o a destra un dispositivo di scorrimento nel portale di Azure classico, ma può anche essere pianificata o aggiunta a un carico di lavoro con T-SQL e PowerShell.

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

> [AZURE.NOTE]Si noti che a causa dell'architettura o di SQL Data Warehouse potrebbe non risultare evidente il ridimensionamento delle prestazioni con volumi di dati limitati. È consigliabile iniziare con volumi di dati minimi di 1 TB per ottenere risultati dei test delle prestazioni accurati.

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

>[AZURE.NOTE]Si desidera migliorare questo articolo. Se si sceglie di rispondere "no" alla domanda "È stato utile questo articolo?", includere un suggerimento breve su cosa manca o su come migliorare l'articolo. Grazie in anticipo!!

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Carico di lavoro del data warehouse]: ./sql-data-warehouse-overview-workload.md
[Caricare i dati di esempio]: ./sql-data-warehouse-get-started-load-samples.md
[provisioning]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->