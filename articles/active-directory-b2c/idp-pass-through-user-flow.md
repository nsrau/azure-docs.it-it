---
title: Passare un token di accesso tramite un flusso utente all'appPass an access token through a user flow to your app
titleSuffix: Azure AD B2C
description: Informazioni su come passare un token di accesso per i provider di identità OAuth 2.0 come attestazione in un flusso utente in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 312d093548b6e3cf3654f45d7610e8fc474a87b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187784"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Passare un token di accesso tramite un flusso utente all'applicazione in Azure Active Directory B2C

Un [flusso di utenti](user-flow-overview.md) in Azure Active Directory B2C (Azure AD B2C) offre agli utenti dell'applicazione l'opportunità di iscriversi o accedere con un provider di identità. Quando viene avviato il percorso, Azure AD B2C riceve un [token di accesso](tokens-overview.md) dal provider di identità. Azure AD B2C usa tale token per recuperare informazioni sull'utente. Si attiva un'attestazione nel flusso utente da passare il token attraverso le applicazioni registrate in Azure AD B2C.

Azure AD B2C attualmente supporta solo il passaggio di token di accesso del provider di identità [OAuth 2.0](authorization-code-flow.md), tra cui [Facebook](identity-provider-facebook.md) e [Google](identity-provider-google.md). Per tutti gli altri provider di identità, l'attestazione viene restituita vuota.

## <a name="prerequisites"></a>Prerequisiti

* L'applicazione deve usare un [flusso utente v2](user-flow-versions.md).
* Il flusso utenti è configurato con un provider di identità OAuth 2.0.

## <a name="enable-the-claim"></a>Abilitare l'attestazione

1. Accedere al [portale](https://portal.azure.com/) di Azure come amministratore globale del tenant B2C di Azure AD.
2. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu superiore, selezionare il filtro **Directory e sottoscrizione** e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Flussi utente (criteri)**, quindi selezionare il flusso utente. Ad esempio, **B2C_1_signupsignin1**.
5. Selezionare **Attestazioni dell'applicazione**.
6. Abilitare l'attestazione del provider di accesso del provider di **identità.**

    ![Abilitare l'attestazione del provider di accesso del provider di identitàEnable the Identity Provider Access Token claim](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Fare clic su **Salva** per salvare il flusso utente.

## <a name="test-the-user-flow"></a>Testare il flusso utente

Durante il test delle applicazioni in Azure AD B2C, può essere utile avere restituito il token di Azure AD B2C a `https://jwt.ms` per esaminare le attestazioni in essa.

1. Nella pagina Panoramica del flusso utente, selezionare **Esegui il flusso utente**.
2. Per **Applicazione**, selezionare l'applicazione che è stata registrata in precedenza. Per visualizzare il token nell'esempio seguente, l'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui il flusso utente**e quindi accedere con le credenziali dell'account. Verrà visualizzato il token di accesso del provider di identità nell'attestazione **idp_access_token**.

    Verrà visualizzato un testo simile al seguente esempio:

    ![Token decodificato in jwt.ms con idp_access_token blocco evidenziato](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Passaggi successivi

Per altre [informazioni, vedere Panoramica dei token B2C di Azure AD.](tokens-overview.md)
