---
title: Account del servizio gestito di gruppo per Servizi di dominio Azure AD Documenti Microsoft
description: Informazioni su come creare un account del servizio gestito di gruppo (gMSA) da usare con i domini gestiti di Servizi di dominio Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 5955f52cda73630f371a46f83ac0fb9a252b80e3
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655478"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Creare un account del servizio gestito di gruppo (gMSA) in Servizi di dominio Azure ADCreate a group managed service account (gMSA) in Azure AD Domain Services

Le applicazioni e i servizi spesso necessitano di un'identità per autenticarsi con altre risorse. Ad esempio, un servizio Web potrebbe essere necessario eseguire l'autenticazione con un servizio di database. Se un'applicazione o un servizio dispone di più istanze, ad esempio una server farm Web, la creazione e la configurazione manuale delle identità per tali risorse richiede molto tempo.

È invece possibile creare un account del servizio gestito di gruppo (gMSA) nel dominio gestito Servizi di dominio Azure Active Directory (Azure AD DS). Il sistema operativo Windows gestisce automaticamente le credenziali per un gMSA, semplificando la gestione di grandi gruppi di risorse.

Questo articolo illustra come creare un gMSA in un dominio gestito di Servizi di dominio Active Directory di Azure usando Azure PowerShell.This article shows you how to create a gMSA in an Azure AD DS managed domain using Azure PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessarie le risorse e i privilegi seguenti:To complete this article, you need the following resources and privileges:

* Una sottoscrizione di Azure attiva.
    * Se non si dispone di una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza][create-azure-ad-ds-instance]di Servizi di dominio Azure Active Directory.
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Servizi di dominio Active Directory di Azure.A Windows Server management VM that is joined to the Azure AD DS managed domain.
    * Se necessario, completare l'esercitazione per creare una macchina virtuale di [gestione.][tutorial-create-management-vm]

## <a name="managed-service-accounts-overview"></a>Panoramica degli account del servizio gestito

Un account del servizio gestito autonomo (sMSA) è un account di dominio la cui password viene gestita automaticamente. Questo approccio semplifica la gestione del nome dell'entità servizio (SPN) e consente la gestione delegata ad altri amministratori. Non è necessario creare e ruotare manualmente le credenziali per l'account.

Un account del servizio gestito di gruppo (gMSA) fornisce la stessa semplificazione di gestione, ma per più server nel dominio. Un account gMSA consente a tutte le istanze di un servizio ospitato in una server farm di utilizzare la stessa entità servizio per il funzionamento dei protocolli di autenticazione reciproca. Quando un gMSA viene usato come entità servizio, il sistema operativo Windows gestisce nuovamente la password dell'account anziché affidarsi all'amministratore.

Per ulteriori informazioni, vedere Panoramica degli account di servizio gestiti di [gruppo (gMSA).][gmsa-overview]

## <a name="using-service-accounts-in-azure-ad-ds"></a>Uso di account di servizio in Azure AD DSUsing service accounts in Azure AD DS

Poiché i domini gestiti di Servizi di dominio Active Directory di Azure sono bloccati e gestiti da Microsoft, esistono alcune considerazioni quando si usano gli account di servizio:As Azure AD DS managed domains are locked down and managed by Microsoft, there are some considerations when using service accounts:

* Creare account di servizio in unità organizzative personalizzate nel dominio gestito.
    * Non è possibile creare un account di servizio nelle applicazioni *AADDC* predefinite o nelle aree di gestione delle varie tà *dipendenti.*
    * Creare invece [un'unità organizzativa personalizzata][create-custom-ou] nel dominio gestito di Servizi di dominio Active Directory di Azure e quindi creare account di servizio in tale unità organizzativa personalizzata.
* La chiave radice di Key Distribution Services (KDS) è stata creata in precedenza.
    * La chiave radice KDS viene utilizzata per generare e recuperare le password per gMSA. In Azure AD DS viene creata automaticamente la radice KDS.In Azure AD DS, the KDS root is created for you.
    * Non si dispone dei privilegi per crearne un altro o visualizzare la chiave radice KDS predefinita.

## <a name="create-a-gmsa"></a>Creare un account del servizio gestito di gruppo

Creare innanzitutto un'unità organizzativa personalizzata utilizzando il cmdlet [New-ADOrganizationalUnit.][New-AdOrganizationalUnit] Per altre informazioni sulla creazione e la gestione di aree utente personalizzate, vedere [OSu personalizzate in Azure AD DS.][create-custom-ou]

> [!TIP]
> Per completare questi passaggi per creare un file GMSA, usare la macchina virtuale di [gestione.][tutorial-create-management-vm] Questa macchina virtuale di gestione deve già disporre dei cmdlet di AD PowerShell necessari e della connessione al dominio gestito.

Nell'esempio seguente viene creata un'unità organizzativa personalizzata denominata *myNewOU* nel dominio gestito di Servizi di dominio Active Directory di Azure denominato *aaddscontoso.com*. Utilizzare la propria unità organizzativa e il nome di dominio gestito:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Creare ora un gMSA utilizzando il cmdlet [New-ADServiceAccount.][New-ADServiceAccount] Vengono definiti i seguenti parametri di esempio:

* **-Name** è impostato su *WebFarmSvc*
* **-Path** parametro specifica l'unità organizzativa personalizzata per il gMSA creato nel passaggio precedente.
* Le voci DNS e i nomi delle entità servizio vengono impostati per *WebFarmSvc.aaddscontoso.com*
* Le entità in *AADDSCONTOSO-SERVER* sono autorizzate a recuperare la password che utilizza l'identità.

Specificare i propri nomi e nomi di dominio.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Le applicazioni e i servizi possono ora essere configurati per utilizzare il file GMSA in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui sistemi di gestione delle risorse di sistema, vedere Introduzione agli account del [servizio gestito di gruppo.][gmsa-start]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
