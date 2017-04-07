---
title: Casi d&quot;uso e scenari NoSQL comuni per Azure DocumentDB | Microsoft Docs
description: 'Informazioni sui primi cinque casi d&quot;uso per DocumentDB: contenuto generato dall&quot;utente, registrazione di eventi, dati di catalogo, dati delle preferenze utente e &quot;Internet delle cose&quot; (IoT).'
services: documentdb
author: h0n
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: hawong
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 9a0ba106b4c2aded8aaac673dfba3610bd4ca0fb
ms.lasthandoff: 03/24/2017


---

# <a name="common-documentdb-use-cases"></a>Casi di utilizzo comuni di DocumentDB
Questo articolo fornisce una panoramica di alcuni casi di utilizzo comuni per DocumentDB.  I consigli forniti in questo articolo sono un punto di partenza per sviluppare l'applicazione con DocumentDB.   

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti: 

* Quali sono i casi di utilizzo comuni per DocumentDB?
* Quali sono i vantaggi dell'uso di DocumentDB per le applicazioni di vendita al dettaglio?
* Quali sono i vantaggi dell'uso di DocumentDB come archivio dati per sistemi Internet delle cose (IoT)?
* Quali sono i vantaggi dell'uso di DocumentDB per le applicazioni Web e per dispositivi mobili?

## <a name="common-use-cases-for-documentdb"></a>Casi di utilizzo comuni per DocumentDB
Azure DocumentDB è un database NoSQL generico usato in un'ampia gamma di applicazioni e casi di utilizzo. È un'ottima scelta per le applicazioni che richiedono tempi di risposta nell'ordine dei millisecondi e che devono essere ridimensionate rapidamente. Di seguito sono indicati alcuni attributi di DocumentDB che lo rendono particolarmente adatto ad applicazioni a prestazioni elevate.

* DocumentDB partiziona in modo nativo i dati per la disponibilità e la scalabilità elevate.
* DocumentDB ha un'archiviazione basata su SSD con tempi di risposta a bassa latenza nell'ordine di millisecondi.
* Il supporto di DocumentDB per i livelli di coerenza di tipo eventuale, di sessione e con obsolescenza associata consente un basso rapporto costo-prestazioni. 
* DocumentDB ha un modello di determinazione dei prezzi flessibile e semplice da usare che calcola in modo indipendente lo spazio di archiviazione e la velocità effettiva.
* Il modello di velocità effettiva riservata di DocumentDB consente un approccio in termini di numero di letture/scritture invece che di CPU/memoria/operazioni di I/O al secondo relativi all'hardware sottostante.
* La progettazione di DocumentDB consente la scalabilità di elevati volumi di richieste, nell'ordine di miliardi di richieste al giorno.

Questi attributi sono utili nel caso di applicazioni Web, mobili, di gioco e IoT che necessitano di tempi di risposta ridotti e che devono gestire grandi quantità di letture e scritture. 

## <a name="iot-and-telematics"></a>IoT e dati telematici
I casi di utilizzo IoT condividono di solito alcuni modelli di inserimento, elaborazione e archiviazione dei dati.  Questi sistemi devono prima di tutto inserire grandi quantità di dati dai sensori dei dispositivi di diverse impostazioni locali. In secondo luogo, questi sistemi elaborano e analizzano i dati di streaming per ottenere informazioni in tempo reale. I dati vengono quindi archiviati nell'archiviazione offline sicura per l'analisi batch. Microsoft Azure offre servizi avanzati che possono essere applicati per i casi di utilizzo di IoT, inclusi Hub eventi di Azure, Azure DocumentDB, Analisi di flusso di Azure, Hub di notifica di Azure, Azure Machine Learning, Azure HDInsight e PowerBI. 

![Architettura di riferimento di IoT per Azure DocumentDB](./media/documentdb-use-cases/documentdb-iot.png)

Grandi quantità di dati possono essere acquisite da Hub eventi di Azure che consente di inserire i dati con una velocità effettiva elevata e una bassa latenza. I dati inseriti che devono essere elaborati per ottenere informazioni in tempo reale possono essere incanalati in Analisi di flusso di Azure per l'analisi in tempo reale. I dati possono essere caricati in DocumentDB per l'esecuzione di query ad hoc. Una volta caricati in DocumentDB, i dati sono pronti per l'esecuzione delle query.  I dati in DocumentDB possono essere usati come dati di riferimento durante l'analisi in tempo reale. I dati possono anche essere ulteriormente affinati ed elaborati connettendo i dati di DocumentDB a HDInsight per processi Pig, Hive o MapReduce.  I dati affinati vengono quindi ricaricati in DocumentDB per la creazione di report.   

Per una soluzione IoT di esempio con DocumentDB, EventHubs e Storm, vedere il [repository hdinsight-storm-examples su GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Per altre informazioni sulle offerte di Azure per IoT, vedere [Creare il proprio "Internet delle cose"](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx). 

## <a name="retail-and-marketing"></a>Vendite e marketing
DocumentDB viene spesso usato nel settore della vendita al dettaglio per l'archiviazione dei dati dei cataloghi. Gli scenari di utilizzo dei dati di un catalogo includono l'archiviazione e l'esecuzione di query di un set di attributi per entità come persone, luoghi e prodotti.  Gli account utente, i cataloghi di prodotti, i Registri di sistema dei dispositivi per IoT e i sistemi di distinta base sono alcuni esempi di dati di catalogo.  Gli attributi di questi dati possono variare e cambiare nel corso del tempo per rispondere ai requisiti dell'applicazione.  

Considerare l'esempio di un catalogo di prodotti per un fornitore di parti di ricambio per automobili. Ogni ricambio può avere propri attributi oltre ai comuni attributi condivisi da tutti i ricambi.  Inoltre, gli attributi di un ricambio specifico possono cambiare l'anno seguente quando viene rilasciato un nuovo modello.  Come archivio documenti JSON, DocumentDB supporta schemi flessibili e dati gerarchici ed è pertanto adatto all'archiviazione dei dati di un catalogo di prodotti.

![Architettura di riferimento per un catalogo di vendita al dettaglio per Azure DocumentDB](./media/documentdb-use-cases/documentdb-retail-catalog.png)

 I dati archiviati in DocumentDB possono essere anche integrati con HDInsight per analisi di Big Data tramite processi Pig, Hive o Map/Reduce. Per altre informazioni sul connettore Hadoop per DocumentDB, vedere [Eseguire un processo Hadoop con DocumentDB e HDInsight](documentdb-run-hadoop-with-hdinsight.md).

 ![Architettura di riferimento per ordini di vendita per Azure DocumentDB](./media/documentdb-use-cases/documentdb-retail-orders.png)
## <a name="gaming"></a>Giochi
Il livello database è un componente fondamentale delle applicazioni di gioco. I giochi moderni eseguono l'elaborazione grafica in client mobili/console, ma si basano sul cloud per distribuire contenuti personalizzati come le statistiche di gioco, l'integrazione con i social media e le classifiche dei punteggi. I giochi richiedono spesso latenze di un solo millisecondo per operazioni di lettura e scrittura, per offrire un'esperienza di gioco accattivante. Un database per giochi deve essere veloce e in grado di gestire picchi significativi a livello di frequenza di richieste durante l'avvio di nuovi giochi e gli aggiornamenti delle funzionalità.

DocumentDB viene usato dai come [The Walking Dead: No Man's Land](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) di [Next Games](http://www.nextgames.com/) e [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). DocumentDB offre i vantaggi seguenti agli sviluppatori di giochi:

* DocumentDB consente di aumentare o ridurre le prestazioni in modo flessibile. In questo modo i giochi possono gestire l'aggiornamento del profilo e le statistiche di decine di milioni di giocatori simultanei con una singola chiamata API.
* DocumentDB supporta le letture e le scritture in millisecondi per evitare eventuali ritardi durante il gioco.
* L'indicizzazione automatica di DocumentDB consente di filtrare in base a più proprietà in tempo reale, ad esempio per individuare il giocatore in base al suo ID interno o all'ID di GameCenter, Facebook o Google o per eseguire query in base all'appartenenza del giocatore a una gilda. Questa operazione può essere eseguita senza compilare indicizzazioni complesse o infrastrutture di partizionamento orizzontale.
* Le funzionalità social, ad esempio i messaggi delle chat interne al gioco, l'appartenenza a gilde del giocatore, le sfide completate, le classifiche dei punteggi e i grafici dei social network, sono più facili da implementare con uno schema flessibile.
* DocumentDB usato come PaaS (Platform-as-a-Service) richiede operazioni minime di installazione e gestione per consentire un'iterazione rapida e ridurre il time-to-market.

![Architettura di riferimento per giochi per Azure DocumentDB](./media/documentdb-use-cases/documentdb-gaming-architecture.png)

## <a name="web-and-mobile-applications"></a>Applicazioni Web e per dispositivi mobili
DocumentDB viene comunemente usato all'interno di applicazioni Web e per dispositivi mobili ed è particolarmente adatto alla creazione di modelli di interazione social, all'integrazione con servizi di terze parti e all'ottimizzazione delle esperienze personalizzate. Gli SDK di DocumentDB possono essere usati per la compilazione avanzata di applicazioni iOS e Android usando il diffuso [framework Xamarin](documentdb-mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Applicazioni social
Un caso di utilizzo comune per DocumentDB prevede l'archiviazione e la query del contenuto generato dall'utente (UGC, User Generated Content) per applicazioni Web e mobili, soprattutto applicazioni per social media. Alcuni esempi di UGC includono sessioni di chat, tweet, post di blog, classificazioni e commenti. Spesso il contenuto UGC nelle applicazioni per social media comprende testo in formato libero, proprietà, tag e relazioni non limitati da una struttura rigida. I contenuti come chat, commenti e post possono essere archiviati in DocumentDB senza richiedere trasformazioni o livelli di mapping relazionale a oggetti complessi.  Le proprietà dei dati possono essere aggiunte o modificate facilmente in modo che rispondano ai requisiti quando gli sviluppatori scorrono il codice dell'applicazione, velocizzando così la fase di sviluppo.  

Le applicazioni che si integrano con social network di terze parti devono adeguarsi alle modifiche degli schemi di queste reti. I dati, man mano che vengono automaticamente indicizzati in DocumentDB per impostazione predefinita, sono pronti per l'esecuzione di query in qualsiasi momento. Queste applicazioni hanno quindi la flessibilità necessaria per recuperare le proiezioni in base alle rispettive esigenze.

Molte applicazioni di social networking vengono eseguite su scala globale e possono presentare modelli di utilizzo non prevedibili. La flessibilità nella scalabilità dell'archivio dati è essenziale man mano che il livello dell'applicazione viene scalato in modo da soddisfare la domanda di utilizzo.  È possibile scalare orizzontalmente aggiungendo altre partizioni di dati con un account DocumentDB.  Si possono anche creare altri account DocumentDB in più aree. Per la disponibilità delle aree del servizio DocumentDB, vedere [Aree di Azure](https://azure.microsoft.com/regions/#services).

![Architettura di riferimento per app Web per Azure DocumentDB](./media/documentdb-use-cases/documentdb-web.png)

### <a name="personalization"></a>Personalizzazione
Oggi le applicazioni moderne sono caratterizzate da esperienze e visualizzazioni complesse, in genere dinamiche e in grado di soddisfare le preferenze o gli stati d'animo degli utenti e le diverse esigenze di personalizzazione. È quindi necessario che le applicazioni possano recuperare le impostazioni personalizzate in modo efficace per eseguire rapidamente il rendering delle esperienze e degli elementi dell'interfaccia utente. 

JSON è un formato adatto a rappresentare i dati di layout dell'interfaccia utente, essendo non solo leggero, ma anche facilmente interpretabile da JavaScript. DocumentDB offre livelli di coerenza regolabili che consentono letture veloci con scritture a bassa latenza. Di conseguenza, l'archiviazione dei dati di layout dell'interfaccia utente, incluse le impostazioni personalizzate, come documenti JSON in DocumentDB è un metodo efficace per avere questi dati in rete.

## <a name="next-steps"></a>Passaggi successivi
Per iniziare a usare DocumentDB, è possibile creare un [account](https://azure.microsoft.com/pricing/free-trial/) e quindi seguire la [percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/documentdb/) per conoscere DocumentDB e trovare le informazioni necessarie. 

Oppure, per altre informazioni sui clienti che usano DocumentDB, leggere le seguenti storie dei clienti:

* [Sulekha.com](https://customers.microsoft.com/en-US/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha usa Azure DocumentDB per connettere clienti e aziende in India.
* [NewOrbit](https://customers.microsoft.com/en-US/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit decolla con Azure DocumentDB.
* [Affinio](https://customers.microsoft.com/en-US/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio passa da AWS ad Azure DocumentDB per sfruttare i dati di social networking su vasta scala.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Il gioco The Walking Dead: No Man's Land arriva al primo posto grazie al supporto di Azure DocumentDB.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Come è stato usato Azure DocumentDB per implementare un'esperienza di gioco social in Halo 5.
* [Cortana Analytics Gallery](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana Analytics Gallery: un sito di community scalabile basato su Azure DocumentDB.
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). L'integratore leader di settore offre alle multinazionali una visione globale in pochi minuti con tecnologie cloud flessibili.
* [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Le notizie "intelligenti" forniscono informazioni mirate ai cittadini impegnati. 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Per un uso uniforme dei colori in tutto il mondo, i principali marchi si rivolgono a SGS. E SGS si rivolge ad Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Telenor, leader mondiale, usa il cloud per muoversi con la velocità di una startup. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). L'archivio del futuro si basa sulla velocità delle ricerche e su un flusso di dati scorrevole.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Piattaforma software basata su Azure che rompe le barriere tra clienti e aziende
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Smart Fridge di Weka migliora la gestione dei vaccini, per offrire una protezione dalle malattie a un maggior numero di persone
* [Orange Tribes](https://customers.microsoft.com/en-US/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Prodotti interattivi per condividere esperienze di viaggi, musica, cinema e anche gastronomia.
* [Real Madrid](https://customers.microsoft.com/en-US/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Il Real Madrid porta allo stadio quasi 450 milioni di fan in tutto il mondo con i Microsoft Cloud.
* [Tuku](https://customers.microsoft.com/en-US/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU rende l'acquisto di un'auto un'esperienza divertente grazie al supporto dei servizi di Azure 

