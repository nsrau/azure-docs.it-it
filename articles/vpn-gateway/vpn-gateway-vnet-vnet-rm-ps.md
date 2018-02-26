---
title: 'Connettere una rete virtuale di Azure a un''altra rete virtuale tramite una connessione da rete virtuale a rete virtuale: PowerShell | Microsoft Docs'
description: Connettere reti virtuali tra loro tramite una connessione da rete virtuale a rete virtuale e PowerShell.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 8f2ecf4f10c5506b79adb033c67c41a4b1e17777
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Configurare una connessione gateway VPN tra reti virtuali usando PowerShell

Questo articolo descrive come connettere reti virtuali tramite il tipo di connessione da rete virtuale a rete virtuale. Le reti virtuali possono trovarsi in aree geografiche uguali o diverse e in sottoscrizioni uguali o diverse. Quando si connettono reti virtuali da sottoscrizioni diverse, non è necessario che le sottoscrizioni siano associate allo stesso tenant di Active Directory.

I passaggi di questo articolo sono applicabili al modello di distribuzione Resource Manager e usano PowerShell. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfaccia della riga di comando di Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Connettersi tra modelli di distribuzione diversi - Portale di Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Connettersi tra modelli di distribuzione diversi - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about"></a>Informazioni sulla connessione di reti virtuali

Ci sono diversi modi per connettere le reti virtuali. Le sezioni seguenti descrivono i diversi metodi di connessione delle reti virtuali.

### <a name="vnet-to-vnet"></a>Tra reti virtuali

La configurazione di una connessione da rete virtuale a rete virtuale rappresenta un buon metodo per connettere facilmente le reti virtuali. La connessione di una rete virtuale a un'altra rete virtuale tramite il tipo di connessione da rete virtuale a rete virtuale (VNet2VNet) è simile alla creazione di una connessione IPsec da sito a sito in una posizione locale.  Entrambi i tipi di connettività usano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE ed entrambi funzionano in modo analogo durante la comunicazione. La differenza tra i tipi di connessione è costituita dal modo in cui viene configurato il gateway di rete locale. Quando si crea una connessione da rete virtuale a rete virtuale, non viene visualizzato lo spazio indirizzi del gateway di rete locale, che viene creato e popolato automaticamente. Se si aggiorna lo spazio indirizzi per una rete virtuale, l'altra rete virtuale eseguirà automaticamente il routing allo spazio indirizzi aggiornato. La creazione di una connessione da rete virtuale a rete virtuale è in genere più veloce e semplice rispetto alla creazione di una connessione da sito a sito tra reti virtuali.

### <a name="site-to-site-ipsec"></a>Da sito a sito (IPsec)

Se si usa una configurazione di rete complessa, potrebbe essere preferibile connettersi alle reti virtuali usando la procedura di connessione [da sito a sito](vpn-gateway-create-site-to-site-rm-powershell.md) invece di quella di connessione da rete virtuale a rete virtuale. Con la procedura di connessione da sito a sito, è necessario creare e configurare manualmente i gateway di rete locali. Il gateway di rete locale per ogni rete virtuale considera l'altra rete virtuale come sito locale. Ciò consente di specificare uno spazio indirizzi aggiuntivo per il gateway di rete locale per il routing del traffico. Se lo spazio indirizzi per una rete virtuale cambia, è necessario aggiornare di conseguenza il gateway di rete locale corrispondente. L'aggiornamento non avviene automaticamente.

### <a name="vnet-peering"></a>Peering reti virtuali

È possibile connettere le reti virtuali tramite il peering reti virtuali. Il peering reti virtuali non usa un gateway VPN e presenta vincoli diversi. I [prezzi per il peering reti virtuali](https://azure.microsoft.com/pricing/details/virtual-network) vengono inoltre calcolati in modo diverso rispetto ai [prezzi per il gateway VPN da rete virtuale a rete virtuale](https://azure.microsoft.com/pricing/details/vpn-gateway). Per altre informazioni, vedere [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Vantaggi di una connessione da rete virtuale a rete virtuale

È possibile connettere le reti virtuali con una connessione da rete virtuale a rete virtuale per i motivi seguenti:

* **Presenza e ridondanza in più aree geografiche**

  * È possibile configurare la sincronizzazione o la replica geografica con connettività sicura senza passare da endpoint con connessione Internet.
  * Con Gestione traffico e il servizio di bilanciamento del carico di Azure è possibile configurare il carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Un esempio importante è rappresentato dalla configurazione SQL AlwaysOn con gruppi di disponibilità distribuiti in più aree di Azure.
* **Applicazioni multilivello in singole aree geografiche con isolamento o limite amministrativo**

  * All'interno di una stessa area è possibile configurare applicazioni multilivello con più reti virtuali connesse tra loro a causa dell'isolamento o di requisiti amministrativi.

La comunicazione tra reti virtuali può essere associata a configurazioni multisito. Questo permette di definire topologie di rete che consentono di combinare la connettività cross-premise con la connettività tra reti virtuali.

## <a name="steps"></a>Quale procedura per la connessione da rete virtuale a rete virtuale è preferibile seguire?

Questo articolo riporta due diverse procedure. Una per le [reti virtuali che si trovano nella stessa sottoscrizione](#samesub) e una per le [reti virtuali che si trovano in sottoscrizioni diverse](#difsub).
La differenza principale tra le due è costituita dal fatto che quando si configurano le connessioni per reti virtuali che si trovano in sottoscrizioni diverse, è necessario usare sessioni di PowerShell separate. 

Per questo esercizio è possibile combinare le configurazioni oppure sceglierne una da usare. Tutte le configurazioni usano il tipo di connessione da rete virtuale a rete virtuale. Il traffico di rete viene trasmesso tra le reti virtuali connesse direttamente tra loro. In questo esercizio, il traffico da TestVNet4 non viene indirizzato a TestVNet5.

* [Reti virtuali che si trovano nella stessa sottoscrizione](#samesub): la procedura per questa configurazione usa TestVNet1 e TestVNet4.

  ![Diagramma V2V](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [Reti virtuali che si trovano in sottoscrizioni diverse](#difsub): la procedura per questa configurazione usa TestVNet1 e TestVNet5.

  ![Diagramma V2V](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="samesub"></a>Come connettere reti virtuali che si trovano nella stessa sottoscrizione

### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare, è necessario installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager, almeno 4.0 o versione successiva. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

### <a name="Step1"></a>Passaggio 1: Pianificare gli intervalli di indirizzi IP

Nei passaggi seguenti vengono create due reti virtuali con le rispettive subnet del gateway e configurazioni. Viene quindi creata una connessione VPN tra le due reti virtuali. È importante pianificare gli intervalli di indirizzi IP per la configurazione di rete. Tenere presente che è necessario assicurarsi che nessuno di intervalli di rete virtuale o intervalli di rete locale si sovrappongano in alcun modo. In questi esempi non viene incluso un server DNS. Per usare la risoluzione dei nomi per le reti virtuali, vedere [Risoluzione dei nomi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Negli esempi vengono usati i valori seguenti:

**Valori per TestVNet1:**

* Nome della rete virtuale: TestVNet1
* Gruppo di risorse: TestRG1
* Location: Stati Uniti orientali
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* IP pubblico: VNet1GWIP
* VPNType: RouteBased
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5 (per reti virtuali in diverse sottoscrizioni)
* ConnectionType: VNet2VNet

**Valori per TestVNet4:**

* Nome della rete virtuale: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Gruppo di risorse: TestRG4
* Località: Stati Uniti occidentali
* GatewayName: VNet4GW
* IP pubblico: VNet4GWIP
* VPNType: RouteBased
* Connessione: VNet4toVNet1
* ConnectionType: VNet2VNet


### <a name="Step2"></a>Passaggio 2: Creare e configurare TestVNet1

1. Dichiarare le variabili. Nell'esempio seguente vengono dichiarate le variabili usando i valori per questo esercizio. Nella maggior parte dei casi è necessario sostituire i valori con quelli personalizzati. È tuttavia possibile usare queste variabili se si esegue la procedura per acquisire familiarità con questo tipo di configurazione. Modificare le variabili se necessario, quindi copiarle e incollarle nella console di PowerShell.

  ```powershell
  $Sub1 = "Replace_With_Your_Subcription_Name"
  $RG1 = "TestRG1"
  $Location1 = "East US"
  $VNetName1 = "TestVNet1"
  $FESubName1 = "FrontEnd"
  $BESubName1 = "Backend"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.11.0.0/16"
  $VNetPrefix12 = "10.12.0.0/16"
  $FESubPrefix1 = "10.11.0.0/24"
  $BESubPrefix1 = "10.12.0.0/24"
  $GWSubPrefix1 = "10.12.255.0/27"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconf1"
  $Connection14 = "VNet1toVNet4"
  $Connection15 = "VNet1toVNet5"
  ```

2. Connettersi all'account. Per eseguire la connessione, usare gli esempi che seguono:

  ```powershell
  Login-AzureRmAccount
  ```

  Controllare le sottoscrizioni per l'account.

  ```powershell
  Get-AzureRmSubscription
  ```

  Specificare la sottoscrizione da usare.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub1
  ```
3. Creare un nuovo gruppo di risorse.

  ```powershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```
4. Creare le configurazioni di subnet per TestVNet1. L'esempio seguente mostra come creare una rete virtuale denominata TestVNet1 e tre subnet, denominate rispettivamente GatewaySubnet, FrontEnd e Backend. Quando si sostituiscono i valori, è importante che la subnet gateway venga denominata sempre esattamente GatewaySubnet. Se si assegnano altri nomi, la creazione del gateway ha esito negativo.

  L'esempio seguente fa uso delle variabili impostate in precedenza. In questo esempio, la subnet del gateway usa /27. Nonostante sia possibile creare una subnet del gateway con dimensioni di /29 soltanto, è consigliabile crearne una più grande che includa più indirizzi selezionando almeno /28 o /27. In questo modo saranno disponibili indirizzi sufficienti per supportare in futuro le possibili configurazioni aggiuntive desiderate.

  ```powershell
  $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
  $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
  $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
  ```
5. Creare TestVNet1.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
  ```
6. Richiedere un indirizzo IP pubblico da allocare per il gateway che verrà creato per la rete virtuale. Si noti che AllocationMethod è dinamico. Non è possibile specificare l'indirizzo IP che si desidera usare. Viene allocato in modo dinamico per il gateway. 

  ```powershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
  ```
7. Definire la configurazione del gateway. La configurazione del gateway definisce la subnet e l'indirizzo IP pubblico da utilizzare. Per creare la configurazione del gateway, usare l'esempio seguente.

  ```powershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```
8. Creare il gateway per TestVNet1. In questo passaggio viene creato il gateway di rete virtuale per TestVNet1. Le configurazioni da rete virtuale a rete virtuale richiedono un tipo di VpnType RouteBased. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dello SKU gateway selezionato.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-3---create-and-configure-testvnet4"></a>Passaggio 3 - Creare e configurare TestVNet4

Dopo aver configurato TestVNet1, creare TestVNet4. Eseguire la procedura seguente, sostituendo i valori con quelli personalizzati quando necessario. Questo passaggio può essere eseguito nella stessa sessione di PowerShell perché si trova nella stessa sottoscrizione.

1. Dichiarare le variabili. Sostituire i valori con quelli desiderati per la propria configurazione.

  ```powershell
  $RG4 = "TestRG4"
  $Location4 = "West US"
  $VnetName4 = "TestVNet4"
  $FESubName4 = "FrontEnd"
  $BESubName4 = "Backend"
  $GWSubName4 = "GatewaySubnet"
  $VnetPrefix41 = "10.41.0.0/16"
  $VnetPrefix42 = "10.42.0.0/16"
  $FESubPrefix4 = "10.41.0.0/24"
  $BESubPrefix4 = "10.42.0.0/24"
  $GWSubPrefix4 = "10.42.255.0/27"
  $GWName4 = "VNet4GW"
  $GWIPName4 = "VNet4GWIP"
  $GWIPconfName4 = "gwipconf4"
  $Connection41 = "VNet4toVNet1"
  ```
2. Creare un nuovo gruppo di risorse.

  ```powershell
  New-AzureRmResourceGroup -Name $RG4 -Location $Location4
  ```
3. Creare le configurazioni di subnet per TestVNet4.

  ```powershell
  $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
  $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
  $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
  ```
4. Creare TestVNet4.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
  ```
5. Richiedere un indirizzo IP pubblico.

  ```powershell
  $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AllocationMethod Dynamic
  ```
6. Definire la configurazione del gateway.

  ```powershell
  $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
  $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
  $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
  ```
7. Creare il gateway di TestVNet4. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dello SKU gateway selezionato.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
  -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-4---create-the-connections"></a>Passaggio 4: Creare le connessioni

1. Ottenere entrambi i gateway di rete virtuale. Se entrambi i gateway sono nella stessa sottoscrizione, come nell'esempio, è possibile completare questo passaggio nella stessa sessione di PowerShell.

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
  ```
2. Creare la connessione da TestVNet1 a TestVNet4. In questo passaggio viene creata la connessione da TestVNet1 a TestVNet4. Verrà visualizzata una chiave condivisa a cui si fa riferimento negli esempi. È possibile utilizzare i propri valori specifici per la chiave condivisa. L'importante è che la chiave condivisa corrisponda per entrambe le configurazioni. Il completamento della creazione di una connessione può richiedere un po' di tempo.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
3. Creare la connessione da TestVNet4 a TestVNet1. Questo passaggio è simile a quello precedente, ma riguarda la creazione della connessione da TestVNet4 a TestVNet1. Assicurarsi che le chiavi condivise corrispondano. Dopo alcuni minuti la connessione verrà stabilita.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
  -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. Verificare la connessione. Vedere la sezione [Come verificare una connessione](#verify).

## <a name="difsub"></a>Come connettere reti virtuali che si trovano in sottoscrizioni diverse

In questo scenario vengono connesse le reti virtuali TestVNet1 e TestVNet5, che si trovano in sottoscrizioni diverse. Non è necessario che le sottoscrizioni siano associate allo stesso tenant di Active Directory. La differenza tra questi passaggi e quelli del set precedente consiste nel fatto che una parte dei passaggi di configurazione deve essere eseguita in una sessione di PowerShell separata nel contesto della seconda sottoscrizione, soprattutto quando le due sottoscrizioni appartengono a organizzazioni diverse.

### <a name="step-5---create-and-configure-testvnet1"></a>Passaggio 5: Creare e configurare TestVNet1

È necessario completare il [passaggio 1](#Step1) e il [passaggio 2](#Step2) della sezione precedente per creare e configurare TestVNet1 e il gateway VPN per TestVNet1. Per questo configurazione, non è necessario creare TestVNet4 dalla sezione precedente, ma, se è già stata creata, non si verificheranno conflitti con questi passaggi. Al termine, continuare con il passaggio 6 per creare TestVNet5. 

### <a name="step-6---verify-the-ip-address-ranges"></a>Passaggio 6: Verificare gli intervalli di indirizzi IP

È importante assicurarsi che lo spazio di indirizzi IP della nuova rete virtuale, TestVNet5, non si sovrapponga ad altri di intervalli di rete virtuale o intervalli di gateway di rete locale. In questo esempio le reti virtuali possono appartenere a organizzazioni diverse. Per questo esercizio è possibile usare i valori seguenti per TestVNet5:

**Valori per TestVNet5:**

* Nome della rete virtuale: TestVNet5
* Gruppo di risorse: TestRG5
* Ubicazione: Giappone orientale
* TestVNet5: 10.51.0.0/16 e 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* IP pubblico: VNet5GWIP
* VPNType: RouteBased
* Connessione: VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>Passaggio 7: Creare e configurare TestVNet5

Questo passaggio deve essere eseguito nel contesto della nuova sottoscrizione. Questa parte può essere eseguita dall'amministratore in un'altra organizzazione che possiede la sottoscrizione.

1. Dichiarare le variabili. Sostituire i valori con quelli desiderati per la propria configurazione.

  ```powershell
  $Sub5 = "Replace_With_the_New_Subcription_Name"
  $RG5 = "TestRG5"
  $Location5 = "Japan East"
  $VnetName5 = "TestVNet5"
  $FESubName5 = "FrontEnd"
  $BESubName5 = "Backend"
  $GWSubName5 = "GatewaySubnet"
  $VnetPrefix51 = "10.51.0.0/16"
  $VnetPrefix52 = "10.52.0.0/16"
  $FESubPrefix5 = "10.51.0.0/24"
  $BESubPrefix5 = "10.52.0.0/24"
  $GWSubPrefix5 = "10.52.255.0/27"
  $GWName5 = "VNet5GW"
  $GWIPName5 = "VNet5GWIP"
  $GWIPconfName5 = "gwipconf5"
  $Connection51 = "VNet5toVNet1"
  ```
2. Connettersi alla sottoscrizione 5. Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

  ```powershell
  Login-AzureRmAccount
  ```

  Controllare le sottoscrizioni per l'account.

  ```powershell
  Get-AzureRmSubscription
  ```

  Specificare la sottoscrizione da usare.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub5
  ```
3. Creare un nuovo gruppo di risorse.

  ```powershell
  New-AzureRmResourceGroup -Name $RG5 -Location $Location5
  ```
4. Creare le configurazioni di subnet per TestVNet5.

  ```powershell
  $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
  $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
  $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
  ```
5. Creare TestVNet5.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
  -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
  ```
6. Richiedere un indirizzo IP pubblico.

  ```powershell
  $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
  -Location $Location5 -AllocationMethod Dynamic
  ```
7. Definire la configurazione del gateway.

  ```powershell
  $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
  $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
  $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
  ```
8. Creare il gateway di TestVNet5.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
  -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-8---create-the-connections"></a>Passaggio 8: Creare le connessioni

In questo esempio, dato che i gateway si trovano in sottoscrizioni diverse, il passaggio è stato suddiviso in due sessioni di PowerShell contrassegnate come [Sottoscrizione 1] e [Sottoscrizione 5].

1. **[Sottoscrizione 1]** Ottenere il gateway di rete virtuale per la Sottoscrizione 1. Accedere e connettersi alla sottoscrizione 1 prima di eseguire questo esempio:

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  ```

  Copiare l'output degli elementi seguenti e inviarli all'amministratore della Sottoscrizione 5 tramite posta elettronica o con un altro metodo.

  ```powershell
  $vnet1gw.Name
  $vnet1gw.Id
  ```

  Questi due elementi avranno valori simili all'esempio di output seguente:

  ```
  PS D:\> $vnet1gw.Name
  VNet1GW
  PS D:\> $vnet1gw.Id
  /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```
2. **[Sottoscrizione 5]** Ottenere il gateway di rete virtuale per la Sottoscrizione 5. Accedere e connettersi alla sottoscrizione 5 prima di eseguire questo esempio:

  ```powershell
  $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
  ```

  Copiare l'output degli elementi seguenti e inviarli all'amministratore della Sottoscrizione 1 tramite posta elettronica o con un altro metodo.

  ```powershell
  $vnet5gw.Name
  $vnet5gw.Id
  ```

  Questi due elementi avranno valori simili all'esempio di output seguente:

  ```
  PS C:\> $vnet5gw.Name
  VNet5GW
  PS C:\> $vnet5gw.Id
  /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```
3. **[Sottoscrizione 1]** Creare la connessione da TestVNet1 a TestVNet5. In questo passaggio viene creata la connessione da TestVNet1 a TestVNet5. La differenza in questo caso consiste nel fatto che non è possibile ottenere direttamente $vnet5gw perché si trova in una sottoscrizione diversa. Sarà necessario creare un nuovo oggetto di PowerShell con i valori comunicati dalla Sottoscrizione 1 nei passaggi precedenti. Usare l'esempio seguente. Sostituire il nome, l'ID e la chiave condivisa con i valori personalizzati. L'importante è che la chiave condivisa corrisponda per entrambe le configurazioni. Il completamento della creazione di una connessione può richiedere un po' di tempo.

  Connettersi alla sottoscrizione 1 prima di eseguire questo esempio:

  ```powershell
  $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet5gw.Name = "VNet5GW"
  $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
  $Connection15 = "VNet1toVNet5"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. **[Sottoscrizione 5]** Creare la connessione da TestVNet5 a TestVNet1. Questo passaggio è simile a quello precedente, ma riguarda la creazione della connessione da TestVNet5 a TestVNet1. Anche in questo caso vale lo stesso processo per la creazione di un oggetto di PowerShell in base ai valori ottenuti dalla Sottoscrizione 1. In questo passaggio assicurarsi che le chiavi condivise corrispondano.

  Connettersi alla sottoscrizione 5 prima di eseguire questo esempio:

  ```powershell
  $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet1gw.Name = "VNet1GW"
  $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
  $Connection51 = "VNet5toVNet1"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

## <a name="verify"></a>Come verificare una connessione

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="faq"></a>Domande frequenti relative alla connessione di reti virtuali

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali - Documentazione](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) .
* Per informazioni su BGP, vedere [Panoramica di BGP](vpn-gateway-bgp-overview.md) e [Come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).