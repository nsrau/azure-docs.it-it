---
title: Concedere agli utenti B2B in Azure AD l'accesso alle applicazioni locali | Microsoft Docs
description: Illustra come fornire agli utenti B2B cloud l'accesso alle app locali con Collaborazione B2B di Azure AD.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 57c807b7729b58d2537ee09ddd5d05d4f2afb55e
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068844"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Concedere agli utenti B2B in Azure AD l'accesso alle applicazioni locali

Un'organizzazione che usa Collaborazione B2B di Azure Active Directory (Azure AD) per invitare gli utenti guest di organizzazioni partner in Azure AD può ora fornire a questi utenti B2B l'accesso alle app locali. Queste app locali possono usare l'autenticazione basata su SAML o l'autenticazione integrata di Windows con la delega vincolata Kerberos.

## <a name="access-to-saml-apps"></a>Accesso alle app SAML

Se l'app locale usa l'autenticazione basata su SAML, è possibile renderla facilmente disponibile per gli utenti di Collaborazione B2B di Azure AD tramite il portale di Azure.

Sono necessarie entrambe queste operazioni:

- Integrare l'app SAML usando il modello di applicazione non nella raccolta, come descritto in [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](../manage-apps/configure-single-sign-on-non-gallery-applications.md). Assicurarsi di prendere nota del valore usato per **URL di accesso**.
-  Usare Azure AD Application Proxy per pubblicare l'app locale, configurando **Azure Active Directory** come origine dell'autenticazione. Per istruzioni, vedere [Pubblicare applicazioni con Azure AD Application Proxy](../manage-apps/application-proxy-publish-azure-portal.md). 

   Quando si configura l'impostazione **URL interno**, usare l'URL di accesso specificato nel modello di applicazione non nella raccolta. In questo modo, gli utenti possono accedere all'app dall'esterno dei confini dell'organizzazione. Application Proxy esegue l'accesso Single Sign-On SAML per l'app locale.
 
   ![URL interno e autenticazione nelle impostazioni dell'app locale](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Accesso alle app di autenticazione integrata di Windows e delega vincolata Kerberos

Per fornire agli utenti B2B l'accesso alle applicazioni locali protette con l'autenticazione integrata di Windows e la delega vincolata Kerberos, sono necessari i componenti seguenti:

- **Autenticazione tramite Azure AD Application Proxy**. Gli utenti B2B devono poter eseguire l'autenticazione nell'applicazione locale. A tale scopo, è necessario pubblicare l'app locale tramite Azure AD Application Proxy. Per altre informazioni, vedere [Attività iniziali del proxy di applicazione e installazione del connettore](../manage-apps/application-proxy-enable.md) e [Pubblicare applicazioni con Azure AD Application Proxy](../manage-apps/application-proxy-publish-azure-portal.md).
- **Autorizzazione tramite un oggetto utente B2B nella directory locale**. L'applicazione deve poter eseguire i controlli di accesso utente e concedere l'accesso alle risorse corrette. L'autenticazione integrata di Windows e la delega vincolata Kerberos richiedono un oggetto utente nell'istanza locale di Windows Server Active Directory per completare l'autorizzazione. Come descritto in [Funzionamento di Single Sign-On con KCD](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), Application Proxy richiede questo oggetto utente per rappresentare l'utente e ottenere un token Kerberos per l'app. 

   Per lo scenario utente B2B, sono disponibili due metodi per creare gli oggetti utente guest necessari per l'autorizzazione nella directory locale:

   - Microsoft Identity Manager (MIM) e l'[agente di gestione MIM per Microsoft Graph](#create-b2b-guest-user-objects-through-mim-preview). 
   - [Script di PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). L'uso dello script è una soluzione più semplice che non richiede MIM. 

Il diagramma seguente offre una panoramica generale del funzionamento combinato di Azure AD Application Proxy e della generazione dell'oggetto utente B2B nella directory locale per concedere agli utenti B2B l'accesso alle app locali di autenticazione integrata di Windows e delega vincolata Kerberos. I passaggi numerati sono descritti in dettaglio nel diagramma seguente.

![Diagramma delle soluzioni script B2B e MIM](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Un utente di un'organizzazione partner (tenant Fabrikam) viene invitato nel tenant Contoso.
2.  Viene creato un oggetto utente guest nel tenant Contoso (ad esempio, un oggetto utente UPN guest_fabrikam.com#EXT#@contoso.onmicrosoft.com).
3.  Il guest Fabrikam viene importato da Contoso tramite MIM o tramite lo script di PowerShell B2B.
4.  Una rappresentazione, o "footprint", dell'oggetto utente guest Fabrikam (Guest#EXT#) viene creata nella directory locale, Contoso.com, tramite MIM o tramite lo script di PowerShell B2B.
5.  L'utente guest accede all'applicazione locale, app.contoso.com.
6.  La richiesta di autenticazione è autorizzata tramite Application Proxy, usando la delega vincolata Kerberos. 
7.  Poiché l'oggetto utente guest si trova in locale, l'autenticazione ha esito positivo.

### <a name="lifecycle-management-policies"></a>Criteri di gestione del ciclo di vita

È possibile gestire gli oggetti utente B2B locali tramite i criteri di gestione del ciclo di vita. Ad esempio: 

- È possibile impostare criteri di autenticazione a più fattori (MFA) per l'utente guest in modo che l'autenticazione a più fattori venga usata durante l'autenticazione di Application Proxy. Per altre informazioni, vedere [Accesso condizionale per gli utenti di Collaborazione B2B](conditional-access.md).
- Qualsiasi sponsorizzazione, verifica di accesso, verifica dell'account e così via eseguita per l'utente B2B cloud si applica agli utenti locali. Se, ad esempio, l'utente cloud viene eliminato tramite i criteri di gestione del ciclo di vita, anche l'utente locale viene eliminato mediante la sincronizzazione MIM oppure la sincronizzazione di Azure AD Connect. Per altre informazioni, vedere [Gestire l'accesso guest con le verifiche di accesso di Azure AD](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Creare oggetti utente guest B2B con MIM

Per informazioni su come usare MIM 2016 Service Pack 1 e l'agente di gestione MIM per Microsoft Graph per creare oggetti utente guest nella directory locale, vedere [Azure AD business-to-business (B2B) collaboration with Microsoft Identity Manager (MIM) 2016 SP1 with Azure Application Proxy (Public Preview)](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario) (Collaborazione B2B (Business to Business) di Azure AD con Microsoft Identity Manager (MIM) 2016 SP1 con Azure Application Proxy).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Creare oggetti utente guest B2B tramite uno script (anteprima)

È disponibile uno script di esempio di PowerShell che è possibile usare come punto di partenza per creare oggetti utente guest nell'istanza locale di Active Directory.

È possibile scaricare lo script e il file leggimi dall'[Area download](https://www.microsoft.com/download/details.aspx?id=51495). Scegliere il file **Script and Readme to pull Azure AD B2B users on-prem.zip**.

Prima di usare lo script, verificare i prerequisiti e leggere le considerazioni importanti nel file leggimi associato. Tenere inoltre presente che lo script viene fornito solo come esempio. Il team di sviluppo o un partner dovrà personalizzare e verificare lo script prima dell'esecuzione.

## <a name="license-considerations"></a>Considerazioni sulle licenze

Assicurarsi di avere le licenze CAL (Client Access License) corrette per gli utenti guest esterni che accedono alle app locali. Per altre informazioni, vedere la sezione relativa ai connettori esterni in [Client Access Licenses and Management Licenses](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx) (Licenze di gestione e CAL). Per le esigenze di licenze specifiche, rivolgersi al rappresentante Microsoft o al rivenditore locale.

## <a name="next-steps"></a>Passaggi successivi

- [Collaborazione B2B di Azure Active Directory per organizzazioni ibride](hybrid-organizations.md)

- Per informazioni su Azure AD Connect, vedere [Integrare le directory locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

