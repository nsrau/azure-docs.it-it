---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742252"
---
Se si vuole abilitare la modifica del profilo nell'applicazione, usare un flusso utente di **modifica del profilo**. Questo flusso utente descrive l'esperienza utente durante la procedura di modifica del profilo e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

In **Gestione** selezionare **Flussi utente** e fare clic su +**Nuovo flusso utente**.

![Selezionare Nuovo flusso utente](media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

Nella scheda **Consigliati** selezionare **Modifica del profilo**.

Immettere un **nome** per il flusso utente a cui l'applicazione può fare riferimento. Ad esempio, immettere `SiPe`.

In **Provider di identità** selezionare**Accesso all'account locale**. Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati.

![Selezionare Accesso all'account locale come provider di identità e quindi fare clic sul pulsante OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

In **Attributi utente** fare clic su **Mostra dettagli**. Nella colonna **Raccogli l'attributo** scegliere gli attributi che l'utente può visualizzare e modificare nel profilo. Selezionare ad esempio **Paese/area geografica**, **Nome visualizzato** e **Codice postale**.

Nella colonna **Restituisci l'attestazione** scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione al completamento dell'esperienza di modifica del profilo. Selezionare ad esempio **Nome visualizzato** e **Codice postale**.

Fare clic su **OK**.

![Selezionare alcune attestazioni dell'applicazione e fare clic sul pulsante OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

Fare clic su **Crea** per aggiungere il flusso utente. Il flusso utente viene elencato come **B2C_1_SiPe**. Il prefisso **B2C_1_** viene aggiunto al nome.

Selezionare **Esegui il flusso utente**. Verificare le impostazioni specificate nella tabella e quindi fare clic su **Esegui il flusso utente**.

![Selezionare il flusso utente ed eseguirlo](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| Impostazione      | Valore  |
| ------------ | ------ |
| **Applicazione** | App Contoso B2C |
| **URL di risposta** | `https://localhost:44316/` |

Viene visualizzata una nuova scheda del browser ed è possibile verificare l'esperienza di modifica del profilo dell'utente in base alla configurazione specificata.

> [!NOTE]
> La creazione e gli aggiornamenti del flusso utente avranno effetto dopo circa un minuto.
>