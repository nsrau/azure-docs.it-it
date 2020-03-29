---
title: Accedi, monitora e gestisci il tuo repository GitHub
description: Monitora gli eventi GitHub e gestisci il repository GitHub creando flussi di lavoro automatizzati con le app per la logica di AzureMonitor GitHub events and manage your GitHub repo by creating automated workflows with Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378450"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Monitorare e gestire il repository GitHub usando le app per la logica di AzureMonitor and manage your GitHub repo by using Azure Logic Apps

GitHub è un servizio di hosting di repository Git basato sul Web. Offre tutte le funzionalità di controllo delle revisioni distribuito e di gestione del codice sorgente (SCM) di Git, con l'aggiunta di altre funzionalità.

Per iniziare a usare il connettore GitHub, [creare per prima cosa un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Creare una connessione a GitHub

Per usare il connettore GitHub in un'app per la logica è necessario creare innanzitutto una *connessione* e quindi specificare i dettagli per queste proprietà: 

| Proprietà | Obbligatoria | Descrizione | 
| -------- | -------- | ----------- | 
| token | Sì | Fornisce le credenziali di GitHub. |

Dopo aver creato la connessione, è possibile eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche sui trigger, le azioni e i limiti descritti dalla descrizione OpenAPI (in precedenza Swagger) del connettore, consultare la pagina di [riferimento del connettore.](/connectors/github/)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)