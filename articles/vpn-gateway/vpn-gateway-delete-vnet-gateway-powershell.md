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
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3032b09d06a103f9cd915e3803355199243488f9
ms.lasthandoff: 03/21/2017


---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Eliminare un gateway di rete virtuale usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Classica: PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Esistono due diversi approcci quando si desidera eliminare un gateway di rete virtuale per una configurazione di gateway VPN.

- Se si desidera eliminare tutto e ricominciare da capo, come nel caso di un ambiente di test, è possibile eliminare un intero gruppo di risorse. Quando si elimina un gruppo di risorse, vengono eliminate tutte le risorse all'interno del gruppo. Si consiglia di farlo solo se non si desidera mantenere nessuna delle risorse nel gruppo di risorse. Con questo approccio non è possibile eliminare in modo selettivo solo alcune risorse.

- Se si desidera mantenere alcune delle risorse nel gruppo di risorse, eliminare un gateway di rete virtuale diventa leggermente più complicato. Per poter eliminare il gateway di rete virtuale prima è necessario eliminare tutte le risorse che dipendono dal gateway. I passaggi variano a seconda del tipo di connessioni che sono state create e delle risorse dipendenti per ogni connessione.

##<a name="before-beginning"></a>Prima di iniziare

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Scaricare i più recenti cmdlet PowerShell di Azure Resource Manager.
Scaricare e installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni su come scaricare e installare i cmdlet PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="2-connect-to-your-azure-account"></a>2. Connettersi all'account di Azure. 
Aprire la console di PowerShell e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

    Login-AzureRmAccount

Controllare le sottoscrizioni per l'account.

    Get-AzureRmSubscription

Se sono disponibili più sottoscrizioni, specificare la sottoscrizione da usare.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

##<a name="S2S"></a>Eliminare un gateway VPN da sito a sito

Per eliminare un gateway di rete virtuale per una configurazione da sito a sito, è necessario innanzitutto eliminare ogni risorsa che riguarda il gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze. Quando si lavora con gli esempi seguenti, alcuni dei valori devono essere chiamati specificamente, mentre altri valori sono un risultato dell'output. Negli esempi vengono usati i seguenti valori specifici a scopo dimostrativo:

Nome VNet: VNet1<br>
Nome del gruppo di risorse: RG1<br>
Nome del gateway di rete virtuale: GW1<br>

La procedura seguente si applica al modello di distribuzione di Azure Resource Manager.

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Ottenere il gateway di rete virtuale che si vuole eliminare.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verificare se il gateway di rete virtuale ha qualche connessione.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
    $Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}

###<a name="3-delete-all-connections"></a>3. Eliminare tutte le connessioni.
Potrebbe essere richiesto di confermare l'eliminazione di ciascuna delle connessioni.

    $Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="4-get-the-list-of-the-corresponding-local-network-gateways"></a>4. Ottenere l'elenco dei gateway di rete locale corrispondenti.

    $LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
    
###<a name="5-delete-the-local-network-gateways"></a>5. Eliminare i gateway di rete locale.
Potrebbe essere richiesto di confermare l'eliminazione di ciascuno dei gateway di rete locale.

    $LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}

###<a name="6-delete-the-virtual-network-gateway"></a>6. Eliminare il gateway di rete virtuale.
Potrebbe essere richiesto di confermare l'eliminazione del gateway.

>[!NOTE]
> Se in aggiunta alla configurazione S2S si dispone di una configurazione P2S per questa rete virtuale, l'eliminazione del gateway di rete virtuale disconnette automaticamente tutti i client P2S senza alcun avviso.
>
>

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="7-get-the-ip-configurations-of-the-virtual-network-gateway"></a>7. Ottenere le configurazioni IP del gateway di rete virtuale.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="8-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>8. Ottenere l'elenco degli indirizzi IP pubblici utilizzati per il gateway di rete virtuale 
Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="9-delete-the-public-ips"></a>9. Eliminare gli indirizzi IP pubblici.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}

###<a name="10-delete-the-gateway-subnet-and-set-the-configuration"></a>10. Eliminare la subnet del gateway e impostare la configurazione.

    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="v2v"></a>Eliminare un gateway VPN da rete virtuale a rete virtuale

Per eliminare un gateway di rete virtuale per una configurazione V2V, è necessario innanzitutto eliminare ogni risorsa che riguarda il gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze. Quando si lavora con gli esempi seguenti, alcuni dei valori devono essere chiamati specificamente, mentre altri valori sono un risultato dell'output. Negli esempi vengono usati i seguenti valori specifici a scopo dimostrativo:

Nome VNet: VNet1<br>
Nome del gruppo di risorse: RG1<br>
Nome del gateway di rete virtuale: GW1<br>

La procedura seguente si applica al modello di distribuzione di Azure Resource Manager.

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Ottenere il gateway di rete virtuale che si vuole eliminare.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verificare se il gateway di rete virtuale ha qualche connessione.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
Potrebbero essere presenti altre connessioni a gateway di rete virtuale che fanno parte di un gruppo di risorse diverso. Verificare le connessioni aggiuntive in ogni gruppo di risorse aggiuntivo. In questo esempio si controllano le connessioni da RG2. Eseguire questa operazione per ogni gruppo di risorse che potrebbe avere una connessione al gateway di rete virtuale.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="3-get-the-list-of-connections-in-both-directions"></a>3. Ottenere l'elenco di connessioni in entrambe le direzioni.
Poiché si tratta di una configurazione da rete virtuale a rete virtuale, è necessario l'elenco delle connessioni in entrambe le direzioni.

    $ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
In questo esempio si controllano le connessioni da RG2. Eseguire questa operazione per ogni gruppo di risorse che potrebbe avere una connessione al gateway di rete virtuale.
 
    $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="4-delete-all-connections"></a>4. Eliminare tutte le connessioni.
Potrebbe essere richiesto di confermare l'eliminazione di ciascuna delle connessioni.

    $ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
    $ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="5-delete-the-virtual-network-gateway"></a>5. Eliminare il gateway di rete virtuale.
Potrebbe essere richiesto di confermare l'eliminazione del gateway di rete locale.

>[!NOTE]
> Se in aggiunta alla configurazione V2V si dispone di una configurazione PS2 per questa rete virtuale, l'eliminazione dei gateway di rete virtuale disconnette automaticamente tutti i client P2S senza alcun avviso.
>
>

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="6-get-the-ip-configurations-of-the-virtual-network-gateway"></a>6. Ottenere le configurazioni IP del gateway di rete virtuale.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="7-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>7. Ottenere l'elenco degli indirizzi IP pubblici utilizzati per il gateway di rete virtuale. 
Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="8-delete-the-public-ips"></a>8. Eliminare gli indirizzi IP pubblici.
Potrebbe essere richiesto di confermare l'eliminazione dell'indirizzo IP pubblico.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="9-delete-the-gateway-subnet-and-set-the-configuration"></a>9. Eliminare la subnet del gateway e impostare la configurazione.
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="deletep2s"></a>Eliminare un gateway VPN da sito a sito

Per eliminare un gateway di rete virtuale per una configurazione P2S, è necessario innanzitutto eliminare ogni risorsa che riguarda il gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze. Quando si lavora con gli esempi seguenti, alcuni dei valori devono essere chiamati specificamente, mentre altri valori sono un risultato dell'output. Negli esempi vengono usati i seguenti valori specifici a scopo dimostrativo:

Nome VNet: VNet1<br>
Nome del gruppo di risorse: RG1<br>
Nome del gateway di rete virtuale: GW1<br>

La procedura seguente si applica al modello di distribuzione di Azure Resource Manager.


>[!NOTE]
> Quando si elimina il gateway VPN, tutti i client connessi vengono disconnessi dalla rete virtuale senza alcun avviso.
>
>

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Ottenere il gateway di rete virtuale che si vuole eliminare.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-delete-the-virtual-network-gateway"></a>2. Eliminare il gateway di rete virtuale.
Potrebbe essere richiesto di confermare l'eliminazione del gateway di rete locale.

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="3-get-the-ip-configurations-of-the-virtual-network-gateway"></a>3. Ottenere le configurazioni IP del gateway di rete virtuale.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="4-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>4. Ottenere l'elenco degli indirizzi IP pubblici utilizzati per il gateway di rete virtuale. 
Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="5-delete-the-public-ips"></a>5. Eliminare gli indirizzi IP pubblici.
Potrebbe essere richiesto di confermare l'eliminazione dell'indirizzo IP pubblico.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="6-delete-the-gateway-subnet-and-set-the-configuration"></a>6. Eliminare la subnet del gateway e impostare la configurazione.
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="delete"></a>Eliminare un gateway VPN eliminando il gruppo di risorse

Se non si ha il problema di dover conservare le risorse e si desidera solo ricominciare da capo, è possibile eliminare un intero gruppo di risorse. Questo è un modo rapido per rimuovere tutto. Quando si elimina un intero gruppo di risorse, non è possibile scegliere le risorse che si desidera eliminare. Assicurarsi perciò che questo sia ciò che si vuole fare prima di eseguire l'esempio.

La procedura seguente si applica al modello di distribuzione di Azure Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Ottenere un elenco di tutti i gruppi di risorse nella sottoscrizione.

    Get-AzureRmResourceGroup
### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Individuare il gruppo di risorse da eliminare.
Individuare il gruppo di risorse che si vuole eliminare e visualizzare l'elenco delle risorse di quel gruppo di risorse. In questo esempio il nome del gruppo di risorse è RG1. Modificare l'esempio per recuperare un elenco di tutte le risorse.

    Find-AzureRmResource -ResourceGroupNameContains RG1

### <a name="3-verify-the-resources-in-the-list"></a>3. Verificare le risorse dell'elenco.
Quando viene restituito l'elenco, esaminarlo per verificare che si desidera eliminare tutte le risorse del gruppo di risorse e anche il gruppo di risorse stesso. 

### <a name="4-delete-the-resource-group-and-resources"></a>4. Eliminare il gruppo di risorse e le risorse.
Per eliminare il gruppo di risorse e tutte le risorse che contiene, modificare l'esempio ed eseguirlo.

    Remove-AzureRmResourceGroup -Name RG1

### <a name="5-check-the-status"></a>5. Controllare lo stato.
Per eliminare tutte le risorse, Azure impiega un po' di tempo. È possibile controllare lo stato del gruppo di risorse usando questo cmdlet.

    Get-AzureRmResourceGroup -ResourceGroupName RG1

Il risultato restituito mostra "Operazione riuscita".

    ResourceGroupName : RG1
    Location          : eastus
    ProvisioningState : Succeeded


