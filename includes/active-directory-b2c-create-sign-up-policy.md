---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 17c0213d63879687e9c6d5f8dca06b9113c44af8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456902"
---
Se si vuole abilitare solo l'iscrizione nell'applicazione, usare il flusso utente di **iscrizione**. Questo flusso utente descrive l'esperienza utente durante la procedura di iscrizione e il contenuto dei token che l'applicazione riceverà al completamento dell'iscrizione.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

In **Gestione** selezionare **Flussi utente**.

Fare clic su +**Nuovo flusso utente** nella parte superiore del pannello.

In **Selezionare un tipo di flusso utente** scegliere **Tutti** e quindi selezionare la versione di **Iscrizione** che si vuole usare.

Il campo **Nome** determina il nome del flusso utente di iscrizione usato dall'applicazione. Immettere ad esempio **SiUp**.

In **Provider di identità** selezionare **Iscrizione posta elettronica**. Facoltativamente, è anche possibile selezionare i provider di identità tramite social network, se già configurati.

In **Attributi e attestazioni utente** fare clic su **Mostra dettagli**.

Nella colonna **Raccogli l'attributo** scegliere gli attributi da raccogliere dall'utente durante l'iscrizione. Selezionare ad esempio **Paese/Area geografica**, **Nome visualizzato** e **Codice postale**.

Nella colonna **Restituisci l'attestazione** scegliere le attestazioni che verranno restituite all'applicazione nei token inviati al termine di una corretta esperienza di iscrizione. Selezionare ad esempio **Nome visualizzato**, **Provider di identità**, **Codice postale**, **L'utente è nuovo** e l'**ID oggetto dell'utente**.

Fare clic su **OK**.

Fare clic su **Create**(Crea). Il flusso utente creato è visualizzato come **B2C_1_SiUp** (il frammento **B2C\_1\_** viene aggiunto automaticamente).

Fare clic su **Esegui il flusso utente**.

Selezionare **Contoso B2C app** nel menu a discesa **Applicazioni** e `https://localhost:44321/` nel menu a discesa **URL di risposta**.

Fare clic su **Esegui il flusso utente**. Verrà visualizzata una nuova scheda del browser in cui è possibile eseguire l'esperienza utente di iscrizione per l'applicazione.

> [!NOTE]
> La creazione e gli aggiornamenti del flusso utente avranno effetto dopo circa un minuto.
>