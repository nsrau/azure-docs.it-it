---
title: Configurare le connessioni VPN site-to-site di Azure Stack | Microsoft Docs
description: Informazioni sui criteri IPsec/IKE per connessioni VNet-to-VNet o VPN site-to-site, in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: e9e24bd19243f13b89d54f0105195b5c17505925
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251509"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-or-vnet-to-vnet-connections"></a>Configurare i criteri IPsec/IKE per connessioni VNet-to-VNet o VPN site-to-site

Questo articolo descrive i passaggi per configurare un criterio IPsec/IKE per VPN site-to-site (S2S) le connessioni in Azure Stack.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>Parametri dei criteri IPsec e IKE per gateway VPN

Il protocollo IPsec e IKE standard supporta un'ampia gamma di algoritmi di crittografia in varie combinazioni. Per informazioni su quali parametri sono supportati in Azure Stack, vedere [parametri IPsec/IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), che può aiutare a soddisfare i requisiti di conformità o sicurezza.

Questo articolo fornisce istruzioni su come creare e configurare un criterio IPsec/IKE e applicarlo a una connessione nuova o esistente.

## <a name="considerations"></a>Considerazioni

Quando si usano questi criteri, tenere presenti le considerazioni importanti seguenti:

- Il criterio IPsec/IKE funziona solo sul *Standard* e *HighPerformance* SKU del gateway (basato su route).

- È possibile specificare solo **uno** combinazione di criteri per una determinata connessione.

- È necessario specificare tutti gli algoritmi e i parametri sia per IKE (modalità principale) che per IPsec (modalità rapida). Non è consentito specificare criteri parziali.

- Consultare le specifiche del fornitore del dispositivo VPN per verificare che i criteri siano supportati dai dispositivi VPN locali. Le connessioni Site-to-site non è possibile stabilire se i criteri non sono compatibili.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>Parte 1: flusso di lavoro per creare e impostare criteri IPsec/IKE

In questa sezione illustra il flusso di lavoro necessario per creare e aggiornare i criteri IPsec/IKE per una connessione VPN site-to-site:

1. Creare una rete virtuale e un gateway VPN.

2. Creare un gateway di rete locale per la connessione cross-premise.

3. Creare un criterio IPsec/IKE con gli algoritmi selezionati e i parametri.

4. Creare una connessione IPSec con i criteri IPsec/IKE.

5. Aggiungere, aggiornare o rimuovere un criterio IPsec/IKE per una connessione esistente.

Le istruzioni riportate in questo articolo fornisce istruzioni impostare e configurare i criteri IPsec/IKE, come illustrato nella figura seguente:

![Installare e configurare i criteri IPsec/IKE](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Complessità delle chiavi e algoritmi di crittografia supportato-parte 2:

La tabella seguente elenca i algoritmi di crittografia supportati e delle forze della chiave configurabili dai clienti di Azure Stack:

| IPsec/IKEv2                                          | Opzioni                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| Crittografia IKEv2                                     | AES256, AES192, AES128, DES3, DES                                        |
| Integrità IKEv2                                      | SHA384, SHA256, SHA1, MD5                                                |
| Gruppo DH                                             | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None         |
| Crittografia IPsec                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None |
| Integrità IPsec                                      | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                       |
| Gruppo PFS                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, None                         |
| Durata associazione di sicurezza in modalità rapida                                       | (Facoltativo: i valori predefiniti vengono usati se non specificati)<br />                         Secondi (integer; min. 300/default salifetimeseconds è 27000 secondi)<br />                         Kbyte (integer; min. 1024/default 102400000 KB) |                                                                          |
| Selettore di traffico                                     | Selettori di traffico basati su criteri non sono supportati in Azure Stack.         |

- La configurazione del dispositivo VPN locale deve contenere o corrispondere agli algoritmi e ai parametri seguenti specificati nei criteri IPsec/IKE di Azure:

  - Algoritmo di crittografia IKE (modalità principale / fase 1)
  - Algoritmo di integrità IKE (modalità principale / fase 1)
  - Gruppo DH (modalità principale / fase 1)
  - Algoritmo di crittografia IPsec (modalità rapida / fase 2)
  - Algoritmo di integrità IPsec (modalità rapida / fase 2)
  - Gruppo PFS (modalità rapida / fase 2)
  - La durata delle associazioni di sicurezza è una specifica locale. Non è necessaria la corrispondenza.

- Se GCMAES viene usato come algoritmo di crittografia IPsec, è necessario selezionare stesso algoritmo GCMAES e la lunghezza della chiave per l'integrità IPsec; ad esempio, gcmaes128 per entrambi.

- Nella tabella precedente:

  - IKEv2 corrisponde alla modalità principale / fase 1
  - IPsec corrisponde alla modalità rapida / fase 2
  - Gruppo DH specifica il gruppo Diffie-Hellman usato in modalità principale o fase 1
  - Gruppo PFS specifica il gruppo Diffie-Hellman usato in modalità rapida o fase 2

- Durata associazione di sicurezza in modalità principale IKEv2 è fissata a 28.800 secondi nei gateway VPN di Azure Stack.

La tabella seguente elenca i gruppi di Diffie-Hellman corrispondenti supportati dal criterio personalizzato:

| Diffie-Hellman Group | DHGroup   | PFSGroup      | Lunghezza chiave    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | MODP a 768 bit  |
| 2                    | DHGroup2  | PFS2          | MODP a 1024 bit |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | MODP a 2048 bit |
| 19                   | ECP256    | ECP256        | ECP a 256 bit   |
| 20                   | ECP384    | ECP384        | ECP a 384 bit   |
| 24                   | DHGroup24 | PFS24         | MODP a 2048 bit |

Per altre informazioni, vedere [RFC3526](https://tools.ietf.org/html/rfc3526) e [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Parte 3: creare una nuova connessione VPN site-to-site con criteri IPsec/IKE

In questa sezione illustra i passaggi per creare una connessione VPN site-to-site con un criterio IPsec/IKE. La procedura seguente crea la connessione, come illustrato nella figura seguente:

![sito-a-sito-policy](media/azure-stack-vpn-s2s/site-to-site.png)

Per informazioni dettagliate fornite istruzioni dettagliate per la creazione di una connessione VPN site-to-site, vedere [creare una connessione VPN site-to-site](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

### <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di avere i prerequisiti seguenti:

- Una sottoscrizione di Azure. Se non si ha già una sottoscrizione di Azure, è possibile attivare i [benefici per sottoscrittori MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), oppure iscriversi per una [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

- I cmdlet di PowerShell per Azure Resource Manager. Visualizzare [installazione di PowerShell per Azure Stack](../azure-stack-powershell-install.md) per altre informazioni sull'installazione dei cmdlet di PowerShell.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Passaggio 1: Creare la rete virtuale, il gateway VPN e il gateway di rete locale

#### <a name="1-declare-variables"></a>1. Dichiarare le variabili

Per questo esercizio, inizierà dichiarando le variabili seguenti. Assicurarsi di sostituire i segnaposto con i propri valori durante la configurazione per la produzione:

```powershell
$Sub1 = "\<YourSubscriptionName\>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Connettersi alla sottoscrizione e creare un nuovo gruppo di risorse

Verificare di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse. Per altre informazioni, vedere [connettersi ad Azure Stack con PowerShell come utente](azure-stack-powershell-configure-user.md).

Aprire la console di PowerShell e connettersi al proprio account. Per connettersi, usare l'esempio seguente:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Creare la rete virtuale, il gateway VPN e il gateway di rete locale

L'esempio seguente crea la rete virtuale, **TestVNet1**, con tre subnet e il gateway VPN. Quando si sostituiscono i valori, è importante che venga denominata sempre la subnet del gateway in particolare **GatewaySubnet**. Se si assegnano altri nomi, la creazione del gateway ha esito negativo.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Passaggio 2: creare una connessione VPN site-to-site con un criterio IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Creare un criterio IPsec/IKE

Questo script di esempio crea un criterio IPsec/IKE con gli algoritmi e seguenti parametri:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, none, SA durata 14.400 secondi e 102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Se si usa GCMAES per IPsec, è necessario usare lo stesso algoritmo e la stessa lunghezza della chiave GCMAES per la crittografia e l'integrità IPsec.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. Creare la connessione VPN site-to-site con il criterio IPsec/IKE

Creare una connessione VPN site-to-site e applicare i criteri IPsec/IKE creato in precedenza.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Dopo aver specificato un criterio IPsec/IKE per una connessione, il gateway VPN di Azure invierà o accetterà la proposta IPsec/IKE con gli algoritmi di crittografia e i principali vantaggi specificati per una particolare connessione. Assicurarsi che il dispositivo VPN locale per la connessione usi o accetti l'esatta combinazione di criteri, in caso contrario che non verrà stabilito il tunnel VPN site-to-site.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Parte 4 - criteri di aggiornamento IPsec/IKE per una connessione

La sezione precedente è stata illustrata come gestire i criteri IPsec/IKE per una connessione site-to-site esistente. La sezione seguente illustra le operazioni seguenti su una connessione:

1. Mostrare il criterio IPsec/IKE di una connessione
2. Aggiungere o aggiornare il criterio IPsec/IKE per una connessione
3. Rimuovere il criterio IPsec/IKE da una connessione

> [!NOTE]
> Criteri IPsec/IKE sono supportati nei *Standard* e *HighPerformance* basato su route unicamente nei gateway VPN. Non funziona nel *base* SKU del gateway.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Mostrare il criterio IPsec/IKE di una connessione

L'esempio seguente illustra come ottenere il criterio IPsec/IKE configurato su una connessione. Gli script continuano dagli esercizi precedenti:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

L'ultimo comando Elenca i criteri IPsec/IKE correnti configurati per la connessione, se presente. L'esempio seguente è un esempio di output per la connessione:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Se è presente alcun criterio IPsec/IKE configurato, il comando `$connection6.policy` restituisce. Questo non significa che IPsec/IKE non è configurato per la connessione; significa che non sono presenti criteri IPsec/IKE personalizzati. La connessione effettiva usa il criterio predefinito negoziato tra il dispositivo VPN locale e il gateway VPN di Azure.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Aggiungere o aggiornare un criterio IPsec/IKE per una connessione

La procedura per aggiungere un nuovo criterio o aggiornare un criterio esistente per una connessione è lo stesso: creare un nuovo criterio, quindi applicare il nuovo criterio alla connessione.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

È possibile ottenere la connessione per verificare se i criteri sono aggiornati:

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Verrà visualizzato l'output dell'ultima riga, come illustrato nell'esempio seguente:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Rimuovere un criterio IPsec/IKE da una connessione

Dopo la rimozione dei criteri personalizzati da una connessione, il gateway VPN di Azure ripristinerà il [proposta IPsec/IKE predefiniti](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)e negozia con il dispositivo VPN locale.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = “AzS123”
$currentpolicy = $connection6.IpsecPolicies\[0\]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

È possibile usare lo stesso script per verificare se il criterio è stato rimosso dalla connessione.

## <a name="next-steps"></a>Passaggi successivi

- [Impostazioni di configurazione di gateway VPN di Azure Stack](azure-stack-vpn-gateway-settings.md)
