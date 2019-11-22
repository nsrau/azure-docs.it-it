---
title: Opzioni avanzate di firma del certificato SAML per le app Azure AD
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
ms.openlocfilehash: 614a90fcc0daef3eb77c43e6b56848c34d160ed5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274752"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opzioni avanzate di firma del certificato nel token SAML per le app della raccolta in Azure Active Directory

Azure Active Directory (Azure AD) supporta attualmente migliaia di applicazioni preintegrate nella raccolta di app di Azure Active Directory. Oltre 500 le applicazioni supportano Single Sign-On usando il protocollo di [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2,0, ad esempio l'applicazione [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) . Quando un cliente esegue l'autenticazione a un'applicazione tramite Azure AD usando SAML, Azure AD invia un token all'applicazione (tramite un HTTP POST). L'applicazione quindi convalida e usa il token per accedere al cliente anziché richiedere un nome utente e una password. I token SAML vengono firmati con il certificato univoco generato in Azure AD e algoritmi standard specifici.

Azure AD usa alcune delle impostazioni predefinite per le applicazioni della raccolta. I valori predefiniti vengono configurati in base ai requisiti dell'applicazione.

In Azure AD, è possibile configurare le opzioni di firma del certificato e l'algoritmo di firma del certificato.

## <a name="certificate-signing-options"></a>Opzioni di firma del certificato

Azure AD supporta tre opzioni per la firma di certificati:

* **Firma asserzione SAML**. Per la maggior parte delle applicazioni della raccolta viene impostata questa opzione predefinita. Se si seleziona questa opzione, Azure AD come provider di identità (IdP) firma l'asserzione SAML e il certificato con il certificato [X. 509](https://wikipedia.org/wiki/X.509) dell'applicazione.

* **Firma risposta SAML**. Se si seleziona questa opzione, Azure AD come IdP firma la risposta SAML con il certificato X. 509 dell'applicazione.

* **Firma asserzione e risposta SAML**. Se si seleziona questa opzione, Azure AD come IdP firma l'intero token SAML con il certificato X. 509 dell'applicazione.

## <a name="certificate-signing-algorithms"></a>Algoritmi per la firma di certificati

Azure AD supporta due algoritmi di firma, ovvero SHAs (Secure Hash Algorithm), per firmare la risposta SAML:

* **SHA-256**. Per firmare la risposta SAML, Azure AD usa questo algoritmo predefinito, Si tratta dell'algoritmo più recente ed è più sicuro rispetto a SHA-1. La maggior parte delle applicazioni supporta l'algoritmo SHA-256. Se un'applicazione supporta solo SHA-1 come algoritmo di firma, è possibile modificare questa impostazione. In caso contrario è consigliabile usare l'algoritmo SHA-256 per firmare la risposta SAML.

* **SHA-1**. Questo algoritmo è meno recente e viene considerato meno sicuro di SHA-256. Se un'applicazione supporta solo questo algoritmo di firma, è possibile selezionare questa opzione nell'elenco a discesa **Algoritmo di firma**. Azure AD firmerà quindi la risposta SAML con l'algoritmo SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Modificare le opzioni di firma del certificato e l'algoritmo di firma

Per modificare le opzioni di firma del certificato SAML di un'applicazione e l'algoritmo di firma del certificato, selezionare l'applicazione in questione:

1. Nel [portale di Azure Active Directory](https://aad.portal.azure.com/)accedere al proprio account. Viene visualizzata la pagina **centro di amministrazione Azure Active Directory** .
1. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Viene visualizzato un elenco delle applicazioni aziendali nell'account.
1. Selezionare un'applicazione. Viene visualizzata una pagina di panoramica per l'applicazione.

   ![Esempio: pagina Panoramica applicazione](./media/certificate-signing-options/application-overview-page.png)

Modificare quindi le opzioni di firma del certificato nel token SAML per l'applicazione:

1. Nel riquadro sinistro della pagina Panoramica applicazione selezionare **Single Sign-on**.
1. Se viene visualizzata la pagina **Configura accesso Single Sign-on con SAML-Preview** , andare al passaggio 5.
1. Se la pagina **selezionare un metodo di Single Sign-on** non viene visualizzata, selezionare **modifica modalità Single Sign-on** per visualizzare la pagina.
1. Nella pagina **selezionare un metodo di Single Sign-on** selezionare **SAML** , se disponibile. Se **SAML** non è disponibile, l'applicazione non supporta SAML ed è possibile ignorare il resto di questa procedura e di questo articolo.
1. Nella pagina **Configura accesso Single Sign-on con SAML-Preview** trovare l'intestazione **certificato di firma SAML** e selezionare l'icona di **modifica** (matita). Viene visualizzata la pagina **certificato di firma SAML** .

   ![Esempio: pagina certificato di firma SAML](./media/certificate-signing-options/saml-signing-page.png)

1. Nell'elenco a discesa **Opzioni di firma** scegliere **Firma risposta SAML**, **Firma asserzione SAML** o **Firma risposta e asserzione SAML**. Le descrizioni di queste opzioni vengono visualizzate in precedenza in questo articolo nelle [Opzioni di firma del certificato](#certificate-signing-options).
1. Nell'elenco a discesa **Algoritmo di firma** scegliere **SHA-1** o **SHA-256**. Le descrizioni di queste opzioni sono illustrate in precedenza in questo articolo nella sezione [algoritmi di firma del certificato](#certificate-signing-algorithms) .
1. Se si è soddisfatti delle scelte, selezionare **Salva** per applicare le nuove impostazioni del certificato di firma SAML. In caso contrario, selezionare la **X** per annullare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare l'accesso Single Sign-On per applicazioni non incluse nella raccolta di app di Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Risolvere i problemi relativi all'accesso Single Sign-On basato su SAML](../develop/howto-v1-debug-saml-sso-issues.md)
