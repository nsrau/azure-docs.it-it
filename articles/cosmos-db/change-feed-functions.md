---
title: Come usare il feed di modifiche di Azure Cosmos DB con Funzioni di Azure
description: Usare il feed di modifiche di Azure Cosmos DB con Funzioni di Azure
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: 943cd79237290cd91d56cc1c51f5d773cdb16634
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636391"
---
# <a name="how-to-use-azure-cosmos-db-change-feed-with-azure-functions"></a>Come usare il feed di modifiche di Azure Cosmos DB con Funzioni di Azure

Se si usa Funzioni di Azure, il modo più semplice per connettersi a un feed di modifiche è quello di aggiungere un [trigger di Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger) all'app Funzioni di Azure. Quando si crea un trigger di Cosmos DB in un'app di Funzioni di Azure, si sceglie il contenitore di Cosmos a cui connettersi e la funzione viene attivata ogni volta che viene apportata una modifica al contenitore.

I trigger possono essere creati nel portale di Funzioni di Azure, nel portale di Azure Cosmos DB o a livello di codice. Per altre informazioni, vedere [Elaborazione di database serverless con Azure Cosmos DB e Funzioni di Azure](serverless-computing-database.md).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>Come si configura Funzioni di Azure per la lettura da una determinata area?

È possibile definire la proprietà [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations) quando si usa il trigger di Azure Cosmos DB per specificare un elenco di aree. Equivale a personalizzare ConnectionPolicy per consentire al trigger di eseguire operazioni di lettura dalle aree preferite. Idealmente, si vogliono eseguire operazioni di lettura dall'area più vicina a quella in cui è distribuito Funzioni di Azure.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Qual è la dimensione predefinita dei batch in Funzioni di Azure?

La dimensione predefinita è 100 elementi per ogni chiamata di Funzioni di Azure. Tuttavia, il numero è configurabile all'interno del file function.json. Qui è disponibile un [elenco completo delle opzioni di configurazione](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Se si eseguono attività di sviluppo in locale, aggiornare le impostazioni dell'applicazione all'interno del file local.settings.json.

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>Durante il monitoraggio di un contenitore e la lettura del relativo feed di modifiche non è possibile vedere tutti i documenti inseriti, alcuni mancano. Perché?

Verificare che nessun'altra istanza di Funzioni di Azure stia leggendo lo stesso contenitore con lo stesso contenitore di lease. I documenti mancanti vengono elaborati da altre istanze di Funzioni di Azure che usano lo stesso lease.

Di conseguenza, se si creano più istanza di Funzioni di Azure per leggere lo stesso feed di modifiche, tali istanze devono usare contenitori di lease diversi oppure devono usare la configurazione "leasePrefix" per condividere lo stesso contenitore. Tuttavia, quando si usa la libreria del processore dei feed di modifiche, è possibile avviare più istanze di Funzioni di Azure e l'SDK provvederà a suddividere automaticamente i documenti tra le diverse istanze.

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>L'elemento di Cosmos viene aggiornato ogni secondo, ma Funzioni di Azure in ascolto sul feed di modifiche non riceve tutte le modifiche. Perché?

Funzioni di Azure esegue continuamente il polling del feed per verificare la presenza di modifiche, con un ritardo massimo predefinito di 5 secondi. Se non sono presenti modifiche in sospeso per la lettura o sono presenti modifiche in sospeso dopo l'applicazione del trigger, la funzione le leggerà immediatamente. Tuttavia, se non sono presenti modifiche in sospeso, la funzione attenderà 5 secondi ed eseguirà il polling per verificare la presenza di altre modifiche.

Se il documento subisce più modifiche nello stesso intervallo di tempo che ha impiegato il trigger per eseguire il polling delle nuove modifiche, è possibile che venga inviata la versione più recente del documento e non quella intermedia.

Se si vuole eseguire il polling del feed di modifiche per un intervallo di tempo inferiore a 5 secondi, ad esempio ogni secondo, è possibile configurare l'intervallo di polling "feedPollDelay". Vedere la [configurazione completa](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations). Il valore è definito in millisecondi e l'impostazione predefinita è 5000. L'esecuzione del polling per un intervallo di tempo inferiore a 1 secondo è possibile ma non consigliabile perché si inizierà a usare una maggior quantità di memoria CPU.

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>Più istanze di Funzioni di Azure possono leggere il feed di modifiche di un solo contenitore?

Sì. Più istanze di Funzioni di Azure possono leggere il feed di modifiche dello stesso contenitore. Tuttavia, per ogni istanza dovrà essere definita una proprietà "leaseCollectionPrefix" separata.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>Se si elabora un feed di modifiche usando Funzioni di Azure, ad esempio su un batch di 10 documenti, e si verifica un errore nel settimo documento, gli ultimi tre documenti non vengono elaborati. In che modo è possibile riprendere l'elaborazione dal documento contenente l'errore, ovvero il settimo, nel feed successivo?

Per gestire l'errore, il modello consigliato è quello di eseguire il wrapping del codice con blocco try-catch e, se si esegue l'iterazione sull'elenco di documenti, eseguire il wrapping di ogni iterazione nel rispettivo blocco try-catch. Questa procedura consiste nel rilevare l'errore, inserire il documento in questione in una coda di documenti non recapitabili e quindi definire la logica per gestire i documenti che hanno generato l'errore. Se il batch comprende 200 documenti e l'errore riguarda un solo documento, questo metodo consente di evitare di invalidare l'intero batch.

In caso di errore, non si dovrà riportare il punto di controllo all'inizio altrimenti i documenti che hanno generato l'errore verranno continuamente recuperati dal feed di modifiche. Tenere presente che il feed di modifiche mantiene l'ultimo snapshot finale dei documenti, quindi, per questo motivo, si potrebbe perdere lo snapshot precedente del documento. Il feed di modifiche mantiene solo l'ultima versione del documento e nel frattempo altri processi possono intervenire a modificare il documento.

Man mano che si continua a correggere il codice, la coda dei documenti non recapitabili si svuoterà. Funzioni di Azure viene chiamato automaticamente dal sistema del feed di modifiche e il punto di controllo viene gestito internamente da Funzioni di Azure. Se si desidera eseguire il rollback del punto di controllo e controllarne tutti gli aspetti, considerare di utilizzare l'SDK processore del feed di modifiche.

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>Sono previsti costi aggiuntivi per l'uso del trigger di Azure Cosmos DB?

Il trigger di Azure Cosmos DB sfrutta i vantaggi offerti dalla libreria del processore dei feed di modifiche internamente. Di conseguenza, è necessaria una raccolta aggiuntiva, definita raccolta di lease, per gestire lo stato e i checkpoint parziali. Questa gestione dello stato è necessaria per la scalabilità dinamica e per continuare nel caso in cui si voglia arrestare Funzioni di Azure e continuare l'elaborazione in un secondo momento. Per altre informazioni, vedere [Uso della libreria del processore dei feed di modifiche di Azure Cosmos DB](change-feed-processor.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere altre informazioni sul feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Metodi per leggere il feed di modifiche](read-change-feed.md)
* [Uso della libreria del processore dei feed di modifiche](change-feed-processor.md)
* [Uso della libreria del processore dei feed di modifiche di Azure Cosmos DB](change-feed-processor.md)
* [Elaborazione di database serverless con Azure Cosmos DB e Funzioni di Azure](serverless-computing-database.md)
