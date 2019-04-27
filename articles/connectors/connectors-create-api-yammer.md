---
title: Connettersi a Yammer da App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i flussi di lavoro per il monitoraggio, la pubblicazione e la gestione di messaggi, feed e altro in Yammer con App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: ca2d28f3438fd166fa282488206662c95777bf3b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104732"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitorare e gestire l'account Yammer con App per la logica di Azure

Con App per la logica di Azure e il connettore Yammer, è possibile creare attività e flussi di lavoro automatizzati che monitorano e gestiscono messaggi, feed e altro in Yammer, nonché altre azioni, quali:

* Monitoraggio quando vengono visualizzati nuovi messaggi in feed e gruppi seguiti.
* Recupero di messaggi, gruppi, reti, dettagli degli utenti e altro ancora.
* Pubblicazione e like di messaggi.

È possibile usare i trigger per ottenere risposte dall'account Yammer e rendere l'output disponibile per altre azioni. È possibile usare azioni che eseguono attività con l'account Yammer. È anche possibile fare in modo che altre azioni usino l'output delle azioni di Yammer. Ad esempio, quando vengono visualizzati nuovi messaggi in feed o gruppi, è possibile condividere tali messaggi con il connettore Slack. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Account Yammer e credenziali utente

   Le credenziali autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account Yammer.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account Yammer. Per iniziare con un trigger di Yammer, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione di Yammer, avviare l'app per la logica con un altro trigger, ad esempio, il trigger **Ricorrenza**.

## <a name="connect-to-yammer"></a>Connettersi a Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Scegliere un percorso: 

   * Per le app per la logica vuote, nella casella di ricerca immettere "yammer" come filtro. 
   Nell'elenco dei trigger selezionare il trigger desiderato. 

     -oppure-

   * Per le app per la logica esistenti: 
   
     * Nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 

       -oppure-

     * Spostare il puntatore del mouse sulla freccia visualizzata tra i passaggi in cui si vuole aggiungere un'azione. 
     Scegliere il segno più (**+**) visualizzato e quindi selezionare **Aggiungi un'azione**.
     
       Nella casella di ricerca immettere "yammer" come filtro. 
       Nell'elenco delle azioni selezionare l'azione desiderata.

1. Se viene chiesto di accedere a Yammer, accedere ora in modo tale da ricevere l'accesso.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/yammer/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)