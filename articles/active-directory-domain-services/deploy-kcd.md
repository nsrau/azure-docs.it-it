---
title: Delega vincolata Kerberos per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come abilitare la delega vincolata Kerberos basata sulle risorse (delega vincolata Kerberos) in un Azure Active Directory Domain Services dominio gestito.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: iainfou
ms.openlocfilehash: 89bc690e5a8c8d24d7732dd4e12f70a9f1f368af
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842666"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Configurare la delega vincolata Kerberos (delega vincolata Kerberos) in Azure Active Directory Domain Services

Quando si eseguono le applicazioni, potrebbe essere necessario che tali applicazioni accedano alle risorse nel contesto di un altro utente. Active Directory Domain Services (AD DS) supporta un meccanismo denominato *delega Kerberos* che consente il caso d'uso. La delega *vincolata* Kerberos (delega vincolata Kerberos) si basa quindi su questo meccanismo per definire risorse specifiche a cui è possibile accedere nel contesto dell'utente. I domini gestiti Azure Active Directory Domain Services (Azure AD DS) sono bloccati in modo più sicuro negli ambienti di servizi di dominio Active Directory locali tradizionali, quindi è consigliabile usare un delega vincolata Kerberos più sicuro *basato sulle risorse* .

Questo articolo illustra come configurare la delega vincolata Kerberos di Resource-basd in un dominio gestito di Azure AD DS.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, sono necessarie le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Azure AD DS.
    * Se necessario, completare l'esercitazione per [creare una macchina virtuale Windows Server e aggiungerla a un dominio gestito e][create-join-windows-vm] quindi [installare gli strumenti di gestione di servizi di dominio Active Directory][tutorial-create-management-vm].
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Panoramica della delega vincolata Kerberos

La delega Kerberos consente a un account di rappresentare un altro account per accedere alle risorse. Ad esempio, un'applicazione Web che accede a un componente Web back-end può essere rappresentata come un account utente diverso quando effettua la connessione back-end. La delega Kerberos non è protetta perché non limita le risorse a cui l'account di rappresentazione può accedere.

La delega vincolata Kerberos (delega vincolata Kerberos) limita i servizi o le risorse che un server o un'applicazione specificata può connettere quando rappresenta un'altra identità. Per la delega vincolata Kerberos tradizionale sono necessari privilegi di amministratore di dominio per configurare un account di dominio per un servizio e l'account viene limitato per l'esecuzione in un singolo dominio. Il delega vincolata Kerberos tradizionale presenta anche alcuni problemi. Ad esempio, nei sistemi operativi precedenti, l'amministratore del servizio non aveva alcun modo utile per individuare i servizi front-end delegati ai servizi delle risorse di cui è proprietario. Qualsiasi servizio front-end che può delegare a un servizio risorse è un potenziale punto di attacco. Se un server che ospita un servizio front-end configurato per delegare i servizi risorse è stato compromesso, è possibile che anche i servizi risorse siano compromessi.

In un dominio gestito Azure AD DS, non si dispone dei privilegi di amministratore di dominio. Di conseguenza, il delega vincolata Kerberos tradizionale basato su account non può essere configurato in un dominio gestito di Azure AD DS. È invece possibile usare il delega vincolata Kerberos basato su risorse, che è anche più sicuro.

### <a name="resource-based-kcd"></a>Delega vincolata Kerberos basata su risorse

Windows Server 2012 e versioni successive offre agli amministratori dei servizi la possibilità di configurare la delega vincolata per il servizio. Questo modello è noto come delega vincolata Kerberos basato su risorse. Con questo approccio, l'amministratore del servizio back-end può consentire o negare ai servizi front-end specifici di usare delega vincolata Kerberos.

È possibile configurare la delega vincolata Kerberos basata sulle risorse usando PowerShell. Usare i cmdlet [set-ADComputer][Set-ADComputer] o [set-aduser][Set-ADUser] , a seconda che l'account di rappresentazione sia un account computer o un account utente o un account del servizio.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Configurare delega vincolata Kerberos basati sulle risorse per un account computer

In questo scenario si supponga di avere un'app Web in esecuzione nel computer denominato *Contoso-webapp.contoso.com*. L'app Web deve accedere a un'API Web in esecuzione nel computer denominato *Contoso-API.contoso.com* nel contesto degli utenti del dominio. Per configurare questo scenario, completare i passaggi seguenti:

1. [Creare un'unità organizzativa personalizzata](create-ou.md). È possibile delegare le autorizzazioni per gestire questa OU personalizzata agli utenti all'interno del dominio gestito Azure AD DS.
1. [Aggiungere un dominio alle macchine virtuali][create-join-windows-vm], sia quello che esegue l'app Web, che quello che esegue l'API Web, nel dominio gestito di Azure AD DS. Creare questi account computer nell'unità organizzativa personalizzata nel passaggio precedente.

    > [!NOTE]
    > Gli account computer per l'app Web e l'API Web devono trovarsi in un'unità organizzativa personalizzata in cui si hanno le autorizzazioni per configurare delega vincolata Kerberos basati sulle risorse. Non è possibile configurare delega vincolata Kerberos basato su risorse per un account computer nel contenitore dei *computer DC di AAD* incorporato.

1. Infine, configurare delega vincolata Kerberos basati sulle risorse usando il cmdlet di PowerShell [set-ADComputer][Set-ADComputer] . Eseguire i cmdlet seguenti dalla macchina virtuale di gestione aggiunta al dominio e connessi come account utente membro del gruppo di *amministratori di Azure ad DC* . Specificare i nomi dei computer in base alle esigenze:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
    Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Configurare delega vincolata Kerberos basati sulle risorse per un account utente

In questo scenario si supponga di avere un'app Web che viene eseguita come account del servizio denominato *appsvc*. L'app Web deve accedere a un'API Web che viene eseguita come account del servizio denominato *backendsvc* nel contesto degli utenti del dominio. Per configurare questo scenario, completare i passaggi seguenti:

1. [Creare un'unità organizzativa personalizzata](create-ou.md). È possibile delegare le autorizzazioni per gestire questa OU personalizzata agli utenti all'interno del dominio gestito Azure AD DS.
1. [Aggiungere un dominio alle macchine virtuali][create-join-windows-vm] che eseguono la risorsa/API Web back-end nel dominio gestito di Azure AD DS. Creare l'account del computer all'interno dell'unità organizzativa personalizzata.
1. Creare l'account del servizio, ad esempio appsvc, usato per eseguire l'app Web all'interno dell'unità organizzativa personalizzata.

    > [!NOTE]
    > Anche in questo caso, l'account computer per la VM dell'API Web e l'account del servizio per l'app Web devono trovarsi in un'unità organizzativa personalizzata in cui si hanno le autorizzazioni per configurare delega vincolata Kerberos basati sulle risorse. Non è possibile configurare il delega vincolata Kerberos basato sulle risorse per gli account nei contenitori di *computer DC AAD* o di *utenti di AAD DC* . Ciò significa anche che non è possibile usare gli account utente sincronizzati da Azure AD per configurare delega vincolata Kerberos basati sulle risorse. È necessario creare e utilizzare gli account del servizio creati in modo specifico in Azure AD DS.

1. Infine, configurare delega vincolata Kerberos basati sulle risorse usando il cmdlet di PowerShell [set-aduser][Set-ADUser] . Eseguire i cmdlet seguenti dalla macchina virtuale di gestione aggiunta al dominio e connessi come account utente membro del gruppo di *amministratori di Azure ad DC* . Specificare i nomi di servizio in base alle esigenze:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul funzionamento della delega in Active Directory Domain Services, vedere [Cenni preliminari sulla delega vincolata Kerberos][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
