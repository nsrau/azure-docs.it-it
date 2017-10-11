---
title: Aggiungere il connettore Facebook alle app per la logica | Documentazione Microsoft
description: Panoramica del connettore Facebook con i parametri dell'API REST.
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia; ladocs
ms.openlocfilehash: e10a30ccef3e81cb3d7749696453d82b8958d076
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-facebook-connector"></a>Introduzione al connettore Facebook
Connettersi a Facebook e pubblicare un post in una sequenza temporale, recuperare il feed di una pagina e così via. Con Facebook è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Facebook. 
* Usare un trigger quando si riceve un nuovo post.
* Usare azioni per pubblicare un post nella sequenza temporale, recuperare il feed di una pagina e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando nella sequenza temporale è presente un nuovo post, è possibile selezionare tale post ed eseguirne il push al feed di Twitter. 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

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