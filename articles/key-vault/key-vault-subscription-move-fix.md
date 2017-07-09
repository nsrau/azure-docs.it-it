---
title: Modificare l'ID tenant per un insieme di credenziali delle chiavi dopo lo spostamento della sottoscrizione | Documentazione Microsoft
description: Informazioni su come modificare l'ID tenant per un insieme di credenziali delle chiavi dopo lo spostamento di una sottoscrizione a un altro tenant
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 4317cf84760289ca29d8d5a78e2adef99c4cedf2
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017


---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Modificare l'ID tenant per un insieme di credenziali delle chiavi dopo lo spostamento di una sottoscrizione
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>D: la sottoscrizione è stata spostata dal tenant A al tenant B. Come è possibile modificare l'ID tenant per l'insieme di credenziali delle chiavi esistente e impostare gli ACL corretti per le entità nel tenant B?
Quando si crea un nuovo insieme di credenziali delle chiavi in una sottoscrizione, questo viene automaticamente associato all'ID tenant di Azure Active Directory predefinito per la sottoscrizione. All'ID tenant vengono associate anche tutte le voci dei criteri di accesso. Quando si sposta la sottoscrizione di Azure dal tenant A al tenant B, gli insiemi di credenziali delle chiavi esistenti non sono accessibili dalle entità, ossia utenti e applicazioni, nel tenant B. Per risolvere questo problema è necessario:

* Modificare in tenant B l'ID tenant associato a tutti gli insiemi di credenziali delle chiavi esistenti nella sottoscrizione.
* Rimuovere tutte le voci dei criteri di accesso esistenti.
* Aggiungere nuove voci dei criteri di accesso associate al tenant B.

Se ad esempio è presente un insieme di credenziali delle chiavi 'myvault' in una sottoscrizione che è stata spostata dal tenant A al tenant B, sarà possibile modificare l'ID tenant per questo insieme di credenziali delle chiavi e rimuovere i criteri di accesso precedenti come segue.

<pre>
$Select-AzureRmSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.Id
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Prima dello spostamento, questo insieme di credenziali si trovava nel tenant A. Il valore originale di **$vault.Properties.TenantId** è quindi il tenant A, mentre **(Get-AzureRmContext).Tenant.TenantId** è il tenant B.

Ora che l'insieme di credenziali è associato all'ID tenant corretto e le voci dei criteri di accesso precedenti sono state rimosse, impostare le nuove voci dei criteri di accesso con [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## <a name="next-steps"></a>Passaggi successivi
In caso di domande sull'insieme di credenziali delle chiavi di Azure, visitare i [forum sull'insieme di credenziali delle chiavi di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).


