---
title: Configurare il comportamento della sessione-Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come configurare il comportamento della sessione in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 091704fabb7b50a0c83625c6ae46d9a807f01ffc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961035"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurare il comportamento della sessione in Azure Active Directory B2C

La gestione delle [sessioni Single Sign-on (SSO)](session-overview.md) in Azure Active Directory B2C (Azure ad B2C) consente a un amministratore di controllare l'interazione con un utente dopo che l'utente è già stato autenticato. Ad esempio, l'amministratore può controllare se la selezione dei provider di identità viene visualizzata o se è necessario immettere di nuovo i dettagli dell'account. In questo articolo viene descritto come configurare le impostazioni di SSO per Azure AD B2C.

## <a name="session-behavior-properties"></a>Proprietà del comportamento della sessione

È possibile usare le proprietà seguenti per gestire le sessioni delle applicazioni Web:

- **Durata della sessione dell'app Web (minuti)**: durata del cookie della sessione di Azure AD B2C archiviato nel browser dell'utente dopo un'autenticazione corretta.
    - Impostazione predefinita: 1440 minuti.
    - Valore minimo (inclusivo): 15 minuti.
    - Valore massimo (inclusivo): 1440 minuti.
- **Timeout della sessione dell'app Web** : [tipo di scadenza della sessione](session-overview.md#session-expiry-type), in *sequenza*o *assoluto*. 
- **Configurazione di Single Sign-on** : [ambito della sessione](session-overview.md#session-scope) del comportamento di Single Sign-on (SSO) tra più app e flussi utente nel tenant di Azure ad B2C.


## <a name="configure-the-properties"></a>Configurare le proprietà

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad B2C.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Flussi utente**.
5. Aprire il flusso utente creato in precedenza.
6. Selezionare **Proprietà**.
7. Configurare la **durata della sessione dell'app Web (minuti)**, il **timeout della sessione dell'app Web**, la **configurazione dell'accesso Single Sign-on**e **richiedere il token ID nelle richieste di disconnessione** , se necessario.

    ![Impostazioni delle proprietà di comportamento della sessione nell'portale di Azure](./media/session-behavior/session-behavior.png)

8. Fare clic su **Save**.

## <a name="configure-sign-out-behavior"></a>Configurare il comportamento di disconnessione

### <a name="secure-your-logout-redirect"></a>Proteggere il reindirizzamento di disconnessione

Dopo la disconnessione, l'utente viene reindirizzato all'URI specificato nel `post_logout_redirect_uri` parametro, indipendentemente dagli URL di risposta specificati per l'applicazione. Tuttavia, se viene passato un oggetto valido `id_token_hint` e il **token ID richiesto nelle richieste di disconnessione** è attivato, Azure ad B2C verifica che il valore `post_logout_redirect_uri` corrisponda a uno degli URI di reindirizzamento configurati dell'applicazione prima di eseguire il reindirizzamento. Se per l'applicazione non è stato configurato alcun URL di risposta corrispondente, viene visualizzato un messaggio di errore e l'utente non viene reindirizzato. Per richiedere un token ID nelle richieste di disconnessione:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Aprire il flusso utente creato in precedenza.
1. Selezionare **Proprietà**.
1. Abilitare il **Richiedi token ID nelle richieste di disconnessione**.
1. Tornare alla  **Azure ad B2C**.
1. Selezionare **registrazioni app**, quindi selezionare l'applicazione.
1. Selezionare **Autenticazione**.
1. Nella casella di testo **URL di disconnessione** Digitare l'URI di reindirizzamento post-disconnessione e quindi selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [sessione Azure AD B2C](session-overview.md).
