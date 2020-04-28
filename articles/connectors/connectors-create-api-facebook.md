---
title: Connetti a Facebook
description: Automatizzare le attività e i flussi di lavoro che gestiscono la cronologia e la pagina di Facebook usando app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75665803"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Gestire la cronologia e la pagina di Facebook usando app per la logica di Azure

Connettersi a Facebook e pubblicare un post in una sequenza temporale, recuperare il feed di una pagina e così via. Con Facebook è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Facebook. 
* Usare un trigger quando si riceve un nuovo post.
* Usare azioni per pubblicare un post nella sequenza temporale, recuperare il feed di una pagina e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando nella sequenza temporale è presente un nuovo post, è possibile selezionare tale post ed eseguirne il push al feed di Twitter. 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Creare una connessione a Facebook

Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Facebook.

1. Accedere al proprio account Facebook.

2. Selezionare **Autorizza**e consentire alle app per la logica di connettersi e usare Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio trigger, azioni e limiti, come descritto dal file OpenAPI (in precedenza spavalderia) del connettore, vedere la [pagina di riferimento del connettore](/connectors/facebook/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)