<properties
   pageTitle="Esempi e scenari di app per la logica | Microsoft Azure"
   description="Vedere gli esempi di app per la logica comuni e le informazioni sull'implementazione di scenari comuni"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/25/2016"
   ms.author="deonhe"/>

# Esempi e scenari comuni di app per la logica

Questo documento mostra scenari comuni ed esempi di alcune modalità d'uso delle app per la logica per l'automazione dei processi aziendali.

## Attivazione di un'app per la logica

Esistono diversi modi per attivare un'app per la logica da un'altra app. Di seguito sono riportati alcuni di questi modi:

- [Richiesta HTTP (POST)](app-service-logic-http-endpoint.md)
- [Ricevere un webhook](app-service-logic-create-api-app.md)
- [Polling di un endpoint](app-service-logic-create-api-app.md)

### Scenari

- [Risposta sincrona alle richieste nelle app per la logica](app-service-logic-http-endpoint.md)

## Azioni personalizzate e con esecuzione prolungata

- [Creazione di un'azione personalizzata](app-service-logic-create-api-app.md)

## Distribuzione e gestione di un'app per la logica

- [Creare un modello di distribuzione di app per la logica](app-service-logic-create-deploy-template.md)
- [Diagnosticare i problemi di un'app per la logica](app-service-logic-diagnosing-failures.md)
- [Distribuire l'app per la logica da Visual Studio](app-service-logic-deploy-from-vs.md)
- [Monitorare un'app per la logica](app-service-logic-monitor-your-logic-apps.md).

## Tipi di contenuto, conversioni e trasformazioni

Il [linguaggio di definizione del flusso di lavoro](http://aka.ms/logicappsdocs) delle app per la logica contiene numerose funzioni che consentono di convertire e utilizzare diversi tipi di contenuto. Il motore esegue tutte le operazioni possibili per mantenere i tipi di contenuto come flussi di dati attraverso il flusso di lavoro.

- [Gestione dei tipi di contenuto](app-service-logic-content-type.md) come application/json, application/xml e text/plain
- [Creazione di definizioni del flusso di lavoro](app-service-logic-author-definitions.md)
- [Linguaggio di definizione del flusso di lavoro](http://aka.ms/logicappsdocs)

## Batch ed esecuzione di cicli

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## Integrazione con Funzioni di Azure

- [Integrazione di funzioni di Azure](app-service-logic-azure-functions.md)

### Scenari

- [Funzioni di Azure come trigger del bus di servizio](app-service-logic-scenario-function-sb-trigger.md)

## HTTP, REST e SOAP

 - [Chiamata SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Verranno aggiunti altri esempi e scenari a questo documento. Usare la sezione dei commenti in basso per segnalare eventuali esempi o scenari da includere.

<!---HONumber=AcomDC_0727_2016-->