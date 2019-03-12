---
title: Aggiornare il proprietario di sottoscrizione utente di Azure Stack | Microsoft Docs
description: Modificare il proprietario di fatturazione per le sottoscrizioni utente di Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 02c5d063246844d5ed8945aba38aaada87691627
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763198"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Modificare il proprietario per una sottoscrizione utente di Azure Stack

Operatori di Azure Stack è possono usare PowerShell per modificare il proprietario di fatturazione di una sottoscrizione utente. Uno dei motivi per modificare il proprietario, ad esempio, consiste nella sostituzione di un utente lascia l'organizzazione.

Esistono due tipi di *proprietari* che vengono assegnati a una sottoscrizione:

- **La fatturazione proprietario**: Per impostazione predefinita, il proprietario di fatturazione è l'account utente che viene ottenuta la sottoscrizione da un'offerta e quindi proprietari della relazione di fatturazione per la sottoscrizione. Questo account è anche un amministratore della sottoscrizione. Solo un account utente può avere questa designazione di una sottoscrizione. Un proprietario di fatturazione è spesso un lead del team o organizzazione.

  È possibile usare il cmdlet di PowerShell [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) per modificare il proprietario di fatturazione.  

- **I proprietari aggiunti tramite i ruoli RBAC** – possono concedere ad altri utenti il **proprietario** ruolo utilizzando la [Role Based Access Control](azure-stack-manage-permissions.md) system (RBAC). Un numero qualsiasi di altri account utente può essere aggiunti come proprietari per completare il proprietario di fatturazione. Altri proprietari sono anche amministratori della sottoscrizione e con tutti i privilegi per la sottoscrizione, ad eccezione dell'autorizzazione per eliminare il proprietario di fatturazione.

  È possibile usare PowerShell per gestire altri proprietari. Per altre informazioni, vedere [questo articolo](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Modificare il proprietario di fatturazione

Eseguire lo script seguente per modificare il proprietario di fatturazione di una sottoscrizione utente. Il computer in uso per eseguire lo script deve connettersi ad Azure Stack ed eseguire il modulo di PowerShell per Azure Stack 1.3.0 o versioni successive. Per altre informazioni, vedere [installare PowerShell per Azure Stack](azure-stack-powershell-install.md).

>[!NOTE]
>In multi-tenant Azure Stack, il nuovo proprietario deve essere nella stessa directory come proprietario esistente. Prima di poter fornire la proprietà della sottoscrizione a un utente che si trova in un'altra directory, è necessario innanzitutto [invitare l'utente come guest alla directory](../active-directory/b2b/add-users-administrator.md).

Sostituire i valori seguenti nello script prima dell'esecuzione:

- **$ArmEndpoint**: L'endpoint di Resource Manager per l'ambiente.
- **$TenantId**: L'ID Tenant.
- **$SubscriptionId**: ID sottoscrizione personale.
- **$OwnerUpn**: Un account, ad esempio **user@example.com**, per aggiungere il nuovo proprietario di fatturazione.

```powershell
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>Passaggi successivi

- [Gestire controllo degli accessi in base al ruolo](azure-stack-manage-permissions.md)
