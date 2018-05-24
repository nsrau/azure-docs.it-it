---
title: Mapping delle attestazioni utente per Collaborazione B2B in Azure Active Directory | Microsoft Docs
description: Personalizzare le attestazioni rilasciate nel token SAML per gli utenti B2B di Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/06/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f2617fb79fc20fcb385c7e2fc3596245ab18caba
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapping delle attestazioni utente per Collaborazione B2B in Azure Active Directory

Azure Active Directory (Azure AD) supporta la personalizzazione delle attestazioni rilasciate nel token SAML per gli utenti di Collaborazione B2B. Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia un token SAML all'applicazione contenente informazioni (o attestazioni) sull'utente, che lo identificano in modo univoco. Per impostazione predefinita, sono inclusi il nome utente, l'indirizzo di posta elettronica, il nome e il cognome dell'utente.

Nel [portale di Azure](https://portal.azure.com) è possibile visualizzare o modificare le attestazioni inviate all'applicazione nel token SAML. Per accedere alle impostazioni, selezionare **Azure Active Directory** > **Applicazioni aziendali** > applicazione configurata per l'accesso Single Sign-On > **Single Sign-On**. Visualizzare le impostazioni del token SAML nella sezione **Attributi utente**.

![Visualizzazione degli attributi del token SAML nell'interfaccia utente](media/claims-mapping/view-claims-in-saml-token.png)

I due possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML sono i seguenti:

1. L'applicazione richiede un set diverso di URI di attestazione o valori di attestazione.

2. L'applicazione richiede che l'attestazione NameIdentifier sia diversa dal nome dell'entità utente archiviato in Azure AD.

Per altre informazioni su come aggiungere e modificare attestazioni, vedere [Personalizzazione delle attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Per gli utenti di Collaborazione B2B, il mapping tra NameID e UPN tra tenant non è consentito per motivi di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle proprietà degli utenti di Collaborazione B2B, vedere [Proprietà di un utente di Collaborazione B2B di Azure Active Directory](user-properties.md).
- Per informazioni sui token utente per gli utenti di Collaborazione B2B, vedere [Informazioni sui token utente in Collaborazione B2B di Azure AD](user-token.md).

