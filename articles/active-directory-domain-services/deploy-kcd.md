---
title: 'Azure Active Directory Domain Services: Abilitare la delega vincolata Kerberos | Microsoft Docs'
description: Abilitare la delega vincolata Kerberos in domini gestiti di Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: f234eaea0d4df3859ef9458ea334f1b7616add34
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612938"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Configurare la delega vincolata Kerberos in un dominio gestito
Molte applicazioni devono accedere alle risorse nel contesto dell'utente. Active Directory supporta un meccanismo denominato delega Kerberos che consente questo caso d'uso. È anche possibile limitare la delega in modo che siano accessibili solo risorse specifiche nel contesto dell'utente. I domini gestiti di Azure AD Domain Services sono diversi dai domini di Active Directory tradizionali perché sono bloccati in modo più sicuro.

Questo articolo illustra come configurare la delega vincolata Kerberos in un dominio gestito di Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Delega vincolata Kerberos (KCD)
La delega Kerberos consente a un account di rappresentare un'altra entità di sicurezza, ad esempio un utente, per accedere alle risorse. Si consideri un'applicazione Web che accede a un'API Web back-end nel contesto di un utente. In questo esempio l'applicazione Web, in esecuzione nel contesto di un account del servizio o di un account del computer, rappresenta l'utente durante l'accesso alla risorsa, ovvero l'API Web back-end. La delega Kerberos non è sicura perché non limita le risorse a cui può accedere l'account di rappresentazione nel contesto dell'utente.

La delega vincolata Kerberos consente di limitare le risorse o i servizi in cui il server specificato può agire per conto di un utente. La delega vincolata Kerberos tradizionale richiede privilegi di amministratore di dominio per configurare un account di dominio per un servizio e limita l'account a un dominio singolo.

Alla delega vincolata Kerberos tradizionale sono anche associati alcuni problemi. Nei sistemi operativi precedenti, se l'amministratore di dominio configurava la delega vincolata Kerberos basata sull'account per il servizio, l'amministratore del servizio non aveva modo di sapere quali servizi front-end delegati ai servizi risorsa fossero di sua proprietà. Qualsiasi servizio front-end che potesse delegare a un servizio risorsa rappresentava un potenziale punto debole. Se un server che ospitava un servizio front-end era compromesso ed era configurato per la delega ai servizi risorsa, anche questi ultimi potevano essere compromessi.

> [!NOTE]
> In un dominio gestito di Azure AD Domain Services non si hanno privilegi di amministratore di dominio. Di conseguenza, **non è possibile configurare la delega vincolata Kerberos tradizionale basata su account in un dominio gestito**. Usare la delega vincolata Kerberos basata su risorse, come illustrato in questo articolo. Questo meccanismo è anche più sicuro.
>
>

## <a name="resource-based-kcd"></a>Delega vincolata Kerberos basata su risorse
A partire da Windows Server 2012 gli amministratori dei servizi hanno la possibilità di configurare la delega vincolata per il servizio. In questo modello l'amministratore del servizio back-end può consentire o negare i servizi front-end usando la delega vincolata. Questo modello è noto come **delega vincolata Kerberos basata su risorse**.

È possibile configurare la delega vincolata Kerberos basata sulle risorse usando PowerShell. Usare il cmdlet `Set-ADComputer` o `Set-ADUser`, a seconda che l'account di rappresentazione sia un account del computer oppure un account utente o un account del servizio.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Configurare la delega vincolata Kerberos basata sulle risorse per un account del computer in un dominio gestito
Si supponga di avere un'app Web in esecuzione nel computer ' contoso-webapp.contoso.com '. Deve accedere alla risorsa (un'API Web in esecuzione su' contoso-api.contoso.com ') nel contesto degli utenti del dominio. Di seguito viene illustrato come configurare la delega vincolata Kerberos basata su risorse per questo scenario:

1. [Creare un'unità organizzativa personalizzata](create-ou.md). È possibile delegare le autorizzazioni per gestire questa unità organizzativa agli utenti appartenenti al dominio gestito.
2. Aggiungere le due macchine virtuali, quella che esegue l'app Web e quella che esegue l'API Web, al dominio gestito. Creare gli account del computer all'interno dell'unità organizzativa personalizzata.
3. Configurare ora la delega vincolata Kerberos basata su risorse usando il comando PowerShell seguente:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Gli account del computer per l'app Web e l'API Web devono essere creati in un'unità organizzativa in cui si hanno le autorizzazioni necessarie per configurare la delega vincolata Kerberos basata su risorse. Non è possibile configurare la delega vincolata Kerberos basata su risorse per un account del computer nel contenitore AAD DC Computers predefinito.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Configurare la delega vincolata Kerberos basata sulle risorse per un account utente in un dominio gestito
Si prenda ad esempio un'app Web in esecuzione come account del servizio "appsvc". L'app deve accedere alla risorsa, ovvero un'API Web in esecuzione come account del servizio "backendsvc", nel contesto degli utenti del dominio. Di seguito viene illustrato come impostare la delega vincolata Kerberos basata su risorse per questo scenario.

1. [Creare un'unità organizzativa personalizzata](create-ou.md). È possibile delegare le autorizzazioni per gestire questa unità organizzativa agli utenti appartenenti al dominio gestito.
2. Aggiungere la macchina virtuale che esegue l'API Web back-end/la risorsa al dominio gestito. Creare l'account del computer all'interno dell'unità organizzativa personalizzata.
3. Creare l'account del servizio, ad esempio appsvc, usato per eseguire l'app Web all'interno dell'unità organizzativa personalizzata.
4. Configurare ora la delega vincolata Kerberos basata su risorse usando il comando PowerShell seguente:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> L'account del computer per l'API Web back-end e l'account del servizio devono essere entrambi creati in un'unità organizzativa in cui si hanno le autorizzazioni necessarie per configurare la delega vincolata Kerberos basata su risorse. Non è possibile configurare la delega vincolata Kerberos basata su risorse per un account del computer nel contenitore AAD DC Computers predefinito o per gli account utente nel contenitore AAD DC Users predefinito. Di conseguenza, non è possibile usare account utente sincronizzati da Azure AD per configurare la delega vincolata Kerberos basata su risorse.
>

## <a name="related-content"></a>Contenuto correlato
* [Servizi di dominio Azure AD: introduzione](tutorial-create-instance.md)
* [Panoramica della delega vincolata Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
