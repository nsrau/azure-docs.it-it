---
title: 'Monitoraggio delle prestazioni delle applicazioni Web: Azure Application Insights | Microsoft Docs'
description: Funzione di Application Insights nel ciclo devOps
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: de94633cabaa7a1562a5a4839a8a8924da91a283
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Diagnostica completa per servizi e app Web con Application Insights
## <a name="why-do-i-need-application-insights"></a>Funzione di Application Insights
Application Insights consente di monitorare l'app Web in esecuzione, segnalando errori e problemi di prestazioni e aiutando ad analizzare l'utilizzo dell'app da parte dei clienti. Può essere utilizzato per le app in esecuzione su molte piattaforme (ASP.NET, J2EE, Node.js, ...) e può essere ospitato nel cloud oppure in locale. 

![Aspetti della complessità della distribuzione di app Web](./media/app-insights-devops/010.png)

È essenziale per monitorare un'applicazione moderna in esecuzione e, in particolare, per rilevare gli errori prima che si presentino ai clienti. È anche utile per rilevare e correggere i problemi di prestazioni che, pur non essendo gravi, possono rallentare il sistema o causare inconvenienti agli utenti. Quando invece il sistema funziona in modo soddisfacente, è opportuno sapere se gli utenti usano le funzionalità più recenti e se stanno traendo vantaggio dal programma.

Le applicazioni Web di tipo moderno sono sviluppate in un'ottica continuativa: dal momento del rilascio, si osserva l'utilità per gli utenti di una nuova funzionalità o di un miglioramento e, sulla base delle informazioni raccolte, si pianifica lo sviluppo successivo. In questo ciclo, l'osservazione riveste un ruolo fondamentale. Application Insights offre gli strumenti necessari per monitorare le prestazioni e l'utilizzo di un'applicazione Web.

Tra questi, l'aspetto più importante è la diagnostica, con le diagnosi che ne conseguono. Il mancato funzionamento dell'applicazione comporta una perdita di business. Pertanto, il principale ruolo di un framework di monitoraggio è quello di rilevare gli errori in modo affidabile, notificarli immediatamente e presentare le informazioni necessarie per diagnosticare il problema. Application Insights permette di fare esattamente questo.

### <a name="where-do-bugs-come-from"></a>Da dove provengono i bug?
Gli errori nei sistemi Web sono in genere causati da problemi di configurazione o difetti nell'interazione fra i numerosi componenti. Pertanto, quando si affronta un problema in un sito attivo, per prima cosa è necessario identificarne l'origine: qual è il componente o la relazione che lo provoca?

I professionisti di più lunga data ricorderanno quando ogni programma informatico veniva eseguito in un solo computer. Gli sviluppatori lo testavano molto accuratamente prima di renderlo disponibile. Dopo averlo reso disponibile, raramente lo rivedevano né ci pensavano più, mentre gli utenti facevano i conti con i bug che rimanevano al suo interno per molti anni a venire. 

Ora è cambiato tutto. Le app sono eseguibili su un'ampia gamma di dispositivi e può essere difficile garantirne sempre lo stesso identico comportamento. L'hosting delle app nel cloud permette di correggere i bug rapidamente, ma implica anche una concorrenza continua e l'aspettativa di una frequente introduzione di nuove funzionalità. 

Stando così le cose, l'unico modo per esercitare un controllo costante sul numero di bug è un testing delle unità automatizzato. Sarebbe impossibile ritestare tutto manualmente a ogni distribuzione. Il testing delle unità ormai è parte integrante del processo di compilazione. Strumenti come Xamarin Test Cloud consentono il testing automatizzato dell'interfaccia utente all'interno più versioni dei browser. Queste prove consentono di ridurre al minimo il numero di bug presenti in un'app.

Le applicazioni Web tipiche includono molti componenti live. Oltre al client (in un'app browser o per dispositivi) e al server Web, sarà probabilmente presente una notevole l'elaborazione di back-end. Il back-end è probabilmente una pipeline di componenti o una raccolta di parti che interagiscono tra loro. Molti di questi non saranno sotto controllo dell'utente, ma servizi esterni da cui dipende tutto.

In configurazioni come queste può essere difficile e dispendioso testare o prevedere ogni errore possibile e immaginabile al di fuori del sistema live. 

### <a name="questions-"></a>Domande...
Alcune domande da chiedersi durante lo sviluppo di un sistema Web:

* L'app si arresta in modo anomalo? 
* Cosa succede esattamente? Se si è verificato un errore in una richiesta, bisogna indagare per sapere tutto. Serve una traccia degli eventi...
* L'app è abbastanza veloce? Quanto tempo ci mette per rispondere a una normale richiesta?
* Il server riesce a gestire il carico? Quando aumenta la frequenza delle richieste, il tempo di risposta rimane costante?
* Qual è il livello di reazione delle dipendenze (le API REST, i database e gli altri componenti chiamati dall'app)? In particolare, se il sistema è lento, è dovuto al componente oppure si ricevono risposte lente da un altro utente?
* L'app è attiva o bloccata? È visibile in tutto il mondo? È utile sapere se si arresta...
* Qual è la causa radice? L'errore si è verificato al livello di un componente o di una dipendenza? Si tratta di un problema di comunicazione?
* Quanti sono gli utenti interessati? Se i più problemi da affrontare sono più di uno, qual è il più importante?

## <a name="what-is-application-insights"></a>Informazioni su Azure Application Insights
![Flusso di lavoro di base di Application Insights](./media/app-insights-devops/020.png)

1. Application Insights consente di instrumentare l'app, inviandone i dati di telemetria durante l'esecuzione. È possibile compilare l'SDK di Application Insights direttamente nell'applicazione oppure applicare la strumentazione in fase di runtime. Il primo metodo è più flessibile, perché consente di aggiungere i propri dati di telemetria ai normali moduli.
2. I dati di telemetria vengono inviati al portale di Application Insights, in cui verranno archiviati ed elaborati. (Pur essendo ospitato in Microsoft Azure, Application Insights consente di monitorare tutte le app Web e non solo le app di Azure.)
3. I dati di telemetria vengono forniti sotto forma di grafici e tabelle di eventi.

Esistono due tipi principali di telemetria: istanze non elaborate e istanze aggregate. 

* I dati delle istanze includono, ad esempio, il report di una richiesta ricevuta da un'app Web. Se si desidera cercare ed esaminare i dettagli di una richiesta, è possibile farlo mediante lo strumento di ricerca del portale di Application Insights. L'istanza includerà, ad esempio, i dati sul tempo impiegato dall'app per rispondere alla richiesta, nonché l'URL richiesto, una posizione indicativa del client e altro ancora.
* I dati aggregati includono il numero di eventi per unità di tempo, in modo che sia possibile confrontare la frequenza delle richieste con i tempi di risposta. È inoltre inclusa la media di metriche quali i tempi di risposta alle richieste.

Le principali categorie di dati sono:

* Richieste inviate all'app (in genere richieste HTTP), con i dati sull'URL, sul tempo di risposta e sull'esito positivo o negativo.
* Le dipendenze, ovvero le chiamate REST e SQL effettuate dall'app, complete di URI, tempi di risposta ed esiti.
* Le eccezioni, incluse le analisi dello stack.
* I dati di visualizzazione delle pagine, provenienti dai browser degli utenti.
* Metriche come i contatori delle prestazioni, nonché metriche scritte manualmente. 
* Eventi personalizzati utilizzabili per tenere traccia degli eventi aziendali.
* Tracce del log a scopo di debug.

## <a name="case-study-real-madrid-fc"></a>Case study: Real Madrid C.F.
Il servizio Web del [Real Madrid Football Club](http://www.realmadrid.com/) serve circa 450 milioni di tifosi in tutto il mondo, che vi accedono tramite Web browser e dalle app per dispositivi mobili della società non solo per prenotare i biglietti, ma anche per consultare informazioni e guardare clip video sui risultati, i giocatori e le partite in programma. I tifosi possono eseguire ricerche applicando filtri come, ad esempio, il numero di goal segnati. Inoltre, sono disponibili i collegamenti ai social media. L'esperienza utente è altamente personalizzata e progettata come strumento di comunicazione bidirezionale per coinvolgere i tifosi.

La soluzione [è un sistema di servizi e applicazioni in Microsoft Azure](https://www.microsoft.com/en-us/enterprise/microsoftcloud/realmadrid.aspx). La scalabilità è un requisito essenziale: il traffico è variabile e può raggiungere volumi molto elevati prima, dopo e durante le partite.

Per il Real Madrid, è fondamentale monitorare le prestazioni del sistema. Application Insights di Azure offre una panoramica completa di tutto il sistema, garantendo un livello di servizio elevato e affidabile. 

Inoltre, la società può sapere tutto dei tifosi: dove sono (in Spagna si trova solo il 3%), quanto sono interessati a giocatori, risultati storici e le partite in calendario e come rispondono ai risultati degli incontri.

La maggior parte dei dati di telemetria viene raccolta automaticamente senza alcun codice aggiuntivo, il che semplifica la soluzione, riducendo la complessità operativa.  Per il Real Madrid, Application Insights gestisce ogni mese 3,8 miliardi di punti di telemetria.

Per consultare i propri dati di telemetria, il Real Madrid usa il modulo PowerBI.

![Visualizzazione di Power BI per Application Insights Telemetry](./media/app-insights-devops/080.png)

## <a name="smart-detection"></a>Rilevamento intelligente
La [diagnostica proattiva](app-insights-proactive-diagnostics.md) è una funzionalità recente. Senza richiedere configurazioni particolari da parte dell'utente, Application Insights rileva automaticamente gli aumenti atipici nella frequenza degli errori dell'app e fornisce avvisi a tale riguardo. È intelligente al punto tale da ignorare gli errori occasionali, come anche l'incremento degli errori semplicemente riconducibile a un aumento delle richieste. Ad esempio, se si verifica un errore in uno dei servizi da cui dipende l'utente o se una nuova build appena distribuita non funziona bene, l'utente lo saprà semplicemente controllando la posta elettronica. Saranno inoltre disponibili webhook che consentiranno di attivare altre app.

Un altro aspetto di questa funzionalità è che consente di eseguire un'approfondita analisi giornaliera dei dati di telemetria, finalizzata a individuare schemi delle prestazioni atipici altrimenti difficili da rilevare, come un rallentamento delle prestazioni associato a una particolare area geografica oppure a una determinata versione di un browser.

In entrambi i casi, nell'avviso non sono indicati solo i sintomi osservati, ma anche i dati necessari per diagnosticare il problema, ad esempio report sulle eccezioni pertinenti.

![Messaggio di posta dalla diagnostica proattiva](./media/app-insights-devops/030.png)

Ricorda il cliente Samtec: "Ultimamente, durante una migrazione completa delle funzionalità, abbiamo scoperto che un database sottodimensionato stava raggiungendo i limiti di risorse e provocava timeout. Gli avvisi proattivi si sono presentati al momento stesso di valutare il problema, praticamente in tempo reale, come promesso. Questi e gli altri avvisi della piattaforma Azure ci hanno aiutato a risolvere il problema quasi nell'immediato. Tempo di inattività totale: neanche 10 minuti."

## <a name="live-metrics-stream"></a>Flusso di metriche live
Distribuire l'ultima build può generare ansia. Se si presenta un problema, è bene saperlo subito, in modo da poterci ripensare, se necessario. Flusso metriche attive offre le metriche essenziali con una latenza di circa un secondo.

![Metriche attive](./media/app-insights-devops/040.png)

E consente di analizzare immediatamente un campione di errori o eccezioni.

![Eventi di errore live](./media/app-insights-devops/live-stream-failures.png)

## <a name="application-map"></a>Mappa delle applicazioni
La mappa delle applicazioni individua automaticamente la topologia dell'applicazione, aggiungendovi informazioni sulle prestazioni per consentire all'utente di identificare con facilità i colli di bottiglia e i flussi problematici nell'intero ambiente distribuito. Questa caratteristica consente di individuare le dipendenze delle applicazioni dai servizi di Azure. È possibile stabilire se il problema sia correlato al codice o alle dipendenze, nonché analizzare la diagnosi da un'unica posizione. Ad esempio, l'applicazione potrebbe non funzionare correttamente a causa di un calo delle prestazioni a livello di SQL. Con la mappa delle applicazioni è possibile vedere immediatamente il problema e risalire ai dettagli in SQL Index Advisor o Query Insights.

![Mappa delle applicazioni](./media/app-insights-devops/050.png)

## <a name="application-insights-analytics"></a>Analytics in Application Insights
Con [Analytics](app-insights-analytics.md)è possibile scrivere query arbitrarie in un efficace linguaggio simile a SQL.  Effettuare la diagnosi nell'intero stack diventa facile, perché il confluire di diverse prospettive permette di porre le domande corrette per correlare le prestazioni del servizio alle metriche aziendali e all'esperienza del cliente. 

È possibile eseguire query di tutte le istanze di telemetria e dei dati non elaborati archiviati nel portale. Il linguaggio include filtri, join, aggregazioni e altre operazioni. È possibile calcolare i campi ed eseguire analisi statistiche e sono disponibili visualizzazioni grafiche e tabulari.

![Query di Analisi e grafico dei risultati](./media/app-insights-devops/025.png)

Ad esempio, è facile:

* Segmentare i dati sulle prestazioni delle richieste dell'applicazione suddividendo i clienti in livelli per comprenderne l'esperienza.
* Cercare specifici codici di errore o nomi di eventi personalizzati durante l'analisi dei siti attivi.
* Esaminare l'utilizzo delle app da parte di determinati clienti per comprendere come vengono acquisite e adottate le funzionalità.
* Tenere traccia di sessioni e tempi di risposta di determinati utenti per consentire al team operativo e di supporto di fornire assistenza in tempi immediati.
* Individuare le funzionalità più utilizzate nelle app per rispondere alle domande sulla priorità delle funzionalità.

Dichiara il cliente DNN: "Application Insights è quanto ci mancava per poter combinare, ordinare, interrogare e filtrare i dati a dovere. Permettendo al nostro team di mettere in campo il proprio ingegno e la propria esperienza per trovare i dati con un linguaggio di query avanzato, questa soluzione ci ha consentito di risalire a informazioni e risolvere problemi che nemmeno sapevamo di avere. Da domande del tipo *"Mi chiedo se...*" sono emerse molte risposte interessanti."

## <a name="development-tools-integration"></a>Integrazione di strumenti di sviluppo
### <a name="configuring-application-insights"></a>Configurazione di Application Insights
In Visual Studio ed Eclipse sono disponibili strumenti per configurare i pacchetti SDK corretti per il progetto in fase di sviluppo. È disponibile un comando di menu per aggiungere Application Insights.

Se si usa un framework di registrazione delle tracce, ad esempio Log4N, NLog o System.Diagnostics.Trace, verrà visualizzata l'opzione per inviare i log ad Application Insights insieme ad altri dati di telemetria, per consentire di mettere facilmente in relazione le tracce con le richieste, le chiamate a dipendenze e le eccezioni.

### <a name="search-telemetry-in-visual-studio"></a>Ricercare i dati di telemetria in Visual Studio
Durante lo sviluppo e il debug di una funzionalità, è possibile visualizzare e cercare i dati di telemetria direttamente in Visual Studio, utilizzando le stesse funzionalità di ricerca del portale Web.

Inoltre, quando Application Insights esegue un'eccezione, è possibile visualizzare il punto dati in Visual Studio e passare direttamente al codice pertinente.

![Ricerca di Visual Studio](./media/app-insights-devops/060.png)

Durante il debug, è possibile scegliere di mantenere i dati di telemetria nel computer su cui si esegue lo sviluppo, visualizzandoli in Visual Studio, ma senza inviarli al portale. Con l'opzione locale si evita di mischiare il debug e la telemetria della produzione.

### <a name="build-annotations"></a>Annotazioni sulla compilazione
Se si utilizza Visual Studio Team Services per compilare e distribuire l'app, nei grafici sul portale vengono visualizzate annotazioni sulla distribuzione. Qualunque effetto sulle metriche dovuto alla versione più recente risulterà molto evidente.

![Annotazioni sulla compilazione](./media/app-insights-devops/070.png)

### <a name="work-items"></a>Elementi di lavoro
Quando viene generato un avviso, Application Insights può creare automaticamente un elemento di lavoro nel sistema di tracciamento delle attività.

## <a name="but-what-about"></a>Altri aspetti
* [Privacy e archiviazione](app-insights-data-retention-privacy.md): i dati di telemetria vengono conservati nei server sicuri di Azure.
* Prestazioni. L'impatto è molto basso. I dati di telemetria sono organizzati in batch.
* [Prezzi](app-insights-pricing.md): il servizio è gratuito finché i volumi sono ridotti.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Passaggi successivi
Iniziare a usare Application Insights è semplice. Le opzioni principali sono le seguenti:

* Instrumentare un'app Web già in esecuzione, ottenendo tutti i dati di telemetria delle prestazioni predefiniti. Questa opzione è disponibile per [Java](app-insights-java-live.md) e [server IIS](app-insights-monitor-performance-live-website-now.md), nonché per le [App Web di Azure](app-insights-azure.md).
* Instrumentare il progetto in fase di sviluppo, cosa possibile per le app [ASP.NET](app-insights-asp-net.md) o [Java](app-insights-java-get-started.md), nonché per [Node. js](app-insights-nodejs.md) e una serie di [altri tipi](app-insights-platforms.md). 
* Instrumentare [qualsiasi pagina Web](app-insights-javascript.md) aggiungendo un breve frammento di codice.

