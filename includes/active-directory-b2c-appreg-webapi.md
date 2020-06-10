---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 24f49885892fb69c14ebd589587470edd78eeefe
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298848"
---
Per poter accettare e rispondere a richieste di risorse protette da parte di applicazioni client che presentano un token di accesso, le risorse API Web devono prima essere registrate nel tenant.

Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare la nuova esperienza unificata **Registrazioni app** oppure l'esperienza legacy **Applicazioni (legacy)** . [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *webapi1*.
1. In **URI di reindirizzamento**  selezionare **Web** e quindi immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. In questa esercitazione, l'esempio viene eseguito in locale ed è in ascolto su `http://localhost:5000`.
1. Selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)** , che sarà necessario in un passaggio successivo.

Abilitare quindi il flusso di concessione implicita:

1. In **Gestisci** selezionare **Autenticazione**.
1. Selezionare **Prova la nuova esperienza** (se visualizzato).
1. In **Concessione implicita** selezionare entrambe le caselle di controllo **Token di accesso** e **ID token**.
1. Selezionare **Salva**.

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni (legacy)** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *webapi1*.
1. Per **App Web/API Web** selezionare **Sì**.
1. Per **Consenti il flusso implicito** selezionare **Sì**.
1. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. In questa esercitazione, l'esempio viene eseguito in locale ed è in ascolto su `https://localhost:5000`.
1. Per **URI ID app** aggiungere l'identificatore di un endpoint API all'URI visualizzato. Per questa esercitazione immettere `api`, in modo che l'URI completo risulti simile a `https://contosob2c.onmicrosoft.com/api`.
1. Selezionare **Crea**.
1. Prendere nota del valore di **ID APPLICAZIONE**, necessario in un passaggio successivo.