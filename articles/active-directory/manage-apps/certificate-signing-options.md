---
title: Opzioni avanzate di firma del certificato token SAML per le app di Azure ADAdvanced SAML token certificate signing options for Azure AD apps
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
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159200"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opzioni avanzate di firma del certificato nel token SAML per le app della raccolta in Azure Active Directory

Azure Active Directory (Azure AD) supporta attualmente migliaia di applicazioni preintegrate nella raccolta di app di Azure Active Directory. Oltre 500 applicazioni supportano l'accesso Single Sign-On utilizzando il protocollo SAML [(Security Assertion Markup Language)](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) 2.0, ad esempio l'applicazione [NetSuite.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) Quando un cliente esegue l'autenticazione a un'applicazione tramite Azure AD tramite SAML, Azure AD invia un token all'applicazione (tramite un HTTP POST). L'applicazione convalida quindi e utilizza il token per accedere al cliente anziché richiedere un nome utente e una password. I token SAML vengono firmati con il certificato univoco generato in Azure AD e algoritmi standard specifici.

Azure AD usa alcune delle impostazioni predefinite per le applicazioni della raccolta. I valori predefiniti vengono configurati in base ai requisiti dell'applicazione.

In Azure AD è possibile configurare le opzioni di firma dei certificati e l'algoritmo di firma dei certificati.

## <a name="certificate-signing-options"></a>Opzioni di firma del certificato

Azure AD supporta tre opzioni per la firma di certificati:

* **Firma asserzione SAML**. Per la maggior parte delle applicazioni della raccolta viene impostata questa opzione predefinita. Se si seleziona questa opzione, Azure AD come provider di identità (IdP) firma l'asserzione SAML e il certificato con il certificato [X.509](https://wikipedia.org/wiki/X.509) dell'applicazione.

* **Firma risposta SAML**. Se si seleziona questa opzione, Azure AD come IdP firma la risposta SAML con il certificato X.509 dell'applicazione.

* **Firma asserzione e risposta SAML**. Se si seleziona questa opzione, Azure AD come IdP firma l'intero token SAML con il certificato X.509 dell'applicazione.

## <a name="certificate-signing-algorithms"></a>Algoritmi per la firma di certificati

Azure AD supporta due algoritmi di firma o sCIA hash sicuri (SHA) per firmare la risposta SAML:Azure AD supports two signing algorithms, or secure hash algorithms (SHAs), to sign the SAML response:

* **SHA-256**. Per firmare la risposta SAML, Azure AD usa questo algoritmo predefinito, È l'algoritmo più recente ed è più sicuro di SHA-1. La maggior parte delle applicazioni supporta l'algoritmo SHA-256. Se un'applicazione supporta solo SHA-1 come algoritmo di firma, è possibile modificare questa impostazione. In caso contrario è consigliabile usare l'algoritmo SHA-256 per firmare la risposta SAML.

* **SHA-1**. Questo algoritmo è più vecchio e viene considerato meno sicuro di SHA-256. Se un'applicazione supporta solo questo algoritmo di firma, è possibile selezionare questa opzione nell'elenco a discesa **Algoritmo di firma**. Azure AD firmerà quindi la risposta SAML con l'algoritmo SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Modificare le opzioni di firma dei certificati e l'algoritmo di firmaChange certificate signing options and signing algorithm

Per modificare le opzioni di firma del certificato SAML di un'applicazione e l'algoritmo di firma del certificato, selezionare l'applicazione in questione:

1. Nel [portale](https://aad.portal.azure.com/)di Azure Active Directory accedere all'account. Viene visualizzata la pagina **dell'interfaccia** di amministrazione di Azure Active Directory.The Azure Active Directory admin center page appears.
1. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Viene visualizzato un elenco delle applicazioni aziendali nel tuo account.
1. Selezionare un'applicazione. Viene visualizzata una pagina di panoramica per l'applicazione.

   ![Esempio: pagina di panoramica dell'applicazioneExample: Application overview page](./media/certificate-signing-options/application-overview-page.png)

Modificare quindi le opzioni di firma del certificato nel token SAML per l'applicazione:Next, change the certificate signing options in the SAML token for that application:

1. Nel riquadro sinistro della pagina di panoramica dell'applicazione selezionare **Single Sign-On**.
1. Se viene visualizzata la pagina **Configura Single Sign-On con SAML - Anteprima,** andare al passaggio 5.
1. Se la pagina Selezionare un metodo Single **Sign-On** non viene visualizzata, selezionare **Modifica modalità Single Sign-On** per visualizzare tale pagina.
1. Nella pagina **Selezionare un metodo Single Sign-On** selezionare **SAML,** se disponibile. Se **SAML** non è disponibile, l'applicazione non supporta SAML e potresti ignorare il resto di questa procedura e l'articolo.
1. Nella pagina **Configura Single Sign-On con SAML - Anteprima,** trova l'intestazione Certificato di **firma SAML** e seleziona l'icona **Modifica** (una matita). Viene visualizzata la pagina **Certificato di firma SAML.**

   ![Esempio: pagina del certificato di firma SAML](./media/certificate-signing-options/saml-signing-page.png)

1. Nell'elenco a discesa **Opzioni di firma** scegliere **Firma risposta SAML**, **Firma asserzione SAML** o **Firma risposta e asserzione SAML**. Le descrizioni di queste opzioni vengono visualizzate in precedenza in questo articolo nelle [opzioni di firma del certificato](#certificate-signing-options).
1. Nell'elenco a discesa **Algoritmo di firma** scegliere **SHA-1** o **SHA-256**. Le descrizioni di queste opzioni vengono visualizzate in precedenza in questo articolo nella sezione Algoritmi di [firma dei](#certificate-signing-algorithms) certificati.
1. Se sei soddisfatto delle tue scelte, seleziona **Salva** per applicare le nuove impostazioni del certificato di firma SAML. In caso contrario, selezionare la **X** per annullare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare l'accesso Single Sign-On per applicazioni non incluse nella raccolta di app di Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Risolvere i problemi relativi all'accesso Single Sign-On basato su SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
