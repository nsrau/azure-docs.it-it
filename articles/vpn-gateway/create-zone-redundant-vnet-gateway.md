---
title: Creare un gateway di rete virtuale con ridondanza della zona in zone di disponibilità di Azure - Anteprima | Microsoft Docs
description: Distribuire gateway VPN ed ExpressRoute in zone di disponibilità - Anteprima.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/25/2018
ms.author: cherylmc
ms.openlocfilehash: a08c0f772965ddb2b40ac1ced1f26ade4cba3197
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017033"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Creare un gateway di rete virtuale con ridondanza della zona in zone di disponibilità di Azure - Anteprima

È possibile distribuire gateway VPN ed ExpressRoute in [zone di disponibilità di Azure](../availability-zones/az-overview.md). In questo modo, i gateway di rete virtuale ottengono maggiore disponibilità, scalabilità e resilienza. La distribuzione di gateway in zone di disponibilità di Azure separa fisicamente e logicamente i gateway all'interno di un'area e consente, al contempo, di proteggere la connettività di rete locale ad Azure da errori a livello di zona.

Nei gateway a livello di zona e con ridondanza della zona sono stati introdotti miglioramenti fondamentali delle prestazioni rispetto ai normali gateway di rete virtuale. Inoltre, la creazione di un gateway di rete virtuale con ridondanza della zona o a livello di zona è più rapida rispetto alla creazione di altri tipi di gateway. I tempi di creazione sono di circa 15 minuti per un gateway ExpressRoute e 19 minuti per un gateway VPN, contro i 45 minuti necessari per altri gateway.

### <a name="zrgw"></a>Gateway con ridondanza della zona

Per distribuire automaticamente i gateway di rete virtuale in zone di disponibilità, è possibile usare i gateway di rete virtuale con ridondanza della zona. Con i gateway con ridondanza della zona, è possibile sfruttare il contratto di servizio relativo al tempo di attività 99,99% in disponibilità generale per accedere ai servizi mission-critical e scalabili in Azure.

<br>
<br>

![Grafico di gateway con ridondanza della zona](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Gateway a livello di zona

Per distribuire i gateway in una zona specifica, si usano i gateway a livello di zona. Quando si distribuisce un gateway a livello di zona, entrambe le istanze del gateway vengono distribuite nella stessa zona di disponibilità.

<br>
<br>

![Grafico dei gateway a livello di zona](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKU del gateway

I gateway con ridondanza della zona e a livello di zona devono usare le nuove SKU del gateway. Dopo aver eseguito l'[autoregistrazione all'anteprima](#enroll), verranno visualizzate le nuove SKU del gateway di rete virtuale in tutte le aree di Azure AZ. Queste SKU sono simili alle SKU corrispondenti per ExpressRoute e Gateway VPN, ad eccezione del fatto che sono specifiche dei gateway con ridondanza della zona e a livello di zona.

Le nuove SKU del gateway sono:

### <a name="vpn-gateway"></a>Gateway VPN

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>SKU dell'indirizzo IP pubblico

I gateway con ridondanza della zona e i gateway a livello di zona si basano sulla SKU *Standard* della risorsa IP pubblico di Azure. La configurazione della risorsa IP pubblico di Azure determina se il gateway distribuito è del tipo con ridondanza della zona o a livello di zona. Se si crea una risorsa IP pubblico con una SKU *Basic*, il gateway non avrà alcuna ridondanza della zona e le risorse del gateway saranno a livello di area.

### <a name="pipzrg"></a>Gateway con ridondanza della zona

Quando si crea un indirizzo IP pubblico tramite la SKU IP pubblico **Standard** senza specificare una zona, il comportamento varia a seconda che il gateway sia VPN o ExpressRoute. 

* Per un gateway VPN, le due istanze del gateway saranno distribuite in due delle tre zone disponibili per garantire la ridondanza della zona. 
* Per un gateway ExpressRoute, poiché possono essere disponibili più di due istanze, il gateway può estendersi su tutte e tre le zone.

### <a name="pipzg"></a>Gateway a livello di zona

Quando si crea un indirizzo IP pubblico usando la SKU IP pubblico **Standard** e si specifica la zona (1, 2 o 3), tutte le istanze del gateway verranno distribuite nella stessa zona.

### <a name="piprg"></a>Gateway a livello di area

Quando si crea un indirizzo IP pubblico usando la SKU IP pubblico **Basic**, il gateway viene distribuito come gateway a livello di area e non dispone di alcuna ridondanza della zona integrata nel gateway.

## <a name="before-you-begin"></a>Prima di iniziare

È possibile usare PowerShell installato nel computer o Azure Cloud Shell. Se si sceglie di installare e usare PowerShell in locale, per questa funzionalità è necessaria l'ultima versione del modulo PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Per usare PowerShell in locale

Se si usa PowerShell in locale nel computer, anziché usare Cloud Shell, è necessario installare il modulo PowerShell 6.1.1 o versione successiva. Per controllare la versione di PowerShell installata, usare il comando seguente:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1. Registrarsi all'anteprima

Prima di poter configurare un gateway con ridondanza della zona o a livello di zona, è necessario innanzitutto eseguire l'autoregistrazione alla sottoscrizione nell'anteprima. Dopo aver eseguito il provisioning della sottoscrizione, verranno visualizzate le nuove SKU del gateway in tutte le aree di Azure AZ. 

Assicurarsi di aver effettuato l'accesso all'account Azure e di usare la sottoscrizione che si vuole inserire nell'elenco degli elementi consentiti per questa versione di anteprima. Per eseguire la registrazione, usare l'esempio seguente:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Per verificare che la funzionalità 'AllowVMSSVirtualNetworkGateway' sia registrata con la sottoscrizione, usare il comando seguente:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Il risultato sarà simile all'esempio seguente:

![Con provisioning](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. Dichiarare le variabili

I valori usati per i passaggi di esempio sono elencati di seguito. Inoltre, in alcuni passaggi degli esempi vengono usate variabili dichiarate. Se si usano questi passaggi nel proprio ambiente, assicurarsi di sostituire i valori indicati con i propri. Quando si specifica un percorso, verificare che l'area specificata sia supportata. Per altre informazioni, vedere la sezione [Domande frequenti](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>3. Creare la rete virtuale

Creare un gruppo di risorse.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Creare una rete virtuale.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>4. Aggiungere la subnet del gateway

La subnet del gateway contiene gli indirizzi IP riservati usati dai servizi del gateway di rete virtuale. Per aggiungere e impostare una subnet del gateway, usare gli esempi seguenti:

Aggiungere la subnet del gateway.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Impostare la configurazione della subnet del gateway per la rete virtuale.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5. Richiedere un indirizzo IP pubblico
 
In questo passaggio scegliere le istruzioni che si applicano al gateway da creare. La selezione delle zone per la distribuzione dei gateway dipende dalle zone specificate per l'indirizzo IP pubblico.

### <a name="ipzoneredundant"></a>Per i gateway con ridondanza della zona

Richiedere un indirizzo IP pubblico con una SKU indirizzo IP pubblico **Standard** e non specificare alcuna zona. In questo caso, l'indirizzo IP pubblico Standard creato sarà un IP pubblico con ridondanza della zona.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard
```

### <a name="ipzonalgw"></a>Per i gateway a livello di zona

Richiedere un indirizzo IP pubblico con una SKU indirizzo IP pubblico **Standard**. Specificare la zona (1, 2 o 3). Tutte le istanze del gateway verranno distribuite in questa zona.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Per i gateway a livello di area

Richiedere un indirizzo IP pubblico con una SKU indirizzo IP pubblico **Basic**. In questo caso, il gateway viene distribuito come gateway a livello di area e non dispone di alcuna ridondanza della zona integrata nel gateway. Le istanze del gateway vengono create rispettivamente in tutte le zone disponibili.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. Creare la configurazione dell'indirizzo IP

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Creare il gateway

Creare il gateway di rete virtuale.

>[!NOTE]
>In questo momento, è possibile specificare la SKU del gateway. La SKU viene impostata automaticamente sul valore ErGw1AZ per ExpressRoute e VpnGw1AZ per Gateway VPN.
>

### <a name="for-expressroute"></a>Per ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Per Gateway VPN

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Come fornire commenti e suggerimenti

I commenti e i suggerimenti degli utenti sono molto apprezzati. Inviare un messaggio di posta elettronica all'indirizzo aznetworkgateways@microsoft.com per segnalare eventuali problemi o fornire commenti e suggerimenti (positivi o negativi) relativi ai gateway VPN ed ExpressRoute con ridondanza della zona e a livello di zona. Includere il nome della propria società tra parentesi quadre "[ ]" nella riga dell'oggetto. Se si sta segnalando un problema, includere anche l'ID sottoscrizione.

## <a name="faq"></a>Domande frequenti

### <a name="how-do-i-sign-up-for-the-preview"></a>Com'è possibile iscriversi per la versione di anteprima?

È possibile eseguire l'[autoregistrazione](#enroll) usando i comandi PowerShell disponibili in questo articolo.

### <a name="what-will-change-when-i-enroll"></a>Cosa comporta la registrazione?

Nella versione di anteprima è possibile distribuire i gateway con ridondanza della zona. Pertanto, tutte le istanze dei gateway verranno distribuite nelle zone di disponibilità di Azure e ogni zona di disponibilità avrà un dominio di aggiornamento e di errore differente. In questo modo, i gateway saranno più affidabili, disponibili e resilienti agli errori di zona.

### <a name="what-regions-are-available-for-the-preview"></a>Quali aree sono disponibili nella versione di anteprima?

I gateway con ridondanza della zona e a livello di zona sono disponibili nelle aree di produzione pubbliche di Azure.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>È previsto un sistema di fatturazione per la partecipazione alla versione di anteprima?

Non è prevista alcuna fatturazione per i gateway durante la versione di anteprima. Tuttavia, insieme alla distribuzione non è previsto alcun contratto di servizio. I commenti e i suggerimenti degli utenti sono molto apprezzati.

> [!NOTE]
> Per i gateway ExpressRoute, al gateway non vengono applicati costi aggiuntivi o un sistema di fatturazione. Tuttavia, sarà applicata la fatturazione al circuito (non al gateway).

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>In quali aree è possibile provare la versione di anteprima?

La versione di anteprima pubblica è disponibile nelle aree degli Stati Uniti centrali e della Francia centrale (aree di Azure con zone di disponibilità disponibili a livello generale). In una fase successiva, i gateway con ridondanza della zona saranno disponibili in altre aree pubbliche di Azure.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>È possibile modificare i gateway di rete virtuale esistenti in gateway con ridondanza della zona o a livello di zona?

La migrazione dei gateway di rete virtuale esistenti in gateway con ridondanza della zona o a livello di zona attualmente non è supportata. È tuttavia possibile eliminare il gateway esistente e ricrearne uno con ridondanza della zona o a livello di zona.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>È possibile distribuire i gateway VPN ed ExpressRoute nella stessa rete virtuale?

La coesistenza di gateway VPN ed ExpressRoute nella stessa rete virtuale è supportata durante la versione di anteprima pubblica. Tenere tuttavia presenti i requisiti e le limitazioni seguenti:

* Riservare un intervallo di indirizzi IP /27 per la subnet del gateway.
* I gateway ExpressRoute con ridondanza della zona o a livello di zona possono coesistere solo con i gateway VPN con ridondanza della zona o a livello di zona.
* Distribuire il gateway ExpressRoute con ridondanza della zona o a livello di zona prima di distribuire il gateway VPN con ridondanza della zona o a livello di zona.
* Un gateway EspressRoute con ridondanza della zona o a livello di zona può essere collegato con 4 circuiti al massimo.

## <a name="next-steps"></a>Passaggi successivi

I commenti e i suggerimenti degli utenti sono molto apprezzati. Inviare un messaggio di posta elettronica all'indirizzo aznetworkgateways@microsoft.com per segnalare eventuali problemi o fornire commenti e suggerimenti (positivi o negativi) relativi ai gateway VPN ed ExpressRoute con ridondanza della zona e a livello di zona. Includere il nome della propria società tra parentesi quadre "[ ]" nella riga dell'oggetto. Se si sta segnalando un problema, includere anche l'ID sottoscrizione.