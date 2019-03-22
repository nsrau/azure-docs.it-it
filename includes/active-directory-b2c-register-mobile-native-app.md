---
title: File di inclusione
description: File di inclusione
services: active-directory-b2c
author: davidmu1
ms.service: active-directory-b2c
ms.topic: include
ms.date: 04/09/2018
ms.author: davidmu
ms.custom: include file
ms.openlocfilehash: 8363d023e89c77aabc0d123f19264c9a0758a656
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "58115818"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Per registrare l'applicazione per dispositivi mobili o nativa, usare le impostazioni specificate nella tabella.

![Impostazioni di registrazione di esempio per una nuova applicazione per dispositivi mobili o nativa](./media/active-directory-b2c-register-mobile-native-app/b2c-new-mobile-native-app-settings.png)

| Impostazione      | Valore di esempio  | DESCRIZIONE                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nome** | App Contoso B2C | Immettere un **Nome** per l'applicazione che descriva l'applicazione agli utenti. |
| **Client nativo** | Sì | Selezionare **Sì** per un'applicazione per dispositivi mobili o nativa. |
| **URI di reindirizzamento personalizzato** | `com.onmicrosoft.contoso.appname://redirect/path` | Immettere un URI di reindirizzamento con uno schema personalizzato. Assicurarsi di scegliere un [URI di reindirizzamento valido](../articles/active-directory-b2c/active-directory-b2c-app-registration.md) e di non includere caratteri speciali come i caratteri di sottolineatura. |

Fare clic su **Crea** per registrare l'applicazione.

L'applicazione appena registrata viene visualizzata nell'elenco di applicazioni per il tenant B2C. Selezionare l'app per dispositivi mobili o nativa dall'elenco. Viene visualizzato il riquadro delle proprietà dell'applicazione.

![Proprietà dell'applicazione](./media/active-directory-b2c-register-mobile-native-app/b2c-mobile-native-app-properties.png)

Annotare l'**ID client applicazione** univoco a livello globale. L'ID viene usato nel codice dell'applicazione.

Se l'applicazione nativa chiama un'API Web protetta da Azure AD B2C, seguire questa procedura:
   1. Creare un segreto dell'applicazione passando al pannello **Chiavi** e facendo clic sul pulsante **Genera chiave**. Annotare il valore di **Chiave dell'app**. Questo valore viene usato come segreto dell'applicazione nel codice dell'applicazione.
   2. Fare clic su **Accesso all'API**, quindi su **Aggiungi** e selezionare l'API Web e gli ambiti (autorizzazioni).

> [!NOTE]
> Un **segreto dell'applicazione** è una credenziale di sicurezza importante e deve essere protetto in modo appropriato.
> 
