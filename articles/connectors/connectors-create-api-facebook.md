---
title: Connettiti a Facebook
description: Automatizzare le attività e i flussi di lavoro che gestiscono la sequenza temporale e la pagina di Facebook usando le app per la logica di AzureAutomate tasks and workflows that manage your Facebook timeline and page by using Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665803"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Gestire la sequenza temporale e la pagina di Facebook usando le app per la logica di AzureManage your Facebook timeline and page by using Azure Logic Apps

Connettersi a Facebook e pubblicare un post in una sequenza temporale, recuperare il feed di una pagina e così via. Con Facebook è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Facebook. 
* Usare un trigger quando si riceve un nuovo post.
* Usare azioni per pubblicare un post nella sequenza temporale, recuperare il feed di una pagina e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando nella sequenza temporale è presente un nuovo post, è possibile selezionare tale post ed eseguirne il push al feed di Twitter. 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Creare una connessione a Facebook

Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Facebook.

1. Accedi al tuo account Facebook.

2. Selezionare **Autorizza**e consentire alle app per la logica di connettersi e usare Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio trigger, azioni e limiti, come descritto dal file OpenAPI del connettore (in precedenza Swagger), vedere la [pagina di riferimento del connettore.](/connectors/facebook/)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)