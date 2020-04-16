---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: ee63c1c0913baf3700d77e6fba1582fe7027ead7
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875681"
---
#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Selezionare **Applicazioni** e quindi l'applicazione Web che deve avere accesso all'API. Ad esempio, *webapp1*.
1. Selezionare **Accesso all'API** e quindi **Aggiungi**.
1. Nell'elenco a discesa **Seleziona API** selezionare l'API per cui è necessario concedere l'accesso all'applicazione Web. Ad esempio, *webapi1*.
1. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti definiti in precedenza. Ad esempio, *demo.read* e *demo.write*.
1. Selezionare **OK**.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Selezionare **Registrazioni app (anteprima)** e quindi l'applicazione Web che deve avere accesso all'API. Ad esempio, *webapp1*.
1. In **Gestisci** selezionare **Autorizzazioni API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare la scheda **Le mie API**.
1. Selezionare l'API per cui è necessario concedere l'accesso all'applicazione Web. Ad esempio, *webapi1*.
1. In **Autorizzazione** espandere **demo** e quindi selezionare gli ambiti definiti in precedenza. Ad esempio, *demo.read* e *demo.write*.
1. Selezionare **Aggiungi autorizzazioni**.
1. Selezionare **Concedi consenso amministratore per (nome del tenant)** .
1. Se viene richiesto di selezionare un account, selezionare l'account amministratore attualmente connesso oppure accedere con un account nel tenant di Azure AD B2C a cui sia stato assegnato almeno il ruolo di *amministratore applicazione cloud*.
1. Selezionare **Sì**.
1. Selezionare **Aggiorna** e quindi verificare che il testo "Concesso per" sia visualizzato in **Stato** per entrambi gli ambiti.
