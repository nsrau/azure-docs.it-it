---
title: Opzioni avanzate di firma del certificato nel token SAML per le app preintegrate in Azure Active Directory | Microsoft Docs
description: Informazioni su come usare le opzioni avanzate di firma del certificato nel token SAML per le app preintegrate in Azure Active Directory
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 70e495965287a0edcb31493b69311fe28e04f6dc
ms.contentlocale: it-it
ms.lasthandoff: 09/19/2017

---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Opzioni avanzate di firma del certificato nel token SAML per le app della raccolta in Azure Active Directory
Attualmente Azure Active Directory supporta migliaia di applicazioni preintegrate nella raccolta di applicazioni di Azure AD, tra cui oltre 500 che supportano l'accesso Single Sign-On usando il protocollo SAML 2.0. Quando un utente esegue l'autenticazione in un'applicazione con Azure AD usando il protocollo SAML, Azure AD invia un token all'applicazione (via HTTP POST). L'applicazione quindi convalida e usa il token per l'accesso dell'utente anziché richiedere l'immissione di nome utente e password. I token SAML vengono firmati con il certificato univoco generato in Azure AD. Il token SAML viene firmato con algoritmi standard specifici.

Azure Active Directory usa alcune impostazioni predefinite per le applicazioni della raccolta. In base al requisito dell'applicazione vengono configurati i valori predefiniti.

Azure Active Directory supporta le impostazioni avanzate di firma del certificato. Per selezionare queste opzioni, selezionare prima la casella di controllo **Mostra impostazioni avanzate per la firma di certificati** come riportato di seguito.

![Opzioni di firma del certificato][1]

Dopo che è stata selezionata questa casella di controllo, è possibile configurare le **opzioni di firma del certificato** e l'**algoritmo di firma del certificato**.

## <a name="certificate-signing-options"></a>Opzioni di firma del certificato

Di seguito sono riportati tre tipi di opzioni di firma del certificato supportati da Azure AD.

1. **Firma asserzione SAML**: opzione predefinita impostata per la maggior parte delle applicazioni della raccolta. Se questa opzione è selezionata, Azure AD come IdP firma le asserzioni e il certificato SAML con un certificato X.509 dell'applicazione. Usa anche l'algoritmo di firma, che viene selezionato nell'elenco a discesa di seguito.

2. **Firma risposta SAML**: se questa opzione è selezionata, Azure AD come IdP firma la risposta SAML con il certificato X.509 dell'applicazione. Usa anche l'algoritmo di firma, che viene selezionato nell'elenco a discesa di seguito.

3. **Firma asserzione e risposta SAML**: se questa opzione è selezionata, Azure AD come IdP firma l'intero token SAML con il certificato X.509 dell'applicazione. Usa anche l'algoritmo di firma, che viene selezionato nell'elenco a discesa di seguito.

    ![Opzioni di firma del certificato][4]

## <a name="certificate-signing-algorithm"></a>Algoritmo di firma del certificato

Azure Active Directory supporta due tipi di algoritmo di firma per firmare la risposta SAML.

1. SHA256: algoritmo predefinito usato da Azure Active Directory per firmare la risposta SAML. Questo algoritmo è più recente e considerato più sicuro rispetto a SHA1. La maggior parte delle applicazioni supporta l'algoritmo SHA256. Se l'applicazione supporta solo l'algoritmo di firma SHA1, è possibile modificare il tipo. In caso contrario è consigliabile usare l'algoritmo SHA256 per firmare la risposta SAML.

    ![Algoritmo di firma del certificato SHA256][3]

2. SHA1: algoritmo meno recente e non considerato sicuro. Se l'applicazione supporta solo questo algoritmo di firma, è possibile selezionare questa opzione nell'elenco a discesa. In questo caso Azure AD firma la risposta SAML con l'algoritmo SHA1.

    ![Algoritmo di firma del certificato SHA1][2]

## <a name="next-steps"></a>Passaggi successivi
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](active-directory-saas-custom-apps.md)
* [Risoluzione dei problemi dell'accesso Single Sign-On basato su SAML](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png
