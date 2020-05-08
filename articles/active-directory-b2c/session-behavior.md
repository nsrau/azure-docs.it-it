---
title: Configurare il comportamento della sessione-Azure Active Directory B2C | Microsoft Docs
description: Configurare il comportamento della sessione in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f5400b47c1e0b4657e40d2c57f8212711bbdaf3f
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927072"
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
4. Selezionare **flussi utente (criteri)**.
5. Aprire il flusso utente creato in precedenza.
6. Selezionare **Proprietà**.
7. Configurare la **durata della sessione dell'app Web (minuti)**, il **timeout della sessione dell'app Web**, la **configurazione dell'accesso Single Sign-on**e **richiedere il token ID nelle richieste di disconnessione** , se necessario.

    ![Impostazioni delle proprietà di comportamento della sessione nell'portale di Azure](./media/session-behavior/session-behavior.png)

8. Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Azure ad B2C sessione](session-overview.md).