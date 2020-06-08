---
title: Supporto delle API in App Web statiche di Azure con Funzioni di Azure
description: Informazioni sulle funzionalità API supportate in App Web statiche di Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 4fa3acf0e6cc767aeee4504bbc4df382a75e256b
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758759"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Supporto delle API in App Web statiche di Azure (anteprima) con Funzioni di Azure

App Web statiche di Azure offre endpoint API serverless tramite [Funzioni di Azure](../azure-functions/functions-overview.md). Grazie a Funzioni di Azure, le API vengono dimensionate in modo dinamico in base alla richiesta e includono le funzionalità seguenti:

- **Sicurezza integrata** con accesso diretto ai dati utente su [autenticazione e autorizzazione basata su ruoli](user-information.md).
- **Routing automatico** in modo che la route _api_ sia disponibile in modo sicuro per l'app Web senza richiedere regole CORS personalizzate.
- **Funzioni di Azure** v3 compatibile con Node.js 12.
- **Trigger HTTP** e associazioni di output.

## <a name="configuration"></a>Configurazione

Gli endpoint API sono disponibili per l'app Web tramite la route _api_. Questa route è fissa, ma è possibile controllare la cartella in cui si trova l'app Funzioni di Azure associata. Per cambiare questo percorso, [modificare il file YAML del flusso di lavoro](github-actions-workflow.md#build-and-deploy) che si trova nella cartella _.github/workflows_ del repository.

## <a name="constraints"></a>Vincoli

App Web statiche di Azure fornisce un'API tramite Funzioni di Azure. Le funzionalità di Funzioni di Azure sono incentrate su un set specifico di funzionalità che consentono di creare un'API per un'app Web e permettono all'app Web di connettersi all'API in modo sicuro. Queste funzionalità prevedono alcuni vincoli, tra cui:

- Il prefisso della route dell'API deve essere _api_.
- L'app per le funzioni dell'API deve essere in JavaScript.
- I trigger e le associazioni sono limitati a quelli [HTTP](../azure-functions/functions-bindings-http-webhook.md).
  - Tutti gli altri [trigger e associazioni di Funzioni di Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings), ad eccezione delle associazioni di output, sono limitati.
- I log sono disponibili solo se all'app per le funzioni si aggiunge [Application Insights](../azure-functions/functions-monitoring.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'API](add-api.md)
