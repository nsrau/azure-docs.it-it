---
title: Creare API Web e API REST come connettori - App per la logica di Azure | Microsoft Docs
description: Creare API Web e API REST per chiamare le API, i servizi o i sistemi nei flussi di lavoro per le integrazioni di sistema con le app per la logica di Azure
keywords: API Web, API REST, connettori, flussi di lavoro, integrazioni di sistema
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/26/2017
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 4ae98804aced23c0261c1d58721cb18d8152c6f1
ms.contentlocale: it-it
ms.lasthandoff: 06/08/2017

---

# <a name="create-custom-apis-as-connectors-for-logic-apps"></a>Creare API personalizzate come connettori per app per la logica

Sebbene le app per la logica di Azure offrano [più di 100 connettori incorporati](../connectors/apis-list.md) che è possibile usare nei flussi di lavoro delle app per la logica, si consiglia di chiamare le API, i sistemi e i servizi che non sono disponibili come connettori. È possibile creare proprie API personalizzate che specificano le azioni e trigger da usare nelle app per la logica. Questi sono alcuni altri motivi per cui può essere necessario creare le proprie API da usare come connettori nelle app per la logica:

* Estendere gli attuali flussi di lavoro di integrazione del sistema e integrazione dei dati.
* Aiutare i clienti a usare il servizio per gestire attività professionali o personali.
* Espandere la copertura, l'individuabilità e l'uso del servizio.

In pratica, i connettori sono API Web che usano REST per le interfacce collegabili, il [formato dei metadati Swagger](http://swagger.io/specification/) per la documentazione e JSON come formato di scambio di dati. Poiché i connettori sono API REST che comunicano attraverso endpoint HTTP, è possibile usare qualsiasi linguaggio, ad esempio .NET, Java o Node.js, per la creazione dei connettori. È anche possibile ospitare le API nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md), una soluzione PaaS (platform-as-a-service) che offre uno dei modi più efficaci, semplici e scalabili per ospitare le API. 

Per consentire alle API personalizzate di funzionare con le app per la logica, l'API può rendere disponibili [*azioni*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) che eseguono attività specifiche nei flussi di lavoro delle app per la logica. L'API può anche agire come un [*trigger*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) che avvia un flusso di lavoro di app per la logica quando nuovi dati o un evento soddisfano una condizione specificata. Questo argomento descrive i modelli comuni che è possibile seguire per la creazione di azioni e trigger nell'API, in base al comportamento previsto per l'API.

> [!TIP] 
> Benché sia possibile implementare le API come [app Web](../app-service-web/app-service-web-overview.md), è consigliabile distribuirle come [app per le API](../app-service-api/app-service-api-apps-why-best-platform.md), in modo da semplificare il lavoro durante la compilazione, l'hosting e l'uso delle API nel cloud e in locale. Non è necessario modificare alcun codice nelle API, è sufficiente implementare il codice a un'app per le API. Informazioni su come [compilare le app per le API create con ASP.NET](../app-service-api/app-service-api-dotnet-get-started.md), [Java](../app-service-api/app-service-api-java-api-app.md) o [Node.js](../app-service-api/app-service-api-nodejs-api-app.md). 
>
> Per gli esempi di app per le API compilate per le app per la logica, visitare il [repository GitHub](http://github.com/logicappsio) o il [blog sulle app per la logica di Azure](http://aka.ms/logicappsblog).

## <a name="helpful-tools"></a>Strumenti utili

Un'API personalizzata funziona meglio con le app per la logica quando l'API include anche un [documento Swagger](http://swagger.io/specification/) che descrive le operazioni e i parametri dell'API.
Molte librerie, ad esempio [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), sono in grado di generare automaticamente il file Swagger. Per annotare il file Swagger per i nomi visualizzati, i tipi di proprietà e così via, è anche possibile usare [TRex](https://github.com/nihaue/TRex) in modo che il file Swagger funzioni bene con le app per la logica.

<a name="actions"></a>

## <a name="action-patterns"></a>Modelli di azione

Affinché le app per la logica eseguano le attività, l'API personalizzata deve specificare delle [*azioni*](./logic-apps-what-are-logic-apps.md#logic-app-concepts). Ogni operazione dell'API è mappata a un'azione. Un'azione di base è un controller che accetta le richieste HTTP e restituisce risposte HTTP. Un'app per la logica, ad esempio, invia una richiesta HTTP all'app Web o all'app per le API. L'app restituisce una risposta HTTP, insieme a contenuto che l'app per la logica è in grado di elaborare.

Per un'azione standard, è possibile scrivere un metodo di richiesta HTTP nell'API e descrivere tale metodo in un file Swagger. È quindi possibile chiamare l'API direttamente con un'[azione HTTP](../connectors/connectors-native-http.md) o un'azione [HTTP + Swagger](../connectors/connectors-native-http-swagger.md). Per impostazione predefinita, le risposte devono essere restituite entro il [limite di timeout della richiesta](./logic-apps-limits-and-config.md). 

![Modello di azione standard](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Per fare in modo che un'app per la logica resti in attesa mentre l'API termina le attività con tempi di esecuzione più lunghi, l'API può seguire il [modello di polling asincrono](#async-pattern) o il [modello webhook asincrono](#webhook-actions) descritto in questo argomento. Per un'analogia che consente di visualizzare i diversi comportamenti di questi modelli, si immagini la procedura necessaria per ordinare una torta personalizzata a un panificio. Il modello di polling rispecchia il comportamento in cui si chiama il panificio ogni 20 minuti per verificare se la torta è pronta. Il modello webhook rispecchia il comportamento in cui il panificio chiede al cliente il numero di telefono in modo da poterlo chiamare quando la torta è pronta.

Per gli esempi, visitare il [repository GitHub per le app per la logica](https://github.com/logicappsio). Vedere anche le informazioni sulla [misurazione dell'utilizzo per le azioni](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Eseguire attività a esecuzione prolungata con il modello di azione di polling

Per fare in modo che l'API esegua attività che possono avere una durata superiore al [limite di timeout della richiesta](./logic-apps-limits-and-config.md), è possibile usare il modello di polling asincrono. Questo modello consente all'API di funzionare in un thread separato, ma mantiene una connessione attiva al motore App per la logica. In questo modo, l'app per la logica non raggiunge il timeout né continua con il passaggio successivo del flusso di lavoro prima che termini l'attività dell'API.

Di seguito è illustrato il modello generale:

1. Verificare che il motore "sappia" che l'API ha accettato la richiesta e ha iniziato a lavorare.
2. Quando il motore effettua le richieste successive dello stato del processo, indicare al motore quando l'API termina l'attività.
3. Restituire i dati pertinenti al motore in modo che il flusso di lavoro dell'app per la logica possa continuare.

<a name="bakery-polling-action"></a> Applicare ora la stessa analogia del panificio al modello di polling e immaginare di chiamare un panificio e ordinare una torta personalizzata da consegnare. Il processo di preparazione della torta richiede tempo e non si vuole attendere al telefono mentre il panificio è al lavoro sulla torta. Il panificio conferma l'ordine e chiede di chiamare ogni 20 minuti per sapere qual è lo stato della torta. Dopo 20 minuti, si chiama il panificio ma dicono che la torta non è pronta e che è necessario chiamare dopo altri 20 minuti. Questo andare avanti e indietro continua fino a quando si telefona e il panificio dice che l'ordine è pronto e consegna la torta. 

Questo scenario si può mappare al modello di polling. Il panificio rappresenta l'API personalizzata, mentre il cliente che acquista la torta rappresenta il motore App per la logica. Quando il motore chiama l'API con una richiesta, l'API conferma la richiesta e risponde con l'intervallo di tempo quando il motore è in grado di controllare lo stato del processo. Il motore continua a verificare lo stato del processo finché l'API risponde indicando che il processo è completato e restituisce i dati all'app per la logica, che quindi continua il flusso di lavoro. 

![Modello di azione di polling](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Questi sono i passaggi specifici che l'API deve seguire, descritti dalla prospettiva dell'API:

1. Quando l'API riceve una richiesta HTTP per avviare il lavoro, restituisce immediatamente una risposta HTTP `202 ACCEPTED` con l'intestazione `location` descritta più avanti in questo passaggio. Questa risposta consente al motore App per la logica di sapere che l'API ha ricevuto la richiesta, ha accettato il payload della richiesta (input dati) e sta elaborando. 
   
   La risposta `202 ACCEPTED` deve includere queste intestazioni:
   
   * *Obbligatoria*: un'intestazione `location` che specifica il percorso assoluto a un URL in cui il motore App per la logica può controllare lo stato del processo dell'API

   * *Facoltativa*: un'intestazione `retry-after` che specifica il numero di secondi che il motore deve attendere prima di controllare l'URL `location` per lo stato del processo. 

     Per impostazione predefinita, il motore esegue il controllo ogni 20 secondi. Per specificare un intervallo diverso, includere l'intestazione `retry-after` e il numero di secondi fino al polling successivo.

2. Trascorso il tempo specificato, il motore App per la logica esegue il polling dell'URL `location` per controllare lo stato del processo. L'API deve eseguire questi controlli e restituire queste risposte:
   
   * Se il processo viene eseguito, restituisce una risposta HTTP `200 OK` insieme al payload di risposta (input per il passaggio successivo).

   * Se il processo è ancora in fase di elaborazione, restituisce un'altra risposta HTTP `202 ACCEPTED` con le stesse intestazioni della risposta originale.

Quando l'API segue questo modello, non è necessario eseguire alcuna operazione nella definizione del flusso di lavoro dell'app per la logica per continuare la verifica dello stato del processo. Quando il motore riceve una risposta HTTP `202 ACCEPTED` e un'intestazione `location` valida, il motore rispetta il modello asincrono e controlla l'intestazione `location` fino a quando l'API restituisce una risposta non 202.

> [!TIP]
> Per un esempio di modello asincrono, esaminare questo [esempio di risposta del controller asincrono in GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Eseguire attività a esecuzione prolungata con il modello di azione webhook

In alternativa, è possibile usare il modello webhook per le attività di lunga durata e l'elaborazione asincrona. Questo modello usa la pausa dell'app per la logica e attende un "callback" dall'API per terminare l'elaborazione prima di continuare il flusso di lavoro. Questo callback è un HTTP POST che invia un messaggio a un URL quando si verifica un evento. 

<a name="bakery-webhook-action"></a> Applicare ora la stessa analogia del panificio al modello webhook e immaginare di chiamare un panificio e ordinare una torta personalizzata da consegnare. Il processo di preparazione della torta richiede tempo e non si vuole attendere al telefono mentre il panificio è al lavoro sulla torta. Il panificio conferma l'ordine, ma questa volta il cliente lascia il proprio numero di telefono in modo che possano chiamarlo quando la torta è pronta. Questa volta il panificio avvisa quando l'ordine è pronto e consegna la torta.

In base all'analogia con il modello webhook, il panificio rappresenta l'API personalizzata, mentre il cliente che acquista la torta rappresenta il motore App per la logica. Il motore chiama l'API con una richiesta e include un URL di "callback".
Quando il processo viene eseguito, l'API usa l'URL per notificare al motore e restituire i dati all'app per la logica, che quindi continua il flusso di lavoro. 

Per questo modello, configurare due endpoint sul controller: `subscribe` e `unsubscribe`

*  Endpoint `subscribe`: quando l'esecuzione raggiunge l'azione dell'API nel flusso di lavoro, il motore App per la logica chiama l'endpoint `subscribe`. Questo passaggio fa sì che l'app per la logica crei un URL di callback, che viene archiviato dall'API, e quindi attenda il callback dell'API quando il lavoro viene completato. L'API quindi richiama con un HTTP POST all'URL e passa eventuali contenuti e intestazioni come input all'app per la logica.

* Endpoint `unsubscribe`: se l'esecuzione dell'app per la logica è stata annullata, il motore App per la logica chiama l'endpoint `unsubscribe`. L'API può quindi annullare la registrazione dell'URL di callback e arrestare i processi in base alle esigenze.

![Modello di azione webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Attualmente, Progettazione app per la logica non supporta l'individuazione di endpoint webhook con Swagger. Per questo modello è quindi necessario aggiungere un'[azione **webhook**](../connectors/connectors-native-webhook.md) e specificare l'URL, le intestazioni e il corpo per la richiesta. Vedere anche [Azioni e trigger del flusso di lavoro](logic-apps-workflow-actions-triggers.md#api-connection-webhook-action). Per passare l'URL di callback, è possibile usare la funzione di flusso di lavoro `@listCallbackUrl()` in uno qualsiasi dei campi precedenti in base alle necessità.

> [!TIP]
> Per un esempio di modello webhook, esaminare questo [esempio di trigger webhook in GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Modelli di trigger

L'API personalizzata può anche agire come un [*trigger*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) che avvia un'app per la logica quando nuovi dati o un evento soddisfano una condizione specificata. Questo trigger può verificare regolarmente, o attendere e restare in ascolto, la presenza di nuovi dati o eventi presso l'endpoint servizio. Se nuovi dati o eventi soddisfano la condizione specificata, il trigger viene attivato e avvia l'app per la logica, che è in ascolto di quel trigger. Per avviare l'app per la logica in questo modo, l'API può seguire il modello di [*trigger di polling*](#polling-triggers) o [*trigger webhook*](#webhook-triggers). Questi modelli sono simili alle loro controparti per le [azioni di polling](#async-pattern) e le [azioni webhook](#webhook-actions). Vedere anche le informazioni sulla [misurazione dell'utilizzo per i trigger](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Verificare la presenza di nuovi dati o eventi regolarmente con il modello di trigger di polling

Un *trigger di polling* funziona in modo molto simile all'[azione di polling](#async-pattern) descritta in precedenza in questo argomento. Il motore App per la logica periodicamente chiama l'endpoint di trigger e verifica la presenza di nuovi dati o eventi. Se il motore rileva nuovi dati o eventi che soddisfano la condizione specificata, il trigger viene attivato. Quindi, il motore crea un'istanza di app per la logica che elabora i dati come input. 

![Modello di trigger di polling](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Ogni richiesta di polling viene considerata un'esecuzione di azione, anche quando non viene creata alcuna istanza di app per la logica. Per evitare che gli stessi dati vengano elaborati più volte, il trigger deve pulire i dati che sono già stati letti e passati all'app per la logica.

Di seguito sono riportati i passaggi specifici per un trigger di polling, descritti dalla prospettiva dell'API:

| Sono stati rilevati nuovi dati o eventi?  | Risposta dell'API | 
| ------------------------- | ------------ |
| Trovato | Restituire uno stato HTTP `200 OK` con il payload di risposta (input per il passaggio successivo). <br/>Questa risposta crea un'istanza di app per la logica e avvia il flusso di lavoro. |
| Non trovato | Restituire uno stato HTTP `202 ACCEPTED` con un'intestazione `location` e un'intestazione `retry-after`. <br/>Per i trigger, l'intestazione `location` deve contenere anche un parametro di query `triggerState`, che in genere è "timestamp". L'API è in grado di usare questo identificatore per verificare quando è stata attivata l'app per la logica per l'ultima volta. |

Ad esempio, per verificare periodicamente se nel servizio sono presenti nuovi file, si può creare un trigger di polling con questi comportamenti:

| La richiesta include `triggerState`? | Risposta dell'API |
| -------------------------------- | -------------|
| No | Restituire uno stato HTTP `202 ACCEPTED` oltre a un'intestazione `location` con `triggerState` impostato sull'ora corrente e l'intervallo `retry-after` su 15 secondi. |
| Sì | Verificare se nel servizio sono presenti file aggiunti dopo `DateTime` per `triggerState`. |

| Numero di file trovati | Risposta dell'API |
| --------------------- | -------------|
| Singolo file | Restituire uno stato HTTP `200 OK` e il payload del contenuto, aggiornare `triggerState` a `DateTime` per il file restituito e impostare l'intervallo `retry-after` su 15 secondi. |
| File multipli | Restituire un file alla volta e uno stato HTTP `200 OK`, aggiornare `triggerState` e impostare l'intervallo `retry-after` su 0 secondi. </br>Questi passaggi consentono al motore di sapere che sono disponibili più dati e che il motore deve richiedere immediatamente i dati dall'URL nell'intestazione `location`. |
| Nessun file | Restituire uno stato HTTP `202 ACCEPTED`, non modificare `triggerState` e impostare l'intervallo `retry-after` su 15 secondi. |

> [!TIP]
> Per un esempio di modello di trigger di polling, vedere questo [esempio di controller di trigger di polling in GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Attendere e restare in ascolto di nuovi dati o eventi con il modello di trigger webhook

Un trigger webhook è un *trigger di push* che attende e resta in ascolto di nuovi dati o eventi in corrispondenza dell'endpoint servizio. Se i nuovi dati o un evento soddisfano la condizione specificata, il trigger viene attivato e crea un'istanza di app per la logica, che quindi elabora i dati come input.
I trigger webhook funzionano in modo molto simile alle [azioni webhook](#webhook-actions) descritte in precedenza in questo argomento e sono impostati con gli endpoint `subscribe` e `unsubscribe`. 

* Endpoint `subscribe`: quando si aggiunge e si salva un trigger webhook nell'app per la logica, il motore App per la logica chiama l'endpoint `subscribe`. Questo passaggio fa sì che l'app per la logica crei un URL di callback che archivia l'API. Quando sono presenti nuovi dati, o un evento, che soddisfano la condizione specificata o nuovi dati, l'API richiama con un HTTP POST all'URL. Il payload di contenuto e le intestazioni vengono passati come input all'app per la logica.

* Endpoint `unsubscribe`: se si elimina il trigger webhook o l'intera app per la logica, il motore App per la logica chiama l'endpoint `unsubscribe`. L'API può quindi annullare la registrazione dell'URL di callback e arrestare i processi in base alle esigenze.

![Modello di trigger webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Attualmente, Progettazione app per la logica non supporta l'individuazione di endpoint webhook con Swagger. Per questo modello è quindi necessario aggiungere un [trigger **webhook**](../connectors/connectors-native-webhook.md) e specificare l'URL, le intestazioni e il corpo per la richiesta. Vedere anche [Trigger HTTPWebhook](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Per passare l'URL di callback, è possibile usare la funzione di flusso di lavoro `@listCallbackUrl()` in uno qualsiasi dei campi precedenti in base alle necessità.
>
> Per evitare che gli stessi dati vengano elaborati più volte, il trigger deve pulire i dati che sono già stati letti e passati all'app per la logica.

> [!TIP]
> Per un esempio di modello webhook, esaminare questo [esempio di controller di trigger webhook in GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="deploy-call-and-secure-custom-apis"></a>Distribuire, chiamare e proteggere le API personalizzate

Dopo aver creato le API personalizzate, impostare le API per la distribuzione in modo da chiamarle in modo sicuro. Informazioni su come [implementare, chiamare e proteggere le API personalizzate per le app per la logica](./logic-apps-custom-hosted-api.md).

## <a name="publish-custom-apis-to-azure"></a>Pubblicare API personalizzate in Azure

Per rendere le API personalizzate disponibili per l'uso pubblico in Azure, inviare le candidature al [programma Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/).

## <a name="get-help"></a>Ottenere aiuto

Per assistenza specifica per le API personalizzate, contattare [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

Per porre domande, fornire risposte e ottenere informazioni sulle attività degli altri utenti delle app per la logica di Azure, vedere il [forum sulle app per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Per contribuire al miglioramento delle app per la logica e dei connettori, votare o inviare idee al [sito dei commenti e suggerimenti degli utenti delle app per la logica](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Passaggi successivi

* [Misurazione dell'utilizzo per azioni e trigger](logic-apps-pricing.md)
* [Gestire i tipi di contenuti](./logic-apps-content-type.md)
* [Gestire errori ed eccezioni](./logic-apps-exception-handling.md)
* [Proteggere l'accesso alle app per la logica](./logic-apps-securing-a-logic-app.md)
* [Chiamare, attivare o annidare le app per la logica con endpoint HTTP](./logic-apps-http-endpoint.md)
