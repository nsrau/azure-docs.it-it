---
title: Connettersi a Wunderlist da App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i flussi di lavoro per il monitoraggio di elenchi, attività, promemoria e altro nel proprio account Wunderlist con App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e3570ab1227ca388ac62bffdc74bb68b1ddc41d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230168"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Monitorare e gestire Wunderlist con App per la logica di Azure

Con App per la logica di Azure e il connettore Wunderlist, è possibile creare attività e flussi di lavoro automatizzati che monitorano e gestiscono elenchi di cose da fare, attività, promemoria e altro ancora nel proprio account Wunderlist, nonché altre azioni, quali:

* Monitorare quando vengono create nuove attività, la scadenza delle attività o i promemoria.
* Creare e gestire elenchi, note, attività, le sottoattività e altro ancora.
* Impostare promemoria.
* Ottenere elenchi, attività, sottoattività, promemoria, file, note, commenti e altro ancora.

[Wunderlist](https://www.wunderlist.com/) è un servizio che consente di pianificare, gestire e completare i progetti, gli elenchi di cose da fare e le attività, su qualsiasi dispositivo e ovunque. È possibile usare i trigger per ottenere risposte dall'account Wunderlist e rendere l'output disponibile per altre azioni. È possibile usare azioni che eseguono attività con l'account Wunderlist. È anche possibile fare in modo che altre azioni usino l'output delle azioni di Wunderlist. Ad esempio, quando nuove attività sono in scadenza, è possibile pubblicare messaggi con il connettore Slack. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Account Wunderlist e credenziali utente

   Le credenziali autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account Wunderlist.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account Yammer. Per iniziare con un trigger di Wunderlist, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione di Wunderlist, avviare l'app per la logica con un altro trigger, ad esempio, il trigger **Ricorrenza**.

## <a name="connect-to-wunderlist"></a>Connettersi a Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Scegliere un percorso: 

   * Per le app per la logica vuote, nella casella di ricerca immettere "wunderlist" come filtro. 
   Nell'elenco dei trigger selezionare il trigger desiderato. 

     -oppure-

   * Per le app per la logica esistenti: 
   
     * Nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 

       -oppure-

     * Spostare il puntatore del mouse sulla freccia visualizzata tra i passaggi in cui si vuole aggiungere un'azione. 
     Scegliere il segno più (**+**) visualizzato e quindi selezionare **Aggiungi un'azione**.
     
       Nella casella di ricerca, digitare "wunderlist" come filtro. 
       Nell'elenco delle azioni selezionare l'azione desiderata.

1. Se viene chiesto di accedere a Wunderlist, accedere ora in modo tale da ricevere l'accesso.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/wunderlist/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)