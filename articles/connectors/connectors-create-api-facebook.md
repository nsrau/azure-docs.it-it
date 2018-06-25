---
title: Connettersi a Facebook - App per la logica di Azure | Microsoft Docs
description: Gestire la sequenza temporale e la pagina personale con le API REST di Facebook e App per la logica di Azure
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 985f3cf70a07b3080f34181e64c5bb1419d530bd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295007"
---
# <a name="get-started-with-the-facebook-connector"></a>Introduzione al connettore Facebook
Connettersi a Facebook e pubblicare un post in una sequenza temporale, recuperare il feed di una pagina e così via. Con Facebook è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Facebook. 
* Usare un trigger quando si riceve un nuovo post.
* Usare azioni per pubblicare un post nella sequenza temporale, recuperare il feed di una pagina e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando nella sequenza temporale è presente un nuovo post, è possibile selezionare tale post ed eseguirne il push al feed di Twitter. 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Creare una connessione a Facebook
Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Facebook.

1. Accedere al proprio account Facebook
2. Selezionare **Autorizza**e consentire alle app per la logica di connettersi e usare Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/facebook/).

## <a name="more-connectors"></a>Altri connettori
Tornare all' [elenco di API](apis-list.md).