---
title: Opzioni avanzate di firma del certificato nel token SAML per le app preintegrate in Azure Active Directory | Microsoft Docs
description: Informazioni su come usare le opzioni avanzate di firma del certificato nel token SAML per le app preintegrate in Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: barbkess
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 368fbf52642b1353b221ce6ebe6f6c40a517c3e6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473419"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opzioni avanzate di firma del certificato nel token SAML per le app della raccolta in Azure Active Directory
Azure Active Directory (Azure AD) supporta attualmente migliaia di applicazioni preintegrate nella raccolta di app di Azure Active Directory. Tra queste sono incluse oltre 500 applicazioni che supportano l'accesso Single Sign-On con il protocollo SAML 2.0. Quando un utente esegue l'autenticazione per un'applicazione con Azure AD usando SAML, Azure AD invia un token all'applicazione (tramite HTTP POST). L'applicazione quindi convalida e usa il token per l'accesso dell'utente anziché richiedere un nome utente e una password. I token SAML vengono firmati con il certificato univoco generato in Azure AD e algoritmi standard specifici.

Azure AD usa alcune delle impostazioni predefinite per le applicazioni della raccolta. I valori predefiniti vengono configurati in base ai requisiti dell'applicazione.

Azure AD supporta impostazioni avanzate per la firma di certificati. Per selezionare queste opzioni, selezionare prima di tutto la casella di controllo **Mostra impostazioni avanzate per la firma di certificati**:

![Mostra impostazioni avanzate per la firma di certificati](./media/certificate-signing-options/saml-advance-certificate.png)

Dopo aver selezionato questa casella di controllo, è possibile configurare le opzioni e l'algoritmo per la firma di certificati.

## <a name="certificate-signing-options"></a>Opzioni di firma del certificato

Azure AD supporta tre opzioni per la firma di certificati:

* **Firma asserzione SAML**. Per la maggior parte delle applicazioni della raccolta viene impostata questa opzione predefinita. Se questa opzione è selezionata, Azure AD, come provider di identità, firma l'asserzione e il certificato SAML con il certificato X509 dell'applicazione. Usa anche l'algoritmo di firma selezionato nell'elenco a discesa **Algoritmo di firma**.

* **Firma risposta SAML**. Se questa opzione è selezionata, Azure AD, come provider di identità, firma la risposta SAML con il certificato X509 dell'applicazione. Usa anche l'algoritmo di firma selezionato nell'elenco a discesa **Algoritmo di firma**.

* **Firma asserzione e risposta SAML**. Se questa opzione è selezionata, Azure AD, come provider di identità, firma l'intero token SAML con il certificato X509 dell'applicazione. Usa anche l'algoritmo di firma selezionato nell'elenco a discesa **Algoritmo di firma**.

    ![Opzioni di firma del certificato](./media/certificate-signing-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>Algoritmi per la firma di certificati

Azure AD supporta due algoritmi di firma per firmare la risposta SAML:

* **SHA-256**. Per firmare la risposta SAML, Azure AD usa questo algoritmo predefinito, che è più recente ed è considerato più sicuro rispetto a SHA-1. La maggior parte delle applicazioni supporta l'algoritmo SHA-256. Se un'applicazione supporta solo SHA-1 come algoritmo di firma, è possibile modificare questa impostazione. In caso contrario è consigliabile usare l'algoritmo SHA-256 per firmare la risposta SAML.

    ![Algoritmo per la firma di certificati SHA-256](./media/certificate-signing-options/saml-signing-algo-sha256.png)

* **SHA-1**. Questo algoritmo è meno recente ed è considerato meno sicuro rispetto a SHA-256. Se un'applicazione supporta solo questo algoritmo di firma, è possibile selezionare questa opzione nell'elenco a discesa **Algoritmo di firma**. Azure AD firmerà quindi la risposta SAML con l'algoritmo SHA-1.

    ![Algoritmo per la firma di certificati SHA-1](./media/certificate-signing-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>Passaggi successivi
* [Configurare l'accesso Single Sign-On per applicazioni non incluse nella raccolta di app di Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Risolvere i problemi relativi all'accesso Single Sign-On basato su SAML](../develop/howto-v1-debug-saml-sso-issues.md)


