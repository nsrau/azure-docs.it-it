---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742309"
---
Se si vuole abilitare solo l'accesso nell'applicazione, usare il flusso utente di **accesso**. Questo flusso utente descrive l'esperienza utente durante la procedura di accesso e il contenuto dei token che l'applicazione riceverà al completamento dell'accesso.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
In **Gestione** selezionare **Flussi utente**.

Fare clic su +**Nuovo flusso utente** nella parte superiore del pannello.

In **Selezionare un tipo di flusso utente** scegliere **Tutti** e quindi selezionare la versione di **Accesso** che si vuole usare.

Il campo **Nome** determina il nome del flusso utente di accesso usato dall'applicazione. Immettere ad esempio **SiIn**.

In **Provider di identità** selezionare un'opzione. È anche possibile selezionare i provider di identità tramite social network, se già configurati. Fare clic su **OK**.

In **Attestazioni dell'applicazione** fare clic su **Mostra dettagli**.

Nella colonna **Restituisci l'attestazione** scegliere le attestazioni che verranno restituite all'applicazione nei token inviati al termine di una corretta esperienza di accesso. Selezionare ad esempio **Nome visualizzato**, **Provider di identità**, **Codice postale** e **ID oggetto dell'utente**. Fare clic su **OK**.

Fare clic su **Create**(Crea). Si noti che il flusso utente appena creato è visualizzato come **B2C_1_SiIn** (il frammento **B2C\_1\_** viene aggiunto automaticamente).

Fare clic su **Esegui il flusso utente**.

Selezionare **Contoso B2C app** nel menu a discesa **Applicazioni** e `https://localhost:44321/` nel menu a discesa **URL di risposta**.

Fare clic su **Esegui il flusso utente**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di accesso all'applicazione.

> [!NOTE]
> La creazione e gli aggiornamenti del flusso utente avranno effetto dopo circa un minuto.
>