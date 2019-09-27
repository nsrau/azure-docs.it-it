---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: b83a6b9185eb4ef127da1dd3b55aba4e8b2945f9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326295"
---
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Nel menu a sinistra selezionare **Azure ad B2C**. In alternativa, selezionare **tutti i servizi** e cercare e selezionare **Azure ad B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *nativeapp1*.
1. Per **Native Client**, selezionare **Sì**.
1. Immettere un **URI di reindirizzamento personalizzato** con uno schema univoco. Ad esempio `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Quando si sceglie un URI di reindirizzamento, occorre tenere presenti due considerazioni importanti:
    1. **Univocità**: Lo schema dell'URI di reindirizzamento deve essere univoco per ogni applicazione. Nell'esempio `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` è lo schema. È necessario seguire questo modello. Se due applicazioni usano lo stesso schema, l'utente ha la possibilità di scegliere una delle due. Se l'utente sceglie in modo non corretto, l'accesso ha esito negativo.
    1. **Completezza**: L'URI di reindirizzamento deve avere uno schema e un percorso. Il percorso deve contenere almeno una barra rovesciata dopo il dominio. Ad esempio, `//oauth/` funziona mentre `//oauth` ha esito negativo. Non includere caratteri speciali nell'URI, ad esempio i caratteri di sottolineatura.
1. Selezionare **Create**.
