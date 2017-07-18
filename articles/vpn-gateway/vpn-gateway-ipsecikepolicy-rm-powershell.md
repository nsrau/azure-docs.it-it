---
title: 'Configurare i criteri IPsec/IKE per le connessioni VPN da sito a sito o da rete virtuale a rete virtuale: Azure Resource Manager: PowerShell | Microsoft Docs'
description: In questo articolo viene illustrata la configurazione di criteri IPsec/IKE per connessioni da sito a sito o da rete virtuale a rete virtuale con i gateway VPN di Azure tramite Azure Resource Manager e PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: yushwang
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 12502c91f7dff01651e3edcfd1dfa6a9d5ffe234
ms.contentlocale: it-it
ms.lasthandoff: 06/15/2017


---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Configurare criteri IPsec/IKE per connessioni VPN da sito a sito o da rete virtuale a rete virtuale

Questo articolo illustra i passaggi per configurare criteri IPsec/IKE per connessioni VPN da sito a sito o da rete virtuale a rete virtuale usando il modello di distribuzione Resource Manager e PowerShell.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Informazioni sui parametri di criteri IPsec e IKE per gateway VPN di Azure
Lo standard di protocollo IPsec e IKE supporta un'ampia gamma di algoritmi di crittografia in varie combinazioni. Vedere l'articolo [About cryptographic requirements and Azure VPN gateways](vpn-gateway-about-compliance-crypto.md) (Informazioni sui requisiti di crittografia e i gateway VPN di Azure) per informazioni su come questo può contribuire ad assicurare che la connettività cross-premise e da rete virtuale a rete virtuale soddisfi i requisiti di conformità o sicurezza.

Questo articolo fornisce istruzioni per creare e configurare un criterio IPsec/IKE e applicarlo a una connessione nuova o esistente:

* [Parte 1: Flusso di lavoro per creare e impostare criteri IPsec/IKE](#workflow)
* [Parte 2: Algoritmi di crittografia supportati e vantaggi chiave](#params)
* [Parte 3: Creare una nuova connessione VPN da sito a sito con criteri IPsec/IKE](#crossprem)
* [Parte 4: Creare una nuova connessione da rete virtuale a rete virtuale con criteri IPsec/IKE](#vnet2vnet)
* [Parte 5: Gestire (creare, aggiungere, rimuovere) criteri IPsec/IKE per una connessione](#managepolicy)

> [!IMPORTANT]
> 1. Notare che i criteri IPsec/IKE funzionano solo nei gateway VPN ***Standard*** e ***Prestazioni elevate*** ***basati su route*** di Azure.
> 2. Per una determinata connessione è possibile specificare ***una*** sola combinazione di criteri.
> 3. È necessario specificare tutti gli algoritmi e i parametri sia per IKE (modalità principale) che per IPsec (modalità rapida). Non è consentito specificare criteri parziali.
> 4. Consultare le specifiche del fornitore del dispositivo VPN per verificare che i criteri siano supportati nei dispositivi VPN locali. Non è possibile stabilire connessioni da sito a sito o da rete virtuale a rete virtuale se i criteri non sono compatibili.

## <a name ="workflow"></a>Parte 1: Flusso di lavoro per creare e impostare criteri IPsec/IKE
Questa sezione descrive il flusso di lavoro per creare e aggiornare i criteri IPsec/IKE per una connessione VPN da sito a sito o da rete virtuale a rete virtuale:
1. Creare una rete virtuale e un gateway VPN
2. Creare un gateway di rete locale per una connessione cross-premises o un'altra rete virtuale e gateway per una connessione da rete virtuale a rete virtuale
3. Creare un criterio IPsec/IKE con gli algoritmi e i parametri selezionati
4. Creare una connessione (IPsec o da rete virtuale a rete virtuale) con il criterio IPsec/IKE
5. Aggiungere, aggiornare o rimuovere un criterio IPsec/IKE per una connessione esistente

Le istruzioni di questo articolo impostano e configurano criteri IPsec/IKE come illustrato nel diagramma:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>Parte 2: Algoritmi di crittografia supportati e vantaggi chiave

La tabella seguente riporta l'elenco degli algoritmi di crittografia e dei tipi di attendibilità della chiave supportati e configurabili dai clienti.

| **IPsec/IKEv2**  | **Opzioni**                                                                 |
| ---              | ---                                                                         |
| Crittografia IKEv2 | AES256, AES192, AES128, DES3, DES                                           |
| Integrità IKEv2  | SHA384, SHA256, SHA1, MD5                                                   |
| Gruppo DH         | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Crittografia IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None    |
| Integrità IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                          |
| Gruppo PFS        | ECP384, ECP256, PFS24, PFS2048, PFS14, PFS2, PFS1, None                     |
| Durata associazione di sicurezza in modalità rapida*  | Secondi (integer) e kilobyte (integer)                                      |
| Selettore di traffico | UsePolicyBasedTrafficSelectors** ($True/$False - default $False)            |
|                  |                                                                             |

> [!NOTE]
> * (*) Nei gateway VPN di Azure, la durata dell'associazione di sicurezza IKEv2 (modalità principale) è fissata a 28.800 secondi.
> * (**) Impostando "UsePolicyBasedTrafficSelectors" su $True per una connessione, si configura il gateway VPN di Azure per la connessione al firewall VPN basato su criteri locale. Se si abilita PolicyBasedTrafficSelectors, è necessario verificare che i selettori di traffico corrispondenti siano definiti nel dispositivo VPN con tutte le combinazioni dei prefissi della rete locale (gateway di rete locale) da/verso i prefissi della rete virtuale di Azure, anziché any-to-any. Se i prefissi della rete locale sono 10.1.0.0/16 e 10.2.0.0/16 e i prefissi della rete virtuale sono 192.168.0.0/16 e 172.16.0.0/16, ad esempio, è necessario specificare i selettori di traffico seguenti:
>   * 10.1.0.0/16 <====> 192.168.0.0/16
>   * 10.1.0.0/16 <====> 172.16.0.0/16
>   * 10.2.0.0/16 <====> 192.168.0.0/16
>   * 10.2.0.0/16 <====> 172.16.0.0/16

Per altri dettagli sui selettori di traffico basati su criteri, vedere l'articolo su come [connettere più dispositivi VPN basati su criteri locali](vpn-gateway-connect-multiple-policybased-rm-ps.md).

## <a name ="crossprem"></a>Parte 3: Creare una nuova connessione VPN da sito a sito con criteri IPsec/IKE

Questa sezione illustra i passaggi per la creazione di una connessione VPN da sito a sito con un criterio IPsec/IKE. La procedura seguente crea la connessione come illustrato nel diagramma:

![s2s-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Per altre istruzioni più dettagliate per la creazione di una connessione VPN da sito a sito, vedere [Creare una connessione VPN da sito a sito](vpn-gateway-create-site-to-site-rm-powershell.md).

### <a name="before-you-begin"></a>Prima di iniziare
* Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
* È necessario installare i cmdlet di PowerShell per Gestione risorse di Azure. Per altre informazioni sull'installazione di cmdlet PowerShell, vedere [Overview of Azure PowerShell](/powershell/azure/overview) (Panoramica di Azure PowerShell).

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Passaggio 1: Creare la rete virtuale, il gateway VPN e il gateway di rete locale

#### <a name="1-declare-your-variables"></a>1. Dichiarare le variabili
Per questo esercizio, si inizierà dichiarando le variabili. Assicurarsi di sostituire i valori con quelli reali durante la configurazione per la produzione.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Connettersi alla sottoscrizione e creare un nuovo gruppo di risorse
Verificare di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Creare la rete virtuale, il gateway VPN e il gateway di rete locale
L'esempio seguente crea la rete virtuale, TestVNet1, con tre subnet e il gateway VPN. Quando si sostituiscono i valori, è importante che la subnet gateway venga denominata sempre esattamente GatewaySubnet. Se si assegnano altri nomi, la creazione del gateway avrà esito negativo.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---creat-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Passaggio 2: Creare una connessione VPN da sito a sito con un criterio IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Creare criteri IPsec/IKE
Lo script di esempio seguente crea un criterio IPsec/IKE con gli algoritmi e i parametri seguenti:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS24, durata dell'associazione di sicurezza 7200 secondi e 2048 KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Creare la connessione VPN da sito a sito con i criteri IPsec/IKE
Creare una connessione VPN da sito a sito e applicare il criterio IPsec/IKE creato prima.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

È possibile aggiungere facoltativamente "-UsePolicyBasedTrafficSelectors $True" al cmdlet di creazione della connessione per abilitare il gateway VPN di Azure per la connessione a dispositivi VPN basati su criteri locali, come descritto sopra.

> [!IMPORTANT]
> Dopo aver specificato un criterio IPsec/IKE per una connessione, il gateway VPN di Azure invierà o accetterà la proposta IPsec/IKE con gli algoritmi di crittografia e i principali vantaggi specificati per una particolare connessione. È necessario assicurarsi che il dispositivo VPN locale per la connessione utilizzi o accetti l'esatta combinazione di criteri, altrimenti non sarà stabilito il tunnel VPN da sito a sito.


## <a name ="vnet2vnet"></a>Parte 4: Creare una nuova connessione da rete virtuale a rete virtuale con criteri IPsec/IKE
La procedura di creazione di una connessione da rete virtuale a rete virtuale con un criterio IPsec/IKE è simile a quella di una connessione VPN da sito a sito. Gli script di esempio seguenti creano la connessione come illustrato nel diagramma:

![v2v-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Per i passaggi dettagliati necessari per creare una connessione da rete virtuale a rete virtuale, vedere [Creare una da rete virtuale a rete virtuale](vpn-gateway-vnet-vnet-rm-ps.md) . È necessario completare la [Parte 3](#crossprem) per creare e configurare TestVNet1 e il gateway VPN. 

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a>Passaggio 1: Creare la seconda rete virtuale e un gateway VPN

#### <a name="1-declare-your-variables"></a>1. Dichiarare le variabili
Sostituire i valori con quelli desiderati per la propria configurazione.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Creare la seconda rete virtuale e un gateway VPN nel nuovo gruppo di risorse
```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---creat-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Passaggio 2: Creare una connessione da rete virtuale a rete virtuale con il criterio IPsec/IKE
Come per la connessione VPN da sito a sito, creare un criterio IPsec/IKE e quindi applicare i criteri alla nuova connessione.

#### <a name="1-create-an-ipsecike-policy"></a>1. Creare criteri IPsec/IKE
Lo script di esempio seguente crea un diverso criterio IPsec/IKE con gli algoritmi e i parametri seguenti:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, durata dell'associazione di sicurezza 7200 secondi e 4096 KB

```powershell
$ipsecpolicy2 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 4096
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Creare connessioni da rete virtuale a rete virtuale con criteri IPsec/IKE
Creare una connessione da rete virtuale a rete virtuale e applicare il criterio IPsec/IKE creato in precedenza. In questo esempio entrambi i gateway sono nella stessa sottoscrizione. È possibile creare e configurare entrambe le connessioni con lo stesso criterio IPsec/IKE nella stessa sessione di PowerShell.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Dopo aver specificato un criterio IPsec/IKE per una connessione, il gateway VPN di Azure invierà o accetterà la proposta IPsec/IKE con gli algoritmi di crittografia e i principali vantaggi specificati per una particolare connessione. È necessario verificare che i criteri IPsec per entrambe le connessioni siano gli stessi, in caso contrario la connessione da rete virtuale a rete virtuale non sarà stabilita.

Dopo aver completato questi passaggi, la connessione sarà stabilita in pochi minuti e si avrà la topologia di rete seguente come illustrato all'inizio:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>Parte 5: Aggiornare i criteri IPsec/IKE per una connessione
L'ultima sezione mostrerà come gestire il criterio IPsec/IKE per una connessione da sito a sito o da rete virtuale a rete virtuale esistente. La procedura descritta di seguito illustra le operazioni seguenti su una connessione:

1. Mostrare il criterio IPsec/IKE di una connessione
2. Aggiungere o aggiornare il criterio IPsec/IKE per una connessione
3. Rimuovere il criterio IPsec/IKE da una connessione

Gli stessi passaggi si applicano sia alle connessioni da sito a sito sia alle connessioni da rete virtuale a rete virtuale.

> [!IMPORTANT]
> Il criterio IPsec/IKE è supportato solo nei gateway VPN *Standard* e *Prestazioni elevate* basati su route di Azure. Non funziona sullo SKU per il gateway Basic o il gateway VPN basato su criteri.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Mostrare il criterio IPsec/IKE di una connessione
L'esempio seguente illustra come ottenere il criterio IPsec/IKE configurato su una connessione. Gli script continuano dagli esercizi precedenti.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

L'ultimo comando elencherà il criterio IPsec/IKE corrente configurato per la connessione se esiste. L'output di esempio illustrato di seguito per la connessione:

    SALifeTimeSeconds   : 3600
    SADataSizeKilobytes : 2048
    IpsecEncryption     : AES256
    IpsecIntegrity      : SHA256
    IkeEncryption       : AES256
    IkeIntegrity        : SHA384
    DhGroup             : DHGroup24
    PfsGroup            : PFS24

Se non c'è alcun criterio IPsec/IKE configurato, il comando (PS > $connection6.policy) non restituirà nulla. Ciò non significa che IPsec/IKE non sia configurato per la connessione, ma che non c'è alcun criterio IPsec/IKE personalizzato. La connessione effettiva userà il criterio predefinito negoziato tra il dispositivo VPN locale e il gateway VPN di Azure.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Aggiungere o aggiornare un criterio IPsec/IKE per una connessione
La procedura per aggiungere un nuovo criterio o aggiornare un criterio esistente per una connessione è la stessa: creare un nuovo criterio, quindi applicare il nuovo criterio alla connessione.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup None -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Per abilitare "UsePolicyBasedTrafficSelectors" quando ci si connette a un dispositivo VPN basato su criteri locale, aggiungere il parametro "-UsePolicyBaseTrafficSelectors" al cmdlet o impostarlo su $False per disabilitare l'opzione:

```powershell
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

È possibile ottenere di nuovo la connessione per verificare se i criteri sono aggiornati.

```powershell
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Verrà visualizzato l'output dell'ultima riga come illustrato di seguito:

    SALifeTimeSeconds   : 3600
    SADataSizeKilobytes : 2048
    IpsecEncryption     : GCMAES128
    IpsecIntegrity      : GCMAES128
    IkeEncryption       : AES128
    IkeIntegrity        : SHA1
    DhGroup             : DHGroup14--
    PfsGroup            : None

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Rimuovere un criterio IPsec/IKE da una connessione
Dopo la rimozione dei criteri personalizzati da una connessione, il gateway VPN di Azure ripristinerà l'[elenco predefinito delle proposte IPsec/IKE](vpn-gateway-about-vpn-devices.md) e rinegozierà l'handshake IKE con il dispositivo VPN locale.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Anche in questo caso è possibile usare lo stesso script precedente per verificare se il criterio è stato rimosso dalla connessione.

## <a name="next-steps"></a>Passaggi successivi
Per altri dettagli sui selettori di traffico basati su criteri, vedere l'articolo su come [connettere più dispositivi VPN basati su criteri locali](vpn-gateway-connect-multiple-policybased-rm-ps.md).

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare la prima macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

