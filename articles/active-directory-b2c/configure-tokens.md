---
title: Configurare i token - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come configurare le impostazioni di compatibilità e durata dei token in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67bc9d6b35d4841999721a00592a6bbe23bff10f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340224"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurare i token in Azure Active Directory B2C

Questo articolo illustra come configurare [la durata e la compatibilità di un token](tokens-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Prerequisiti

[Creare un flusso utente](tutorial-create-user-flows.md) per consentire agli utenti di iscriversi e accedere all'applicazione.

## <a name="configure-jwt-token-lifetime"></a>Configurare la durata di un token JWT

È possibile configurare la durata di un token in qualsiasi flusso utente.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory contenente il tenant di Azure AD B2C.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Flussi utente (criteri)** .
5. Aprire il flusso utente creato in precedenza.
6. Selezionare **Proprietà**.
7. In **Durata token**, modificare le proprietà seguenti per adattarle alle esigenze dell'applicazione:

    ![Impostazioni della proprietà di durata del token nel portale di Azure](./media/configure-tokens/token-lifetime.png)

8. Fare clic su **Salva**.

> [!NOTE]
> Le applicazioni a singola pagina che usano il flusso del codice di autorizzazione con PKCE hanno sempre una durata del token di aggiornamento di 24 ore. [Altre informazioni sulle implicazioni di sicurezza dei token di aggiornamento nel browser](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="configure-jwt-token-compatibility"></a>Configurare la compatibilità del token JWT

1. Selezionare **Flussi utente (criteri)** .
2. Aprire il flusso utente creato in precedenza.
3. Selezionare **Proprietà**.
4. In **Impostazioni di compatibilità del token**, modificare le proprietà seguenti per adattarle alle esigenze dell'applicazione:

    ![Impostazioni della proprietà di compatibilità del token nel portale di Azure](./media/configure-tokens/token-compatibility.png)

5. Fare clic su **Salva**.

## <a name="provide-optional-claims-to-your-app"></a>Fornire attestazioni facoltative all'app

Le attestazioni dell'applicazione sono valori restituiti all'applicazione. Aggiornare il flusso utente per includere le attestazioni desiderate.

1. Selezionare **Flussi utente (criteri)** .
1. Aprire il flusso utente creato in precedenza.
1. Selezionare **Attestazioni dell'applicazione**.
1. Scegliere le attestazioni e gli attributi che si desidera inviare nuovamente all'applicazione.
1. Fare clic su **Salva**.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [richiedere token di accesso](access-tokens.md).



