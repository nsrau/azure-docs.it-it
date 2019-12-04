---
title: Connettersi a GitHub
description: Monitorare gli eventi di GitHub con le API REST di GitHub e App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 7dc865f520b6f4667ace720e656a210e0252d1a1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789743"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Connettersi a GitHub da app per la logica di Azure

GitHub è un servizio di hosting di repository Git basato sul Web. Offre tutte le funzionalità di controllo delle revisioni distribuito e di gestione del codice sorgente (SCM) di Git, con l'aggiunta di altre funzionalità.

Per iniziare a usare il connettore GitHub, [creare per prima cosa un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Creare una connessione a GitHub

Per usare il connettore GitHub in un'app per la logica è necessario creare innanzitutto una *connessione* e quindi specificare i dettagli per queste proprietà: 

| Proprietà | Obbligatoria | Description | 
| -------- | -------- | ----------- | 
| token | SÌ | Fornisce le credenziali di GitHub. |

Dopo aver creato la connessione, è possibile eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, descritti dalla descrizione del connettore OpenAPI (in precedenza spavalderia), vedere la [pagina di riferimento del connettore](/connectors/github/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)