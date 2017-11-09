---
title: 'Azure Active Directory Domain Services: abilitare la delega vincolata Kerberos | Documentazione Microsoft'
description: Abilitare la delega vincolata Kerberos in domini gestiti di Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: f36f16a7bb00ace9fd5164eb38ba77f015f22f5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Configurare la delega vincolata Kerberos (KCD) in un dominio gestito
Molte applicazioni devono accedere alle risorse nel contesto dell'utente. Active Directory supporta un meccanismo denominato delega Kerberos che consente questo caso d'uso. È anche possibile limitare la delega in modo che siano accessibili solo risorse specifiche nel contesto dell'utente. I domini gestiti di Azure AD Domain Services sono diversi dai domini di Active Directory tradizionali perché sono bloccati in modo più sicuro.

Questo articolo illustra come configurare la delega vincolata Kerberos in un dominio gestito di Azure AD Domain Services.

## <a name="kerberos-constrained-delegation-kcd"></a>Delega vincolata Kerberos (KCD)
La delega Kerberos consente a un account di rappresentare un'altra entità di sicurezza, ad esempio un utente, per accedere alle risorse. Si consideri un'applicazione Web che accede a un'API Web back-end nel contesto di un utente. In questo esempio l'applicazione Web, in esecuzione nel contesto di un account del servizio o di un account del computer, rappresenta l'utente durante l'accesso alla risorsa, ovvero l'API Web back-end. La delega Kerberos non è sicura perché non limita le risorse a cui può accedere l'account di rappresentazione nel contesto dell'utente.

La delega vincolata Kerberos consente di limitare le risorse o i servizi in cui il server specificato può agire per conto di un utente. La delega vincolata Kerberos tradizionale richiede privilegi di amministratore di dominio per configurare un account di dominio per un servizio e limita l'account a un dominio singolo.

Alla delega vincolata Kerberos tradizionale sono anche associati alcuni problemi. Nei sistemi operativi precedenti, in cui l'amministratore di dominio configurava il servizio, l'amministratore del servizio non aveva modo di sapere quali servizi front-end delegati ai servizi risorsa fossero di sua proprietà. Qualsiasi servizio front-end che potesse delegare a un servizio risorsa rappresentava un potenziale punto debole. Se un server che ospitava un servizio front-end era compromesso ed era configurato per la delega ai servizi risorsa, anche questi ultimi potevano essere compromessi.

> [!NOTE]
> In un dominio gestito di Azure AD Domain Services non si hanno privilegi di amministratore di dominio. Di conseguenza, **non è possibile configurare la delega vincolata Kerberos tradizionale in un dominio gestito**. Usare la delega vincolata Kerberos basata su risorse, come illustrato in questo articolo. Questo meccanismo è anche più sicuro.
>
>

## <a name="resource-based-kerberos-constrained-delegation"></a>Delega vincolata Kerberos basata sulle risorse
In Windows Server 2012 R2 e Windows Server 2012 la possibilità di configurare la delega vincolata per il servizio è stata trasferita dall'amministratore di dominio all'amministratore del servizio. In questo modo l'amministratore del servizio back-end può consentire o negare i servizi front-end. Questo modello è noto come **delega vincolata Kerberos basata sulle risorse**.

È possibile configurare la delega vincolata Kerberos basata sulle risorse usando PowerShell. Usare i cmdlet Set-ADComputer o Set-ADUser, a seconda che l'account di rappresentazione sia un account del computer oppure un account utente o un account del servizio.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Configurare la delega vincolata Kerberos basata sulle risorse per un account del computer in un dominio gestito
Si prenda ad esempio un'app Web in esecuzione nel computer "contoso100-webapp.contoso100.com". L'app deve accedere alla risorsa, ovvero un'API Web in esecuzione su "contoso100-api.contoso100.com", nel contesto degli utenti del dominio. Di seguito viene illustrato come impostare la delega vincolata Kerberos basata su risorse per questo scenario.

```
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Configurare la delega vincolata Kerberos basata sulle risorse per un account utente in un dominio gestito
Si prenda ad esempio un'app Web in esecuzione come account del servizio "appsvc". L'app deve accedere alla risorsa, ovvero un'API Web in esecuzione come account del servizio "backendsvc", nel contesto degli utenti del dominio. Di seguito viene illustrato come impostare la delega vincolata Kerberos basata su risorse per questo scenario.

```
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Panoramica della delega vincolata Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
