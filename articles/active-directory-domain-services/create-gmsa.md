---
title: Account del servizio gestito del gruppo per Azure AD Domain Services | Microsoft Docs
description: Informazioni su come creare un account del servizio gestito del gruppo (gMSA) per l'uso con Azure Active Directory Domain Services domini gestiti
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: a943d2a8453cb727e9d01e35b12ca90d939ee5e8
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546319"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Creare un account del servizio gestito del gruppo (gMSA) in Azure AD Domain Services

Le applicazioni e i servizi spesso necessitano di un'identità per autenticarsi con altre risorse. È ad esempio possibile che un servizio Web debba eseguire l'autenticazione con un servizio di database. Se un'applicazione o un servizio dispone di più istanze, ad esempio un server farm Web, la creazione e la configurazione manuale delle identità per tali risorse richiede molto tempo.

È invece possibile creare un account del servizio gestito del gruppo (gMSA) nel dominio gestito Azure Active Directory Domain Services (Azure AD DS). Il sistema operativo Windows gestisce automaticamente le credenziali per un gMSA, semplificando la gestione di gruppi di risorse di grandi dimensioni.

Questo articolo illustra come creare un gMSA in un dominio gestito Azure AD DS usando Azure PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Azure AD DS.
    * Se necessario, completare l'esercitazione per [creare una macchina virtuale di gestione][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Panoramica degli account del servizio gestiti

Un account del servizio gestito autonomo (sMSA) è un account di dominio la cui password viene gestita automaticamente. Questo approccio semplifica la gestione del nome dell'entità servizio (SPN) e consente la gestione delegata ad altri amministratori. Non è necessario creare e ruotare manualmente le credenziali per l'account.

Un account del servizio gestito del gruppo (gMSA) offre la stessa semplificazione della gestione, ma per più server nel dominio. Un gMSA consente a tutte le istanze di un servizio ospitato in un server farm di usare la stessa entità servizio per il funzionamento dei protocolli di autenticazione reciproca. Quando un gMSA viene usato come entità servizio, il sistema operativo Windows gestisce nuovamente la password dell'account anziché affidarsi all'amministratore.

Per ulteriori informazioni, vedere [Panoramica degli account del servizio gestito del gruppo (gMSA)][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Uso degli account del servizio in Azure AD DS

Poiché i domini gestiti di Azure AD DS sono bloccati e gestiti da Microsoft, è necessario tenere presenti alcune considerazioni relative all'utilizzo degli account del servizio:

* Creare account del servizio in unità organizzative (OU) personalizzate nel dominio gestito.
    * Non è possibile creare un account del servizio nelle ou *Aaddc computers users* o *computer aaddc Computers* predefiniti.
    * Al contrario, [creare un'unità organizzativa personalizzata][create-custom-ou] nel dominio gestito di Azure AD DS, quindi creare gli account del servizio nell'unità organizzativa personalizzata.
* La chiave radice di servizi di distribuzione chiavi (KDS) è già creata.
    * La chiave radice KDS viene utilizzata per generare e recuperare le password per servizi gestiti. In Azure AD DS viene creata automaticamente la radice KDS.
    * Non si dispone dei privilegi necessari per crearne un altro o visualizzare la chiave radice KDS predefinita.

## <a name="create-a-gmsa"></a>Creare un gMSA

Per prima cosa, creare un'unità organizzativa personalizzata usando il cmdlet [New-ADOrganizationalUnit][New-AdOrganizationalUnit] . Per altre informazioni sulla creazione e la gestione di unità organizzative personalizzate, vedere [ou personalizzate in Azure AD DS][create-custom-ou].

> [!TIP]
> Per completare questi passaggi per creare un gMSA, [usare la VM di gestione][tutorial-create-management-vm]. Questa macchina virtuale di gestione deve avere già i cmdlet e la connessione necessari ad PowerShell per il dominio gestito.

Nell'esempio seguente viene creata una OU personalizzata denominata *myNewOU* nel dominio gestito Azure AD DS denominato *contoso.com*. Usare la propria unità organizzativa e il nome di dominio gestito:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=contoso,DC=COM"
```

A questo punto, creare un gMSA con il cmdlet [New-ADServiceAccount][New-ADServiceAccount] . Vengono definiti i parametri di esempio seguenti:

* **-Name** è impostato su *WebFarmSvc*
* **-Path** parametro specifica l'unità organizzativa personalizzata per il gMSA creato nel passaggio precedente.
* Le voci DNS e i nomi dell'entità servizio sono impostati per *WebFarmSvc.contoso.com*
* Le entità in *Contoso-server $* possono recuperare la password usando l'identità.

Specificare i nomi e i nomi di dominio.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.contoso.com `
    -Path "OU=MYNEWOU,DC=contoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.contoso.com/contoso.com, `
        http/WebFarmSvc.contoso.com/contoso, `
        http/WebFarmSvc/contoso.com, `
        http/WebFarmSvc/contoso `
    -PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

È ora possibile configurare le applicazioni e i servizi per l'uso di gMSA in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su servizi gestiti, vedere la pagina relativa all' [Introduzione agli account del servizio gestiti del gruppo][gmsa-start].

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
