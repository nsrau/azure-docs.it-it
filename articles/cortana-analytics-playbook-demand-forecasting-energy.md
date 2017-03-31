---
title: Studio del modello di soluzione Cortana Intelligence per la previsione della domanda di energia | Documentazione Microsoft
description: Modello di soluzione di Microsoft Cortana Intelligence utile per la previsione della domanda di energia per un&quot;azienda di pubblici servizi.
services: cortana-analytics
documentationcenter: 
author: ilanr9
manager: ilanr9
editor: yijichen
ms.assetid: 8855dbb9-8543-45b9-b4c6-aa743a04d547
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2016
ms.author: ilanr9;yijichen;garye
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 0c07495a932c8bfdabb97e8eebe8d99de19fbd15
ms.lasthandoff: 01/30/2017


---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Studio del modello di soluzione Cortana Intelligence per la previsione della domanda di energia
## <a name="executive-summary"></a>Sunto
Negli ultimi anni la combinazione di Big Data, Internet delle cose (IoT) e fonti alternative di energia ha creato ampie opportunità nel settore dell'energia e dei pubblici servizi. Allo stesso tempo, l'intero settore dell'energia e dei pubblici servizi ha assistito a un livellamento dei consumi con la ricerca, da parte dei consumatori, di modi migliori per controllare il consumo energetico. Per questo motivo le aziende operanti nel settore dei pubblici servizi e delle smart grid, ovvero le reti elettriche intelligenti, hanno un grande bisogno di innovazione e rinnovamento. Molte reti elettriche e di pubblici servizi stanno diventando obsolete e molto costose da mantenere e gestire. Nell'ultimo anno il team ha lavorato a diversi interventi nel settore dell'energia. Nel corso di tali interventi il team ha incontrato diverse aziende di pubblici servizi e fornitori di software indipendenti (ISV) interessati a strumenti di previsione della futura domanda di energia. Le previsioni hanno un ruolo importante per l'attività attuale e futura di tali aziende e sono alla base di diversi casi d'uso, come previsioni del carico elettrico a breve e lungo termine, commercializzazione, bilanciamento del carico, ottimizzazione delle reti e così via. Big Data e metodi di analisi avanzata, come ad esempio Machine Learning (ML), sono particolarmente importanti per la creazione di previsioni accurate e affidabili.  

Questo studio sintetizza le linee guida aziendali e analitiche per sviluppare e distribuire nel modo corretto una soluzione di previsione della domanda di energia. Le linee guida qui proposte consentono ad aziende di pubblici servizi, data scientist e data engineer di creare soluzioni per la previsione della domanda completamente operative e basate sul cloud. Per le aziende che muovo i primi passi nel settore dei Big Data e dell'analisi avanzata, questa soluzione può rappresentare un ottimo punto di partenza per una strategia di smart grid a lungo termine.

> [!TIP]
> Per scaricare un diagramma che offre una panoramica dell'architettura del modello, vedere [Architettura del modello di soluzione di Cortana Intelligence per la previsione della domanda di energia](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Overview
Questo documento illustra gli aspetti tecnici, commerciali e relativi ai dati legati all'uso di Cortana Intelligence e, in particolare, di Azure Machine Learning (Azure ML) per l'implementazione e la distribuzione di soluzioni di previsione energetica. Il documento è costituito da tre parti principali:  

1. Informazioni commerciali  
2. Informazioni sui dati  
3. Implementazione tecnica

La parte relativa alle **informazioni commerciali** descrive l'aspetto legato alle esigenze aziendali che è necessario comprendere ed esaminare prima di prendere una decisione di investimento. Illustra come qualificare il problema aziendale per assicurarsi che analisi predittiva e apprendimento automatico siano davvero efficaci e applicabili. Il documento approfondisce le nozioni di base sull'apprendimento automatico e come viene usato per risolvere i problemi di previsione energetica. Descrive i prerequisiti e i criteri di qualificazione di un caso d'uso. Fornisce anche alcuni casi d'uso di esempio e scenari di casi aziendali.

I dati sono l'elemento principale di qualsiasi soluzione di apprendimento automatico. La parte relativa alle **informazioni sui dati** di questo documento illustra alcuni aspetti importanti relativi ai dati. Descrive il tipo di dati necessario per la previsione energetica, i requisiti di qualità dei dati e le origini dati generalmente disponibili. Illustra anche come usare i dati non elaborati per preparare le funzionalità dei dati che determinano effettivamente la modellazione.

La terza parte del documento illustra l'aspetto relativo all' **implementazione tecnica** di una soluzione. La progettazione e la modellazione di funzionalità sono alla base del processo di analisi scientifica dei dati e vengono quindi discusse in modo dettagliato. Questa parte illustra il concetto di servizi Web, un veicolo importante per la distribuzione cloud di soluzioni di analisi predittiva. Descrive anche una tipica architettura di soluzione end-to-end operativa

e include materiale di riferimento utile per ottenere altre informazioni sul settore e sulla tecnologia.

È importante notare che una trattazione approfondita del processo di analisi scientifica dei dati e dei relativi aspetti matematici e tecnici non rientra nelle finalità di questo documento. Tali informazioni dettagliate sono reperibili nella [documentazione di Azure ML](http://azure.microsoft.com/services/machine-learning/) e nei [blog](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Destinatari
I destinatari di questo documento sono aziende e personale tecnico che vogliono conoscere e comprendere le soluzioni basate su Machine Learning e l'uso specifico nell'ambito della previsione energetica.

I data scientist possono leggere questo documento per comprendere meglio il processo generale che determina la distribuzione di una soluzione di previsione energetica. In questo contesto il documento può essere usato anche come base e punto di partenza per accedere a materiale più dettagliato e avanzato.

### <a name="industry-trends"></a>Tendenze del settore
Negli ultimi anni la combinazione di IoT, fonti alternative di energia e Big Data ha creato ampie opportunità nel settore dell'energia e dei pubblici servizi. Allo stesso tempo, l'intero settore dell'energia e dei pubblici servizi ha assistito a un livellamento dei consumi con la ricerca, da parte dei consumatori, di modi migliori per controllare il consumo energetico.

Molte aziende del settore energetico e dei pubblici servizi hanno avviato la realizzazione di [smart grid](https://en.wikipedia.org/wiki/Smart_grid) , distribuendo diversi casi d'uso che fanno uso dei dati generati dalla rete. Molti casi d'uso riguardano le caratteristiche intrinseche della produzione di elettricità, che non può essere accumulata né immagazzinata. L'energia prodotta deve essere usata. Le aziende di pubblici servizi che vogliono migliorare l'efficienza devono elaborare previsioni del consumo energetico per riuscire a **bilanciare meglio la domanda e l'offerta**, prevenire gli sprechi, **ridurre le emissioni di gas serra** e controllare i costi.

In tema di costi, un altro aspetto importante è quello del prezzo. Le nuove possibilità legate al commercio di energia tra aziende di pubblici servizi hanno fatto crescere la necessità di **prevedere la domanda e il prezzo futuri dell'energia elettrica**. Questo può aiutare le aziende a determinare i volumi di produzione.

Il termine "smart grid" indica una rete elettrica intelligente, che può apprendere ed eseguire previsioni. Può prevedere le variazioni stagionali del consumo energetico nonché **situazioni di sovraccarico temporaneo ed eseguire automaticamente le regolazioni necessarie**. La regolazione remota del consumo energetico, con l'aiuto di contatori intelligenti, permette di gestire situazioni di sovraccarico localizzate. Attraverso le attività di **previsione e reazione**, la rete diventa sempre più intelligente.

La parte restante di questo documento è incentrata su un gruppo specifico di casi d'uso che riguarda la previsione della futura domanda di energia, a breve e a lungo termine. Le conoscenze e le competenze acquisite in diversi mesi di lavoro in queste aree hanno permesso di produrre risultati a livello di settore. In un prossimo futuro nel documento verranno illustrati anche altri casi d'uso.

## <a name="business-understanding"></a>Informazioni commerciali
### <a name="business-goals"></a>Obiettivi commerciali
L'obiettivo della **demo sull'energia** è dimostrare una soluzione tipica di analisi predittiva e apprendimento automatico che può essere distribuita in un intervallo di tempo molto breve. In particolare, l'obiettivo attuale è l'abilitazione di soluzioni di previsione della domanda di energia per valutarne e sfruttarne rapidamente il valore commerciale. Le informazioni contenute in questo studio permettono al cliente di raggiungere gli obiettivi seguenti:

* Ottenere tempi di esecuzione ridotti per la soluzione basata sull'apprendimento automatico.
* Estendere un caso d'uso pilota ad altri casi d'uso oppure a un ambito più ampio in base alle esigenze aziendali.
* Apprendere rapidamente l'uso della suite Cortana Intelligence.

Questo studio intende fornire le conoscenze tecniche e commerciali che consentiranno di raggiungere tali obiettivi.

### <a name="power-load-and-demand-forecasting"></a>Previsione della domanda e del carico elettrico
La previsione della domanda può contribuire in diversi modi a risolvere problemi commerciali critici nel settore energetico. La previsione della domanda può essere considerata, di fatto, la base di molti casi d'uso nel settore. In generale, vengono presi in considerazione due tipi di previsione della domanda di energia: a breve termine e a lungo termine. Ognuno di essi può avere uno scopo diverso e usare un approccio differente. La differenza principale tra i due è l'orizzonte di previsione, vale a dire l'intervallo di tempo nel futuro rispetto al quale si esegue la previsione.

#### <a name="short-term-load-forecasting"></a>Previsione di carico a breve termine
Nel contesto della domanda di energia, la previsione di carico a breve termine è definita come il carico aggregato che viene previsto per il prossimo futuro in varie parti della rete o in tutta la rete. In questo contesto il breve termine è un orizzonte temporale compreso tra un'ora e 24 ore. In alcuni casi è anche possibile avere un orizzonte di 48 ore. Di conseguenza, la previsione di carico a breve termine è molto diffusa nei casi d'uso operativi della rete. Di seguito sono riportati alcuni esempi di casi d'uso basati sulla previsione di carico a breve termine:

* Bilanciamento della domanda e dell'offerta.
* Supporto alla commercializzazione dell'energia.
* Market making e determinazione del prezzo dell'energia.
* Ottimizzazione operativa della rete.
* [Risposta alla domanda](https://en.wikipedia.org/wiki/Demand_response)
* Previsione dei picchi nella domanda.
* Gestione sul lato della domanda.
* Bilanciamento del carico e prevenzione del sovraccarico.
* Previsione di carico a lungo termine.
* Rilevamento di guasti e anomalie.
* Riduzione e livellamento dei picchi. 

I modelli di previsione di carico a breve termine sono basati principalmente sui dati relativi al consumo del giorno o della settimana precedente e usano le temperature previste come un predittore importante. Ottenere previsioni accurate sulle temperature da un'ora a 24 ore dopo sta diventando sempre meno difficile. Questi modelli sono meno sensibili agli schemi stagionali o alle tendenze di consumo a lungo termine.

Le soluzioni di previsione di carico a breve termine hanno anche maggiori probabilità di generare un volume elevato di chiamate di previsione, o richieste di servizio, dal momento che vengono richiamate ogni ora e, in alcuni casi, anche con una frequenza maggiore. Non è raro trovare impianti in cui ogni singola sottostazione o trasformatore è rappresentato come un modello autonomo e, di conseguenza, il volume delle richieste di previsione è ancora maggiore.

#### <a name="long-term-load-forecasting"></a>Previsione di carico a lungo termine.
L'obiettivo della previsione di carico a lungo termine è la previsione della domanda di energia con un orizzonte di previsione che va da una settimana a diversi mesi e, in alcuni casi, anche diversi anni. Questo orizzonte di previsione si applica principalmente a casi d'uso di pianificazione e investimento.

Per gli scenari a lungo termine è importante avere a disposizione dati di qualità elevata che coprano un intervallo di almeno tre anni. In genere questi modelli estraggono gli schemi stagionali dai dati storici e fanno uso di indicatori esterni, come ad esempio schemi meteorologici e climatici.

È importante chiarire che a un orizzonte di previsione più esteso può corrispondere una previsione meno precisa. È quindi importante produrre alcuni intervalli di confidenza insieme alla previsione effettiva, per permettere ai tecnici di includere la possibile variazione nel processo di pianificazione.

Dal momento che lo scenario di utilizzo della previsione di carico a lungo termine è principalmente la pianificazione, ci si aspettano volumi di previsione molto più bassi rispetto alla previsione di carico a breve termine. Tali previsioni sono in genere incorporate in strumenti di visualizzazione come Excel o Power BI e vengono richiamate manualmente dall'utente.

### <a name="short-term-vs-long-term-prediction"></a>Confronto tra la previsione a breve termine e la previsione a lungo termine
La tabella seguente mette a confronto la previsione di carico a breve e a lungo termine rispetto agli attributi più importanti:

| Attributo | Previsione di carico a breve termine | Previsione di carico a lungo termine |
| --- | --- | --- |
| Orizzonte di previsione |Da un'ora a 48 ore |Da 1 a 6 mesi o più |
| Granularità dei dati |Oraria |Oraria o giornaliera |
| Casi d'uso tipici |<ul><li>Bilanciamento di domanda/offerta</li><li>Previsione delle ore di picco</li><li>Risposta alla domanda</li></ul> |<ul><li>Pianificazione a lungo termine</li><li>Pianificazione degli asset della rete</li><li>Pianificazione delle risorse</li></ul> |
| Predittori tipici |<ul><li>Giorno o settimana</li><li>Ora del giorno</li><li>Temperatura oraria</li></ul> |<ul><li>Mese dell'anno</li><li>Giorno del mese</li><li>Condizioni climatiche e temperatura a lungo termine</li></ul> |
| Intervallo di dati storici |Da due a tre anni di dati |Da cinque a dieci anni di dati |
| Precisione tipica |MAPE* pari al 5% o inferiore |MAPE* pari al 25% o inferiore |
| Frequenza di previsione |Ogni ora oppure ogni 24 ore |Mensile, trimestrale o annuale |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error): errore medio assoluto percentuale.

Come si può notare dalla tabella, la distinzione tra scenari di previsione a breve e lungo termine è molto importante dal momento che rappresentano esigenze commerciali diverse e possono avere schemi di distribuzione e consumo differenti.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Caso d'uso di esempio 1: eSmart Systems - Ottimizzazione del sovraccarico
Tra i compiti importanti di una [smart grid](https://en.wikipedia.org/wiki/Smart_grid) c'è quello di eseguire l'ottimizzazione e la regolazione in modo dinamico e costante in base agli schemi di variazione dei consumi. Il consumo energetico può essere influenzato da cambiamenti a breve termine causati principalmente da variazioni della temperatura. *Ad esempio*, i maggiori consumi dovuti all'uso del riscaldamento o dell'aria condizionata. Allo stesso tempo, il consumo energetico è influenzato anche da tendenze a lungo termine. Sono inclusi, ad esempio, effetti stagionali, festività nazionali, aumento del consumo a lungo termine e persino fattori economici come l'indice al consumo, il prezzo del greggio e il PIL.

In questo caso d'uso [eSmart Systems](http://www.esmartsystems.com/) vuole distribuire una soluzione basata sul cloud che consenta di prevedere la propensione a una situazione di sovraccarico per una data sottostazione della rete. In particolare, eSmart Systems vuole identificare le sottostazioni con una maggiore probabilità di sovraccarico nell'ora successiva, per poter intervenire immediatamente ed evitare o risolvere tale situazione.

Una previsione rapida e accurata richiede l'implementazione di tre modelli predittivi:

* Modello a lungo termine, che consente di prevedere il consumo energetico in ogni sottostazione nelle settimane o nei mesi successivi.
* Modello a breve termine, che consente di prevedere una situazione di sovraccarico in una determinata sottostazione nell'ora successiva.
* Modello di temperatura, che consente di prevedere le temperature future in più scenari.

L'obiettivo del modello a lungo termine è classificare le sottostazioni in base alla relativa propensione al sovraccarico, data la capacità di trasmissione dell'energia elettrica, nella settimana o nel mese successivo. Ciò consente la creazione di un breve elenco di sottostazioni da usare come input per la previsione a breve termine. Dal momento che la temperatura è un predittore importante per il modello a lungo termine, è necessario produrre costantemente previsioni di temperatura multi-scenario e inserirle come input nel modello a lungo termine. Viene quindi richiamata la previsione a breve termine per prevedere quale sottostazione ha più probabilità di sovraccarico nell'ora successiva.

I modelli a breve e a lungo termine vengono distribuiti singolarmente per ogni sottostazione. L'esecuzione pratica di questi modelli richiede quindi un'orchestrazione estesa. Per ottenere una maggiore precisione della previsione a breve termine, è possibile usare un modello più granulare dedicato per ogni ora del giorno. Tutti questi modelli vengono eseguiti con cadenza oraria e terminano l'esecuzione in pochi minuti per dare tempo sufficiente per rispondere e adottare le misure preventive necessarie. Questa raccolta di modelli viene mantenuta aggiornata attraverso la ripetizione periodica del training con i dati più recenti.

Altre informazioni su questo caso d'uso sono disponibili [qui](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Criteri di qualificazione dei casi d'uso - Prerequisiti
Il punto di forza di Cortana Intelligence è la grande capacità di distribuire e ridimensionare soluzioni incentrate sul Machine Learning. È progettato per supportare l'esecuzione simultanea di migliaia di previsioni. Consente la scalabilità automatica in base ai cambiamenti dello schema di consumo. La soluzione è quindi incentrata sulla precisione e sulle prestazioni di calcolo. Si prenda, ad esempio, un'azienda di pubblici servizi interessata alla produzione di una previsione accurata della domanda di energia per l'ora successiva e per ogni ora del giorno. È meno interessante sapere perché la domanda viene prevista come tale, dato che è il modello stesso a occuparsi di questo aspetto.

È quindi importante tenere presente che non tutti i casi d'uso e i problemi aziendali possono essere risolti in modo efficace con l'uso dell'apprendimento automatico.

Cortana Intelligence e Machine Learning possono risultare molto efficaci per la risoluzione di un problema aziendale quando sono soddisfatti i criteri seguenti:

* Il problema aziendale è di natura **predittiva** . Un esempio di caso d'uso predittivo è un'azienda di pubblici servizi che voglia prevedere il carico elettrico in una determinata sottostazione nell'ora successiva. L'analisi e la classificazione dei fattori che storicamente hanno determinato la domanda sarebbe, invece, di natura **descrittiva** e quindi meno applicabile.
* Esiste un chiaro **percorso da seguire** quando la previsione è disponibile. Ad esempio, una previsione di sovraccarico in una sottostazione nell'ora successiva può attivare un'azione preventiva di riduzione del carico associato a tale sottostazione e quindi prevenire il problema.
* Il caso d'uso rappresenta un **tipo di problema tipico** che, dopo la risoluzione, può aprire la strada alla risoluzione di altri casi d'uso simili.
* Il cliente può impostare **obiettivi quantitativi e qualitativi** per illustrare l'implementazione di una soluzione con esito positivo. Un obiettivo quantitativo valido per la previsione della domanda di energia potrebbe essere la soglia di precisione richiesta, *ad esempio* specificando che l'errore massimo consentito è del 5%. Nella previsione del sovraccarico di una sottostazione, invece, la precisione, ovvero la percentuale di veri positivi, e il richiamo, ovvero la quantità di veri positivi, dovrebbero essere superiori a una determinata soglia. Questi obiettivi devono derivare dagli obiettivi aziendali del cliente.
* Esiste un chiaro **scenario di integrazione** con il flusso di lavoro aziendale della società. Ad esempio, la previsione di carico di una sottostazione può essere integrata nel centro di controllo della rete per consentire le attività di prevenzione del sovraccarico.
* Il cliente ha **dati di qualità sufficiente** pronti all'uso per supportare il caso d'uso. Per altre informazioni, vedere la sezione **Qualità dei dati** di questo studio.
* Il cliente usa un'architettura dei dati basata sul cloud o il **Machine Learning basato sul cloud**, ad esempio Azure ML e altri componenti della suite Cortana Intelligence.
* Il cliente è disposto a specificare **un flusso di dati end-to-end** che agevola il recapito dei dati nel cloud con regolarità ed è disposto a **rendere operativa** la soluzione.
* Il cliente è pronto a destinare **risorse dedicate** che saranno coinvolte attivamente durante l'implementazione del progetto pilota iniziale, in modo che al termine sia possibile trasferire al cliente la competenza e la proprietà della soluzione.
* La risorsa del cliente deve essere un **esperto di dati**, preferibilmente un data scientist.

La qualificazione di un caso d'uso in base ai criteri appena descritti può migliorarne notevolmente i tassi di successo e stabilire un buon punto di partenza per l'implementazione di casi d'uso futuri.

### <a name="cloud-based-solutions"></a>Soluzioni basate sul cloud
La suite Cortana Intelligence in Azure è un ambiente integrato che risiede nel cloud. La distribuzione di una soluzione di analisi avanzata in un ambiente cloud offre notevoli vantaggi alle aziende e, allo stesso tempo, può rappresentare un grande cambiamento per quelle aziende che usano ancora soluzioni IT locali. Nel settore dell'energia c'è una chiara tendenza alla graduale migrazione delle operazioni nel cloud. Questa tendenza va di pari passo con lo sviluppo delle smart grid, discusso nella sezione **Tendenze del settore**. Dato che questo studio è incentrato su una soluzione basata sul cloud nel settore dell'energia, è importante illustrare i vantaggi e altre considerazioni relative alla distribuzione di una soluzione basata sul cloud.

Il vantaggio principale di una soluzione basata sul cloud è probabilmente il costo. Dal momento che la soluzione fa uso di componenti distribuiti nel cloud, non c'è una componente COGS (costo del venduto) o costi iniziali associati. Ciò significa che non è necessario investire in hardware, software e manutenzione IT, con una riduzione sostanziale del rischio d'impresa.

Un altro vantaggio importante è la struttura dei costi con pagamento in base al consumo delle soluzioni basate sul cloud. I server basati sul cloud destinati al calcolo o all'archiviazione possono essere distribuiti e ridimensionati in base alle necessità. Questa è la vera efficienza economica di una soluzione basata sul cloud.

Infine, non è necessario investire nella manutenzione IT o nello sviluppo futuro dell'infrastruttura, perché è tutto incluso nell'offerta basata sul cloud. Sotto questo aspetto, la suite Cortana Intelligence include servizi di altissimo livello e l'offerta è in continua evoluzione. Nuove funzionalità e nuovi componenti vengono introdotti e sviluppati continuamente.

Per le aziende che hanno appena iniziato la transizione al cloud, è consigliabile adottare un approccio graduale implementando un piano di migrazione cloud. Per le aziende che operano nel settore dell'energia e dei pubblici servizi, i casi d'uso illustrati in questo studio possono rappresentare un'ottima opportunità per l'introduzione di soluzioni di analisi predittiva nel cloud.

#### <a name="business-case-justification-considerations"></a>Considerazioni per la motivazione dei casi aziendali
In molti casi il cliente può essere interessato a una motivazione aziendale per un caso d'uso specifico in cui una soluzione basata sul cloud e Machine Learning siano componenti importanti. A differenza di una soluzione locale, nel caso di una soluzione basata sul cloud i costi iniziali sono minimi e la maggior parte degli elementi di costo è associata all'uso effettivo. Per la distribuzione di una soluzione di previsione energetica nella suite Cortana Intelligence, è possibile integrare più servizi in un'unica struttura dei costi comune. Ad esempio, è possibile usare database come*SQL Azure*per archiviare i dati non elaborati, mentre per le previsioni effettive viene usato Azure ML per ospitare i servizi di previsione. In questo esempio la struttura dei costi potrebbe includere componenti transazionali e di archiviazione.

È importante avere una buona conoscenza del valore commerciale di una previsione della domanda di energia, che sia a breve o a lungo termine. Di fatto, è importante conoscere il valore commerciale di ogni operazione di previsione. Ad esempio, la previsione accurata del carico elettrico per le 24 ore successive può evitare la sovrapproduzione e contribuire a prevenire i sovraccarichi della rete. Tutto questo può essere quantificato in termini di risparmio economico su base giornaliera.

Di seguito è riportata una formula di base per il calcolo del vantaggio economico della soluzione di previsione della domanda: ![Formula di base per il calcolo del vantaggio economico della soluzione di previsione della domanda](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Dal momento che la suite Cortana Intelligence offre un modello tariffario con pagamento in base al consumo, non è necessario inserire una componente di costo fisso in questa formula. La formula può essere calcolata su base giornaliera, mensile o annuale.

I piani tariffari correnti della suite Cortana Intelligence e di Azure ML sono disponibili [qui](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Processo di sviluppo di soluzioni
Il ciclo di sviluppo di una soluzione per la previsione della domanda di energia prevede in genere quattro fasi, in ognuna delle quali si usano tecnologie e servizi basati sul cloud all'interno della suite Cortana Intelligence.

Il ciclo è illustrato nel diagramma seguente:

![Ciclo della smart grid](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Di seguito viene descritto questo processo in quattro fasi:

1. **Raccolta dei dati**: qualsiasi soluzione basata sull'analisi avanzata si fonda sui dati. Vedere in proposito la sezione **Informazioni sui dati**. In particolare, la previsione e l'analisi predittiva si basano su un flusso di dati dinamico e continuo. Per la previsione della domanda di energia, questi dati possono derivare direttamente da contatori intelligenti o essere già aggregati in un database locale. È possibile basarsi anche su altre origini dati esterne, ad esempio le condizioni meteorologiche e la temperatura. Questo flusso di dati continuo deve essere orchestrato, pianificato e archiviato. [Azure Data Factory](http://azure.microsoft.com/services/data-factory/) è lo strumento principale per eseguire questa attività.
2. **Modellazione** : per ottenere previsioni energetiche accurate e affidabili è necessario sviluppare, eseguire il training, e gestire un modello per l'uso dei dati storici e l'estrazione di schemi significativi e predittivi dai dati. L'area di Machine Learning (ML) sta crescendo rapidamente grazie al continuo sviluppo di algoritmi sempre più avanzati. Azure ML Studio offre un'esperienza utente eccezionale che permette di usare gli algoritmi di apprendimento automatico più avanzati all'interno di un flusso di lavoro completo. Il flusso di lavoro, illustrato di seguito in un diagramma di flusso intuitivo, include la preparazione dei dati, l'estrazione delle funzionalità, la modellazione e la valutazione del modello. L'utente può inserire centinaia di modelli diversi inclusi in questo ambiente. Al termine di questa fase, un data scientist avrà a disposizione un modello di lavoro interamente valutato e pronto per la distribuzione.
   
   Il diagramma seguente illustra un flusso di lavoro tipico:
   
   ![Flusso di lavoro di modellazione](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Distribuzione** : con un modello di lavoro a disposizione, il passaggio successivo è la distribuzione. Il modello viene convertito in un servizio Web che espone un'API RESTful che può essere richiamata contemporaneamente tramite Internet dai vari client di utilizzo. Azure ML consente la semplice distribuzione di un modello direttamente da Azure ML Studio, con un solo clic. L'intero processo di distribuzione avviene in background. Questa soluzione consente la scalabilità automatica in base alle esigenze di consumo.
4. **Consumo** : in questa fase si fa effettivamente uso del modello di previsione per generare previsioni. Il consumo può essere determinato da un'applicazione utente,*ad esempio*un dashboard, o direttamente da un sistema operativo, ad esempio un sistema di bilanciamento della domanda e dell'offerta o una soluzione di ottimizzazione della rete. Più casi d'uso possono essere determinati da un unico modello.

## <a name="data-understanding"></a>Informazioni sui dati
Dopo aver discusso le considerazioni commerciali relative a una soluzione per la previsione della domanda di energia nella sezione **Informazioni commerciali**, verrà ora discussa la parte relativa ai dati. Qualsiasi soluzione di analisi predittiva si basa su dati affidabili. Per la previsione della domanda di energia ci si basa su dati storici relativi al consumo con vari livelli di granularità. Tali dati storici vengono trattati come dati non elaborati. Vengono sottoposti a un'attenta analisi in cui il data scientist identifica i predittori, anche detti funzionalità, da inserire in un modello che poi genererà le previsioni richieste.

In questa sezione vengono descritti i vari passaggi e le considerazioni che permettono di comprendere i dati e come ottenerli in un formato utilizzabile.

### <a name="the-model-development-cycle"></a>Ciclo di sviluppo del modello
Per generare un buon modello di previsione è necessario eseguire un'attenta preparazione e pianificazione. Suddividere il processo di modellazione in più passaggi e concentrarsi su un passaggio alla volta può migliorare notevolmente il risultato dell'intero processo.

Il diagramma seguente illustra come è possibile suddividere in più passaggi il processo di modellazione:

![Ciclo di sviluppo del modello](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Il ciclo è costituito da sei passaggi:

* Formulazione del problema.
* Inserimento ed esplorazione dei dati.
* Preparazione dei dati e progettazione di funzionalità.
* Modellazione
* Valutazione del modello.
* Sviluppo.

La parte restante di questa sezione è dedicata alla descrizione dei singoli passaggi e dei relativi elementi da considerare.

### <a name="problem-formulation"></a>Formulazione del problema.
La formulazione del problema è probabilmente il passaggio più importante da intraprendere prima di implementare qualsiasi soluzione di analisi predittiva. In questo passaggio il problema aziendale viene trasformato e scomposto in elementi specifici che possono essere risolti usando tecniche di modellazione e dati. È buona norma formulare il problema come una serie di domande alle quali si cerca risposta. Di seguito sono riportate alcune possibili domande applicabili anche all'ambito della previsione della domanda di energia:

* Qual è il carico previsto in una singola sottostazione nell'ora o nel giorno successivo?
* A che ora del giorno si verifica il picco della domanda nella rete?
* Qual è la probabilità che la rete riesca a sostenere il carico di picco previsto?
* Quanta energia deve generare la centrale elettrica durante ogni ora del giorno?

La formulazione di queste domande porta l'attenzione sui dati corretti da ottenere e permette di implementare una soluzione completamente in linea con le esigenze aziendali. È anche possibile impostare alcune metriche chiave che consentono di valutare le prestazioni del modello. Ad esempio, è possibile impostare la precisione richiesta per la previsione e il margine di errore accettabile per l'azienda.

### <a name="data-sources"></a>Origini dati
Le smart grid moderne raccolgono dati da vari componenti e parti della rete. Questi dati rappresentano i diversi aspetti del funzionamento e dell'utilizzo della rete elettrica. Nell'ambito della previsione della domanda di energia questa discussione prende in considerazione le origini dati che riflettono il consumo effettivo. Un'origine dati importante per il consumo energetico è costituita dai contatori intelligenti. Le aziende di pubblici servizi in tutto il mondo stanno rapidamente distribuendo i contatori intelligenti agli utenti. I contatori intelligenti registrano il consumo energetico effettivo e inoltrano continuamente i dati all'azienda. I dati vengono raccolti e inviati a intervalli fissi compresi tra cinque minuti e un'ora. I contatori intelligenti più avanzati possono essere programmati in modalità remota per controllare e bilanciare il consumo effettivo nell'abitazione. I dati forniti dai contatori intelligenti sono relativamente affidabili e includono un timestamp. Questo li rende un elemento importante per la previsione della domanda. I dati dei contatori possono essere aggregati e riepilogati a vari livelli nella topologia della rete, in base a trasformatore, sottostazione, area *e così via*. È quindi possibile selezionare il livello di aggregazione necessario per compilare il relativo modello di previsione. Se, ad esempio, l'azienda di pubblici servizi vuole prevedere il carico futuro in ognuna delle sottostazioni della rete, è possibile aggregare i dati di tutti i contatori per ogni singola sottostazione e usarli come input per il modello di previsione. I contatori intelligenti sono considerati un'origine dati interna.

Perché una previsione della domanda di energia sia affidabile è necessario avere a disposizione anche origini dati esterne. Un fattore importante che influisce sul consumo energetico sono le condizioni meteorologiche e, in particolare, la temperatura. I dati storici mostrano una stretta correlazione tra temperatura esterna e consumo energetico. Con il caldo estivo aumenta il consumo dovuto ai condizionatori d'aria, mentre con il freddo invernale il consumo è dovuto ai sistemi di riscaldamento. È quindi molto importante avere a disposizione una fonte affidabile di dati storici sulle temperature in corrispondenza della posizione della rete. Una previsione accurata delle temperature è un altro predittore del consumo energetico.

Anche altre origini dati esterne possono contribuire alla compilazione di modelli di previsione della domanda di energia. Sono inclusi,*ad esempio*, i cambiamenti climatici a lungo termine, gli indici economici come il PIL e così via. In questo documento non vengono trattate le altre origini dati.

### <a name="data-structure"></a>Struttura dei dati
Dopo aver identificato le origini dati necessarie, occorre verificare che i dati non elaborati raccolti includano le funzionalità dei dati corrette. Per compilare un modello di previsione della domanda affidabile, è necessario assicurarsi che i dati raccolti includano elementi di dati che consentono di prevedere la domanda futura. Di seguito sono riportati alcuni requisiti di base relativi alla struttura dei dati, ovvero lo schema, per i dati non elaborati.

I dati non elaborati sono costituiti da righe e colonne. Ogni misurazione è rappresentata come una singola riga di dati. Ogni riga di dati include più colonne, dette anche campi o funzionalità.

1. **Timestamp** : il campo timestamp rappresenta l'ora effettiva in cui è stata registrata la misurazione. Deve essere conforme a uno dei formati di data/ora più comuni. Devono essere incluse sia la data che l'ora. Nella maggior parte dei casi non è necessario registrare l'ora fino al secondo livello di granularità. È importante specificare il fuso orario in cui i dati vengono registrati.
2. **ID contatore** : questo campo identifica il contatore o il dispositivo di misurazione. È una variabile di categoria e può essere una combinazione di numeri e lettere.
3. **Valore di consumo**: il consumo effettivo in una specifica data/ora. Il consumo può essere misurato in chilowattora (kWh) o in qualsiasi altra unità di misura, purché questa rimanga coerente in tutte le misurazioni dei dati. In alcuni casi il consumo può essere fornito in tre fasi di alimentazione. In tal caso è necessario raccogliere i dati relativi a tutte le fasi di consumo indipendenti.
4. **Temperatura**: in genere la temperatura viene raccolta da un'origine indipendente, ma deve essere compatibile con i dati sul consumo. Deve includere un timestamp come descritto in precedenza che ne consente la sincronizzazione con i dati sul consumo effettivo. Il valore della temperatura può essere specificato in gradi Celsius o gradi Fahrenheit, ma deve rimanere coerente in tutte le misurazioni.
5. **Posizione** : il campo della posizione è in genere associato al luogo in cui sono stati raccolti i dati di temperatura. Può essere rappresentata come codice di avviamento postale o sotto forma di latitudine/longitudine.

Le tabelle seguenti illustrano esempi di dati sul consumo e sulla temperatura in un formato valido:

| **Data** | **Ora** | **ID contatore** | **Fase 1** | **Fase 2** | **Fase 3** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **Data** | **Ora** | **Posizione** | **Temperatura** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Come illustrato sopra, questo esempio include tre valori diversi per il consumo associati a tre fasi di alimentazione. I campi di data e ora sono separati, ma possono anche essere combinati in un'unica colonna. In questo caso la colonna della posizione è rappresentata nel formato CAP a cinque cifre e la temperatura è espressa in gradi Celsius.

### <a name="data-format"></a>Formato dati
Cortana Intelligence Suite supporta i formati di dati più comuni come CSV, TSV, JSON *e così via*. È importante che il formato dati rimanga coerente per l'intero ciclo di vita del progetto.

### <a name="data-ingestion"></a>Inserimento di dati
Dal momento che la previsione della domanda di energia viene eseguita in modo frequente e costante, è necessario garantire il flusso dei dati non elaborati attraverso un processo di inserimento dati solido e affidabile. Il processo di inserimento deve garantire la disponibilità dei dati non elaborati per la previsione quando richiesto. Ciò significa che la frequenza di inserimento dati deve essere maggiore della frequenza di previsione.

Ad esempio: se la soluzione di previsione della domanda genera una nuova previsione ogni mattina alle 8.00, è necessario assicurarsi che tutti i dati raccolti durante le 24 ore precedenti siano stati inseriti fino a quel momento, inclusi i dati dell'ultima ora.

A questo scopo, la suite Cortana Intelligence offre diversi modi per supportare un processo di inserimento dati affidabile. Questo punto verrà approfondito nella sezione **Distribuzione** di questo documento.

### <a name="data-quality"></a>Qualità dei dati
L'origine dei dati non elaborati necessaria per eseguire previsioni della domanda affidabili e accurate deve soddisfare alcuni criteri di base relativi alla qualità dei dati. Anche se è possibile usare metodi statistici avanzati per compensare un eventuale problema di qualità dei dati, è necessario assicurarsi che la qualità dei nuovi dati inseriti sia superiore a una certa soglia di base. Di seguito sono riportate alcune considerazioni sulla qualità dei dati non elaborati:

* **Valore mancante** : si riferisce a una situazione in cui non sono state raccolte misurazioni specifiche. Il requisito di base in questo caso è che il tasso di valori mancanti non superi il 10% per un determinato periodo di tempo. Un singolo valore mancante dovrà essere indicato usando un valore predefinito, ad esempio "9999", e non "0" che può essere una misurazione valida.
* **Precisione della misurazione** : è necessario registrare in modo accurato il valore effettivo del consumo o della temperatura. Misurazioni poco accurate generano previsioni imprecise. In genere, l'errore di misurazione deve essere inferiore all'1% rispetto al valore reale.
* **Ora della misurazione** : la differenza tra il timestamp effettivo dei dati raccolti e l'ora della effettiva della misurazione non deve superare i 10 secondi.
* **Sincronizzazione** : quando si usano più origini dati,*ad esempio*il consumo e la temperatura, è necessario assicurarsi che non presentino problemi di sincronizzazione. Ciò significa che la differenza tra il timestamp raccolto da due origini dati indipendenti qualsiasi non deve superare i 10 secondi.
* **Latenza**: come illustrato nella sezione **Inserimento di dati**, un flusso di dati affidabile e il processo di inserimento sono componenti fondamentali. Per controllarli è necessario controllare la latenza dei dati. Questa è definita come la differenza tra l'ora in cui la misurazione effettiva è stata eseguita e l'ora in cui è stata caricata nell'archivio della suite Cortana Intelligence, pronta per l'uso. Per la previsione di carico a breve termine la latenza totale non deve essere maggiore di 30 minuti. Per la previsione di carico a lungo termine la latenza totale non deve essere maggiore di un giorno.

### <a name="data-preparation-and-feature-engineering"></a>Preparazione dei dati e progettazione di funzionalità.
Dopo aver inserito i dati non elaborati come indicato nella sezione **Inserimento di dati** e averli archiviati in modo sicuro, è possibile procedere con l'elaborazione. La fase di preparazione dei dati consiste principalmente nel prendere i dati non elaborati e convertirli o trasformarli in un formato adatto alla fase di modellazione. La preparazione può includere operazioni semplici, usando ad esempio la colonna di dati non elaborati con il valore misurato effettivo o valori standardizzati, oppure operazioni più complesse, come ad esempio l'[uso di un operatore di ritardo](https://en.wikipedia.org/wiki/Lag_operator). Le colonne di dati appena create sono dette funzionalità dei dati e il relativo processo di generazione è detto progettazione di funzionalità. Al termine di questo processo sarà disponibile un nuovo set di dati derivato dai dati non elaborati che potrà essere usato per la modellazione. Durante la fase di preparazione dei dati è necessario anche inserire eventuali valori mancanti. Vedere in proposito la sezione **Qualità dei dati**. In alcuni casi, potrebbe essere necessario normalizzare i dati per fare in modo che tutti i valori siano rappresentati in modo coerente.

Questa sezione illustra alcune delle funzionalità dei dati più comuni incluse nei modelli di previsione della domanda di energia.

**Funzionalità basate sul tempo:** queste funzionalità vengono derivate dai dati relativi a data e timestamp. Tali dati vengono estratti e convertiti in funzionalità categoriche quali:

* Ora del giorno: rappresenta l'ora del giorno, accetta valori compresi tra 0 e 23.
* Giorno della settimana: rappresenta il giorno della settimana, accetta valori compresi tra 1 (domenica) e 7 (sabato).
* Giorno del mese: rappresenta la data effettiva, accetta valori compresi tra 1 e 31.
* Mese dell'anno: rappresenta il mese, accetta valori compresi tra 1 (gennaio) e 12 (dicembre).
* Fine settimana: funzionalità con valore binario che accetta i valori 0 (giorni della settimana) o 1 (fine settimana).
* Festivo: funzionalità con valore binario che accetta i valori 0 (giorni feriali) o 1 (giorni festivi).
* Termini di Fourier: i termini di Fourier sono pesi derivati dal timestamp e vengono usati per acquisire la stagionalità, vale a dire i cicli, nei dati. Dato che nei dati sono presenti più stagioni, possono essere necessari più termini di Fourier. Ad esempio, se i valori della domanda hanno stagioni/cicli annuali, settimanali e giornalieri saranno necessari tre termini di Fourier.

**Funzionalità di misurazione indipendenti:** le funzionalità indipendenti includono tutti gli elementi di dati da usare come predittori nel modello. Qui viene esclusa la funzionalità dipendente di cui deve essere eseguita la previsione.

* Funzione Lag: esegue lo spostamento temporale dei valori della domanda effettivi. Ad esempio, in caso di dati con cadenza oraria, la funzione Lag 1 conserva il valore della domanda nell'ora precedente rispetto al timestamp corrente. Analogamente è possibile aggiungere le funzioni Lag 2, Lag 3 *e così via*. La combinazione effettiva delle funzioni Lag usate viene stabilita durante la fase di modellazione attraverso la valutazione dei risultati del modello.
* Tendenza a lungo termine: rappresenta la crescita lineare della domanda negli anni.

**Funzionalità dipendente:** la funzionalità dipendente è la colonna di dati di cui il modello deve eseguire la previsione. Con l' [apprendimento supervisionato](https://en.wikipedia.org/wiki/Supervised_learning)è necessario prima di tutto eseguire il training del modello usando le funzionalità dipendenti, anche dette etichette. In questo modo il modello può apprendere gli schemi contenuti nei dati associati alla funzionalità dipendente. Nella previsione della domanda di energia in genere si cerca di prevedere la domanda effettiva, che viene quindi usata come funzionalità dipendente.

**Gestione dei valori mancanti:** durante la fase di preparazione dei dati è necessario stabilire quale sia la strategia migliore per gestire i valori mancanti. Nella maggior parte dei casi questi vengono gestiti usando vari [metodi statistici di imputazione dei dati](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). Nella previsione della domanda di energia, in genere l'imputazione dei valori mancanti viene eseguita usando la media mobile calcolata sui punti dati precedenti disponibili.

**Normalizzazione dei dati:** la normalizzazione dei dati è un altro tipo di trasformazione che viene usato per uniformare i dati numerici, come quelli usati nella previsione della domanda. Questo permette di migliorare l'accuratezza e la precisione del modello. In genere questa operazione viene eseguita dividendo il valore effettivo per l'intervallo dei dati.
Il valore originale risulta quindi ridotto a un intervallo più piccolo, in genere compreso tra -1 e 1.

## <a name="modeling"></a>Modellazione
La fase di modellazione è quella in cui i dati vengono convertiti in un modello. Alla base di questo processo ci sono algoritmi avanzati che analizzano i dati storici, o dati di training, ne estrapolano gli schemi e compilano un modello. Tale modello può essere usato successivamente per eseguire previsioni su nuovi dati non ancora usati per la compilazione del modello.

Dopo aver ottenuto un modello affidabile funzionante, è possibile usarlo per il calcolo del punteggio dei nuovi dati strutturati in modo da includere le funzionalità necessarie (X). Il processo di calcolo del punteggio farà uso del modello persistente, proveniente dalla fase di training, per prevedere la variabile di destinazione, contrassegnata da Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Tecniche di modellazione della previsione della domanda
Nella previsione della domanda si usano i dati storici in ordine cronologico. I dati che includono la dimensione temporale sono in genere definiti [serie storiche](https://en.wikipedia.org/wiki/Time_series). Nella modellazione delle serie storiche l'obiettivo è identificare le tendenze legate al tempo, la stagionalità, l'auto-correlazione o correlazione nel tempo ed elaborare tali informazioni in un modello.

Negli ultimi anni sono stati sviluppati algoritmi avanzati per la previsione basata sulle serie storiche e per migliorare l'accuratezza delle previsioni. Alcuni di questi vengono illustrati brevemente in questa sezione,

> [!NOTE]
> che non intende fornire una panoramica dell'apprendimento automatico e delle previsioni ma piuttosto un breve sguardo sulle tecniche di modellazione comunemente usate per la previsione della domanda. Per altre informazioni e materiale didattico sulla previsione basata sulle serie storiche, vedere il libro online [Forecasting: principles and practice](https://www.otexts.org/book/fpp).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**Media mobile**](https://www.otexts.org/fpp/6/2)
La media mobile è una delle prime tecniche di analisi usate per la previsione basata sulle serie storiche ed è ancora oggi una delle più diffuse. È anche la base di tecniche di previsione più avanzate. Con la media mobile viene previsto il punto dati successivo calcolando la media dei K punti più recenti, dove K indica l'ordine della media mobile.

La tecnica della media mobile ha un effetto di smorzamento sulla previsione e potrebbe non gestire bene una grande volatilità dei dati.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**Smorzamento esponenziale**](https://www.otexts.org/fpp/7/5)
Lo smorzamento esponenziale raggruppa una famiglia di metodi che usano la media ponderata dei punti dati recenti per prevedere il punto dati successivo. Ai valori più recenti viene assegnato un peso maggiore che diminuisce gradualmente man mano che i valori misurati si fanno meno recenti. I metodi di questa famiglia sono diversi e alcuni includono la gestione della stagionalità nei dati, ad esempio il [modello con stagionalità Holt Winters](https://www.otexts.org/fpp/7/5).

Alcuni di questi metodi tengono anche conto della stagionalità dei dati.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**Modello autoregressivo integrato a media mobile (ARIMA)**](https://www.otexts.org/fpp/8)
Il modello autoregressivo integrato a media mobile (ARIMA) raggruppa un'altra famiglia di metodi comunemente usati per la previsione basata sulle serie storiche. Unisce i metodi autoregressivi alla media mobile. I metodi autoregressivi usano i modelli regressivi prendendo i valori delle serie storiche precedenti per calcolare il punto dati successivo. I metodi ARIMA applicano anche metodi di differenziazione che prevedono il calcolo della differenza tra i punti dati e l'uso di questi anziché del valore misurato originale. Il modello ARIMA fa anche uso delle tecniche con media mobile illustrate in precedenza. La combinazione in vari modi di tutti questi metodi costituisce la famiglia di metodi ARIMA.

ARIMA e smorzamento esponenziale sono ampiamente usati per la previsione della domanda di energia e per molti altri problemi di previsione. In molti casi questi metodi vengono combinati per fornire una maggiore accuratezza dei risultati.

**Regressione multipla** : i modelli regressivi sono probabilmente l'approccio più importante alla modellazione in ambito statistico e di apprendimento automatico. Nel contesto delle serie storiche si usa la regressione per prevedere i valori futuri,*ad esempio*quelli della domanda. Nella regressione si prende una combinazione lineare dei predittori e se ne ricavano i pesi, anche detti coefficienti, durante il processo di training. L'obiettivo è produrre una linea di regressione che permetterà di prevedere il valore previsto. I metodi regressivi possono essere usati quando la variabile di destinazione è numerica e quindi adatta anche alla previsione basata sulle serie storiche. La vasta gamma dei metodi regressivi esistenti include modelli regressivi molto semplici, come la [regressione lineare](https://en.wikipedia.org/wiki/Linear_regression), e altri più avanzati, come gli alberi di decisione, le [foreste casuali](https://en.wikipedia.org/wiki/Random_forest), le [reti neurali](https://en.wikipedia.org/wiki/Artificial_neural_network) e gli alberi di decisione con boosting.

Formulare la previsione della domanda di energia come un problema di regressione offre una notevole flessibilità nella selezione delle funzionalità dei dati, che possono essere combinate dai dati della serie storica della domanda effettiva e da fattori esterni come la temperatura. Per altre informazioni sulle funzionalità selezionate, vedere la sezione **Preparazione dei dati e progettazione di funzionalità**di questo studio.

L'esperienza nell'implementazione e nella distribuzione del progetto pilota di previsione della domanda di energia mostra che i modelli regressivi avanzati disponibili in Azure ML tendono a produrre i risultati migliori.

## <a name="model-evaluation"></a>Valutazione del modello.
La valutazione ha un ruolo fondamentale nel **ciclo di sviluppo del modello**. In questa fase vengono esaminate la convalida del modello e le relative prestazioni con dati reali. Nella fase di modellazione viene usata una parte dei dati disponibili per il training del modello. Nella fase di valutazione viene usato il resto dei dati per testare il modello. In sostanza, nel modello vengono inseriti nuovi dati che sono stati ristrutturati e contengono le stesse funzionalità del set di dati di training. Tuttavia, durante il processo di convalida il modello viene usato per prevedere la variabile di destinazione anziché per fornire la variabile di destinazione disponibile. Questo processo è anche detto calcolo del punteggio del modello. Vengono quindi usati i valori di destinazione reali e messi a confronto con quelli previsti. L'obiettivo è misurare e ridurre al minimo l'errore di previsione, vale a dire la differenza tra il valore previsto e il valore reale. Quantificare la misura dell'errore è fondamentale per ottimizzare il modello e stabilire se l'errore viene effettivamente ridotto. Per ottimizzare il modello, è possibile modificarne i parametri che controllano il processo di apprendimento oppure aggiungere o rimuovere funzionalità dei dati. Questa operazione è detta [sweep dei parametri](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep). In sostanza, ciò significa che potrebbe essere necessario eseguire più volte l'iterazione tra le fasi di progettazione di funzionalità, modellazione e valutazione del modello, fino a ridurre l'errore al livello richiesto.

È importante sottolineare che l'errore di previsione non sarà mai uguale a zero perché non esiste un modello che possa prevedere esattamente qualsiasi risultato. Tuttavia, una certa misura dell'errore è accettabile per l'azienda. Durante il processo di convalida si vuole fare in modo che l'errore di previsione del modello sia pari o inferiore alla soglia di tolleranza dell'azienda. È quindi importante impostare il livello di errore tollerabile all'inizio del ciclo, durante la fase di **formulazione del problema** .

### <a name="typical-evaluation-techniques"></a>Tecniche di valutazione tipiche
L'errore di previsione può essere misurato e quantificato in vari modi. Questa sezione illustra le tecniche di valutazione relative alle serie storiche e in particolare alla previsione della domanda di energia.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE è l'acronimo di Mean Absolute Percentage Error, errore medio assoluto percentuale. Il MAPE permette di calcolare la differenza tra ogni punto previsto e il valore effettivo di tale punto. È quindi possibile quantificare l'errore per ogni punto calcolando la proporzione tra la differenza e il valore effettivo. L'ultimo passaggio consiste nel calcolare la media di questi valori. La formula matematica usata per il MAPE è la seguente:

![Formula MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
* Dove A<sub>t</sub> è il valore effettivo, F<sub>t</sub> è il valore previsto e n è l'orizzonte di previsione.*

## <a name="deployment"></a>Distribuzione
Dopo aver stabilito la fase di modellazione e convalidato le prestazioni del modello, è possibile iniziare la fase di distribuzione. In questo contesto, distribuzione significa permettere al cliente di utilizzare il modello per eseguire previsioni effettive su larga scala. Il concetto di distribuzione è fondamentale in Azure ML, perché l'obiettivo principale è richiamare continuamente le previsioni anziché ottenere semplicemente le informazioni dai dati. La fase di distribuzione è la parte in cui il modello viene abilitato per l'utilizzo su larga scala.

Nel contesto della previsione della domanda di energia, l'obiettivo è richiamare previsioni continue e periodiche garantendo anche la disponibilità di dati aggiornati per il modello e l'invio dei dati di previsione al client di utilizzo.

### <a name="web-services-deployment"></a>Distribuzione di servizi Web
Il principale blocco predefinito distribuibile in Azure ML è il servizio Web. Si tratta del modo più efficace per consentire l'utilizzo di un modello predittivo nel cloud. Il servizio Web incapsula il modello e lo include in un'API [RESTful](http://www.restapitutorial.com/) . L'API (Application Programming Interface) può essere usata in qualsiasi codice client, come illustrato nel diagramma seguente.

![Distribuzione del servizio e consumo](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Come si può notare, il servizio Web viene distribuito nel cloud della suite Cortana Intelligence e può essere richiamato sul relativo endpoint API REST esposto. Il servizio può essere richiamato contemporaneamente da diversi tipi di client in domini differenti tramite l'API Web. La scalabilità del servizio Web permette di supportare migliaia di chiamate simultanee.

### <a name="a-typical-solution-architecture"></a>Esempio di architettura della soluzione tipica
Quando si distribuisce una soluzione di previsione della domanda di energia, l'obiettivo è distribuire una soluzione end-to-end che vada oltre il servizio Web di previsione e gestisca l'intero flusso di dati. Quando si richiama una nuova previsione, è necessario assicurarsi che nel modello vengano immesse funzionalità dei dati aggiornate. Questo significa che i dati non elaborati appena raccolti vengono continuamente inseriti, elaborati e trasformati nel set di funzionalità richiesto in cui è stato compilato il modello. Allo stesso tempo, i dati di previsione devono essere resi disponibili per i client di utilizzo finali. Il diagramma seguente illustra un esempio di ciclo del flusso di dati o pipeline di dati:

![Flusso di dati end-to-end di previsione della domanda di energia](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Il ciclo di previsione della domanda di energia include i passaggi seguenti:

1. Milioni di contatori di dati distribuiti generano continuamente dati sul consumo energetico in tempo reale.
2. I dati vengono raccolti e caricati in un repository cloud,*ad esempio*BLOB di Azure.
3. Prima dell'elaborazione, i dati non elaborati vengono aggregati in una sottostazione o un livello regionale, in base alle indicazioni dell'azienda.
4. Viene quindi eseguita la progettazione di funzionalità, illustrata nella sezione **Preparazione dei dati e progettazione di funzionalità**, che produce i dati necessari per il training o il calcolo del punteggio del modello. I dati del set di funzionalità vengono archiviati in un database, *ad esempio* SQL Azure.
5. Viene richiamato il servizio di re-training per eseguire nuovamente il training del modello di previsione. La versione aggiornata del modello viene mantenuta per l'uso da parte del servizio Web di calcolo del punteggio.
6. Quest'ultimo viene richiamato in base a una pianificazione adeguata alla frequenza di previsione richiesta.
7. I dati di previsione vengono archiviati in un database accessibile dal client di utilizzo finale.
8. Il client di utilizzo recupera le previsioni, le applica alla rete e le utilizza in base al caso d'uso richiesto.

È importante notare che l'intero ciclo è completamente automatizzato e viene eseguito in base a una pianificazione. L'intera orchestrazione del ciclo dei dati può essere eseguita con strumenti come [Azure Data Factory](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Architettura di distribuzione end-to-end
Per distribuire una soluzione di previsione della domanda di energia in Cortana Intelligence, è necessario verificare che i componenti richiesti siano installati e configurati correttamente.

Il diagramma seguente illustra una tipica architettura basata su Cortana Intelligence che implementa e orchestra il ciclo del flusso di dati descritto in precedenza:

![Architettura di distribuzione end-to-end](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Per altre informazioni su ognuno dei componenti e sull'intera architettura, vedere il modello di soluzione energetica.


