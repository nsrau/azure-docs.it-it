---
title: Configurare firewall Azure Key Vault e reti virtuali - Azure Key Vault
description: Istruzioni passo per passo per configurare reti virtuali e firewall di Azure Key Vault
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: c375defe5fd8356d64879a65d6f09f40ea30271d
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042474"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Configurare reti virtuali e firewall di Azure Key Vault

Questo articolo fornisce indicazioni per configurare il firewall di Azure Key Vault. Questo documento illustra in dettaglio le diverse configurazioni del firewall di Key Vault e include le istruzioni dettagliate su come configurare Azure Key Vault per l'uso con altre applicazioni e altri servizi di Azure.

## <a name="firewall-settings"></a>Impostazioni del firewall

Questa sezione descrive i vari modi in cui è possibile configurare il firewall di Azure Key Vault.

### <a name="key-vault-firewall-disabled-default"></a>Firewall Key Vault disabilitato (impostazione predefinita)

Per impostazione predefinita, quando si crea un nuovo insieme di credenziali delle chiavi, il firewall di Azure Key Vault è disabilitato. Tutte le applicazioni e i servizi di Azure possono accedere e inviare richieste all'insieme di credenziali delle chiavi. Si noti che questa configurazione non significa che qualsiasi utente sarà in grado di eseguire operazioni nell'insieme di credenziali delle chiavi. L'insieme di credenziali delle chiavi applica comunque restrizioni su segreti, chiavi e certificati archiviati al suo interno richiedendo l'autenticazione di Azure Active Directory e le autorizzazioni dei criteri di accesso. Per capire in maggior dettaglio l'autenticazione dell'insieme di credenziali delle chiavi, vedere il documento di informazioni di base [qui](https://docs.microsoft.com/azure/key-vault/general/authentication-fundamentals).

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Firewall di Key Vault abilitato (solo servizi attendibili)

Quando si abilita il firewall di Key Vault, viene offerta l'opzione 'Consentire ai servizi Microsoft attendibili di ignorare il firewall?' L'elenco dei servizi attendibili non copre ogni singolo servizio di Azure. Ad esempio, Azure DevOps non è presente nell'elenco dei servizi attendibili. **Ciò non implica che i servizi non inclusi nell'elenco non siano considerati attendibili o sicuri.** L'elenco di servizi attendibili include i servizi per cui Microsoft controlla tutto il codice eseguito al loro interno. Poiché gli utenti possono scrivere codice personalizzato nei servizi di Azure, ad esempio Azure DevOps, Microsoft non offre la possibilità di creare un'approvazione generale per il servizio. Inoltre, il semplice fatto che un servizio sia incluso nell'elenco di servizi attendibili non significa che sia consentito per tutti gli scenari.

Per determinare se un servizio che si prova a usare è incluso nell'elenco di servizi attendibili, vedere [questo documento](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services).

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Firewall di Key Vault abilitato (indirizzi e intervalli IPv4 - IP statici)

Per autorizzare un particolare servizio ad accedere all'insieme di credenziali delle chiavi attraverso il firewall di Key Vault, è possibile aggiungere il relativo indirizzo IP all'elenco di indirizzi consentiti del firewall. Questa configurazione è ottimale per i servizi che usano indirizzi IP statici o intervalli noti.

Per consentire un indirizzo IP o un intervallo di una risorsa di Azure, ad esempio un'app Web o App per la logica, seguire questa procedura.

1. Accedere al Portale di Azure
1. Selezionare la risorsa (istanza specifica del servizio)
1. Fare clic sul pannello 'Proprietà' in 'Impostazioni'
1. Cercare il campo "Indirizzo IP".
1. Copiare questo valore o intervallo e immetterlo nell'elenco di indirizzi consentiti del firewall di Key Vault.

Per consentire un intero servizio di Azure attraverso il firewall di Key Vault, usare l'elenco di indirizzi IP di data center documentati pubblicamente per Azure disponibile [qui](https://www.microsoft.com/download/details.aspx?id=41653). Trovare gli indirizzi IP associati al servizio nell'area desiderata e aggiungerli al firewall di Key Vault seguendo la procedura precedente.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Firewall di Key Vault abilitato (reti virtuali - IP dinamici)

Se si prova ad autorizzare una risorsa di Azure, ad esempio una macchina virtuale, nell'insieme di credenziali delle chiavi, potrebbe non essere possibile usare indirizzi IP statici e non è consigliabile consentire a tutti gli indirizzi IP per le macchine virtuali di Azure di accedere all'insieme di credenziali delle chiavi.

In questo caso, è necessario creare la risorsa all'interno di una rete virtuale e quindi consentire l'accesso all'insieme di credenziali delle chiavi al traffico proveniente dalla rete virtuale e dalla subnet specifiche. A questo scopo, eseguire i passaggi seguenti.

1. Accedere al Portale di Azure
1. Selezionare l'insieme di credenziali delle chiavi da configurare
1. Selezionare il pannello 'Rete'.
1. Selezionare '+ Aggiungi rete virtuale esistente'.
1. Selezionare la rete virtuale e la subnet da autorizzare attraverso il firewall di Key Vault.

### <a name="key-vault-firewall-enabled-private-link"></a>Firewall di Key Vault abilitato (collegamento privato)

Per informazioni su come configurare una connessione di collegamento privato nell'insieme di credenziali delle chiavi, vedere il documento [qui](https://docs.microsoft.com/azure/key-vault/general/private-link-service).

> [!IMPORTANT]
> Quando le regole del firewall sono operative, gli utenti possono eseguire le operazioni del [piano dati](secure-your-key-vault.md#data-plane-access-control) Key Vault solo se le loro richieste hanno origine da reti virtuali o intervalli di indirizzi IPv4 consentiti. Questo vale anche per l'accesso a Key Vault dal portale di Azure. Benché gli utenti possano accedere a un insieme di credenziali delle chiavi dal portale di Azure, potrebbero non essere in grado di elencare chiavi, segreti o certificati se il computer client in uso non è presente nell'elenco dei computer consentiti. Ciò influisce anche sul selettore dell'insieme di credenziali delle chiavi di altri servizi di Azure. Se le regole del firewall bloccano i computer client, gli utenti potrebbero essere in grado di visualizzare l'elenco degli insiemi di credenziali delle chiavi ma non di elencare le chiavi.

> [!NOTE]
> Tenere presente le seguenti limitazioni di configurazione:
> * Sono consentite al massimo 127 regole di rete virtuale e 127 regole IPv4. 
> * Le regole di rete IP sono consentite solo per gli indirizzi IP pubblici. Gli intervalli di indirizzi IP riservati per le reti private (come da definizione in RFC 1918) non sono consentiti nelle regole IP. Le reti private includono indirizzi che iniziano con **10.** , **172.16-31.** e **192.168.** . 
> * Attualmente sono supportati solo gli indirizzi IPv4.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Di seguito viene illustrato come configurare firewall e reti virtuali di Key Vault usando il portale di Azure:

1. Passare all'insieme di credenziali delle chiavi che si vuole proteggere.
2. Selezionare **Rete** e quindi selezionare la scheda **Firewall e reti virtuali**.
3. Selezionare **Reti selezionate** in **Consenti l'accesso da**.
4. Per aggiungere reti virtuali esistenti ai firewall e alle regole di rete virtuale, selezionare **+ Aggiungi reti virtuali esistenti**.
5. Nel nuovo pannello che si apre selezionare la sottoscrizione, le reti virtuali e le subnet a cui consentire l'accesso all'insieme di credenziali delle chiavi. Se per le reti virtuali e le subnet selezionate non sono abilitati endpoint di servizio, confermare di voler abilitare gli endpoint di servizio e selezionare **Abilita**. La modifica può richiedere fino a 15 minuti per diventare operativa.
6. In **Reti IP** aggiungere intervalli di indirizzi IPv4 digitando gli intervalli di indirizzi IPv4 in [notazione CIDR (Classless Inter-domain Routing)](https://tools.ietf.org/html/rfc4632) o i singoli indirizzi IP.
7. Se si vuole consentire ai servizi Microsoft attendibili di ignorare il firewall di Key Vault, selezionare 'Sì'. Per un elenco completo dei servizi attendibili di Key Vault correnti, vedere il collegamento seguente. [Servizi attendibili di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
7. Selezionare **Salva**.

È anche possibile aggiungere nuove reti virtuali e subnet e poi abilitare gli endpoint di servizio per le reti virtuali e le subnet appena create selezionando **+ Aggiungi nuova rete virtuale**. Seguire quindi le istruzioni.

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure 

Di seguito viene illustrato come configurare firewall e reti virtuali di Key Vault usando l'interfaccia della riga di comando di Azure

1. [Installare l'interfaccia della riga di comando di Azure ](https://docs.microsoft.com/cli/azure/install-azure-cli) e [accedere](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

2. Indicare le regole di rete virtuale disponibili. Se non è stata impostata alcuna regola per questo insieme di credenziali delle chiavi, l'elenco sarà vuoto.
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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Di seguito viene illustrato come configurare firewall e reti virtuali di Key Vault usando PowerShell:

1. Installare la versione più aggiornata di [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) ed [effettuare l'accesso](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

2. Indicare le regole di rete virtuale disponibili. Se non è stata impostata alcuna regola per questo insieme di credenziali delle chiavi, l'elenco sarà vuoto.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Abilitare l'endpoint di servizio per Key Vault in una rete virtuale e una subnet esistenti.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Aggiungere una regola di rete per una rete virtuale e una subnet.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Aggiungere un intervallo di indirizzi IP da cui consentire il traffico.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Se questo insieme di credenziali delle chiavi deve essere accessibile da tutti i servizi attendibili, impostare `bypass` su `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Attivare le regole della rete impostando l'azione predefinita su `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Riferimenti
* Informazioni di riferimento sui modelli di Azure Resource Manager: [Informazioni di riferimento sui modelli di Azure Resource Manager per Azure Key Vault](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/vaults)
* Comandi dell'interfaccia della riga di comando di Azure: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Cmdlet di Azure PowerShell: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Passaggi successivi

* [Endpoint servizio di rete virtuale per Key Vault](overview-vnet-service-endpoints.md)
* [Proteggere l'insieme di credenziali delle chiavi](secure-your-key-vault.md)
