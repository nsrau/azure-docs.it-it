---
title: Connettersi a GitHub con App per la logica di Azure | Microsoft Docs
description: Automatizzare i flussi di lavoro per GitHub con App per la logica di Azure
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/02/2018
ms.author: estfan; ladocs
ms.openlocfilehash: 830c3e6b5462c5d2d917388c0d0924e444342f93
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="connect-to-github"></a>Connettersi a GitHub

GitHub è un servizio di hosting di repository Git basato sul Web. Offre tutte le funzionalità di controllo delle revisioni distribuito e di gestione del codice sorgente (SCM) di Git, con l'aggiunta di altre funzionalità.

Per iniziare a usare il connettore GitHub, [creare per prima cosa un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Creare una connessione a GitHub

Per usare il connettore GitHub in un'app per la logica è necessario creare innanzitutto una *connessione* e quindi specificare i dettagli per queste proprietà: 

| Proprietà | Obbligatoria | DESCRIZIONE | 
| -------- | -------- | ----------- | 
| token | Sì | Fornisce le credenziali di GitHub. |

Dopo aver creato la connessione, è possibile eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per le azioni e i trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/github/).

## <a name="find-more-connectors"></a>Trovare altri connettori

* Vedere [Elenco dei connettori](apis-list.md).