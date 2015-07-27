<properties 
    pageTitle="Casi di utilizzo comuni di DocumentDB |Azure" 
    description="Informazioni sui primi cinque casi di utilizzo per DocumentDB: contenuto generato dall'utente, registrazione di eventi, dati di catalogo, dati delle preferenze utente e ";Internet delle cose"; (IoT)." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/10/2015" 
    ms.author="hawong"/>

# Casi di utilizzo comuni di DocumentDB
Questo articolo fornisce una panoramica di alcuni casi di utilizzo comuni per DocumentDB. I consigli forniti in questo articolo sono un punto di partenza per sviluppare l'applicazione con DocumentDB.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:
 
- Quali sono i casi di utilizzo comuni per DocumentDB?
- Quali sono i vantaggi dell'uso di DocumentDB come archivio contenuto generato dall'utente?
- Quali sono i vantaggi dell'uso di DocumentDB come archivio dati di catalogo?
- Quali sono i vantaggi dell'uso di DocumentDB come archivio di registri eventi?
- Quali sono i vantaggi dell'uso di DocumentDB come archivio dati di preferenze utente?
- Quali sono i vantaggi dell'uso di DocumentDB come archivio dati per sistemi Internet delle cose (IoT)?

    
## Contenuto generato dall'utente
Un caso di utilizzo comune per DocumentDB prevede l'archiviazione e la query del contenuto generato dall'utente (UGC, User Generated Content) per applicazioni Web e mobili, soprattutto applicazioni per social media. Alcuni esempi di UGC includono sessioni di chat, tweet, post di blog, classificazioni e commenti. Spesso il contenuto UGC nelle applicazioni per social media comprende testo in formato libero, proprietà, tag e relazioni non limitati da una struttura rigida.

I contenuti come chat, commenti e post possono essere archiviati in DocumentDB senza richiedere trasformazioni o livelli di mapping relazionale a oggetti complessi. Le proprietà dei dati possono essere aggiunte o modificate facilmente in modo che rispondano ai requisiti quando gli sviluppatori scorrono il codice dell'applicazione, velocizzando così la fase di sviluppo.

Le applicazioni che si integrano con diversi social network devono rispondere alla modifica degli schemi di queste reti. I dati, man mano che vengono automaticamente indicizzati in DocumentDB per impostazione predefinita, sono pronti per l'esecuzione di query in qualsiasi momento. Queste applicazioni hanno quindi la flessibilità necessaria per recuperare le proiezioni in base alle rispettive esigenze.

Molte applicazioni di social networking vengono eseguite su scala globale e possono presentare modelli di utilizzo non prevedibili. La flessibilità nella scalabilità dell'archivio dati è essenziale man mano che il livello dell'applicazione viene scalato in modo da soddisfare la domanda di utilizzo. È possibile scalare orizzontalmente aggiungendo altre partizioni di dati con un account DocumentDB. Si possono anche creare altri account DocumentDB in più aree. Per la disponibilità delle aree del servizio DocumentDB, vedere [Aree di Azure](http://azure.microsoft.com/regions/#services).

## Dati del catalogo
Gli scenari di utilizzo dei dati di un catalogo includono l'archiviazione e l'esecuzione di query di un set di attributi per entità come persone, luoghi e prodotti. Gli account utente, i cataloghi di prodotti, i Registri di sistema dei dispositivi per IoT e i sistemi di distinta base sono alcuni esempi di dati di catalogo. Gli attributi di questi dati possono variare e cambiare nel corso del tempo per rispondere ai requisiti dell'applicazione.

Considerare l'esempio di un catalogo di prodotti per un fornitore di parti di ricambio per automobili. Ogni ricambio può avere propri attributi oltre ai comuni attributi condivisi da tutti i ricambi. Inoltre, gli attributi di un ricambio specifico possono cambiare l'anno seguente quando viene rilasciato un nuovo modello. Come archivio documenti JSON, DocumentDB supporta schemi flessibili e consente di rappresentare i dati con proprietà annidate ed è pertanto adatto all'archiviazione dei dati di un catalogo di prodotti.

## Dati di registro
La registrazione dell'applicazione spesso viene emessa in volumi di grandi dimensioni e può avere diversi attributi basati sulla versione dell'applicazione distribuita o sugli eventi di registrazione dei componenti. I dati di registro non sono limitati da strutture rigide o relazioni complesse. I dati di registro vengono sempre più resi persistenti in formato JSON perché JSON è leggero e facilmente leggibile.
   
In genere esistono due casi di utilizzo principali correlati ai dati dei registri eventi. Il primo caso di utilizzo è l'esecuzione di query ad hoc su un subset di dati per la risoluzione dei problemi. Durante la risoluzione dei problemi, un subset di dati viene prima recuperato dai registri, in genere in base alla serie temporale. Successivamente viene eseguito un drill-down filtrando il set di dati in base ai livelli di errore o ai messaggi di errore. Questo è il vantaggio di archiviare i registri eventi in DocumentDB. I dati dei registri archiviati in DocumentDB vengono automaticamente indicizzati per impostazione predefinita e sono quindi pronti per l'esecuzione di query in qualsiasi momento. Inoltre, dati dei registri possono essere resi persistenti nelle partizioni di dati come serie temporali. I registri meno recenti possono essere distribuiti nell'archiviazione offline sicura in base ai criteri di conservazione.

Il secondo caso di utilizzo prevede l'esecuzione offline prolungata di processi di analisi dei dati su un grande volume di dati di registro. L'analisi della disponibilità del server, analisi degli errori dell'applicazione e l'analisi dei dati clickstream sono esempi di questo caso di utilizzo. Per eseguire questi tipi di analisi di solito viene usato Hadoop. Con il connettore Hadoop per DocumentDB, i database DocumentDB funzionano come origini dati e sink per processi Pig, Hive e MapReduce. Per altre informazioni sul connettore Hadoop per DocumentDB, vedere [Eseguire un processo Hadoop con DocumentDB e HDInsight](documentdb-run-hadoop-with-hdinsight/).

## Dati delle preferenze utente
Al giorno d'oggi, le più moderne applicazioni Web e mobili offrono esperienze e visualizzazioni complesse. Queste visualizzazioni ed esperienze sono in genere dinamiche, in grado di soddisfare le preferenze o i feedback e le esigenze di personalizzazione degli utenti. È quindi necessario che le applicazioni possano recuperare le impostazioni personalizzate in modo efficace per eseguire rapidamente il rendering delle esperienze e degli elementi dell'interfaccia utente.

JSON è un formato adatto a rappresentare i dati di layout dell'interfaccia utente, essendo non solo leggero, ma anche facilmente interpretabile da JavaScript. DocumentDB offre livelli di coerenza regolabili che consentono letture veloci con scritture a bassa latenza. Di conseguenza, l'archiviazione dei dati di layout dell'interfaccia utente, incluse le impostazioni personalizzate, come documenti JSON in DocumentDB è un metodo efficace per avere questi dati in rete.

## Dati di sensori dei dispositivi
I casi di utilizzo IoT condividono di solito alcuni modelli di inserimento, elaborazione e archiviazione dei dati. Innanzitutto, questi sistemi consentono l'acquisizione dei dati che permette di inserire grandi quantità di dati dai sensori dei dispositivi di diverse impostazioni locali. In secondo luogo, questi sistemi elaborano e analizzano i dati di streaming per ottenere informazioni in tempo reale. In ultimo, ma non meno importante, la maggior parte dei dati, se non tutti, alla fine verrà inserita in un archivio dati per l'esecuzione di query ad hoc e l'analisi offline.

Microsoft Azure offre servizi avanzati che possono essere sfruttati per i casi di utilizzo IoT. Il set di servizi IoT di Azure include Hub eventi di Azure, Azure DocumentDB, Azure Stream Analytics, Hub di notifica di Azure, Azure Machine Learning, Azure HDInsight e PowerBI.

Grandi quantità di dati possono essere acquisite da Hub eventi di Azure che consente di inserire i dati con una velocità effettiva elevata e una bassa latenza. I dati inseriti che devono essere elaborati per ottenere informazioni in tempo reale possono essere incanalati in Azure Stream Analytics per l'analisi in tempo reale. I dati possono essere caricati in DocumentDB per l'esecuzione di query ad hoc. Una volta caricati in DocumentDB, i dati sono pronti per l'esecuzione delle query. I dati in DocumentDB possono essere usati come dati di riferimento durante l'analisi in tempo reale. I dati possono anche essere ulteriormente affinati ed elaborati connettendo i dati di DocumentDB a HDInsight per processi Pig, Hive o MapReduce. I dati affinati vengono quindi ricaricati in DocumentDB per la creazione di report.

Per una soluzione IoT di esempio con DocumentDB, EventHubs e Storm, vedere il [repository hdinsight-storm-examples su GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Per altre informazioni sulle offerte di Azure per IoT, vedere [Creare il proprio "Internet delle cose"](http://www.microsoft.com/it-it/server-cloud/internet-of-things.aspx).

## Passaggi successivi
 
Per iniziare a usare DocumentDB, è possibile creare un [account](http://azure.microsoft.com/pricing/free-trial/) e quindi seguire la [guida alla formazione](documentdb-learning-map.md) per conoscere DocumentDB e trovare le informazioni necessarie.

Oppure, per altre informazioni sui clienti che usano DocumentDB, leggere le seguenti storie dei clienti:

- [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). L'integratore leader di settore offre alle multinazionali una visione globale in pochi minuti con tecnologie cloud flessibili.
- [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Le notizie "intelligenti" forniscono informazioni mirate ai cittadini impegnati. 
- [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Per un uso uniforme dei colori in tutto il mondo, i principali marchi si rivolgono a SGS. E SGS si rivolge ad Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Telenor, leader mondiale, usa il cloud per muoversi con la velocità di una startup. 
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). L'archivio del futuro si basa sulla velocità delle ricerche e su un flusso di dati scorrevole.
 

<!---HONumber=July15_HO3-->