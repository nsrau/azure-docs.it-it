---
title: 'Elaborazione di database senza server: Funzioni di Azure e Azure Cosmos DB | Microsoft Docs'
description: Informazioni su come Funzioni di Azure e Azure Cosmos DB possono essere usati insieme per creare app di elaborazione senza server guidate dagli eventi.
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 084dacc6a07b64500652481fbcabf1b0fc44306c
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="azure-cosmos-db-serverless-database-computing-using-azure-functions"></a>Azure Cosmos DB: elaborazione di database senza server con Funzioni di Azure

L'elaborazione senza server consiste nella possibilità di concentrarsi sulle singole parti logiche ripetibili e senza stato. Queste parti non richiedono la gestione dell'infrastruttura e usano le risorse solo per i secondi o millisecondi in cui vengono eseguite. Alla base dello spostamento di elaborazioni senza server ci sono le funzioni, che vengono rese disponibili all'interno dell'ecosistema di Azure da [Funzioni di Azure](https://azure.microsoft.com/services/functions).

Grazie all'integrazione nativa tra [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) e Funzioni di Azure, è possibile creare trigger database, associazioni di input e output direttamente dall'account di Azure Cosmos DB. Con Funzioni di Azure e Azure Cosmos DB, è possibile creare e distribuire app senza server guidate da eventi con accesso a bassa latenza a dati dettagliati per una base utenti globale.

## <a name="overview"></a>Panoramica

Funzioni di Azure e Azure Cosmos DB consentono di integrare i database e le app senza server nei modi seguenti:

* Creare un **trigger di Azure Cosmos DB** guidato dagli eventi in una funzione di Azure. Questo trigger si basa sui flussi dei [feed di modifiche](change-feed.md) per monitorare le modifiche del contenitore di Azure Cosmos DB. Quando vengono apportate delle modifiche a un contenitore, il flusso del feed di modifiche viene inviato al trigger, che richiama la funzione di Azure.
* In alternativa, è possibile associare una funzione di Azure a una raccolta di Azure Cosmos DB usando un'**associazione di input**. Le associazioni di input leggono i dati dal contenitore quando viene eseguita una funzione.
* Associare una funzione di Azure a una raccolta di Azure Cosmos DB usando un'**associazione di output**. Le associazioni di output scrivono i dati in un contenitore al termine di una funzione.

Il diagramma seguente illustra ognuna di questi tre integrazioni: 

![Modalità di integrazione di Azure Cosmos DB e Funzioni di Azure](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Il trigger di Azure Cosmos DB e le associazioni di input e output possono essere usati nelle combinazioni seguenti:
* Un trigger di Azure Cosmos DB può essere usato con un'associazione di output in un contenitore di Azure Cosmos DB diverso. Dopo che una funzione ha eseguito un'azione su un elemento nel feed di modifiche è possibile scriverla in un altro contenitore. Scriverla nello stesso contenitore da cui proviene creerebbe in realtà un ciclo ricorsivo. In alternativa, è possibile usare un trigger di Azure Cosmos DB per eseguire in modo efficace la migrazione di tutti gli elementi modificati da un contenitore a un altro, grazie all'uso di un'associazione di output.
* Le associazioni di input e output per Azure Cosmos DB possono essere usate nella stessa funzione di Azure. Questo procedimento funziona bene in casi in cui si desidera trovare determinati dati con l'associazione di input, modificarli nella funzione di Azure e quindi salvarli nello stesso contenitore o in un contenitore diverso, dopo la modifica.
* Un'associazione di input in un contenitore di Azure Cosmos DB può essere usata nella stessa funzione come trigger di Azure Cosmos DB, con o senza un'associazione di output. È possibile usare questa combinazione per applicare informazioni aggiornate sul tasso di cambio, inserite con un'associazione di input in un contenitore di cambio di valuta, per il feed di modifiche dei nuovi ordini nel servizio carrello. Il totale del carrello aggiornato, applicando la conversione valuta corrente, può essere scritto in un terzo contenitore usando un'associazione di output.

> [!NOTE]
> In questo momento, il trigger di Azure Cosmos DB, le associazioni di input e output usano solo gli account di DocumentDB, di tabella e di API Graph.

## <a name="use-cases"></a>Casi d'uso

I casi d'uso seguenti illustrano alcuni metodi grazie ai quali è possibile sfruttare al massimo i dati di Azure Cosmos DB, collegando i dati a Funzioni di Azure guidato da eventi.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Caso d'uso IoT: trigger di Azure Cosmos DB e associazione di output

Nelle implementazioni IoT, è possibile richiamare una funzione quando viene mostrata la luce del motore di controllo in un'automobile collegata.

**Implementazione:** usare un trigger di Azure Cosmos DB e un'associazione di output

1. Il **trigger di Azure Cosmos DB** viene usato per attivare gli eventi relativi agli avvisi dell'auto, ad esempio quando la luce del motore di controllo entra in un'automobile collegata.
2. Quando compare la luce del motore di controllo, i dati del sensore vengono inviati ad Azure Cosmos DB.
3. Azure Cosmos DB crea o aggiorna i nuovi documenti di dati del sensore, quindi tali modifiche sono trasmesse al trigger di Azure Cosmos DB.
4. Il trigger viene richiamato per ogni modifica di dati effettuata sulla raccolta di dati del sensore, poiché tutte le modifiche vengono trasmesse tramite il feed di modifiche.
5. Nella funzione viene usata una condizione di soglia per inviare i dati del sensore al reparto di garanzia.
6. Se la temperatura supera un determinato valore, viene inviato un avviso anche al proprietario.
7. L'**associazione di output** nella funzione aggiorna il record dell'auto in un'altra raccolta di Azure Cosmos DB per archiviare le informazioni relative all'evento del motore di controllo.

La figura seguente mostra il codice scritto nel portale di Azure per questo trigger.

![Creare un trigger di Azure Cosmos DB nel portale di Azure](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Caso d'uso finanziario: trigger timer e associazione di input

Nelle implementazioni finanziarie, è possibile richiamare una funzione quando un saldo del conto bancario rientra in un determinato importo.

**Implementazione:** trigger timer con un'associazione di input di Azure Cosmos DB

1. Usando un [trigger timer](../azure-functions/functions-bindings-timer.md), è possibile recuperare le informazioni relative al saldo del conto bancario archiviate in un contenitore di Azure Cosmos DB a intervalli di tempo programmati tramite un'**associazione di input**.
2. Se il saldo è inferiore alla soglia di saldo più bassa impostata dall'utente, intervenire con un'azione di Funzioni di Azure.
3. L'associazione di output può essere un'[integrazione SendGrid](../azure-functions/functions-bindings-sendgrid.md) che invia un messaggio di posta elettronica da un account del servizio a indirizzi di posta elettronica identificati per ciascuno degli account di saldo basso.

Le immagini seguenti mostrano il codice nel portale di Azure per questo scenario.

![File index.js di un trigger timer per uno scenario finanziario](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![File Run.csx di un trigger timer per uno scenario finanziario](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Caso d'uso di gioco: trigger di Azure Cosmos DB e associazione di output

Nei giochi, quando viene creato un nuovo utente è possibile cercare altri utenti che potrebbero conoscerlo usando l'[API Graph di Azure Cosmos DB](graph-introduction.md). È quindi possibile scrivere i risultati in un [database di tabella di Azure Cosmos DB](table-introduction.md) per semplificarne il recupero.

**Implementazione:** usare un trigger di Azure Cosmos DB e un'associazione di output

1. Usando il [database dei grafici](graph-introduction.md) di Azure Cosmos DB per archiviare tutti gli utenti, è possibile creare una nuova funzione con un trigger di Azure Cosmos DB. 
2. Ogni volta che viene inserito un nuovo utente, viene richiamata la funzione e quindi il risultato viene archiviato con un'**associazione di output**.
3. La funzione interroga il database dei grafici per cercare tutti gli utenti direttamente correlati al nuovo utente e restituisce i set di dati alla funzione.
4. Questi dati vengono quindi archiviati in un [database della tabella](table-introduction.md) di Azure Cosmos DB come set di coppia chiave-valore, che quindi può essere facilmente recuperato da qualsiasi applicazione front-end che mostri al nuovo utente gli amici connessi.

### <a name="retail-use-case---multiple-functions"></a>Caso d'uso di vendita al dettaglio: funzioni multiple

Nelle implementazioni della vendite al dettaglio, quando un utente aggiunge un elemento al carrello, è ora possibile creare e richiamare funzioni per i componenti di pipeline aziendali facoltativi.

**Implementazione:** più trigger di Azure Cosmos DB in ascolto in una raccolta

1. È possibile creare più Funzioni di Azure aggiungendo i trigger di Azure Cosmos DB a ognuno di essi, tutti in ascolto dello stesso feed di modifiche dei dati del carrello. 
2. Ogni volta che viene aggiunto un nuovo elemento a un carrello dell'utente, ogni funzione viene richiamata in modo indipendente dal feed di modifiche dal contenitore del carrello.
    * Una funzione può usare i contenuti del carrello corrente per modificare la visualizzazione di altri elementi a cui l'utente potrebbe essere interessato.
    * Un'altra funzione può aggiornare i totali dell'inventario.
    * Un'altra funzione può inviare informazioni sul cliente per determinati prodotti al reparto marketing, che le invia ai mailer promozionali. 

    Qualsiasi reparto può creare un trigger di Azure Cosmos DB in ascolto del feed di modifiche ed essere sicuro di non ritardare gli eventi di elaborazione di ordini importanti nel processo.

In tutti questi casi d'uso, poiché la funzione ha diviso l'app stessa non è necessario creare nuove istanze dell'app ogni volta. Al contrario, Funzioni di Azure crea le singole funzioni per completare processi distinti in base alle esigenze.

## <a name="tooling"></a>Strumenti

L'integrazione nativa tra Azure Cosmos DB e Funzioni di Azure è disponibile nel portale di Azure.
* Nel portale di Funzioni di Azure è possibile creare un trigger di Azure Cosmos DB. Per istruzioni di avvio rapido, vedere [Creare un trigger di Azure Cosmos DB nel portale di Azure](https://aka.ms/cosmosdbtriggerportalfunc) ![Creare un trigger di Azure Cosmos DB nel portale di Funzioni di Azure](./media/serverless-computing-database/azure-function-cosmos-db-trigger.png) 
* Nel portale di Funzioni di Azure è possibile anche aggiungere associazioni di input e output di Azure Cosmos DB ad altri tipi di trigger. Per istruzioni di avvio rapido, vedere [Archiviare dati non strutturati tramite Funzioni di Azure e Cosmos DB](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md).
    ![Creare un trigger di Azure Cosmos DB nel portale di Funzioni di Azure](./media/serverless-computing-database/function-portal-input-binding.png)
*   Nel portale di Azure Cosmos DB è possibile aggiungere un trigger di Azure Cosmos DB a un'app di Funzioni di Azure esistente nello stesso gruppo di risorse.
    ![Creare un trigger di Azure Cosmos DB nel portale di Funzioni di Azure](./media/serverless-computing-database/cosmos-db-portal.png)

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Motivi per cui scegliere l'integrazione di Funzioni di Azure per l'elaborazione senza server

Funzioni di Azure offre la possibilità di creare unità di lavoro scalabili o parti logiche concise che possono essere eseguite su richiesta, senza eseguire il provisioning o avere un'infrastruttura di gestione. Con Funzioni di Azure non è necessario creare un'app completa per rispondere alle modifiche nel database di Azure Cosmos DB, è possibile creare piccole funzioni riutilizzabili per attività specifiche. Inoltre, è possibile usare i dati del database di Azure Cosmos DB come input o output per una funzione di Azure in risposta a eventi quali richieste HTTP o trigger temporizzati.

Azure Cosmos DB è il database consigliato per l'architettura di elaborazione senza server per i motivi seguenti:

* **Accesso immediato a tutti i dati**: l'utente ha accesso granulare a ogni valore archiviato poiché Azure Cosmos DB [indicizza automaticamente](indexing-policies.md) tutti i dati per impostazione predefinita e rende gli indici immediatamente disponibili. Ciò significa che è sempre possibile eseguire query, aggiornare e aggiungere nuovi elementi al database e avere accesso immediato tramite Funzioni di Azure.

* **Senza schema**. Azure Cosmos DB è senza schema, quindi è in grado di gestire in modo univoco qualsiasi output di dati di una funzione di Azure. Questo approccio di "gestione di tutti gli elementi" rende più semplice creare una vasta gamma di funzioni inviate tutte ad Azure Cosmos DB.

* **Velocità effettiva scalabile**. La velocità effettiva può essere immediatamente aumentata o ridotta in Azure Cosmos DB. Se si dispone di centinaia o migliaia di funzioni che eseguono query e scrivono nella stessa raccolta, è possibile aumentare le [UR/sec](request-units.md) per gestire il carico. Tutte le funzioni possono operare in parallelo usando l'UR/sec allocata e viene garantita la [coerenza](consistency-levels.md) dei dati.

* **Replica globale**. È possibile replicare i dati di Azure Cosmos DB [in tutto il mondo](distribute-data-globally.md) per ridurre la latenza, geo-localizzando i dati più vicini agli utenti. Come avviene per tutte le query di Azure Cosmos DB, i dati dei trigger guidati dagli eventi sono dati in lettura del database di Azure Cosmos DB più vicino all'utente.

Se si sta cercando di integrare Funzioni di Azure per archiviare i dati e non è necessaria un'indicizzazione approfondita, oppure se è necessario archiviare gli allegati e i file multimediali, il [trigger di Archiviazione BLOB di Azure](../azure-functions/functions-bindings-storage-blob.md) potrebbe essere l'opzione migliore.

Vantaggi di Funzioni di Azure: 

* **Guidato dagli eventi**. Funzioni di Azure è guidato dagli eventi e può ascoltare un feed di modifiche di Azure Cosmos DB. Ciò significa che non è necessario creare la logica di ascolto, ma solo controllare le modifiche per cui si è in ascolto. 

* **Nessun limite**. Le funzioni vengono eseguite in parallelo e il servizio ne crea la quantità necessaria. L'utente imposta i parametri.

* **Utile per operazioni rapide**. Il servizio crea nuove istanze delle funzioni ogni volta che un evento viene generato e le chiude non appena la funzione viene completata. Si paga solo per il periodo in cui le funzioni sono in esecuzione.

Se non si è certi se scegliere Flow, App per la logica, Funzioni di Azure o Processi Web per l'implementazione, vedere [Scegliere tra Flow, App per la logica, Funzioni e Processi Web](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Passaggi successivi

Ora bisogna davvero collegare Funzioni di Azure e Azure Cosmos DB: 

* [Creare un trigger di Azure Cosmos DB nel portale di Azure](https://aka.ms/cosmosdbtriggerportalfunc)
* [Create an Azure Functions HTTP trigger with an Azure Cosmos DB input binding](https://aka.ms/cosmosdbinputbind) (Creare un trigger HTTP di Funzioni di Azure con un'associazione di input di Azure Cosmos DB)
* [Archiviare dati non strutturati tramite Funzioni di Azure e Cosmos DB](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)
* [Associazioni e trigger di Azure Cosmos DB](../azure-functions/functions-bindings-documentdb.md)


 




