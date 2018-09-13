---
ms.assetid: ''
title: Configurare reti virtuali e firewall di Azure Key Vault
description: Istruzioni passo per passo per configurare reti virtuali e firewall di Azure Key Vault
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: c58fc56742c0a11771bdd84e4195e6f476622a3b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346524"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurare reti virtuali e firewall di Azure Key Vault

Questa guida presenta le istruzioni passo per passo per configurare reti virtuali e firewall di Key Vault in modo da limitare l'accesso all'insieme di credenziali delle chiavi. Gli [endpoint del servizio di rete virtuale per Key Vault](key-vault-overview-vnet-service-endpoints.md) consentono di limitare l'accesso all'insieme di credenziali delle chiavi alla rete virtuale specificata e/o a un insieme di intervalli di indirizzi IPv4 (protocollo IP versione 4).

> [!IMPORTANT]
> Quando le regole del firewall e di rete virtuale sono operative, tutte le operazioni del [piano dati](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) Key Vault possono essere eseguite SOLO se le richieste del chiamante hanno origine da reti virtuali o intervalli di indirizzi IPv4 consentiti. Questo vale anche per l'accesso all'insieme di credenziali delle chiavi dal portale di Azure. Anche se un utente può accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbe non riuscire a elencare le chiavi/segreti/certificati se il computer client non è presente nell'elenco dei computer consentiti. Ciò influisce inoltre sul "selettore dell'insieme di credenziali delle chiavi" di altri servizi di Azure. Se le regole del firewall bloccano i computer client, gli utenti potrebbero riuscire a visualizzare l'elenco degli insiemi di credenziali delle chiavi ma non riuscire a elencare le chiavi.

## <a name="azure-portal"></a>Portale di Azure

1. Passare all'insieme di credenziali delle chiavi che si vuole proteggere.
2. Care clic su **Firewall e reti virtuali**.
3. Fare clic su **Reti selezionate** sotto **Consenti l'accesso da**.
4. Per aggiungere reti virtuali esistenti ai firewall e alle regole di rete virtuale, fare clic su **+ Aggiungi reti virtuali esistenti**.
5. Nel nuovo pannello che appare selezionare la sottoscrizione, le reti virtuali e le subnet a cui consentire l'accesso all'insieme di credenziali delle chiavi. Se le reti virtuali e le subnet selezionate non hanno endpoint di servizio abilitati, compare il messaggio "The following networks don't have service endpoints enavled..." (Le reti seguenti non hanno endpoint di servizio abilitati...). Fare clic su **Abilita** dopo aver confermato che si vuole abilitare gli endpoint di servizio per le reti virtuali e le subnet elencate. La modifica potrebbe richiedere fino a 15 minuti per diventare operativa.
6. È anche possibile aggiungere nuove reti virtuali e subnet e poi abilitare gli endpoint di servizio per le reti virtuali e le subnet appena create facendo clic **+ Aggiungi nuova rete virtuale** e seguendo le istruzioni visualizzate.
7. Sotto **Reti IP** è possibile aggiungere intervalli di indirizzi IPv4 digitando gli intervalli di indirizzi IPv4 in [notazione CIDR (Classless Inter-domain Routing)](https://tools.ietf.org/html/rfc4632) o i singoli indirizzi IP.
8. Fare clic su **Save**.

## <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

1. [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) ed [effettuare l'accesso](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Elencare le regole di rete virtuale disponibili: se non sono state impostate regole per questo insieme di credenziali delle chiavi, l'elenco sarà vuoto.
```azurecli
az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
```

3. Abilitare l'endpoint di servizio per Key Vault in una rete virtuale e subnet esistenti
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
```

4. Aggiungere una regola di rete per una rete virtuale di Microsoft Azure e una subnet
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
```

5. Aggiungere un intervallo di indirizzi IP da cui consentire il traffico
```azurecli
az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
```

6. Se questo insieme di credenziali delle chiavi deve essere accessibile da tutti i servizi attendibili, impostare "ignora" su AzureServices
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. Ora c'è l'ultimo e importante passaggio di attivare le regole di rete impostando l'azione predefinita su "Nega"
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Azure PowerShell

1. Installare la versione più aggiornata di [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ed [effettuare l'accesso](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Elencare le regole di rete virtuale disponibili: se non sono state impostate regole per questo insieme di credenziali delle chiavi, l'elenco sarà vuoto.
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. Abilitare l'endpoint di servizio per Key Vault in una rete virtuale e subnet esistenti
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. Aggiungere una regola di rete per una rete virtuale di Microsoft Azure e una subnet
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. Aggiungere un intervallo di indirizzi IP da cui consentire il traffico
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. Se questo insieme di credenziali delle chiavi deve essere accessibile da tutti i servizi attendibili, impostare "ignora" su AzureServices
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. Ora c'è l'ultimo e importante passaggio di attivare le regole di rete impostando l'azione predefinita su "Nega"
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>Riferimenti

* Comandi dell'interfaccia della riga di comando di Azure 2.0 - [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Cmdlet di Azure PowerShell - [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [Add-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [Update-AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Passaggi successivi

* [Endpoint del servizio di rete virtuale per Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Proteggere l'insieme di credenziali delle chiavi](key-vault-secure-your-key-vault.md)