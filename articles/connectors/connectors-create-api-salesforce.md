---
title: Connettersi a Salesforce da App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i flussi di lavoro per il monitoraggio, la creazione e la gestione dei record Salesforce con App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 03c250f153402c68889c2e3ac187ccab3e2d858b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887486"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorare, creare e gestire le risorse di Salesforce con App per la logica di Azure

Con App per la logica di Azure e il connettore Salesforce, è possibile creare attività e flussi di lavoro automatizzati per le risorse di Salesforce, come record, processi e oggetti, ad esempio:

* Eseguire il monitoraggio quando i record vengono creati o modificati. 
* Creare, ottenere e gestire processi e record, tra cui azioni di inserimento, aggiornamento ed eliminazione.

È possibile usare i trigger di Salesforce per ottenere risposte dall'account Salesforce e rendere l'output disponibile per altre azioni. È possibile usare azioni nelle app per la logica per eseguire attività con le risorse di Salesforce. Se non si ha familiarità con App per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Un [account Salesforce](https://salesforce.com/).

* Conoscenza di base sulla [creazione delle app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* L'app per la logica in cui si vuole accedere all'account Salesforce. Per iniziare con un trigger di Salesforce, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione di Salesforce, avviare l'app per la logica con un altro trigger, ad esempio, il trigger **Ricorrenza**.

## <a name="connect-to-salesforce"></a>Connettersi a Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Scegliere un percorso: 

   * Per le app per la logica vuote, nella casella di ricerca immettere "salesforce" come filtro. 
   Nell'elenco dei trigger selezionare il trigger desiderato. 

     -oppure-

   * Per le app per la logica esistenti, nel passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. Nella casella di ricerca immettere "salesforce" come filtro. Nell'elenco delle azioni scegliere l'azione desiderata.

1. Se viene chiesto di accedere a Salesforce, effettuare e consentire l'accesso.

   Le credenziali autorizzano l'app per la logica alla creazione di una connessione a Salesforce e all'accesso ai dati.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/salesforce/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)