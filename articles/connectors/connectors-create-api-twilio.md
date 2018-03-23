---
title: Aggiungere il connettore Twilio alle app per la logica di Azure | Microsoft Docs
description: Panoramica del connettore Twilio con i parametri dell'API REST
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 2bb7961c850fc8a35f3e114d9497941b188cf51b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-twilio-connector"></a>Introduzione al connettore Twilio
Connettersi a Twilio per inviare e ricevere messaggi SMS, MMS e IP globali. Con Twilio è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Twilio. 
* Usare le azioni per recuperare un messaggio, elencare i messaggi e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando si recupera un nuovo messaggio di Twilio, è possibile usarlo come flusso di lavoro del bus di servizio. 

Creare prima di tutto un'app per la logica. Vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Creare una connessione a Twilio
Quando si aggiunge questo connettore alle app per la logica, immettere i valori di Twilio seguenti:

| Proprietà | Obbligatoria | DESCRIZIONE |
| --- | --- | --- |
| Account ID |Sì |Immettere l'ID dell'account Twilio |
| Access Token |Sì |Immettere Il token di accesso di Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Se non è disponibile un token di accesso di Twilio, vedere [User Identity & Access Tokens](https://www.twilio.com/docs/api/chat/guides/identity) (Identità utente e token di accesso).

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/twilio/).

## <a name="more-connectors"></a>Altri connettori
Tornare all' [elenco di API](apis-list.md).