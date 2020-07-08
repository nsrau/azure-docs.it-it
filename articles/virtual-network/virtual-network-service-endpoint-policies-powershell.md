---
title: Limitare i dati exfiltration ad archiviazione di Azure-Azure PowerShell
description: Questo articolo illustra come limitare e limitare i dati di rete virtuale exfiltration alle risorse di archiviazione di Azure con i criteri dell'endpoint di servizio di rete virtuale usando Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 1d4fcc280ba2e34d2fa81584846441ad6fe81431
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708196"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Gestire i dati exfiltration negli account di archiviazione di Azure con i criteri dell'endpoint di servizio di rete virtuale usando Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I criteri dell'endpoint del servizio rete virtuale consentono di applicare il controllo di accesso agli account di archiviazione di Azure dall'interno di una rete virtuale tramite endpoint di servizio. Si tratta di una chiave per proteggere i carichi di lavoro, la gestione degli account di archiviazione consentiti e la posizione in cui sono consentiti i dati exfiltration.
In questo articolo vengono illustrate le operazioni seguenti:

* Creare una rete virtuale.
* Aggiungere una subnet e abilitare l'endpoint servizio per archiviazione di Azure.
* Creare due account di archiviazione di Azure e consentire l'accesso alla rete dalla subnet creata in precedenza.
* Creare un criterio di endpoint di servizio per consentire l'accesso solo a uno degli account di archiviazione.
* Distribuire una macchina virtuale (VM) nella subnet.
* Verificare l'accesso all'account di archiviazione consentito dalla subnet.
* Verificare che l'accesso sia stato negato all'account di archiviazione non consentito dalla subnet.

Se non si ha una sottoscrizione di Azure, prima di iniziare creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 1.0.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Prima di creare una rete virtuale, è necessario creare un gruppo di risorse per la rete virtuale e tutte le altre risorse create in questo articolo. Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup*: 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork* con il prefisso dell'indirizzo *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Abilitare un endpoint di servizio

Creare una subnet nella rete virtuale. In questo esempio viene creata una subnet denominata *Private* con un endpoint di servizio per *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Limitare l'accesso alla rete per la subnet

Creare regole di sicurezza del gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). La regola seguente consente l'accesso in uscita agli indirizzi IP pubblici assegnati al servizio Archiviazione di Azure: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

La regola seguente rifiuta l'accesso a tutti gli indirizzi IP pubblici. La regola precedente esegue l'override di questa regola, a causa della priorità più alta, che consente l'accesso agli indirizzi IP pubblici di Archiviazione di Azure.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

La regola seguente consente il traffico in entrata di Remote Desktop Protocol (RDP) nella subnet da qualsiasi posizione. Sono consentite connessioni Desktop remoto alla subnet in modo che sia possibile verificare l'accesso di rete a una risorsa in un passaggio successivo.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Associare il gruppo di sicurezza di rete alla subnet *private* con [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) e quindi scrivere la configurazione della subnet nella rete virtuale. L'esempio seguente associa il gruppo di sicurezza di rete *myNsgPrivate* alla subnet *privata*:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Limitare l'accesso alla rete per gli account di archiviazione di Azure

I passaggi necessari per limitare l'accesso di rete alle risorse create tramite i servizi di Azure abilitati per gli endpoint di servizio variano a seconda dei servizi. Vedere la documentazione relativa ai singoli servizi per i passaggi specifici. La parte rimanente di questo articolo include, a titolo di esempio, i passaggi da eseguire per limitare l'accesso di rete per un account di archiviazione di Azure.

### <a name="create-two-storage-accounts"></a>Creare due account di archiviazione

Creare un account di archiviazione di Azure con [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Dopo aver creato l'account di archiviazione, recuperare la chiave per l'account di archiviazione in una variabile con [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

La chiave viene usata per creare una condivisione file in un passaggio successivo. Immettere `$storageAcctKey` e prendere nota del valore, poiché sarà necessario immetterlo manualmente in un passaggio successivo, quando si eseguirà il mapping della condivisione file a un'unità in una VM.

A questo punto, ripetere i passaggi precedenti per creare un secondo account di archiviazione.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Recuperare anche la chiave dell'account di archiviazione da questo account per usare in seguito per creare una condivisione file.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Creare una condivisione file in ogni account di archiviazione

Creare un contesto per l'account di archiviazione e la chiave con [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). Il contesto incapsula il nome e la chiave dell'account di archiviazione:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Creare una condivisione file con [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Negare l'accesso di rete a un account di archiviazione

Per impostazione predefinita, gli account di archiviazione accettano connessioni di rete dai client in qualsiasi rete. Per limitare l'accesso alle reti selezionate, modificare l'azione predefinita in *Nega* con [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Dopo che l'accesso di rete è stato rifiutato, l'account di archiviazione non sarà accessibile da nessuna rete.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Abilitare l'accesso alla rete solo dalla subnet VNet

Recuperare la rete virtuale creata con [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) e quindi recuperare l'oggetto subnet privata in una variabile con [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Consentire l'accesso di rete all'account di archiviazione dalla subnet *privata* con [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Applicare i criteri per consentire l'accesso a un account di archiviazione valido

Per assicurarsi che gli utenti nella rete virtuale possano accedere solo agli account di archiviazione di Azure sicuri e consentiti, è possibile creare un criterio dell'endpoint di servizio con l'elenco degli account di archiviazione consentiti nella definizione. Questo criterio viene quindi applicato alla subnet della rete virtuale connessa alla risorsa di archiviazione tramite gli endpoint del servizio.

### <a name="create-a-service-endpoint-policy"></a>Creare un criterio di endpoint di servizio

Questa sezione Crea la definizione dei criteri con l'elenco delle risorse consentite per l'accesso tramite endpoint di servizio

Recuperare l'ID risorsa per il primo account di archiviazione (consentito) 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Creare la definizione dei criteri per consentire la risorsa precedente

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Creare i criteri dell'endpoint di servizio usando la definizione dei criteri creata in precedenza

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Associare i criteri dell'endpoint di servizio alla subnet della rete virtuale

Dopo aver creato i criteri dell'endpoint di servizio, sarà possibile associarli alla subnet di destinazione con la configurazione dell'endpoint del servizio per archiviazione di Azure.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Convalida la restrizione di accesso agli account di archiviazione di Azure

### <a name="deploy-the-virtual-machine"></a>Distribuire la macchina virtuale

Per testare l'accesso di rete a un account di archiviazione, distribuire una macchina virtuale nella subnet.

Creare una macchina virtuale nella subnet *private* con [New-AzVM](/powershell/module/az.compute/new-azvm). Quando si esegue il comando seguente, vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la VM. L'opzione `-AsJob` crea la VM in background, pertanto è possibile continuare con il passaggio successivo.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Viene restituito un output simile all'output di esempio seguente:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Verificare l'accesso all'account di archiviazione *consentito*

Usare [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) per restituire l'indirizzo IP pubblico di una macchina virtuale. L'esempio seguente restituisce l'indirizzo IP pubblico della macchina virtuale *myVmPrivate*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Sostituire `<publicIpAddress>` nel comando seguente con l'indirizzo IP pubblico restituito dal comando precedente e quindi immettere il comando seguente:

```powershell
mstsc /v:<publicIpAddress>
```

Viene creato e scaricato nel computer un file Remote Desktop Protocol con estensione rdp. Aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della VM. Potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un altro account** per specificare le credenziali immesse al momento della creazione della VM. Selezionare **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione.

Nella VM *myVmPrivate* eseguire il mapping della condivisione file di Azure dall'account di archiviazione consentito all'unità Z usando PowerShell. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

L'output restituito da PowerShell è simile all'output di esempio seguente:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Il mapping della condivisione file di Azure all'unità Z è stato eseguito correttamente.

Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPrivate*.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Confermare l'accesso negato a un account di archiviazione *non consentito*

Nella stessa VM *myVmPrivate* provare a eseguire il mapping della condivisione file di Azure all'unità X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

L'accesso alla condivisione viene rifiutato e si riceve un errore `New-PSDrive : Access is denied`. Accesso negato perché l'account di archiviazione *notallowedaccount* non è presente nell'elenco di risorse consentite nei criteri dell'endpoint di servizio. 

Chiudere la sessione Desktop remoto alla VM *myVmPublic*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuoverli:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato applicato un criterio di endpoint di servizio su un endpoint di servizio di rete virtuale di Azure ad archiviazione di Azure. Sono stati creati account di archiviazione di Azure e un accesso limitato alla rete solo ad alcuni account di archiviazione (e di conseguenza ne sono stati negati altri) da una subnet di rete virtuale Per altre informazioni sui criteri degli endpoint di servizio, vedere [Cenni preliminari sui criteri degli endpoint di servizio](virtual-network-service-endpoint-policies-overview.md).
