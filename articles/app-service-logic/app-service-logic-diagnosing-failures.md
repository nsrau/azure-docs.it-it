<properties
   pageTitle="Diagnosi delle app per la logica | Microsoft Azure"
   description="Informazioni sugli approcci comuni per la comprensione degli errori delle app"
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
   
# Diagnosi delle app per la logica

Se si verificano problemi o errori con un'app per la logica, esistono diversi approcci utili per ottenere una migliore comprensione delle cause degli errori.

## Strumenti del portale di gestione

Il portale di Azure offre molti strumenti per la diagnosi di ogni app per la logica a ogni passaggio.

### Cronologia di attivazione

Ogni app per la logica ha almeno un trigger. Se si nota che alcune app non vengono attivate, è prima di tutto necessario verificare la cronologia di attivazione. È possibile accedere alla cronologia di attivazione dal pannello principale delle app per la logica:

![][1]

Verranno elencati tutti i tentativi di attivazione dell'app per la logica. È possibile selezionare ogni voce per ottenere il livello di dettaglio successivo, ovvero, in particolare, eventuali input oppure output generati dal tentativo di attivazione. Se si notano trigger con stato 'Non riuscito', è necessario selezionare il tentativo di attivazione e quindi il collegamento per gli output, in modo da comprendere eventuali messaggi di errore generati, ad esempio relativi a credenziali FTP non valide.

Possono essere visualizzati i diversi stati seguenti:

* Ignorato: è stato eseguito il poll dell'endpoint per verificare se i dati sono disponibili ed è stata ottenuta una risposta che indica che non sono disponibili dati.
* Operazione riuscita: il trigger ha ricevuto una risposta che indica che i dati sono disponibili. Questo può essere il risultato di un trigger manuale, un trigger di ricorrenza o un trigger di poll. È possibile che sia associato a uno stato 'Attivato', ma questo stato non viene visualizzato se non è stata soddisfatta una condizione esistente o un valore splitOn in visualizzazione Codice.
* Non riuscito: è stato generato un errore.

#### Avviare manualmente un trigger

Se si vuole che l'app per la logica verifichi immediatamente la presenza di un trigger disponibile, senza attendere la ricorrenza successiva, è sempre possibile fare clic sul pulsante **Seleziona trigger** nel pannello principale per forzare un controllo. Ad esempio, se si fa clic su questo pulsante con un trigger di Dropbox, il flusso di lavoro eseguirà immediatamente il poll di Dropbox alla ricerca di nuovi file.

### Cronologia di esecuzione

Ogni volta che viene attivato un trigger, si ottiene come risultato un'esecuzione. È possibile accedere alle esecuzioni dal pannello principale, in modo da visualizzare molte informazioni utili per comprendere cosa si è verificato durante il flusso.

![][2]

Un'esecuzione può avere uno degli stati seguenti:

* Operazione riuscita: tutte le azioni hanno avuto esito positivo oppure, se si è verificato un errore, tale errore è stato gestito da un'azione successiva nel flusso di lavoro, ovvero è stata configurata un'azione per l'esecuzione dopo un'azione con stato 'Operazione non riuscita'.
* Operazione non riuscita: almeno un'azione presenta un errore non gestito da un'azione successiva nel flusso di lavoro.
* Operazione annullata: il flusso di lavoro era in esecuzione ma ha ricevuto una richiesta di annullamento.
* In esecuzione: visualizzato se un flusso di lavoro è attualmente in esecuzione, ad esempio per flussi limitati e con il piano di servizio app corrente. Per informazioni dettagliate sui limiti delle azioni, vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/app-service/plans/). La configurazione della diagnostica, ovvero i grafici disponibili sotto la cronologia di esecuzione, offre anche informazioni su eventuali eventi di limitazione in corso.

Dopo l'accesso a un'esecuzione, è possibile visualizzare informazioni più dettagliate.

#### Output dei trigger

Gli output dei trigger mostrano i dati ricevuti dal trigger. Queste informazioni possono essere utili per verificare se tutte le proprietà vengono restituite come previsto.

>[AZURE.NOTE] Potrebbe essere utile comprendere il modo in cui le app per la logica [gestiscono diversi tipi di contenuto](app-service-logic-content-type.md), se si notano contenuti poco chiari.

![][3]

#### Input e output delle azioni

È possibile visualizzare informazioni dettagliate sugli input e sugli output ricevuti da un'azione, in modo da semplificare la comprensione delle dimensioni e delle forme degli output, oltre a visualizzare eventuali messaggi di errore generati.

![][4]

## Debug del runtime del flusso di lavoro

Per monitorare gli input, gli output e i trigger di un'esecuzione, potrebbe essere utile aggiungere alcuni passaggi in un flusso di lavoro per semplificare il debug. [RequestBin](http://requestb.in) è uno strumento utile che è possibile aggiungere come passaggio in un flusso di lavoro. RequestBin consente di configurare un controllo per le richieste HTTP, in modo da comprendere esattamente le dimensioni, la forma e il formato di una richiesta HTTP. È possibile creare un nuovo RequestBin e incollare l'URL in un'azione HTTP POST dell'app per la logica, con qualsiasi contenuto del corpo da verificare, ovvero un'espressione, l'output di un altro passaggio e così via. Dopo l'esecuzione dell'app per la logica, è possibile aggiornare RequestBin per verificare in che modo è stata formata la richiesta durante la generazione dal motore di app per la logica.




<!-- image references -->
[1]: ./media/app-service-logic-diagnosing-failures/triggerHistory.PNG
[2]: ./media/app-service-logic-diagnosing-failures/runHistory.PNG
[3]: ./media/app-service-logic-diagnosing-failures/triggerOutputsLink.PNG
[4]: ./media/app-service-logic-diagnosing-failures/ActionOutputs.PNG

<!---HONumber=AcomDC_0601_2016-->