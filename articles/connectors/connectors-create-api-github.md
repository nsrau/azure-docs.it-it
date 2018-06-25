---
title: Connettersi a GitHub - App per la logica di Azure | Microsoft Docs
description: Monitorare gli eventi di GitHub con le API REST di GitHub e App per la logica di Azure
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ce567dc631c3a147b795eb2355a4961faa8881d6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295813"
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