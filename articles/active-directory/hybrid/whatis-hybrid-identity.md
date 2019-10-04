---
title: Che cos'è l'identità ibrida con Azure Active Directory?
description: Un'identità ibrida consente di avere un'identità utente comune per l'autenticazione e l'autorizzazione sia in locale che nel cloud.
keywords: introduzione ad Azure AD Connect, panoramica di Azure AD Connect, che cos'è Azure AD Connect, installare active directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3d681dd06f07f6174e31b59cccf42df5dc16a1e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779839"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Che cos'è l'identità ibrida con Azure Active Directory?

Oggi nelle aziende di piccole e grandi dimensioni si ricorre sempre più spesso a una combinazione di applicazioni locali e cloud e  gli utenti devono poter accedere a tali applicazioni sia in locale che nel cloud. Per gestire gli utenti sia in locale che nel cloud è necessario affrontare scenari complessi. 

Le soluzioni di gestione delle identità di Microsoft includono sia funzionalità locali che basate sul cloud,  in modo da creare una singola identità utente per l'autenticazione e l'autorizzazione a tutte le risorse, indipendentemente dalla loro posizione. Tale identità costituisce la cosiddetta **identità ibrida**.

Con l'identità ibrida per Azure AD e la gestione delle identità ibride, questi scenari diventano realizzabili.

Per usare l'identità ibrida con Azure AD, a seconda degli scenari è possibile adottare uno dei tre metodi di autenticazione   seguenti: 

- **[Sincronizzazione dell'hash delle password](whatis-phs.md)**  
- **[Autenticazione pass-through](how-to-connect-pta.md)**  
- **[Federazione (AD FS)](whatis-fed.md)** 

Questi metodi di autenticazione offrono anche funzionalità [Single Sign-On](how-to-connect-sso.md).  La funzionalità Single Sign-On consente agli utenti di eseguire l'accesso automaticamente dai dispositivi di proprietà dell'azienda connessi alla rete aziendale.

Per altre informazioni, vedere [Scegliere il metodo di autenticazione appropriato per la soluzione ibrida di gestione delle identità di Azure AD](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn). 

## <a name="common-scenarios-and-recommendations"></a>Scenari comuni e raccomandazioni 

Di seguito sono riportati alcuni scenari comuni di gestione di identità ibride e degli accessi con raccomandazioni riguardo all'opzione o alle opzioni di gestione delle identità ibride più appropriate per ciascuno di essi. 

|Esigenza:|PHS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Sincronizzare automaticamente nel cloud nuovi account utente, di contatti o di gruppo creati in Active Directory locale.|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| ![Consigliato](./media/whatis-hybrid-identity/ic195031.png) |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| 
|Configurare il tenant per scenari ibridi di Office 365.|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| ![Consigliato](./media/whatis-hybrid-identity/ic195031.png) |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| 
|Consentire agli utenti di accedere ai servizi cloud usando la propria password locale.|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| ![Consigliato](./media/whatis-hybrid-identity/ic195031.png) |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementare l'accesso SSO usando le credenziali aziendali.|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| ![Consigliato](./media/whatis-hybrid-identity/ic195031.png) |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|  
|Assicurarsi che gli hash delle password non vengano archiviati nel cloud.| |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| 
|Abilitare soluzioni di autenticazione a più fattori basate sul cloud.|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)|![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| 
|Abilitare soluzioni di autenticazione a più fattori locali.| | |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| 
|Supportare l'autenticazione tramite smart card per gli utenti.<sup>4</sup>| | |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| 
|Visualizzare le notifiche di scadenza delle password nel portale di Office e sul desktop di Windows 10.| | |![Consigliato](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Sincronizzazione dell'hash delle password con accesso Single Sign-On. 
> 
> <sup>2</sup> Autenticazione pass-through e accesso Single Sign-On.  
> 
> <sup>3</sup> Accesso Single Sign-On federato con AD FS.  
>  
> <sup>4</sup> ADFS può essere integrato con l'infrastruttura a chiave pubblica aziendale per consentire l'accesso usando certificati. Questi certificati possono essere certificati software distribuiti tramite canali di provisioning attendibili, ad esempio i certificati di gestione di dispositivi mobili (MDM) o l'oggetto Criteri di gruppo o smart card (comprese le schede PIV/CAC) o Hello for Business (cert-trust). Per altre informazioni sul supporto dell'autenticazione con smart card, vedere [questo blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Requisiti di licenza per l'uso di Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Passaggi successivi 

- [Informazioni su Azure AD Connect e Connect Health](whatis-azure-ad-connect.md) 
- [Informazioni sulla sincronizzazione dell'hash delle password](whatis-phs.md) 
- [Informazioni sull'autenticazione pass-through](how-to-connect-pta.md) 
- [Informazioni sulla federazione](whatis-fed.md) 
- [Informazioni su Single Sign-On](how-to-connect-sso.md) 

