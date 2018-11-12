---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: fcd71f74e0b00934958828024094773e42496b66
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51017271"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Per registrare l'API Web, usare le impostazioni specificate nella tabella.

![Impostazioni di registrazione di esempio per una nuova API Web](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Impostazione      | Valore di esempio  | DESCRIZIONE                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nome** | API Contoso B2C | Immettere un **Nome** per l'applicazione che illustra l'API ai clienti. | 
| **Includi app Web/API Web** | Yes | Selezionare **Sì** per un'API Web. |
| **Consenti il flusso implicito** | Yes | Selezionare **Sì** se l'applicazione usa l'[accesso con OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md). |
| **URL di risposta** | `https://localhost:44316/` | Gli URL di risposta sono gli endpoint a cui Azure AD B2C restituisce eventuali token richiesti dall'applicazione. In questo esempio l'API Web è locale ed è in ascolto sulla porta 44316. |
| **URI ID app** | api | L'URI ID app è l'identificatore usato per l'API Web. L'URI completo dell'identificatore, incluso il dominio, viene generato automaticamente. |

Fare clic su **Crea** per registrare l'applicazione.

L'applicazione appena registrata viene visualizzata nell'elenco di applicazioni per il tenant B2C. Selezionare l'API Web dall'elenco. Viene visualizzato il riquadro delle proprietà dell'API.

![Proprietà dell'API Web](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

Annotare l'**ID client applicazione** univoco a livello globale. L'ID viene usato nel codice dell'applicazione.