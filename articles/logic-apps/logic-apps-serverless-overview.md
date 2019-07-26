---
title: Panoramica-senza server di Azure
description: Crea soluzioni potenti nel cloud senza preoccuparti dell'infrastruttura
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 363002712bdd06e74360de9af186f5a458a4999d
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385365"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Panoramica Azure senza server con app per la logica di Azure e funzioni di Azure

Le app senza [Server](https://azure.microsoft.com/solutions/serverless/) offrono vantaggi come una maggiore velocità di sviluppo, codice ridotto, semplicità e scalabilità. Questo articolo illustra i diversi attributi delle soluzioni senza server e delle offerte senza server di Azure.

## <a name="what-is-serverless"></a>Cosa significa "senza server"?

Senza server non si intende alcun server, ma gli sviluppatori non devono preoccuparsi dei server. Lo sviluppo di applicazioni tradizionali si basa essenzialmente sulla capacità di rispondere a domande su scalabilità, hosting e soluzioni di monitoraggio per soddisfare le esigenze specifiche dell'applicazione. Con senza server, queste domande vengono gestite come parte della soluzione. Inoltre, le app senza server vengono fatturate in base a un piano a consumo. Se l'app non viene mai usata, non viene addebitato alcun addebito. Queste funzionalità consentono agli sviluppatori di concentrarsi esclusivamente sulla logica di business di una soluzione.

I servizi di base di Azure per server sono app per la [logica di Azure](https://azure.microsoft.com/services/logic-apps/) e funzioni di [Azure](https://azure.microsoft.com/services/functions/). Entrambe le soluzioni seguono i principi descritti in precedenza e aiutano gli sviluppatori a creare app cloud affidabili con codice minimo.

## <a name="what-is-azure-logic-apps"></a>Informazioni su App per la logica di Azure

[App](logic-apps-overview.md) per la logica di Azure offre un modo per semplificare e implementare flussi di lavoro e integrazioni scalabili nel cloud. Questo servizio fornisce una finestra di progettazione visiva per modellare e automatizzare il processo come una serie di passaggi chiamati flusso di lavoro. Esistono molti [connettori](../connectors/apis-list.md) tra i servizi cloud e i sistemi locali che consentono di connettere rapidamente un'app senza server ad altre API. Ogni app per la logica inizia con un trigger, ad esempio "quando un account viene aggiunto a Dynamics CRM". Dopo che il trigger è stato attivato, il flusso di lavoro può eseguire combinazioni di azioni, conversioni e logica condizionale. App per la logica è un'ottima scelta quando si orchestrano funzioni di Azure diverse in un processo, soprattutto quando il processo richiede l'interazione con un sistema o un'API esterna.

Per iniziare ad acquisire familiarità con App per la logica, [creare una prima app per la logica](quickstart-create-first-logic-app-workflow.md). Per informazioni più tecniche sulle app per la logica, vedere la Guida di riferimento per gli [sviluppatori](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Che cos'è funzioni di Azure?

Funzioni di Azure è un servizio che consente di eseguire facilmente parti di codice o "funzioni" nel cloud. È possibile scrivere solo il codice necessario per il problema corrente, senza preoccuparsi di un'intera applicazione o dell'infrastruttura necessaria. Funzioni può rendere più produttiva l'attività di sviluppo e consente di usare il linguaggio di sviluppo preferito, ad esempio C#, F#, Node.js, Python o PHP. Si paga solo per il tempo di esecuzione del codice e Azure Scales in base alle esigenze.

Per iniziare a usare funzioni di Azure, iniziare con [creare la prima funzione di Azure](../azure-functions/functions-create-first-azure-function.md). Per informazioni più tecniche sulle funzioni, vedere la Guida di riferimento per gli [sviluppatori](../azure-functions/functions-reference.md).

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Come è possibile creare e distribuire app senza server in Azure?

Azure offre strumenti avanzati per lo sviluppo, la distribuzione e la gestione di app senza server. È possibile compilare app direttamente nel portale di Azure, con [strumenti in Visual Studio](logic-apps-serverless-get-started-vs.md)o [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md). Dopo aver compilato l'app, è possibile [distribuirla rapidamente con i modelli Azure Resource Manager](logic-apps-deploy-azure-resource-manager-templates.md). Azure fornisce anche il monitoraggio, a cui è possibile accedere tramite l'portale di Azure, tramite l'API o gli SDK o con strumenti integrati per i log di monitoraggio di Azure e Application Insights.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'app senza server in Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Creare un dashboard Customer Insights con server senza server](logic-apps-scenario-social-serverless.md)
* [Automatizzare la distribuzione delle app per la logica](logic-apps-azure-resource-manager-templates-overview.md)
