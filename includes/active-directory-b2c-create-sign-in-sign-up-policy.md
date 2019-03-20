---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "58114333"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

In **Gestione** selezionare **Flussi utente** e fare clic su +**Nuovo flusso utente**.

![Selezionare Nuovo flusso utente](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

Nella scheda **Consigliati** selezionare **Iscrizione e accesso**.

![Selezionare il flusso utente di iscrizione e di accesso](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

Immettere un **nome** per il flusso utente a cui l'applicazione può fare riferimento. Ad esempio, immettere `SiUpIn`.

In **Provider di identità** selezionare **Iscrizione posta elettronica**. Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati.

In **Autenticazione a più fattori** scegliere **Abilitata** oppure **Disabilitata**.

![Immettere un nome e selezionare Iscrizione posta elettronica come provider di identità](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

In **Attributi e attestazioni utente** selezionare **Mostra dettagli** per visualizzare l'elenco completo degli attributi e delle attestazioni disponibili.

Nella colonna **Raccogli l'attributo** scegliere gli attributi da raccogliere dall'utente durante l'iscrizione. Selezionare ad esempio **Paese/area geografica**, **Nome visualizzato** e **Codice postale**.

Nella colonna **Restituisci l'attestazione** scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione al completamento dell'esperienza di iscrizione o accesso. Selezionare ad esempio **Nome visualizzato**, **Provider di identità**, **Codice postale**, **Nuovo utente** e **ID oggetto dell'utente**.

Fare clic su **OK**.

![Selezionare alcuni attributi utente e attestazioni e fare clic sul pulsante OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

Fare clic su **Crea** per aggiungere il flusso utente. Il flusso utente viene elencato come **B2C_1_SiUpIn**. Il prefisso **B2C_1_** viene aggiunto al nome.

Selezionare **Esegui il flusso utente**. Verificare le impostazioni specificate nella tabella e quindi fare clic su **Esegui il flusso utente**.

![Selezionare Esegui il flusso utente](media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| Impostazione      | Valore  |
| ------------ | ------ |
| **Applicazione** | App Contoso B2C |
| **URL di risposta** | `https://localhost:44316/` |

Viene visualizzata una nuova scheda del browser ed è possibile verificare l'esperienza di iscrizione o di accesso dell'utente in base alla configurazione specificata.

> [!NOTE]
> La creazione e gli aggiornamenti del flusso utente avranno effetto dopo circa un minuto.
>