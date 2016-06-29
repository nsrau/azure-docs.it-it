<properties
   pageTitle="Diagnosticare gli errori delle app per la logica | Microsoft Azure"
   description="Approcci comuni per la comprensione degli errori delle app per la logica"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/18/2016"
   ms.author="jehollan"/>

# Diagnosi degli errori delle app per la logica

Se si verificano problemi o errori con la funzionalità App per la logica del servizio app di Azure, con alcuni accorgimenti sarà possibile capire meglio l'origine degli errori.

## Strumenti del portale di Azure

Il portale di Azure offre molti strumenti per la diagnosi delle app per la logica a ogni passaggio.

### Cronologia di attivazione

Ogni app per la logica ha almeno un trigger. Se le app non si attivano, la prima risorsa in cui cercare informazioni aggiuntive è la cronologia di attivazione. La cronologia di attivazione è disponibile dal pannello principale delle app per la logica.

![Accedere alla cronologia dei trigger][1]

Al suo interno sono elencati tutti i tentativi di attivazione eseguiti dall'app per la logica. Facendo clic su ogni tentativo di attivazione è possibile accedere a ulteriori dettagli, nello specifico sugli eventuali input o output generati dal tentativo di attivazione. Se si notano trigger non riusciti, fare clic sul tentativo di attivazione e quindi accedere al collegamento **Output** per visualizzare gli eventuali messaggi di errore generati, ad esempio relativi a credenziali FTP non valide.

Possono essere visualizzati i seguenti stati:

* **Operazione ignorata**. È stato eseguito il poll dell'endpoint per verificare la presenza di dati e la risposta ricevuta ha indicato che non ci sono dati disponibili.
* **Operazione completata**. La risposta ricevuta dal trigger ha indicato che non sono disponibili dati. Questo può essere il risultato di un trigger manuale, un trigger di ricorrenza o un trigger di poll. È possibile che sia associato a uno stato **Attivato**, ma questo stato non viene visualizzato se in visualizzazione Codice è presente un valore splitOn o una condizione non soddisfatta.
* **Operazione non riuscita**. È stato generato un errore.

#### Avviare manualmente un trigger

Se si desidera che l'app per la logica verifichi immediatamente la presenza di un trigger disponibile, senza attendere la ricorrenza successiva, è possibile fare clic sul pulsante **Seleziona trigger** nel pannello principale per forzare un controllo. Ad esempio, facendo clic su questo collegamento con un trigger di Dropbox, il flusso di lavoro eseguirà immediatamente il poll di Dropbox alla ricerca di nuovi file.

### Cronologia di esecuzione

Ogni trigger attivato ha come risultato un'esecuzione. Le informazioni sull'esecuzione sono accessibili dal pannello principale, che contiene numerose informazioni potenzialmente utili per capire cosa si è verificato durante il flusso di lavoro.

![Accedere alla cronologia di attivazione][2]

Un'esecuzione può visualizzare uno dei seguenti stati:

* **Operazione completata**. Tutte le azioni hanno avuto esito positivo oppure, se si è verificato un errore, è stato gestito da un'azione che si è verificata in un secondo momento nel flusso di lavoro. Vale a dire, l'errore è stato gestito da un'azione che è stata impostata per l'esecuzione dopo un'azione non riuscita.
* **Operazione non riuscita**. Almeno un'azione presenta un errore non gestito da un'azione successiva nel flusso di lavoro.
* **Operazione annullata**. Il flusso di lavoro era in esecuzione ma ha ricevuto una richiesta di annullamento.
* **In esecuzione**. Il flusso di lavoro è attualmente in esecuzione, ad esempio per flussi di lavoro limitati o a causa del piano di servizio app corrente. Per informazioni dettagliate sui limiti delle azioni, vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/app-service/plans/). Inoltre, la configurazione della diagnostica (i grafici che riportano la cronologia di esecuzione) possono fornire informazioni sugli eventuali eventi di limitazione in atto.

Quando si analizza una cronologia di esecuzione, è possibile visualizzare ulteriori dettagli.

#### Output dei trigger

Gli output dei trigger mostrano i dati ricevuti dal trigger. Ciò consente di determinare se tutte le proprietà siano state restituite come previsto.

>[AZURE.NOTE] Potrebbe essere utile comprendere il modo in cui le app per la logica [gestiscono diversi tipi di contenuto](app-service-logic-content-type.md), se si notano contenuti poco chiari.

![Esempi di output di trigger][3]

#### Input e output delle azioni

È possibile visualizzare informazioni dettagliate sugli input e sugli output ricevuti da un'azione, in modo da semplificare la comprensione delle dimensioni e delle forme degli output, oltre a visualizzare eventuali messaggi di errore generati.

![Input e output delle azioni][4]

## Debug del runtime del flusso di lavoro

Per monitorare gli input, gli output e i trigger di un'esecuzione, potrebbe essere utile aggiungere alcuni passaggi in un flusso di lavoro per semplificare il debug. [RequestBin](http://requestb.in) è uno strumento utile che è possibile aggiungere come passaggio in un flusso di lavoro. Utilizzando RequestBin, è possibile configurare un controllo per le richieste HTTP, in modo da stabilire esattamente le dimensioni, la forma e il formato di una richiesta HTTP. È possibile creare un nuovo RequestBin e incollare l'URL in un'azione HTTP POST dell'app per la logica, con qualsiasi contenuto del corpo da verificare, ad esempio un'espressione o l'output di un altro passaggio. Dopo l'esecuzione dell'app per la logica, è possibile aggiornare RequestBin per verificare in che modo è stata formata la richiesta durante la generazione dal motore di app per la logica.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0615_2016-->