---
title: Delega vincolata Kerberos per Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su come abilitare la delega vincolata Kerberos basata sulle risorse in un dominio gestito di Servizi di dominio Azure Active Directory.Learn how to enable resource-based Kerberos constrained delegation (KCD) in an Azure Active Directory Domain Services managed domain.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 216fdeca9893f4e290474512617f13382d22890f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614021"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Configurare la delega vincolata Kerberos in Servizi di dominio Azure Active DirectoryConfigure Kerberos constrained delegation (KCD) in Azure Active Directory Domain Services

Durante l'esecuzione delle applicazioni, potrebbe essere necessario che tali applicazioni accedano alle risorse nel contesto di un utente diverso. Servizi di dominio Active Directory (AD DS) supporta un meccanismo denominato *delega Kerberos* che consente questo caso d'uso. La delega *vincolata* Kerberos (KCD) si basa quindi su questo meccanismo per definire risorse specifiche a cui è possibile accedere nel contesto dell'utente. I domini gestiti di Servizi di dominio Azure Active Directory (Azure AD DS) sono bloccati in modo più sicuro rispetto agli ambienti di Servizi di dominio Active Directory locali tradizionali, pertanto usare un KCD più sicuro *basato sulle risorse.*

Questo articolo illustra come configurare la delega vincolata Kerberos basata sulle risorse in un dominio gestito di Servizi di dominio Active Directory di Azure.This article shows you how to configure resource-based Kerberos constrained delegation in an Azure AD DS managed domain.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, sono necessarie le risorse seguenti:To complete this article, you need the following resources:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Servizi di dominio Active Directory di Azure.A Windows Server management VM that is joined to the Azure AD DS managed domain.
    * Se necessario, completare l'esercitazione per [creare una macchina virtuale Windows Server e aggiungerla a un dominio gestito,][create-join-windows-vm] quindi installare gli strumenti di gestione di Servizi di dominio [Active][tutorial-create-management-vm]Directory.
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Panoramica della delega vincolata Kerberos

La delega Kerberos consente a un account di rappresentare un altro account per accedere alle risorse. Ad esempio, un'applicazione Web che accede a un componente Web back-end può rappresentare come un account utente diverso quando effettua la connessione back-end. La delega Kerberos non è sicura in quanto non limita le risorse a cui l'account di rappresentazione può accedere.

La delega vincolata Kerberos (KCD) limita i servizi o le risorse che un server o un'applicazione specificata può connettere quando rappresenta un'altra identità. Il KCD tradizionale richiede privilegi di amministratore di dominio per configurare un account di dominio per un servizio e limita l'esecuzione dell'account in un singolo dominio.

KCD tradizionale ha anche alcuni problemi. Ad esempio, nei sistemi operativi precedenti, l'amministratore del servizio non aveva alcun modo utile per sapere quali servizi front-end delegavano ai servizi delle risorse di cui possedevano. Qualsiasi servizio front-end che potrebbe delegare a un servizio di risorse era un potenziale punto di attacco. Se un server che ospitava un servizio front-end configurato per la delega ai servizi delle risorse è stato compromesso, anche i servizi delle risorse potrebbero essere compromessi.

In un dominio gestito di Servizi di dominio Active Directory di Azure non si dispone dei privilegi di amministratore di dominio. Di conseguenza, non è possibile configurare un KCD tradizionale basato su account in un dominio gestito di Servizi di dominio Active Directory di Azure.As a result, traditional account-based KCD can't be configured in an Azure AD DS a managed domain. KCD basato sulle risorse può invece essere utilizzato, che è anche più sicuro.

### <a name="resource-based-kcd"></a>Delega vincolata Kerberos basata su risorse

Windows Server 2012 e versioni successive offre agli amministratori del servizio la possibilità di configurare la delega vincolata per il servizio. Questo modello è noto come delega vincolata Kerberos basata su risorse. Con questo approccio, l'amministratore del servizio back-end può consentire o negare a servizi front-end specifici di utilizzare KCD.

È possibile configurare la delega vincolata Kerberos basata sulle risorse usando PowerShell. Utilizzare i cmdlet [Set-ADComputer][Set-ADComputer] o [Set-ADUser,][Set-ADUser] a seconda che l'account di rappresentazione sia un account computer o un account utente/account di servizio.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Configurare KCD basato sulle risorse per un account computer

In questo scenario, si supponga di disporre di un'app Web in esecuzione nel computer denominato *contoso-webapp.aaddscontoso.com*. L'app Web deve accedere a un'API Web in esecuzione nel computer denominato *contoso-api.aaddscontoso.com* nel contesto degli utenti di dominio. Completare i passaggi seguenti per configurare questo scenario:Complete the following steps to configure this scenario:

1. [Creare un'unità organizzativa personalizzata](create-ou.md). È possibile delegare le autorizzazioni per gestire questa unità organizzativa personalizzata agli utenti all'interno del dominio gestito di Servizi di dominio Active Directory di Azure.You can delegate permissions to manage this custom OU to users within the Azure AD DS managed domain.
1. [Aggiungere al][create-join-windows-vm]dominio gestito Servizi di dominio delle macchine virtuali sia quella che esegue l'app Web che quella che esegue l'API Web. Creare questi account computer nell'unità organizzativa personalizzata del passaggio precedente.

    > [!NOTE]
    > Gli account computer per l'app Web e l'API Web devono trovarsi in un'unità organizzativa personalizzata in cui si dispone delle autorizzazioni per configurare kCD basato sulle risorse. Non è possibile configurare KCD basato sulle risorse per un account computer nel contenitore predefinito *AAD DC Computers.*

1. Infine, configurare KCD basato sulle risorse utilizzando il cmdlet [PowerShell Set-ADComputer.][Set-ADComputer] Dalla macchina virtuale di gestione aggiunta al dominio ed è stato eseguito l'accesso come account utente membro del gruppo di amministratori del controller di dominio di *Azure AD,* eseguire i cmdlet seguenti. Specificare i nomi dei computer in base alle esigenze:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Configurare kCD basata sulle risorse per un account utenteConfigure resource-based KCD for a user account

In questo scenario, si supponga di disporre di un'app Web che viene eseguita come account di servizio denominato *appsvc*. L'app Web deve accedere a un'API Web che viene eseguita come account di servizio denominato *backendsvc* nel contesto degli utenti di dominio. Completare i passaggi seguenti per configurare questo scenario:Complete the following steps to configure this scenario:

1. [Creare un'unità organizzativa personalizzata](create-ou.md). È possibile delegare le autorizzazioni per gestire questa unità organizzativa personalizzata agli utenti all'interno del dominio gestito di Servizi di dominio Active Directory di Azure.You can delegate permissions to manage this custom OU to users within the Azure AD DS managed domain.
1. [Aggiunta di un dominio alle macchine virtuali][create-join-windows-vm] che eseguono l'API/risorsa Web back-end al dominio gestito di Servizi di dominio Active Directory di Azure. Creare l'account del computer all'interno dell'unità organizzativa personalizzata.
1. Creare l'account del servizio, ad esempio appsvc, usato per eseguire l'app Web all'interno dell'unità organizzativa personalizzata.

    > [!NOTE]
    > Anche in questo caso, l'account computer per la macchina virtuale dell'API Web e l'account del servizio per l'app Web devono trovarsi in un'unità organizzativa personalizzata in cui si dispone delle autorizzazioni per configurare KCD basato sulle risorse. Non è possibile configurare KCD basato sulle risorse per gli account nei contenitori a sistema di *computer dc AAD* incorporati o *utenti del controller di dominio AAD.* Ciò significa anche che non è possibile usare gli account utente sincronizzati da Azure AD per configurare KCD basato sulle risorse. È necessario creare e usare account di servizio creati in modo specifico in Servizi di dominio Active Directory di Azure.You must create and use service accounts specifically created in Azure AD DS.

1. Infine, configurare KCD basato sulle risorse utilizzando il cmdlet [PowerShell Set-ADUser.][Set-ADUser] Dalla macchina virtuale di gestione aggiunta al dominio ed è stato eseguito l'accesso come account utente membro del gruppo di amministratori del controller di dominio di *Azure AD,* eseguire i cmdlet seguenti. Specificare i propri nomi di servizio in base alle esigenze:Provide your own service names as needed:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul funzionamento della delega in Servizi di dominio Active Directory, vedere [Panoramica della delega vincolata Kerberos][kcd-technet].

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
