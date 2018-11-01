---
title: Connettersi a Slack da App per la logica di Azure | Microsoft Docs
description: Automatizzare attività e flussi di lavoro che monitorano i file e gestire canali, gruppi e i messaggi nell'account di Slack tramite le App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 675e37120b06af3add58b564495f22875647a0fa
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230353"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitorare e gestire Slack con App per la logica di Azure

Con App per la logica di Azure e il connettore Slack, è possibile creare attività e flussi di lavoro automatizzati per monitorare e gestire canali di Slack, messaggi, gruppi, e così via, ad esempio:

* Monitorare la creazione di nuovi file.
* Creare, elencare e aggiungere canali 
* Pubblicare messaggi.
* Creare gruppi e impostare su non disturbare.

È possibile usare i trigger per ottenere risposte dall'account Slack e rendere l'output disponibile per altre azioni. È possibile usare azioni che eseguono attività con l'account Slack. È anche possibile che altre azioni usino l'output delle azioni di Slack. Ad esempio, quando viene creato un nuovo file, è possibile inviare messaggi di posta elettronica con il connettore Office 365 Outlook. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* L'account [Slack](https://slack.com/) e le credenziali utente

  Le credenziali autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account Slack.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account Slack. Per iniziare con un trigger di Slack, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione di Slack, avviare l'app per la logica con un trigger, ad esempio un trigger di Slack o un altro trigger, ad esempio trigger di **Recurrence**.

## <a name="connect-to-slack"></a>Connettersi a Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica vuote, nella casella di ricerca immettere "slack" come filtro. Nell'elenco dei trigger selezionare il trigger desiderato. 

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 
   Nella casella di ricerca immettere "slack" come filtro. 
   Nell'elenco delle azioni selezionare l'azione desiderata.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
   Scegliere il segno più (**+**) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Se viene richiesto di accedere a Slack, accedere all'area di lavoro Slack personale. 

   ![Accedi all'area di lavoro Slack](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autorizzare l'accesso per l'app per la logica.

   ![Autorizzare l'accesso a Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Specificare i dettagli necessari per l'azione o il trigger selezionato. Per continuare la compilazione del flusso di lavoro dell'app per la logica, aggiungere altre azioni.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/slack/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
