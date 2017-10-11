---
title: Gestire l'accesso Single Sign-On per il proxy di applicazione di Azure AD | Microsoft Docs
description: Informazioni di base sull'accesso Single Sign-On con il proxy di applicazione
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1deb3d91049d45fe26791783e13bd23e0a7d9f95
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Come viene offerto l'accesso Single Sign-On dal proxy di applicazione di Azure AD?

Single Sign-On è un elemento chiave del proxy di applicazione di Azure AD.  Offre un'esperienza d'uso ottimale, poiché gli utenti devono solo eseguire l'accesso ad Azure Active Directory nel cloud. Dopo che un utente ha eseguito l'autenticazione ad Azure Active Directory, il connettore del proxy di applicazione gestisce l'autenticazione all'applicazione locale. L'applicazione di back-end non è in grado di rilevare la differenza tra un utente remoto che accede tramite il proxy di applicazione e un utilizzo standard basato su un dispositivo aggiunto al dominio. 

Per usare Azure Active Directory per l'accesso Single Sign-On alle applicazioni, è necessario selezionare **Azure Active Directory** come metodo di pre-autenticazione. Se si seleziona **PassThrough**, gli utenti non eseguono l'autenticazione ad Azure Active Directory ma vengono indirizzati direttamente all'applicazione. È possibile configurare questa impostazione quando si pubblica per la prima volta un'applicazione. In alternativa, è possibile accedere all'applicazione nel portale di Azure e modificare le impostazioni del proxy di applicazione. 

Per visualizzare le opzioni relative all'accesso Single Sign-On, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory** > **Applicazioni aziendali** > **Tutte le applicazioni**.
3. Selezionare l'app di cui si vuole gestire le opzioni di accesso Single Sign-On.
4. Selezionare **Single Sign-On**.

   ![Menu a discesa Single Sign-On](./media/application-proxy-sso-overview/single-sign-on-mode.png)

Nel menu a discesa sono disponibili cinque opzioni per l'accesso Single Sign-On all'applicazione:

* Single Sign-On di Azure AD disabilitato
* Accesso basato su password
* Linked sign-on (Accesso collegato)
* Autenticazione integrata di Windows
* Accesso basato su intestazione

## <a name="azure-ad-single-sign-on-disabled"></a>Single Sign-On di Azure AD disabilitato

Se non si vuole usare l'integrazione di Azure Active Directory per l'accesso Single Sign-On all'applicazione, scegliere **Single Sign-On di Azure AD disabilitato**. Con questa opzione selezionata, gli utenti possono eseguire l'autenticazione due volte. Eseguire prima l'autenticazione ad Azure Active Directory e quindi accedere all'applicazione. 

Questa opzione è la scelta migliore se l'applicazione locale non prevede l'autenticazione ma si vuole aggiungere Azure Active Directory come un livello di sicurezza per l'accesso remoto. 

## <a name="password-based-sign-on"></a>Accesso basato su password

Se si vuole usare Azure Active Directory come un insieme di credenziali delle password per le applicazioni locali, scegliere **Accesso basato su password**. Questa opzione è la scelta migliore se l'autenticazione all'applicazione è bastata su nome utente e password e non su intestazioni o token di accesso. Con l'accesso basato su password, gli utenti devono eseguire la procedura di accesso all'applicazione solo al primo accesso. Successivamente, sarà Azure Active Directory a fornire il nome utente e la password per conto dell'utente. 

Per informazioni sulla configurazione dell'accesso basato su password, vedere [Insieme di credenziali delle password per l'accesso Single Sign-On con il proxy di applicazione](application-proxy-sso-azure-portal.md).

## <a name="linked-sign-on"></a>Linked sign-on (Accesso collegato)

Se è già stata configurata una soluzione di accesso Single Sign-On per le identità locali, scegliere **Accesso collegato**. Questa opzione consente ad Azure Active Directory di sfruttare le soluzioni Single Sign-On esistenti, ma continua a offrire agli utenti l'accesso remoto all'applicazione. 

Per altre informazioni sul linked sign-on (accesso collegato), noto formalmente come Single Sign-On esistente, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows

Se le applicazioni locali usano l'autenticazione di Windows integrata o se si preferisce usare la delega vincolata Kerberos per l'accesso Single Sign-On, scegliere **Autenticazione di Windows integrata**. Con questa opzione, l'utente deve solo eseguire l'autenticazione ad Azure Active Directory e il connettore del proxy di applicazione acquisirà un token Kerberos e accederà all'applicazione per conto dell'utente. 

Per informazioni sulla configurazione dell'autenticazione di Windows integrata, vedere [Delega vincolata Kerberos per l'accesso Single Sign-On con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md).

## <a name="header-based-sign-on"></a>Accesso basato su intestazione 

Se le applicazioni usano le intestazioni per l'autenticazione, scegliere **Accesso basato su intestazione**. Con questa opzione, gli utenti devono solo eseguire l'autenticazione ad Azure Active Directory. Microsoft si avvale di un servizio di autenticazione di terze parti chiamato PingAccess, che converte il token di accesso ad Azure Active Directory in un formato di intestazione per l'applicazione. 

Per informazioni sulla configurazione dell'autenticazione basata su intestazione, vedere [Autenticazione basata su intestazione per l'accesso Single Sign-On con il proxy di applicazione](application-proxy-ping-access.md).

## <a name="next-steps"></a>Passaggi successivi

- [Insieme di credenziali delle password per l'accesso Single Sign-On con il proxy di applicazione](application-proxy-sso-azure-portal.md)
- [Delega vincolata Kerberos per l'accesso Single Sign-On con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)
- [Autenticazione basata su intestazione per l'accesso Single Sign-On con il proxy di applicazione](application-proxy-ping-access.md) 
