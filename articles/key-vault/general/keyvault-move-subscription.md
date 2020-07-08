---
title: Azure Key Vault lo stato di trasferimento di un insieme di credenziali a una sottoscrizione diversa | Microsoft Docs
description: Linee guida per lo trasferimento di un insieme di credenziali delle chiavi in una sottoscrizione diversa.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: 6fe25efe8f38772162c916c8d0b965267d457c0c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84488818"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Trasferimento di un Azure Key Vault a un'altra sottoscrizione

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica

**Se si trasferisce un insieme di credenziali delle chiavi in un'altra sottoscrizione, si verificherà una modifica sostanziale nell'ambiente**

Assicurarsi di comprendere l'effetto di questa modifica e seguire attentamente le istruzioni contenute in questo articolo prima di decidere di spostare l'insieme di credenziali delle chiavi in una nuova sottoscrizione.

Quando si crea un insieme di credenziali delle chiavi, questo viene automaticamente associato all'ID tenant predefinito Azure Active Directory per la sottoscrizione in cui viene creato. All'ID tenant vengono associate anche tutte le voci dei criteri di accesso. Se si sposta la sottoscrizione di Azure dal tenant A al tenant B, gli insiemi di credenziali delle chiavi esistenti saranno inaccessibili dalle entità servizio (utenti e applicazioni) nel tenant B. Per risolvere il problema, è necessario:

* Modificare l'ID tenant associato a tutti gli insiemi di credenziali delle chiavi esistenti nella sottoscrizione impostandolo sul tenant B.
* Rimuovere tutte le voci dei criteri di accesso esistenti.
* Aggiungere nuove voci dei criteri di accesso associate al tenant B.

## <a name="limitations"></a>Limitazioni

Alcune entità servizio (utenti e applicazioni) sono associate a un tenant specifico. Se si sposta l'insieme di credenziali delle chiavi in una sottoscrizione in un altro tenant, è probabile che non sia possibile ripristinare l'accesso a un'entità servizio specifica. Verificare che tutte le entità servizio essenziali esistano nel tenant in cui si sta migrando l'insieme di credenziali delle chiavi.

## <a name="design-considerations"></a>Considerazioni sulla progettazione

È possibile che l'organizzazione abbia implementato criteri di Azure con l'imposizione o le esclusioni a livello di sottoscrizione. Nella sottoscrizione in cui è attualmente presente l'insieme di credenziali delle chiavi e la sottoscrizione in cui si sta migrando l'insieme di credenziali delle chiavi può essere presente un set di assegnazioni di criteri diverso. Un conflitto nei requisiti dei criteri può causare l'interruzione delle applicazioni.

### <a name="example"></a>Esempio

Si dispone di un'applicazione connessa a Key Vault che crea certificati validi per due anni. La sottoscrizione in cui si tenta di spostare l'insieme di credenziali delle chiavi dispone di un'assegnazione di criteri che blocca la creazione di certificati validi per più di un anno. Dopo aver spostato l'insieme di credenziali delle chiavi nella nuova sottoscrizione, l'operazione di creazione di un certificato valido per due anni verrà bloccata da un'assegnazione di criteri di Azure.

### <a name="solution"></a>Soluzione

Assicurarsi di passare alla pagina Criteri di Azure nella portale di Azure ed esaminare le assegnazioni dei criteri per la sottoscrizione corrente, nonché la sottoscrizione a cui si sta effettuando il passaggio e assicurarsi che non ci siano mancate corrispondenze.

## <a name="prerequisites"></a>Prerequisiti

* Accesso a livello di collaboratore o superiore alla sottoscrizione corrente in cui è presente l'insieme di credenziali delle chiavi.
* Accesso a livello di collaboratore o superiore alla sottoscrizione in cui si vuole spostare l'insieme di credenziali delle chiavi.
* Un gruppo di risorse nella nuova sottoscrizione.

## <a name="procedure"></a>Procedura

### <a name="initial-steps-moving-key-vault"></a>Passaggi iniziali (trasferimento Key Vault)

1. Accedere al Portale di Azure
2. Passare a Key Vault
3. Fare clic sulla scheda "panoramica"
4. Selezionare il pulsante "Sposta"
5. Selezionare "sposta in un'altra sottoscrizione" dalle opzioni a discesa
6. Selezionare il gruppo di risorse in cui si vuole spostare l'insieme di credenziali delle chiavi
7. Confermare l'avviso relativo allo scambio di risorse
8. Scegliere "OK"

### <a name="additional-steps-post-move"></a>Passaggi aggiuntivi (post-spostamento)

Ora che l'insieme di credenziali delle chiavi è stato spostato nella nuova sottoscrizione, è necessario aggiornare l'ID tenant e rimuovere i criteri di accesso precedenti. Ecco le esercitazioni per questi passaggi in PowerShell e nell'interfaccia della riga di comando di Azure.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Ora che l'insieme di credenziali è associato all'ID tenant corretto e che le voci dei criteri di accesso precedenti sono state rimosse, impostare le nuove voci dei criteri di accesso con il cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) di Azure PowerShell o il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) dell'interfaccia della riga di comando di Azure.

Se si usa un'identità gestita per le risorse di Azure, sarà necessario aggiornarla con il nuovo tenant di Azure AD. Per altre informazioni sulle identità gestite, vedere [Eseguire l'autenticazione a Key Vault con un'identità gestita](managed-identity.md).

Se si usa l'identità del servizio gestito, sarà anche necessario aggiornare l'identità del servizio gestita perché l'identità precedente non risiederà più nel tenant AAD corretto.


