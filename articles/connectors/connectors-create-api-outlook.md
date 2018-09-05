---
title: Connettersi a Outlook.com - App per la logica di Azure | Microsoft Docs
description: Gestire posta elettronica, calendari e contatti con le API REST di Outlook.com per le App per la logica di Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 8030ab9d317c1deefaf441008b9022c4a26bb17c
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746163"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Gestire posta elettronica, calendari e contatti in Outlook.com per le App per la logica di Azure

Questo articolo illustra come creare e gestire l'account Outlook.com all'interno di un'app per la logica con il connettore Box. In questo modo, è possibile creare app per la logica che automatizzano le attività e i flussi di lavoro per l'account Outlook.com, ad esempio:

* Inviare posta elettronica. 
* Pianificare riunioni.
* Aggiungere contatti. 

Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Un [account Outlook.com](https://outlook.live.com/owa/)

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* L'app per la logica in cui si vuole accedere all'account Outlook.com. Per avviare l'app per la logica con un trigger Outlook, è necessario un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Connessione ad Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio su trigger, azioni e limiti indicati nel file Swagger del connettore, vedere la [pagina di riferimento del connettore](/connectors/outlook/). 

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)