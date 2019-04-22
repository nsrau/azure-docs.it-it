---
title: Passare un token di accesso tramite un flusso utente all'applicazione - Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come passare attraverso un token di accesso per i provider di identità OAuth 2.0 come attestazione in un flusso utente in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7863bea9f3fe6ef146dc1e1f2b29bbfda09a9d6d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683635"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Passare un token di accesso tramite un flusso utente all'applicazione in Azure Active Directory B2C

> [!NOTE]
> Questa funzionalità è attualmente in anteprima pubblica.

Un [flusso utente](active-directory-b2c-reference-policies.md) in Azure Active Directory (Azure AD) B2C fornisce agli utenti dell'applicazione un'opportunità di iscriversi o accedere con un provider di identità. Quando viene avviato il percorso, Azure AD B2C riceve un [token di accesso](active-directory-b2c-reference-tokens.md) dal provider di identità. Azure AD B2C usa tale token per recuperare informazioni sull'utente. Si attiva un'attestazione nel flusso utente da passare il token attraverso le applicazioni registrate in Azure AD B2C.

Azure AD B2C attualmente supporta solo il passaggio di token di accesso del provider di identità [OAuth 2.0](active-directory-b2c-reference-oauth-code.md), tra cui [Facebook](active-directory-b2c-setup-fb-app.md) e [Google](active-directory-b2c-setup-goog-app.md). Per tutti gli altri provider di identità, l'attestazione viene restituita vuota.

## <a name="prerequisites"></a>Prerequisiti

- L'applicazione deve usare un [flusso utente v2](user-flow-versions.md).
- Il flusso utenti è configurato con un provider di identità OAuth 2.0.

## <a name="enable-the-claim"></a>Abilitare l'attestazione

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi che si usa la directory che contiene il tenant di Azure AD B2C. Selezionare il **Directory e sottoscrizione filtro** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **flussi utente, criteri,** e quindi selezionare il flusso utente. Ad esempio, **B2C_1_signupsignin1**.
5. Selezionare **Attestazioni dell'applicazione**.
6. Abilitare la **Token di accesso Provider di identità** di attestazione.

    ![Abilitare l'attestazione Token di accesso Provider di identità](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Fare clic su **Salva** per salvare il flusso utente.

## <a name="test-the-user-flow"></a>Testare il flusso utente

Durante il test delle applicazioni in Azure AD B2C, può essere utile avere restituito il token di Azure AD B2C a `https://jwt.ms` per esaminare le attestazioni in essa.

1. Nella pagina Panoramica del flusso utente, selezionare **Esegui il flusso utente**.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token nell'esempio seguente, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui il flusso utente**e quindi accedere con le credenziali dell'account. Verrà visualizzato il token di accesso del provider di identità nell'attestazione **idp_access_token**.

    Verrà visualizzato un testo simile al seguente esempio:

    ![Token decodificato](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni, vedere la [Panoramica di Azure AD B2C token](active-directory-b2c-reference-tokens.md).




