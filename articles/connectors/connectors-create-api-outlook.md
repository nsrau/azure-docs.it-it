---
title: Connessione ad Outlook.com
description: Gestire posta elettronica, calendari e contatti con le API REST di Outlook.com per le App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 750efc2cb928bf127c4f3c68d5a58c5f52ca7d51
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789367"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Gestire posta elettronica, calendari e contatti in Outlook.com per le App per la logica di Azure

Questo articolo illustra come creare e gestire l'account Outlook.com all'interno di un'app per la logica con il connettore Box. In questo modo, è possibile creare app per la logica che automatizzano le attività e i flussi di lavoro per l'account Outlook.com, ad esempio:

* Inviare messaggi di posta elettronica. 
* Pianificare riunioni.
* Aggiungere contatti. 

Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Un [account Outlook.com](https://outlook.live.com/owa/)

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). 

* L'app per la logica in cui si vuole accedere all'account Outlook.com. Per avviare l'app per la logica con un trigger Outlook, è necessario un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Connessione ad Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio su trigger, azioni e limiti indicati nel file Swagger del connettore, vedere la [pagina di riferimento del connettore](/connectors/outlook/). 

## <a name="get-support"></a>Ottenere supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)