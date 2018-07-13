---
title: Aggiornare il proprietario di sottoscrizione utente di Azure Stack | Microsoft Docs
description: Modificare il proprietario di fatturazione per le sottoscrizioni utente di Azure STack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 06/12/2018
ms.author: brenduns
ms.reviewer: shnatara
ms.openlocfilehash: de8610944e11d8cf106ac484a0c6634c8661b5a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009490"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Modificare il proprietario per una sottoscrizione utente di Azure Stack

Operatori di Azure Stack è possono usare PowerShell per modificare il proprietario di fatturazione di una sottoscrizione utente. Uno dei motivi per modificare il proprietario consiste nella sostituzione di un utente lascia l'organizzazione.   

Esistono due tipi di *proprietari* che vengono assegnati a una sottoscrizione:

- **La fatturazione proprietario** – per impostazione predefinita, il proprietario di fatturazione è l'account utente che viene ottenuta la sottoscrizione da un'offerta e quindi proprietari della relazione di fatturazione per la sottoscrizione. Questo account è anche un amministratore della sottoscrizione.  Solo un account utente può avere questa designazione di una sottoscrizione. Un proprietario di fatturazione è spesso un lead del team o organizzazione. 

  Si usa il cmdlet di PowerShell la **Set-AzsUserSubscription** per modificare il proprietario di fatturazione.  

- **I proprietari aggiunti tramite i ruoli RBAC** : utenti aggiuntivi è possibile concedere il ruolo di proprietario usando la [Role Based Access Control](azure-stack-manage-permissions.md) system (RBAC).  Un numero qualsiasi di altri account utente può essere aggiunti come proprietari per completare il proprietario di fatturazione. Altri proprietari sono anche amministratori della sottoscrizione e con tutti i privilegi per la sottoscrizione, ad eccezione delle autorizzazioni per eliminare il proprietario di fatturazione. 

  È possibile usare PowerShell per gestire i proprietari aggiuntivi, vedere [Azure PowerShell per gestire controllo degli accessi in base al ruolo]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="change-the-billing-owner"></a>Modificare il proprietario di fatturazione
Eseguire lo script seguente per modificare il proprietario di fatturazione di una sottoscrizione utente.  Il computer in uso per eseguire lo script deve connettersi ad Azure Stack ed eseguire il modulo di PowerShell per Azure Stack 1.3.0 o versioni successive. Per altre informazioni, vedere [installare PowerShell per Azure Stack](azure-stack-powershell-install.md). 

> [!Note]  
>  In uno Stack di Azure multi-tenant, il nuovo proprietario deve essere nella stessa directory come proprietario esistente. Prima di poter fornire la proprietà della sottoscrizione a un utente che si trova in un'altra directory, è necessario innanzitutto [invitare l'utente come guest alla directory](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). 


Sostituire i valori seguenti nello script prima dell'esecuzione: 
 
- **$ArmEndpoint** : specificare l'endpoint di Resource Manager per l'ambiente.  
- **$TenantId** -specificare l'ID Tenant. 
- **$SubscriptionId** : specificare l'ID sottoscrizione.
- **$OwnerUpn** -specificare un account *user@example.com* da aggiungere come nuovo proprietario di fatturazione.  


```PowerSHell   
# Setup Azure Stack Admin Environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select Admin Subscriptionr
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change User Subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```


## <a name="next-steps"></a>Passaggi successivi
[Gestire controllo degli accessi in base al ruolo](azure-stack-manage-permissions.md)
