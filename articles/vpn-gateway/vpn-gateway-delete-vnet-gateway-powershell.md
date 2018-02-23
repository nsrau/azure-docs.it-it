---
title: 'Eliminare un gateway di rete virtuale: PowerShell: Azure Resource Manager | Microsoft Docs'
description: Eliminare un gateway di rete virtuale usando PowerShell nel modello di distribuzione Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: cherylmc
ms.openlocfilehash: 4d0f085423d5bd60b24d88649ee1d77bcd1d009f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Eliminare un gateway di rete virtuale usando PowerShell
> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (classico)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Esistono due diversi approcci quando si desidera eliminare un gateway di rete virtuale per una configurazione di gateway VPN.

- Se si vuole eliminare tutto e ricominciare da capo, come nel caso di un ambiente di testing, è possibile eliminare il gruppo di risorse. Quando si elimina un gruppo di risorse, vengono eliminate tutte le risorse all'interno del gruppo. Questo metodo è consigliato solo se non si vuole mantenere alcuna risorsa del gruppo di risorse. Con questo approccio non è possibile eliminare in modo selettivo solo alcune risorse.

- Se si desidera mantenere alcune delle risorse nel gruppo di risorse, eliminare un gateway di rete virtuale diventa leggermente più complicato. Per poter eliminare il gateway di rete virtuale prima è necessario eliminare tutte le risorse che dipendono dal gateway. I passaggi variano a seconda del tipo di connessioni che sono state create e delle risorse dipendenti per ogni connessione.

## <a name="before-beginning"></a>Prima di iniziare

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Scaricare i più recenti cmdlet PowerShell di Azure Resource Manager.

Scaricare e installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni su come scaricare e installare i cmdlet PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Connettersi all'account di Azure.

Aprire la console di PowerShell e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

```powershell
Login-AzureRmAccount
```

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

Se sono disponibili più sottoscrizioni, specificare la sottoscrizione da usare.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>Eliminare un gateway VPN da sito a sito

Per eliminare un gateway di rete virtuale per una configurazione da sito a sito, è necessario innanzitutto eliminare ogni risorsa che riguarda il gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze. Quando si usano gli esempi seguenti, alcuni valori devono essere specificati, mentre altri sono un risultato di output. Negli esempi vengono usati i seguenti valori specifici a scopo dimostrativo:

Nome VNet: VNet1<br>
Nome del gruppo di risorse: RG1<br>
Nome del gateway di rete virtuale: GW1<br>

La procedura seguente si applica al modello di distribuzione di Azure Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Ottenere il gateway di rete virtuale che si vuole eliminare.

```powershell
$Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verificare se il gateway di rete virtuale ha qualche connessione.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. Eliminare tutte le connessioni.

Potrebbe essere richiesto di confermare l'eliminazione di ciascuna delle connessioni.

```powershell
$Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. Eliminare il gateway di rete virtuale.

Potrebbe essere richiesto di confermare l'eliminazione del gateway. Se in aggiunta alla configurazione S2S si dispone di una configurazione P2S per questa rete virtuale, l'eliminazione del gateway di rete virtuale disconnette automaticamente tutti i client P2S senza alcun avviso.


```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

A questo punto, il gateway di rete virtuale è stato eliminato. È possibile usare i passaggi successivi per eliminare le risorse che non vengono più usate.

### <a name="5-delete-the-local-network-gateways"></a>5 Eliminare i gateway di rete locale.

Ottenere l'elenco dei gateway di rete locale corrispondenti.

```powershell
$LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Eliminare i gateway di rete locale. Potrebbe essere richiesto di confermare l'eliminazione di ciascuno dei gateway di rete locale.

```powershell
$LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. Eliminare le risorse degli indirizzi IP pubblici.

Ottenere le configurazioni IP del gateway di rete virtuale.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Ottenere l'elenco delle risorse degli indirizzi IP pubblici usate per il gateway di rete virtuale. Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Eliminare le risorse IP pubbliche.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Eliminare la subnet del gateway e impostare la configurazione.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>Eliminare un gateway VPN da rete virtuale a rete virtuale

Per eliminare un gateway di rete virtuale per una configurazione V2V, è necessario innanzitutto eliminare ogni risorsa che riguarda il gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze. Quando si usano gli esempi seguenti, alcuni valori devono essere specificati, mentre altri sono un risultato di output. Negli esempi vengono usati i seguenti valori specifici a scopo dimostrativo:

Nome VNet: VNet1<br>
Nome del gruppo di risorse: RG1<br>
Nome del gateway di rete virtuale: GW1<br>

La procedura seguente si applica al modello di distribuzione di Azure Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Ottenere il gateway di rete virtuale che si vuole eliminare.

```powershell
$Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verificare se il gateway di rete virtuale ha qualche connessione.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Potrebbero essere presenti altre connessioni a gateway di rete virtuale che fanno parte di un gruppo di risorse diverso. Verificare le connessioni aggiuntive in ogni gruppo di risorse aggiuntivo. In questo esempio si controllano le connessioni da RG2. Eseguire questa operazione per ogni gruppo di risorse che potrebbe avere una connessione al gateway di rete virtuale.

```powershell
get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Ottenere l'elenco di connessioni in entrambe le direzioni.

Poiché si tratta di una configurazione da rete virtuale a rete virtuale, è necessario l'elenco delle connessioni in entrambe le direzioni.

```powershell
$ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
In questo esempio si controllano le connessioni da RG2. Eseguire questa operazione per ogni gruppo di risorse che potrebbe avere una connessione al gateway di rete virtuale.

```powershell
 $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. Eliminare tutte le connessioni.

Potrebbe essere richiesto di confermare l'eliminazione di ciascuna delle connessioni.

```powershell
$ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. Eliminare il gateway di rete virtuale.

Potrebbe essere richiesto di confermare l'eliminazione del gateway di rete locale. Se in aggiunta alla configurazione V2V si dispone di una configurazione PS2 per questa rete virtuale, l'eliminazione dei gateway di rete virtuale disconnette automaticamente tutti i client P2S senza alcun avviso.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

A questo punto, il gateway di rete virtuale è stato eliminato. È possibile usare i passaggi successivi per eliminare le risorse che non vengono più usate.

### <a name="6-delete-the-public-ip-address-resources"></a>6. Eliminare le risorse degli indirizzi IP pubblici

Ottenere le configurazioni IP del gateway di rete virtuale.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Ottenere l'elenco delle risorse degli indirizzi IP pubblici usate per il gateway di rete virtuale. Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Eliminare le risorse IP pubbliche. Potrebbe essere richiesto di confermare l'eliminazione dell'indirizzo IP pubblico.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. Eliminare la subnet del gateway e impostare la configurazione.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>Eliminare un gateway VPN da sito a sito

Per eliminare un gateway di rete virtuale per una configurazione P2S, è necessario innanzitutto eliminare ogni risorsa che riguarda il gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze. Quando si usano gli esempi seguenti, alcuni valori devono essere specificati, mentre altri sono un risultato di output. Negli esempi vengono usati i seguenti valori specifici a scopo dimostrativo:

Nome VNet: VNet1<br>
Nome del gruppo di risorse: RG1<br>
Nome del gateway di rete virtuale: GW1<br>

La procedura seguente si applica al modello di distribuzione di Azure Resource Manager.


>[!NOTE]
> Quando si elimina il gateway VPN, tutti i client connessi vengono disconnessi dalla rete virtuale senza alcun avviso.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Ottenere il gateway di rete virtuale che si vuole eliminare.

```powershell
$Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. Eliminare il gateway di rete virtuale.

Potrebbe essere richiesto di confermare l'eliminazione del gateway di rete locale.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

A questo punto, il gateway di rete virtuale è stato eliminato. È possibile usare i passaggi successivi per eliminare le risorse che non vengono più usate.

### <a name="3-delete-the-public-ip-address-resources"></a>3. Eliminare le risorse degli indirizzi IP pubblici

Ottenere le configurazioni IP del gateway di rete virtuale.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Ottenere l'elenco degli indirizzi IP pubblici utilizzati per il gateway di rete virtuale. Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici.

```powershell
$PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Eliminare gli indirizzi IP pubblici. Potrebbe essere richiesto di confermare l'eliminazione dell'indirizzo IP pubblico.

```powershell
$PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. Eliminare la subnet del gateway e impostare la configurazione.

```powershell
$GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>Eliminare un gateway VPN eliminando il gruppo di risorse

Se non si è interessati a mantenere risorse del gruppo di risorse e si vuole solo ricominciare da capo, è possibile eliminare un intero gruppo di risorse. Questo è un modo rapido per rimuovere tutto. La procedura seguente si applica solo al modello di distribuzione Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Ottenere un elenco di tutti i gruppi di risorse nella sottoscrizione.

```powershell
Get-AzureRmResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Individuare il gruppo di risorse da eliminare.

Individuare il gruppo di risorse che si vuole eliminare e visualizzare l'elenco delle risorse di quel gruppo di risorse. In questo esempio il nome del gruppo di risorse è RG1. Modificare l'esempio per recuperare un elenco di tutte le risorse.

```powershell
Find-AzureRmResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Verificare le risorse dell'elenco.

Quando viene restituito l'elenco, esaminarlo per verificare che si desidera eliminare tutte le risorse del gruppo di risorse e anche il gruppo di risorse stesso. Se si vogliono mantenere alcune risorse del gruppo, usare la procedura illustrata nelle sezioni precedenti di questo articolo per eliminare il gateway.

### <a name="4-delete-the-resource-group-and-resources"></a>4. Eliminare il gruppo di risorse e le risorse.

Per eliminare il gruppo di risorse e tutte le risorse che contiene, modificare l'esempio ed eseguirlo.

```powershell
Remove-AzureRmResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. Controllare lo stato.

Per eliminare tutte le risorse, Azure impiega un po' di tempo. È possibile controllare lo stato del gruppo di risorse usando questo cmdlet.

```powershell
Get-AzureRmResourceGroup -ResourceGroupName RG1
```

Il risultato restituito mostra "Operazione riuscita".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```