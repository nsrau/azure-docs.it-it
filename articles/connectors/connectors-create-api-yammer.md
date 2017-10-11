---
title: Aggiungere il connettore Yammer alle app per la logica di Azure | Microsoft Docs
description: Panoramica del connettore Yammer con i parametri dell'API REST
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: c7a213343b4fb2b5a89a5052a459061b404a431c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-yammer-connector"></a>Introduzione al connettore Yammer
Connettersi a Yammer per accedere alle conversazioni della rete dell'organizzazione. Con Yammer è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Yammer. 
* Usare i trigger quando c'è un nuovo messaggio in un gruppo o un feed che si sta seguendo.
* Usare le azioni per pubblicare un messaggio, recuperare tutti i messaggi e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando viene visualizzato un nuovo messaggio, è possibile inviare un messaggio di posta elettronica tramite Office 365.

Creare prima di tutto un'app per la logica. Vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-yammer"></a>Creare una connessione a Yammer
Per usare il connettore Yammer, creare prima una **connessione**, quindi specificare i dettagli di queste proprietà: 

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Specificare le credenziali di Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/yammer/).

## <a name="more-connectors"></a>Altri connettori
Tornare all' [elenco di API](apis-list.md).