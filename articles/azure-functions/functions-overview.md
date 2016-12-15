---
title: Panoramica di Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come usare Funzioni di Azure per ottimizzare i carichi di lavoro asincroni in pochi minuti.
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/20/2016
ms.author: cfowler;mahender;glenga
translationtype: Human Translation
ms.sourcegitcommit: 30cc3b8749d5b36b89b242e2691003cc6f67f7d2
ms.openlocfilehash: 00359057d702c556cd8beb91cf17ccf41c96f601


---
# <a name="azure-functions-overview"></a>Panoramica di Funzioni di Azure
Funzioni di Azure è una soluzione che consente di eseguire facilmente piccole parti di codice, o "funzioni", nel cloud. È possibile scrivere solo il codice necessario per il problema effettivo, senza preoccuparsi di un'intera applicazione o dell'infrastruttura necessaria per eseguirlo. Le funzioni possono rendere più produttiva l'attività di sviluppo e consentono di usare il linguaggio di sviluppo preferito, ad esempio C#, F#, Node.js, Python o PHP. Pagare solo per il tempo in cui il codice viene eseguito ed essere certi che Azure venga ridimensionato in base alle esigenze.

Questo argomento offre una panoramica generale di Funzioni di Azure. Se si vuole iniziare immediatamente a usare Funzioni di Azure, passare a [Creare la prima funzione di Azure](functions-create-first-azure-function.md). Per informazioni più tecniche su Funzioni, vedere [Guida di riferimento per gli sviluppatori](functions-reference.md).

## <a name="features"></a>Funzionalità
Ecco alcune delle principali funzionalità di Funzioni di Azure:

* **Scelta del linguaggio**: è possibile scrivere funzioni con C#, F#, Node.js, Python, PHP, batch, bash o qualsiasi eseguibile.
* **Modello tariffario in base al consumo** : si paga solo per il tempo impiegato per l'esecuzione del codice. Visualizzare l'opzione del piano di hosting A consumo nella [sezione relativa ai prezzi](#pricing).  
* **Trasferimento delle dipendenze** : Funzioni supporta NuGet e NPM, quindi è possibile usare le librerie preferite.  
* **Sicurezza integrata** : è possibile proteggere le funzioni attivate da HTTP con provider OAuth, ad esempio Azure Active Directory, Facebook, Google, Twitter e account Microsoft.  
* **Integrazione semplificata** : è possibile sfruttare facilmente i servizi di Azure e le offerte di software come un servizio (SaaS). Per alcuni esempi, vedere la [sezione relativa alle integrazioni](#integrations).  
* **Sviluppo flessibile** : è possibile creare le funzioni direttamente nel portale o configurare l'integrazione continua e distribuire il codice con GitHub, Visual Studio Team Services e altri [strumenti di sviluppo supportati](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Open source** : il runtime di Funzioni è open source e [disponibile in GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Quali operazioni si possono eseguire con Funzioni?
Funzioni di Azure è un'ottima soluzione per l'elaborazione di dati, l'integrazione di sistemi, l'utilizzo di Internet delle cose (IoT) e la compilazione di semplici API e microservizi. È possibile usare Funzioni per attività come l'elaborazione di immagini o di ordini, la manutenzione di file, le attività con esecuzione prolungata che si preferisce eseguire in un thread in background o per qualsiasi attività si voglia eseguire in base a una pianificazione. 

Funzioni fornisce modelli per iniziare con scenari chiave, inclusi i seguenti:

* **BlobTrigger** : elabora i BLOB di archiviazione di Azure quando vengono aggiunti ai contenitori. Questa funzione può essere usata per il ridimensionamento delle immagini.
* **EventHubTrigger**: risponde agli eventi inviati a un hub eventi di Azure. È particolarmente utile negli scenari di strumentazione delle applicazioni, elaborazione dei flussi di lavoro o dell'esperienza utente e di Internet delle cose (IoT).
* **Webhook generico** : elabora le richieste HTTP di webhook da qualsiasi servizio che supporti webhook.
* **Webhook GitHub** : risponde a eventi che si verificano nei repository GitHub. Per un esempio, vedere [Creare un webhook o una funzione API](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** : attiva l'esecuzione del codice usando una richiesta HTTP.
* **QueueTrigger** : risponde ai messaggi che arrivano in una coda di archiviazione di Azure. Per un esempio, vedere [Creare una funzione di Azure associata a un servizio di Azure](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** : connette il codice ad altri servizi di Azure o locali rimanendo in ascolto sulle code di messaggi. 
* **ServiceBusTopicTrigger** : connette il codice ad altri servizi di Azure o servizi locali mediante la sottoscrizione di argomenti. 
* **TimerTrigger** : esegue attività di pulizia o altre attività batch secondo una pianificazione predefinita. Per un esempio, vedere [Creare una funzione di Azure di elaborazione di eventi](functions-create-an-event-processing-function.md).

Funzioni di Azure supporta i *trigger*, che permettono di avviare l'esecuzione del codice, e le *associazioni*, che permettono di semplificare la codifica dei dati di input e output. Per una descrizione dettagliata di associazioni e trigger forniti da Funzioni di Azure, vedere [Guida di riferimento per gli sviluppatori di trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md).

## <a name="a-nameintegrationsaintegrations"></a><a name="integrations"></a>Integrazioni
Funzioni di Azure si integra con un'ampia gamma di servizi di Azure e di terze parti. Questi servizi consentono di attivare la funzione e avviare l'esecuzione o possono essere usati come input e output per il codice. Funzioni di Azure supporta le integrazioni di servizi seguenti. 

* Azure DocumentDB
* Hub eventi di Azure 
* App per dispositivi mobili di Azure (tabelle)
* Hub di notifica di Azure
* Bus di servizio di Azure (code e argomenti)
* Archiviazione di Azure (BLOB, code e tabelle) 
* GitHub (webhook)
* Locale (tramite il bus di servizio)

## <a name="a-namepricingahow-much-does-functions-cost"></a><a name="pricing"></a>Quanto costa Funzioni?
Funzioni di Azure viene offerto con due tipi di piani tariffari. Scegliere quello più adatto alle proprie esigenze: 

* **Piano A consumo**: quando viene eseguita la funzione, Azure offre tutte le risorse di calcolo necessarie. Non ci si deve preoccupare della gestione delle risorse e si paga solo in base al tempo impiegato per l'esecuzione del codice. 
* **Piano di Servizio app** : consente di eseguire le funzioni esattamente come le app Web, per dispositivi mobili e per le API. Se si usa già il servizio app per altre applicazioni, è possibile eseguire le funzioni nello stesso piano senza costi aggiuntivi. 

I dettagli completi sui prezzi sono disponibili nella [pagina Prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/). Per altre informazioni sul ridimensionamento delle funzioni, vedere [Come ridimensionare Funzioni di Azure](functions-scale.md).

## <a name="next-steps"></a>Passaggi successivi
* [Creare la prima funzione di Azure](functions-create-first-azure-function.md)  
   Informazioni su come iniziare immediatamente a creare la prima funzione tramite Avvio rapido di Funzioni di Azure. 
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)  
   Include informazioni più tecniche sul runtime di Funzioni di Azure, nonché informazioni di riferimento per la codifica di funzioni e la definizione di trigger e associazioni.
* [Test di Funzioni di Azure](functions-test-a-function.md)  
   Descrive diversi strumenti e tecniche per il test delle funzioni.
* [Come aumentare le prestazioni di Funzioni di Azure](functions-scale.md)  
  Presenta i piani di servizio disponibili con Funzioni di Azure, tra cui il piano di hosting A consumo, e spiega come scegliere quello più appropriato. 
* [Informazioni sul servizio app di Azure](../app-service/app-service-value-prop-what-is.md)  
   Funzioni di Azure sfrutta la piattaforma del servizio app di Azure per le funzionalità di base, ad esempio distribuzioni, variabili di ambiente e diagnostica. 




<!--HONumber=Dec16_HO1-->


