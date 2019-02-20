---
title: Modificare l'ID tenant dell'insieme di credenziali delle chiavi dopo lo spostamento di una sottoscrizione - Azure Key Vault | Microsoft Docs
description: Informazioni su come modificare l'ID tenant per un insieme di credenziali delle chiavi dopo lo spostamento di una sottoscrizione a un altro tenant
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: a83bff5a494ce338f43b6e967df5fe67cacfab01
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112190"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Modificare l'ID tenant per un insieme di credenziali delle chiavi dopo lo spostamento di una sottoscrizione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>D: La sottoscrizione è stata spostata dal tenant A al tenant B. Come è possibile modificare l'ID tenant per l'insieme di credenziali delle chiavi esistente e impostare gli ACL corretti per le entità nel tenant B?

Quando si crea un nuovo insieme di credenziali delle chiavi in una sottoscrizione, questo viene automaticamente associato all'ID tenant di Azure Active Directory predefinito per la sottoscrizione. All'ID tenant vengono associate anche tutte le voci dei criteri di accesso. Quando si sposta la sottoscrizione di Azure dal tenant A al tenant B, gli insiemi di credenziali delle chiavi esistenti non sono accessibili dalle entità, ossia utenti e applicazioni, nel tenant B. Per risolvere questo problema è necessario:

* Modificare in tenant B l'ID tenant associato a tutti gli insiemi di credenziali delle chiavi esistenti nella sottoscrizione.
* Rimuovere tutte le voci dei criteri di accesso esistenti.
* Aggiungere nuove voci dei criteri di accesso associate al tenant B.

Se ad esempio è presente un insieme di credenziali delle chiavi 'myvault' in una sottoscrizione che è stata spostata dal tenant A al tenant B, sarà possibile modificare l'ID tenant per questo insieme di credenziali delle chiavi e rimuovere i criteri di accesso precedenti come segue.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Prima dello spostamento, questo insieme di credenziali si trovava nel tenant A. Il valore originale di **$vault.Properties.TenantId** è quindi il tenant A, mentre per **(Get-AzContext).Tenant.TenantId** è il tenant B.

Ora che l'insieme di credenziali è associato all'ID tenant corretto e le voci dei criteri di accesso precedenti sono state rimosse, impostare le nuove voci dei criteri di accesso con [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy).

## <a name="next-steps"></a>Passaggi successivi

In caso di domande sull'insieme di credenziali delle chiavi di Azure, visitare i [forum sull'insieme di credenziali delle chiavi di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
