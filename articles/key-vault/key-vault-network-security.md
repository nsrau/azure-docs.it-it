---
ms.assetid: ''
title: Configurare firewall Azure Key Vault e reti virtuali - Azure Key Vault
description: Istruzioni passo per passo per configurare reti virtuali e firewall di Azure Key Vault
services: key-vault
author: amitbapat
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: 4b3225dd25fee2859a36f98add51fcf612a45c83
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108892"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurare reti virtuali e firewall di Azure Key Vault

Questo articolo contiene istruzioni dettagliate per configurare reti virtuali e firewall di Azure Key Vault in modo da limitare l'accesso all'insieme di credenziali delle chiavi. Gli [endpoint del servizio di rete virtuale per Key Vault](key-vault-overview-vnet-service-endpoints.md) consentono di limitare l'accesso alla rete virtuale specificata e a un set di intervalli di indirizzi IPv4 (protocollo IP versione 4).

> [!IMPORTANT]
> Quando le regole del firewall sono operative, gli utenti possono eseguire le operazioni del [piano dati](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) Key Vault solo se le loro richieste hanno origine da reti virtuali o intervalli di indirizzi IPv4 consentiti. Questo vale anche per l'accesso a Key Vault dal portale di Azure. Benché gli utenti possano accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbero non essere in grado di elencare chiavi, segreti o certificati se il computer client in uso non è presente nell'elenco dei computer consentiti. Ciò influisce anche sul selettore dell'insieme di credenziali delle chiavi di altri servizi di Azure. Se le regole del firewall bloccano i computer client, gli utenti potrebbero essere in grado di visualizzare l'elenco degli insiemi di credenziali delle chiavi ma non di elencare le chiavi.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Di seguito viene illustrato come configurare firewall e reti virtuali di Key Vault usando il portale di Azure:

1. Passare all'insieme di credenziali delle chiavi che si vuole proteggere.
2. Selezionare **Firewall e reti virtuali**.
3. Selezionare **Reti selezionate** in **Consenti l'accesso da**.
4. Per aggiungere reti virtuali esistenti ai firewall e alle regole di rete virtuale, selezionare **+ Aggiungi reti virtuali esistenti**.
5. Nel nuovo pannello che si apre selezionare la sottoscrizione, le reti virtuali e le subnet a cui consentire l'accesso all'insieme di credenziali delle chiavi. Se per le reti virtuali e le subnet selezionate non sono abilitati endpoint di servizio, confermare di voler abilitare gli endpoint di servizio e selezionare **Abilita**. La modifica può richiedere fino a 15 minuti per diventare operativa.
6. In **Reti IP** aggiungere intervalli di indirizzi IPv4 digitando gli intervalli di indirizzi IPv4 in [notazione CIDR (Classless Inter-domain Routing)](https://tools.ietf.org/html/rfc4632) o i singoli indirizzi IP.
7. Selezionare **Salva**.

È anche possibile aggiungere nuove reti virtuali e subnet e poi abilitare gli endpoint di servizio per le reti virtuali e le subnet appena create selezionando **+ Aggiungi nuova rete virtuale**. Seguire quindi le istruzioni.

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure 

Di seguito viene illustrato come configurare firewall e reti virtuali di Key Vault usando l'interfaccia della riga di comando di Azure

1. [Installare l'interfaccia della riga di comando di Azure ](https://docs.microsoft.com/cli/azure/install-azure-cli) e [accedere](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Indicare le regole disponibili per la rete virtuale. Se non è stata impostata alcuna regola per questo insieme di credenziali delle chiavi, l'elenco sarà vuoto.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Abilitare un endpoint di servizio per Key Vault in una rete virtuale e una subnet esistenti.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Aggiungere una regola di rete per una rete virtuale e una subnet.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Aggiungere un intervallo di indirizzi IP da cui consentire il traffico.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Se questo insieme di credenziali delle chiavi deve essere accessibile da tutti i servizi attendibili, impostare `bypass` su `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Attivare le regole della rete impostando l'azione predefinita su `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Usare Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Di seguito viene illustrato come configurare firewall e reti virtuali di Key Vault usando PowerShell:

1. Installare la versione più aggiornata di [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) ed [effettuare l'accesso](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Indicare le regole disponibili per la rete virtuale. Se non è stata impostata alcuna regola per questo insieme di credenziali delle chiavi, l'elenco sarà vuoto.
   ```PowerShell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Abilitare l'endpoint di servizio per Key Vault in una rete virtuale e una subnet esistenti.
   ```PowerShell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Aggiungere una regola di rete per una rete virtuale e una subnet.
   ```PowerShell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Aggiungere un intervallo di indirizzi IP da cui consentire il traffico.
   ```PowerShell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Se questo insieme di credenziali delle chiavi deve essere accessibile da tutti i servizi attendibili, impostare `bypass` su `AzureServices`.
   ```PowerShell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Attivare le regole della rete impostando l'azione predefinita su `Deny`.
   ```PowerShell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Riferimenti

* Comandi dell'interfaccia della riga di comando di Azure: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Cmdlet di Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Passaggi successivi

* [Endpoint del servizio di rete virtuale per Key Vault](key-vault-overview-vnet-service-endpoints.md)
* [Proteggere l'insieme di credenziali delle chiavi](key-vault-secure-your-key-vault.md)
