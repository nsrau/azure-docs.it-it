---
title: Opzioni avanzate di firma del certificato nel token SAML per le app preintegrate in Azure Active Directory | Microsoft Docs
description: Informazioni su come usare le opzioni avanzate di firma del certificato nel token SAML per le app preintegrate in Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1baf4af41fd5dcb6723b6ee2827ae91b43b072d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780955"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opzioni avanzate di firma del certificato nel token SAML per le app della raccolta in Azure Active Directory

Azure Active Directory (Azure AD) supporta attualmente migliaia di applicazioni preintegrate nella raccolta di app di Azure Active Directory. Oltre 500 applicazioni supportano l'accesso single sign-on tramite il [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 del protocollo, ad esempio le [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) dell'applicazione. Quando un cliente esegue l'autenticazione a un'applicazione tramite Azure AD usando SAML, Azure AD invia un token all'applicazione (via HTTP POST). L'applicazione quindi convalida e Usa il token per accedere al cliente anziché richiedere l'immissione di nome utente e password. I token SAML vengono firmati con il certificato univoco generato in Azure AD e algoritmi standard specifici.

Azure AD usa alcune delle impostazioni predefinite per le applicazioni della raccolta. I valori predefiniti vengono configurati in base ai requisiti dell'applicazione.

In Azure AD, è possibile impostare le opzioni di firma del certificato e l'algoritmo di firma del certificato.

## <a name="certificate-signing-options"></a>Opzioni di firma del certificato

Azure AD supporta tre opzioni per la firma di certificati:

* **Firma asserzione SAML**. Per la maggior parte delle applicazioni della raccolta viene impostata questa opzione predefinita. Se si seleziona questa opzione, Azure AD come Provider di identità (IdP), firma l'asserzione SAML e il certificato con il [X.509](https://wikipedia.org/wiki/X.509) certificato dell'applicazione.

* **Firma risposta SAML**. Se si seleziona questa opzione, Azure AD come IdP firma la risposta SAML con il certificato X.509 dell'applicazione.

* **Firma asserzione e risposta SAML**. Se si seleziona questa opzione, Azure AD come IdP firma l'intero token SAML con il certificato X.509 dell'applicazione.

## <a name="certificate-signing-algorithms"></a>Algoritmi per la firma di certificati

Azure AD supporta due algoritmi di firma o secure hash Algorithm (integrità), per firmare la risposta SAML:

* **SHA-256**. Per firmare la risposta SAML, Azure AD usa questo algoritmo predefinito, È l'algoritmo più recente e più sicuro rispetto a SHA-1. La maggior parte delle applicazioni supporta l'algoritmo SHA-256. Se un'applicazione supporta solo SHA-1 come algoritmo di firma, è possibile modificare questa impostazione. In caso contrario è consigliabile usare l'algoritmo SHA-256 per firmare la risposta SAML.

* **SHA-1**. Questo algoritmo è meno recente e è considerato meno sicuro rispetto a SHA-256. Se un'applicazione supporta solo questo algoritmo di firma, è possibile selezionare questa opzione nell'elenco a discesa **Algoritmo di firma**. Azure AD firmerà quindi la risposta SAML con l'algoritmo SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Modificare le opzioni di firma e l'algoritmo di firma di certificato

Per modificare l'algoritmo di firma del certificato e il certificato SAML dell'applicazione opzioni di firma, selezionare l'applicazione in questione:

1. Nel [portale di Azure Active Directory](https://aad.portal.azure.com/), accedi al tuo account. Il **interfaccia di amministrazione di Azure Active Directory** verrà visualizzata la pagina.
1. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Viene visualizzato un elenco delle applicazioni aziendali nell'account.
1. Selezionare un'applicazione. Viene visualizzata una pagina di panoramica per l'applicazione.

   ![Pagina di panoramica dell'applicazione](./media/certificate-signing-options/application-overview-page.png)

Successivamente, modificare il certificato nel token SAML per l'applicazione opzioni di firma:

1. Nel riquadro sinistro della pagina di panoramica dell'applicazione, selezionare **l'accesso Single sign-on**.

2. Se il **impostata su Single Sign-On con SAML - Preview** verrà visualizzata la pagina, andare al passaggio 5.

3. Se il **selezionare un metodo single sign-on** pagina non viene visualizzata, selezionare **cambiare le modalità single sign-on** per visualizzare la pagina.

4. Nel **selezionare un metodo single sign-on** pagina, selezionare **SAML** se disponibile. (Se **SAML** non è disponibile, l'applicazione non supporta SAML, e si può ignorare il resto di questa procedura e l'articolo.)

5. Nel **impostata su Single Sign-On con SAML - Preview** pagina, trovare il **certificato di firma SAML** titolo e selezionare il **modifica** icona (una matita). Il **certificato di firma SAML** verrà visualizzata la pagina.

   ![Pagina di firma SAML](./media/certificate-signing-options/saml-signing-page.png)

6. Nel **opzione di firma** elenco a discesa scegliere **risposta SAML Sign**, **firma asserzione SAML**, oppure **asserzione e risposta SAML Sign**. Le descrizioni di queste opzioni vengono visualizzate in precedenza in questo articolo nel [opzioni di firma del certificato](#certificate-signing-options).

7. Nel **algoritmo di firma** elenco a discesa scegliere **SHA-1** oppure **SHA-256**. Le descrizioni di queste opzioni vengono visualizzate in precedenza in questo articolo nel [algoritmi di firma del certificato](#certificate-signing-algorithms) sezione.

8. Se si è soddisfatti con le scelte effettuate, selezionare **salvare** applicare di nuovo le impostazioni del certificato di firma SAML. In caso contrario, selezionare la **X** per annullare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare l'accesso Single Sign-On per applicazioni non incluse nella raccolta di app di Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Risolvere i problemi relativi all'accesso Single Sign-On basato su SAML](../develop/howto-v1-debug-saml-sso-issues.md)
