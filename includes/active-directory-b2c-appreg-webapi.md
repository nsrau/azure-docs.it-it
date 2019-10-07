---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/30/2019
ms.author: marsma
ms.openlocfilehash: 4ac69b51e69031a1a4ce83a2c09dc6cc010ccd62
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720267"
---
Per poter accettare e rispondere a richieste di risorse protette da parte di applicazioni client che presentano un token di accesso, le risorse API Web devono prima essere registrate nel tenant.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *webapi1*.
1. Per **App Web/API Web** selezionare **Sì**.
1. Per **Consenti il flusso implicito** selezionare **Sì**.
1. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. In questa esercitazione, l'esempio viene eseguito in locale ed è in ascolto su `https://localhost:5000`.
1. Per **URI ID app** aggiungere l'identificatore di un endpoint API all'URI visualizzato. Per questa esercitazione immettere `api`, in modo che l'URI completo risulti simile a `https://contosob2c.onmicrosoft.com/api`.
1. Selezionare **Create** (Crea).
1. Prendere nota del valore di **ID APPLICAZIONE**, necessario in un passaggio successivo.
