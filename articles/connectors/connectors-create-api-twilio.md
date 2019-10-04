---
title: Connettersi a Twilio da App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i flussi di lavoro per la gestione dei messaggi SMS, MMS e IP globali tramite il proprio account Twilio usando App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 09d4a6acf93f5fb01c70f9e9bd935c404c5abf50
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050714"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Gestire i messaggi in Twilio con App per la logica di Azure

Con App per la logica di Azure e il connettore Twilio, è possibile creare attività e flussi di lavoro automatizzati per ottenere, inviare ed elencare messaggi in Twilio, inclusi i messaggi SMS, MMS e IP globali. È possibile usare queste azioni per eseguire attività con l'account Twilio. È anche possibile fare in modo che altre azioni usino l'output delle azioni di Twilio. Ad esempio, quando arriva un nuovo messaggio, è possibile inviare il contenuto del messaggio con il connettore Slack. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* Da [Twilio](https://www.twilio.com/): 

  * L'ID dell'account Twilio e il [token di autenticazione](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), che è possibile trovare nel dashboard di Twilio

    Le credenziali autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account Twilio dall'app per la logica. 
    Se si usa un account di valutazione di Twilio, è possibile inviare SMS esclusivamente ai numeri di telefono *verificati*.

  * Un numero di telefono Twilio verificato in grado di inviare SMS

  * Un numero di telefono Twilio verificato in grado di ricevere SMS

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account Twilio. Per usare un'azione di Twilio, avviare l'app per la logica con un altro trigger, ad esempio, il trigger **Ricorrenza**.

## <a name="connect-to-twilio"></a>Connettersi a Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Scegliere un percorso: 

     * Nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 

       -oppure-

     * Spostare il puntatore del mouse sulla freccia visualizzata tra i passaggi in cui si vuole aggiungere un'azione. 
     Scegliere il segno più ( **+** ) visualizzato e quindi selezionare **Aggiungi un'azione**.
     
       Nella casella di ricerca immettere "twilio" come filtro. 
       Nell'elenco delle azioni selezionare l'azione desiderata.

1. Specificare i dettagli necessari per la connessione e quindi scegliere **Crea**:

   * Nome da usare per la connessione
   * ID dell'account Twilio 
   * Token di accesso (autenticazione) di Twilio

1. Specificare i dettagli necessari per l'azione selezionata e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/twilio/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)