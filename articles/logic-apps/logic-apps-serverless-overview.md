---
title: Panoramica dell'architettura senza server di Azure | Microsoft Docs
description: Creare potenti soluzioni nel cloud senza preoccuparsi dell'infrastruttura.
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 5cc6837ed0b0f4467e48c736f5d596a51a799fae
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="overview-of-azure-serverless-with-functions-and-logic-apps"></a>Panoramica dell'architettura senza server di Azure con Funzioni e App per la logica

Le applicazioni senza server offrono una serie di vantaggi, tra cui un aumento della velocità di sviluppo, una riduzione del codice necessario e una scalabilità più semplice.  Questo articolo illustra i diversi attributi delle soluzioni senza server e delle offerte senza server di Azure.

## <a name="what-is-serverless"></a>Cosa significa "senza server"?

Con l'espressione "senza server" non si intende assenza di server, ma che lo sviluppatore non deve preoccuparsi dei server.  Lo sviluppo di applicazioni tradizionali si basa essenzialmente sulla capacità di rispondere a domande su scalabilità, hosting e soluzioni di monitoraggio per soddisfare le esigenze specifiche dell'applicazione.  Con un'infrastruttura senza server, queste domande vengono gestite nell'ambito della soluzione.  Le applicazioni senza server, inoltre, prevedono una fatturazione in base al consumo.  Se l'applicazione non viene mai usata, non viene mai addebitato niente.  Queste funzionalità consentono agli sviluppatori di concentrarsi esclusivamente sulla logica di business della soluzione.

[Funzioni di Azure](https://azure.microsoft.com/services/functions/) e [App per la logica di Azure ](https://azure.microsoft.com/services/logic-apps/) sono i principali servizi di Azure che si avvalgono di un'infrastruttura senza server.  Entrambe le soluzioni seguono i principi sopra elencati e consentono agli sviluppatori di creare avanzate applicazioni cloud con una quantità minima di codice.

## <a name="what-are-azure-functions"></a>Che cos'è Funzioni di Azure?

Funzioni di Azure è una soluzione che consente di eseguire facilmente piccole parti di codice, o "funzioni", nel cloud. È possibile scrivere solo il codice necessario per il problema effettivo, senza preoccuparsi di un'intera applicazione o dell'infrastruttura necessaria per eseguirlo. Funzioni può rendere più produttiva l'attività di sviluppo e consente di usare il linguaggio di sviluppo preferito, ad esempio C#, F#, Node.js, Python o PHP. Il costo addebitato si basa solo sul tempo in cui viene eseguito il codice e Azure viene scalato in base alle esigenze.

Se si vuole iniziare immediatamente a usare Funzioni di Azure, passare a [Creare la prima funzione di Azure](../azure-functions/functions-create-first-azure-function.md). Per informazioni più tecniche su Funzioni, vedere [Guida di riferimento per gli sviluppatori](../azure-functions/functions-reference.md).

## <a name="what-are-azure-logic-apps"></a>Che cos'è App per la logica di Azure?

App per la logica di Azure consente di semplificare e implementare nel cloud flussi di lavoro e integrazioni scalabili. Offre una finestra di progettazione visiva in cui poter modellare e automatizzare il processo tramite una serie di passaggi, ovvero un flusso di lavoro.  Nei servizi cloud e locali sono disponibili [numerosi connettori](../connectors/apis-list.md) che consentono di connettere rapidamente un'app senza server ad altre API.  Un'app per la logica viene avviata con un trigger (corrispondente ad esempio all'aggiunta di un account a Dynamics CRM) e dopo l'attivazione può avviare molte azioni di combinazione, conversioni e logica condizionale.  App per la logica è la scelta ideale quando si devono coordinare varie funzioni di Azure in un processo, in particolar modo se il processo richiede l'interazione con un'API o un sistema esterno.

Per iniziare ad acquisire familiarità con App per la logica, [creare una prima app per la logica](quickstart-create-first-logic-app-workflow.md).  Per informazioni più tecniche su App per la logica, vedere [Guida di riferimento per gli sviluppatori](logic-apps-workflow-actions-triggers.md).

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>Come è possibile compilare e distribuire applicazioni senza server in Azure?

Azure offre un'ampia gamma di strumenti di sviluppo, distribuzione e gestione per app senza server.  Le app possono essere compilate direttamente nel portale di Azure oppure con [gli strumenti di Visual Studio](logic-apps-serverless-get-started-vs.md).  Dopo essere stata sviluppata, un'applicazione può essere [immediatamente distribuita](logic-apps-create-deploy-template.md).  Azure fornisce anche strumenti per il monitoraggio di app senza server,  accessibili dal portale di Azure, tramite l'API o SDK o con gli strumenti integrati in OMS e Application Insights.

## <a name="next-steps"></a>Passaggi successivi

* [Iniziare a compilare un'app senza server in Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Creare un dashboard Customer Insights con app senza server](logic-apps-scenario-social-serverless.md)
* [Creare un modello di distribuzione per un'app per la logica](logic-apps-create-deploy-template.md)