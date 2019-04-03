---
title: Limitare l'accesso di rete alle risorse PaaS - Azure PowerShell | Microsoft Docs
description: In questo articolo viene descritto come limitare l'accesso di rete alle risorse di Azure, ad esempio Archiviazione di Azure e il database SQL di Azure, tramite endpoint servizio di rete virtuale con Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: bf69fe0b817011f63ef0a792e01084aedfb83ddc
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881291"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Limitare l'accesso di rete alle risorse PaaS con gli endpoint di servizio della rete virtuale usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Gli endpoint di servizio della rete virtuale consentono di limitare l'accesso di rete ad alcune risorse dei servizi di Azure a una subnet della rete virtuale. È anche possibile rimuovere l'accesso Internet alle risorse. Gli endpoint di servizio forniscono la connessione diretta dalla rete virtuale ai servizi di Azure supportati, consentendo di usare lo spazio indirizzi privato della rete virtuale per accedere ai servizi di Azure. Il traffico destinato alle risorse di Azure tramite gli endpoint di servizio rimane sempre nella rete backbone di Microsoft Azure. In questo articolo viene spiegato come:

* Creare una rete virtuale con una subnet
* Aggiungere una subnet e abilitare un endpoint di servizio
* Creare una risorsa di Azure e consentire l'accesso di rete alla risorsa da una sola subnet
* Distribuire una macchina virtuale (VM) in ogni subnet
* Verificare che venga consentito l'accesso a una risorsa da una subnet
* Verificare che venga rifiutato l'accesso a una risorsa da una subnet e da Internet

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, questo articolo richiede il modulo Azure PowerShell versione 1.0.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Prima di creare una rete virtuale, è necessario creare un gruppo di risorse per la rete virtuale e tutte le altre risorse create in questo articolo. Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup*: 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork* con il prefisso dell'indirizzo *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Creare una configurazione di subnet con [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). L'esempio seguente crea una configurazione per una subnet denominata *Public*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Creare la subnet nella rete virtuale scrivendo la configurazione della subnet nella rete virtuale con [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork):

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Abilitare un endpoint di servizio

È possibile abilitare gli endpoint di servizio solo per i servizi che supportano tali endpoint. Visualizzare i servizi abilitati endpoint servizio in una località di Azure con [Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice). L'esempio seguente restituisce un elenco di servizi abilitati per gli endpoint di servizio nell'area *eastus*. L'elenco dei servizi restituiti aumenterà nel corso del tempo poiché altri servizi di Azure verranno abilitati per gli endpoint di servizio.

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

Creare un'altra subnet nella rete virtuale. In questo esempio viene creata una subnet denominata *Private* con un endpoint di servizio per *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Limitare l'accesso di rete per una subnet

Creare regole di sicurezza gruppo con sicurezza di rete [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). La regola seguente consente l'accesso in uscita agli indirizzi IP pubblici assegnati al servizio Archiviazione di Azure: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
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
  -Priority 110 `
  -Protocol * `
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

Associare il gruppo di sicurezza di rete per la *privati* subnet con [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) e quindi scrivere la configurazione della subnet della rete virtuale. L'esempio seguente associa il gruppo di sicurezza di rete *myNsgPrivate* alla subnet *privata*:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Limitare l'accesso di rete a una risorsa

I passaggi necessari per limitare l'accesso di rete alle risorse create tramite i servizi di Azure abilitati per gli endpoint di servizio variano a seconda dei servizi. Vedere la documentazione relativa ai singoli servizi per i passaggi specifici. La parte rimanente di questo articolo include, a titolo di esempio, i passaggi da eseguire per limitare l'accesso di rete per un account di archiviazione di Azure.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un account di archiviazione di Azure con [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Sostituire `<replace-with-your-unique-storage-account-name>` con un nome univoco per tutte le località di Azure, di lunghezza compresa tra 3 e 24 caratteri e costituito solo da numeri e lettere minuscole.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Dopo aver creato l'account di archiviazione, recuperare la chiave dell'account di archiviazione in una variabile con [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

La chiave viene usata per creare una condivisione file in un passaggio successivo. Immettere `$storageAcctKey` e prendere nota del valore, poiché sarà necessario immetterlo manualmente in un passaggio successivo, quando si eseguirà il mapping della condivisione file a un'unità in una VM.

### <a name="create-a-file-share-in-the-storage-account"></a>Creare una condivisione file nell'account di archiviazione

Creare un contesto dell'account di archiviazione e la chiave con [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). Il contesto incapsula il nome e la chiave dell'account di archiviazione:

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

Creare una condivisione di file con [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

$share = New-AzStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Rifiutare l'accesso di rete a un account di archiviazione

Per impostazione predefinita, gli account di archiviazione accettano connessioni di rete dai client in qualsiasi rete. Per limitare l'accesso alle reti selezionate, modificare l'azione predefinita per *Deny* con [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Dopo che l'accesso di rete è stato rifiutato, l'account di archiviazione non sarà accessibile da nessuna rete.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Abilitare l'accesso di rete da una subnet

Recuperare la rete virtuale creata con [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) e quindi recuperare l'oggetto subnet privata in una variabile con [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

Consenti accesso alla rete all'account di archiviazione dal *privati* subnet con [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Per testare l'accesso di rete a un account di archiviazione, distribuire una VM in ogni subnet.

### <a name="create-the-first-virtual-machine"></a>Creare la prima macchina virtuale

Creare una macchina virtuale nel *pubbliche* subnet con [New-AzVM](/powershell/module/az.compute/new-azvm). Quando si esegue il comando seguente, vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la VM. L'opzione `-AsJob` crea la VM in background, pertanto è possibile continuare con il passaggio successivo.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Viene restituito un output simile all'output di esempio seguente:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>Creare la seconda macchina virtuale

Creare una macchina virtuale nella subnet *Private*:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Per creare la VM, Azure impiega alcuni minuti. Prima di continuare con il passaggio successivo, attendere che Azure finisca di creare la VM e restituisca l'output a PowerShell.

## <a name="confirm-access-to-storage-account"></a>Verificare che venga consentito l'accesso a un account di archiviazione

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

Nella VM *myVmPrivate* eseguire il mapping della condivisione file di Azure all'unità Z usando PowerShell. Prima di eseguire i comandi seguenti, sostituire `<storage-account-key>` e `<storage-account-name>` con i valori specificati o recuperati in [Creare un account di archiviazione](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

L'output restituito da PowerShell è simile all'output di esempio seguente:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Il mapping della condivisione file di Azure all'unità Z è stato eseguito correttamente.

Verificare che la VM non abbia connettività in uscita ad altri indirizzi IP pubblici:

```powershell
ping bing.com
```

Non si ricevono risposte perché il gruppo di sicurezza di rete associato alla subnet *privata* non consente l'accesso in uscita a indirizzi IP pubblici diversi dagli indirizzi assegnati al servizio Archiviazione di Azure.

Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Verificare che venga rifiutato l'accesso a un account di archiviazione

Ottenere l'indirizzo IP pubblico della VM *myVmPublic*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Sostituire `<publicIpAddress>` nel comando seguente con l'indirizzo IP pubblico restituito dal comando precedente e quindi immettere il comando seguente: 

```powershell
mstsc /v:<publicIpAddress>
```

Nella VM *myVmPublic* provare a eseguire il mapping della condivisione file di Azure all'unità Z. Prima di eseguire i comandi seguenti, sostituire `<storage-account-key>` e `<storage-account-name>` con i valori specificati o recuperati in [Creare un account di archiviazione](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

L'accesso alla condivisione viene rifiutato e si riceve un errore `New-PSDrive : Access is denied`. L'accesso viene rifiutato perché la VM *myVmPublic* è distribuita nella subnet *Public*. La subnet *Public* non ha un endpoint di servizio abilitato per Archiviazione di Azure e l'account di archiviazione consente l'accesso di rete solo dalla subnet *Private* e non dalla subnet *Public*.

Chiudere la sessione Desktop remoto alla VM *myVmPublic*.

Dal computer provare a visualizzare le condivisioni file nell'account di archiviazione con il comando seguente:

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Accesso negato e viene visualizzato un *Get-AzStorageFile: Il server remoto ha restituito un errore: 403 - Accesso negato. Codice stato HTTP: 403 - HTTP Error Message: This request is not authorized to perform this operation* (Messaggio di errore HTTP: La richiesta non è autorizzata a eseguire questa operazione) perché il computer non si trova nella subnet *Private* della rete virtuale *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato abilitato un endpoint servizio per una subnet della rete virtuale. È stato illustrato che gli endpoint di servizio possono essere abilitati per le risorse distribuite con più servizi di Azure. È stato creato un account di archiviazione di Azure ed è stato limitato l'accesso di rete all'account di archiviazione solo alle risorse in una subnet della rete virtuale. Per altre informazioni sugli endpoint servizio, vedere [Panoramica degli endpoint servizio](virtual-network-service-endpoints-overview.md) e [Gestire le subnet](virtual-network-manage-subnet.md).

Se nell'account sono presenti più reti virtuali, può essere necessario connettere due reti virtuali per consentire alle risorse di ogni rete virtuale di comunicare tra loro. Per informazioni su come fare, vedere [Connettere reti virtuali](tutorial-connect-virtual-networks-powershell.md).
