---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: c1628894205754c1b8f1fc954796009afbdc8ed4
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84680306"
---
#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/) 

1. Selezionare **Registrazioni app** e quindi l'applicazione Web che deve avere accesso all'API. Ad esempio, *webapp1*.
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

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Selezionare **Applicazioni (legacy)** e quindi l'applicazione Web che deve avere accesso all'API. Ad esempio, *webapp1*.
1. Selezionare **Accesso all'API** e quindi **Aggiungi**.
1. Nell'elenco a discesa **Seleziona API** selezionare l'API per cui è necessario concedere l'accesso all'applicazione Web. Ad esempio, *webapi1*.
1. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti definiti in precedenza. Ad esempio, *demo.read* e *demo.write*.
1. Selezionare **OK**.
