---
title: Panoramica - Azure Serverless per le app e le soluzioni basate su cloud
description: Informazioni su come creare app e soluzioni basate su cloud senza preoccuparsi dell'infrastruttura tramite App per la logica di Azure e Funzioni di Azure
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666551"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure Serverless: Overview for building cloud-based apps and solutions with Azure Logic Apps and Azure Functions

[Le](https://azure.microsoft.com/solutions/serverless/) app senza server offrono vantaggi come maggiore velocità di sviluppo, riduzione del codice, semplicità e scalabilità. Questo articolo illustra i diversi attributi delle soluzioni senza server e delle offerte senza server di Azure.This article covers the different attributes of serverless solutions and Azure serverless offerings.

## <a name="what-is-serverless"></a>Cosa significa "senza server"?

Serverless non significa che non ci siano server, ma piuttosto gli sviluppatori non devono preoccuparsi dei server. Lo sviluppo di applicazioni tradizionali si basa essenzialmente sulla capacità di rispondere a domande su scalabilità, hosting e soluzioni di monitoraggio per soddisfare le esigenze specifiche dell'applicazione. Con serverless, queste domande sono prese in considerazione come parte della soluzione. Inoltre, le app senza server vengono fatturate in base a un piano basato sul consumo. Se l'app non viene mai utilizzata, non viene addebitato alcun costo. Queste funzionalità consentono agli sviluppatori di concentrarsi esclusivamente sulla logica di business di una soluzione.

I servizi di Azure principali per serverless sono [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) e Azure [Functions](https://azure.microsoft.com/services/functions/). Entrambe le soluzioni seguono i principi descritti in precedenza e aiutano gli sviluppatori a creare app cloud affidabili con codice minimo.

## <a name="what-is-azure-logic-apps"></a>Informazioni su App per la logica di Azure

[App per la logica](logic-apps-overview.md) di Azure offre un modo per semplificare e implementare integrazioni e flussi di lavoro scalabili nel cloud. Questo servizio fornisce una finestra di progettazione visiva per modellare e automatizzare il processo come una serie di passaggi denominati flusso di lavoro. Esistono molti [connettori](../connectors/apis-list.md) tra i servizi cloud e i sistemi locali che connettono rapidamente un'app senza server ad altre API. Ogni app per la logica inizia con un trigger, ad esempio "Quando un account viene aggiunto a Dynamics CRM". Dopo l'attivazione del trigger, il flusso di lavoro può eseguire combinazioni di azioni, conversioni e logica condizionale. App per la logica è un'ottima scelta quando si orchestrano funzioni di Azure diverse in un processo, soprattutto quando il processo richiede l'interazione con un sistema esterno o un'API.

Per iniziare ad acquisire familiarità con App per la logica, [creare una prima app per la logica](quickstart-create-first-logic-app-workflow.md). Per ulteriori informazioni tecniche sulle app per la logica, vedere le informazioni di [riferimento per gli sviluppatori](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Che cos'è Funzioni di Azure?

Funzioni di Azure è un servizio per l'esecuzione di parti di codice o "funzioni" nel cloud. È possibile scrivere solo il codice necessario per il problema corrente, senza preoccuparsi di un'intera app o dell'infrastruttura necessaria. Funzioni può rendere più produttiva l'attività di sviluppo e consente di usare il linguaggio di sviluppo preferito, ad esempio C#, F#, Node.js, Python o PHP. Si paga solo per il tempo di esecuzione del codice e Azure viene ridimensionato in base alle esigenze.

Per iniziare a usare Funzioni di Azure, iniziare con Creare la prima funzione di [Azure.](../azure-functions/functions-create-first-azure-function.md) Per ulteriori informazioni tecniche sulle funzioni, vedere le informazioni di [riferimento per gli sviluppatori](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Come è possibile creare e distribuire app senza server in Azure?

Azure offre strumenti avanzati per lo sviluppo, la distribuzione e la gestione di app senza server. È possibile compilare app direttamente nel portale di Azure, con [gli strumenti in Visual Studio](logic-apps-serverless-get-started-vs.md)o visual Studio [Code](quickstart-create-logic-apps-visual-studio-code.md). Dopo aver creato l'app, è possibile [distribuirla rapidamente con](logic-apps-deploy-azure-resource-manager-templates.md)i modelli di Azure Resource Manager. Azure fornisce anche il monitoraggio, a cui è possibile accedere tramite il portale di Azure, tramite api o SDK o strumenti integrati per i log di Monitoraggio di Azure e Application Insights.Azure also provides monitoring, which you can access through the Azure portal, through the API or SDK, or with integrated tooling for Azure Monitor logs and Application Insights.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app senza server in Visual StudioBuild a serverless app in Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Crea un dashboard di Customer Insights senza server](logic-apps-scenario-social-serverless.md)
* [Automatizzare la distribuzione di app per la logica](logic-apps-azure-resource-manager-templates-overview.md)
