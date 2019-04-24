---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456941"
---
Per abilitare la reimpostazione della password nell'applicazione con granularità fine, usare un flusso utente di **reimpostazione della password**. Tenere presente che l'opzione di reimpostazione della password a livello di tenant è specificata [qui](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Questo flusso utente descrive l'esperienza utente durante la procedura di reimpostazione della password e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

In **Gestione** selezionare **Flussi utente** e fare clic su +**Nuovo flusso utente**.

![Selezionare Nuovo flusso utente](media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

Nella scheda **Consigliati** selezionare **Reimpostazione password**.

Immettere un **nome** per il flusso utente a cui l'applicazione può fare riferimento. Ad esempio, immettere `SSPR`.

In **Provider di identità** selezionare **Reimposta la password usando l'indirizzo di posta elettronica**.

![Immettere il nome e selezionare Reimposta la password usando l'indirizzo di posta elettronica come provider di identità](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

In **Attestazioni dell'applicazione** fare clic su **Mostra dettagli** e scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione al completamento dell'esperienza di reimpostazione della password. Selezionare ad esempio **ID oggetto dell'utente**.

Fare clic su **OK**.

![Selezionare alcune attestazioni dell'applicazione e fare clic sul pulsante OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Fare clic su **Crea** per aggiungere il flusso utente. Il flusso utente viene elencato come **B2C_1_SSPR**. Il prefisso **B2C_1_** viene aggiunto al nome.

Fare clic su **Esegui il flusso utente**. Verificare le impostazioni specificate nella tabella e quindi fare clic su **Esegui il flusso utente**.

![Selezionare il flusso utente ed eseguirlo](media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| Impostazione      | Value  |
| ------------ | ------ |
| **Applicazione** | App Contoso B2C |
| **Selezionare l'URL di risposta** | `https://localhost:44316/` |

Viene visualizzata una nuova scheda del browser ed è possibile verificare l'esperienza di reimpostazione della password dell'utente nell'applicazione.

> [!NOTE]
> La creazione e gli aggiornamenti dei criteri avranno effetto dopo circa un minuto.
>
