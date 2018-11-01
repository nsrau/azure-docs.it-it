---
title: Connettersi a Trello da App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i flussi di lavoro per il monitoraggio di elenchi, bacheche e schede nei progetti di Trello con App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 31357fa313cfa31c0f8a90c0f7722f627e4394d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229333"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitorare e gestire Trello con App per la logica di Azure

Con App per la logica di Azure e il connettore Trello, è possibile creare attività e flussi di lavoro automatizzati per monitorare e gestire elenchi, schede, bacheche, membri del team e così via, ad esempio:

* Eseguire il monitoraggio quando vengono aggiunte nuove schede agli elenchi e alle bacheche. 
* Creare, ottenere e gestire bacheche, schede ed elenchi.
* Aggiungere commenti e membri alle schede.
* Elencare bacheche, etichette delle bacheche, schede sulle bacheche, commenti alle schede, membri delle schede, membri del team e team di cui si è membri. 
* Ottenere team.

È possibile usare i trigger per ottenere risposte dall'account Trello e rendere l'output disponibile per altre azioni. È possibile usare azioni che eseguono attività con l'account Trello. È anche possibile fare in modo che altre azioni usino l'output delle azioni di Trello. Ad esempio, quando una nuova scheda viene aggiunta a una bacheca o a un elenco, è possibile inviare messaggi con il connettore Slack. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* L'account Trello e le credenziali utente.

  Le credenziali autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account Trello.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account Trello. Per iniziare con un trigger di Trello, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione di Trello, avviare l'app per la logica con un trigger, ad esempio, il trigger **Ricorrenza**.

## <a name="connect-to-trello"></a>Connettersi a Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica vuote, nella casella di ricerca immettere "trello" come filtro. Nell'elenco dei trigger selezionare il trigger desiderato. 

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 
   Nella casella di ricerca immettere "trello" come filtro. 
   Nell'elenco delle azioni scegliere l'azione desiderata.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
   Scegliere il segno più (**+**) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Se viene chiesto di accedere a Trello, autorizzare l'accesso per l'app per la logica e accedere.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/trello/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)