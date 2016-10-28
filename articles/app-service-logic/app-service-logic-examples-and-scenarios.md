<properties
   pageTitle="Esempi e scenari di app per la logica | Microsoft Azure"
   description="Vedere gli esempi di app per la logica comuni e le informazioni sull'implementazione di scenari comuni"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/11/2016"
   ms.author="jehollan"/>

# Esempi e scenari comuni di app per la logica

Questo documento mostra scenari comuni ed esempi di alcune modalità d'uso delle app per la logica per l'automazione dei processi aziendali.

## Azioni e trigger personalizzati

Esistono diversi modi per attivare un'app per la logica da un'altra app. Ecco alcuni esempi comuni:

- [Creazione di un trigger o un'azione personalizzata](app-service-logic-create-api-app.md)
- [Azioni a esecuzione prolungata](app-service-logic-create-api-app.md)
- [Trigger della richiesta HTTP (POST)](app-service-logic-http-endpoint.md)
- [Trigger e azioni webhook](app-service-logic-create-api-app.md)
- [Trigger di polling](app-service-logic-create-api-app.md)

### Scenari

- [Risposta sincrona alle richieste](app-service-logic-http-endpoint.md)
- [Risposta alle richieste con SMS](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## Registrazione e gestione degli errori

- [Gestione degli errori e delle eccezioni](app-service-logic-exception-handling.md)
- [Configurare gli avvisi e la diagnostica di Azure](app-service-logic-monitor-your-logic-apps.md)

### Scenari

- [Caso d'uso: Gestione di errori ed eccezioni](app-service-logic-scenario-error-and-exception-handling.md)

## Distribuzione e gestione

- [Creare un modello di distribuzione di app per la logica](app-service-logic-create-deploy-template.md)
- [Compilare e distribuire app per la logica in Visual Studio](app-service-logic-deploy-from-vs.md)
- [Monitorare le app per la logica](app-service-logic-monitor-your-logic-apps.md)

## Tipi di contenuto, conversioni e trasformazioni

Il [linguaggio di definizione del flusso di lavoro](http://aka.ms/logicappsdocs) delle app per la logica include numerose funzioni che consentono di convertire e usare diversi tipi di contenuto. Il motore esegue tutte le operazioni possibili per mantenere i tipi di contenuto come flussi di dati attraverso il flusso di lavoro.

- [Gestione dei tipi di contenuto](app-service-logic-content-type.md), come application/json, application/xml e text/plain
- [Creazione di definizioni del flusso di lavoro](app-service-logic-author-definitions.md)
- [Riferimento al linguaggio di definizione del flusso di lavoro](http://aka.ms/logicappsdocs)

## Batch ed esecuzione di cicli

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## Integrazione con Funzioni di Azure

- [Integrazione di Funzioni di Azure](app-service-logic-azure-functions.md)

### Scenari

- [Funzioni di Azure come trigger del bus di servizio](app-service-logic-scenario-function-sb-trigger.md)

## HTTP, REST e SOAP

 - [Chiamata SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Verranno aggiunti altri esempi e scenari a questo documento. Usare la sezione dei commenti in basso per segnalare eventuali esempi o scenari da includere.

<!---HONumber=AcomDC_0817_2016-->