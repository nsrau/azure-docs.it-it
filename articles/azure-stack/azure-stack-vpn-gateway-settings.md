---
title: Impostazioni del gateway VPN per lo Stack di Azure | Documenti Microsoft
description: "Informazioni sulle impostazioni per i gateway VPN che è utilizzare con lo Stack di Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: ed4a84965c37f66bbc7734f6043ad6f8f1666c1f
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2017
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Impostazioni di configurazione di gateway VPN di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Un gateway VPN è un tipo di gateway di rete virtuale che invia il traffico crittografato tra la rete virtuale nello Stack di Azure e un gateway VPN remoto. Il gateway VPN remoto può essere in Azure, un dispositivo nel Data Center o un dispositivo in un altro sito.  Se c'è connettività di rete tra due endpoint, è possibile stabilire una connessione VPN da sito a sito (S2S) protetta tra le due reti.

Una connessione di gateway VPN si basa sulla configurazione di più risorse, ognuna delle quali contiene impostazioni configurabili. Le sezioni di questo articolo descrivono le risorse e le impostazioni correlate a un gateway VPN per una rete virtuale creata nel modello di distribuzione Resource Manager. È possibile trovare le descrizioni e i diagrammi di topologia per ogni soluzione di connessione in [sul Gateway VPN per Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Impostazioni del gateway VPN

### <a name="gateway-types"></a>Tipi di gateway
Ogni rete virtuale di Azure Stack supporta un gateway di rete virtuale singola, che deve essere del tipo **Vpn**.  Questo comportamento è diverso da Azure, che supporta i tipi aggiuntivi.  

Quando si crea un gateway di rete virtuale, è necessario assicurarsi che il tipo di gateway sia corretto per la configurazione in uso. È necessario un gateway VPN di `-GatewayType Vpn`.

Esempio:
```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>SKU del gateway
Quando si crea un gateway di rete virtuale è necessario specificare il codice SKU del gateway da usare. Selezionare gli SKU che soddisfano i requisiti inerenti ai tipi di carichi di lavoro, alle velocità effettive, alle funzionalità e ai contratti di servizio.

>[!NOTE]
> Le reti virtuali di tipo classico devono continuare a usare gli SKU precedenti. Per altre informazioni sugli SKU di gateway di versione precedente, vedere [Working with virtual network gateway SKUs (old)](/azure/vpn-gateway/vpn-gateway-about-skus-legacy) (Uso degli SKU di gateway di rete virtuale - Versione precedente).

Stack di Azure offre il gateway VPN seguente SKU:

|   | Velocità effettiva Gateway VPN |Tunnel IPsec max Gateway VPN |
|-------|-------|-------|
|**SKU Basic**  | 100 Mbps  | 10    |
|**SKU standard**           | 100 Mbps  | 10    |
|**SKU con prestazioni elevate** | 200 Mbps    | 30    |

### <a name="resizing-gateway-skus"></a>Ridimensionamento SKU di gateway
Stack di Azure non supporta un ridimensionamento di SKU tra gli SKU legacy supportati.

Analogamente, Stack di Azure non supporta un ridimensionamento da un SKU legacy supportato (Basic, Standard e ad alte prestazioni), per gli SKU più recenti supportato da Azure (VpnGw1 VpnGw2 e VpnGw3).

### <a name="configure-the-gateway-sku"></a>Configurare lo SKU del gateway
#### <a name="azure-stack-portal"></a>Portale di Azure Stack
Se si usa il portale di Azure Stack per creare un gateway di rete virtuale di gestione delle risorse, è possibile selezionare il gateway SKU utilizzando l'elenco a discesa. Le opzioni disponibili corrispondono al tipo di gateway e al tipo di VPN selezionati.

#### <a name="powershell"></a>PowerShell
Nell'esempio di PowerShell seguente specifica GatewaySku - come VpnGw1.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```
### <a name="connection-types"></a>Tipi di connessione
Nel modello di distribuzione Resource Manager ogni configurazione richiede un tipo di connessione di gateway di rete virtuale specifico. I valori disponibili di PowerShell di gestione risorse per - ConnectionType sono:

- IPsec

Nell'esempio di PowerShell riportato di seguito viene creata una connessione S2S che richiede il tipo di connessione IPsec.  

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>Tipi di VPN
Quando si crea il gateway di rete virtuale per una configurazione di gateway VPN, è necessario specificare un tipo di VPN. Il tipo di VPN scelto dipende dalla topologia di connessione che si desidera creare.  Un tipo di VPN può anche dipendere dall'hardware usato. Le configurazioni S2S richiedono un dispositivo VPN. Alcuni dispositivi VPN supportano solo un determinato tipo di VPN.

> [!IMPORTANT]  
> In questo momento, Stack di Azure supporta solo il tipo VPN basato su Route.  Se il dispositivo supporta solo i criteri di reti VPN, le connessioni a tali dispositivi dallo Stack di Azure non sono supportate.

- **Basato su criteri**: *(supportato da Azure, ma non dallo Stack di Azure)* VPN basate su criteri di crittografare e indirizzare i pacchetti attraverso i tunnel IPsec in base ai criteri IPsec configurati con le combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure dello Stack. I criteri o selettori di traffico vengono in genere definiti come un elenco di accesso nella configurazione del dispositivo VPN.

- **Tipo RouteBased**: tipo RouteBased VPN utilizzare "routing" IP di inoltro o tabella di routing per i pacchetti diretti nelle interfacce tunnel corrispondenti. Le interfacce tunnel consentono quindi di crittografare o decrittografare i pacchetti all'interno e all'esterno dei tunnel. I criteri o il selettore di traffico per le VPN RouteBased vengono configurati come any-to-any (o caratteri jolly). Il valore per un tipo RouteBased VPN è di tipo RouteBased.

Nell'esempio di PowerShell seguente specifica VpnType - come tipo RouteBased. Quando si crea un gateway, è necessario assicurarsi che -VpnType sia corretto per la configurazione.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```
### <a name="gateway-requirements"></a>Requisiti di gateway
Nella tabella seguente elenca i requisiti per gateway VPN.

| |Gateway VPN Basic basato su criteri | Gateway VPN di tipo RouteBased Basic | Gateway VPN Standard di tipo RouteBased | Gateway VPN con prestazioni elevate di tipo RouteBased|
|--|--|--|--|--|
| **Connettività Site-to-Site** | Non supportato | Configurazione VPN RouteBased | Configurazione VPN RouteBased | Configurazione VPN RouteBased |
| **Metodo di autenticazione**  | Non supportato | Chiave precondivisa per la connettività S2S  | Chiave precondivisa per la connettività S2S  | Chiave precondivisa per la connettività S2S  |   
| **Numero massimo di connessioni Site-to-Site**  | Non supportato | 10 | 10| 30|
|**Supporto routing attivo (BGP)** | Non supportate | Non supportate | Supportato | Supportato |

### <a name="gateway-subnet"></a>Subnet gateway
Prima di creare un gateway VPN, è necessario creare una subnet del gateway. La subnet del gateway contiene gli indirizzi IP usati dalle VM e dai servizi del gateway di rete virtuale. Quando si crea il gateway di rete virtuale, le VM del gateway vengono distribuite nella subnet del gateway e configurate con le impostazioni del gateway VPN necessarie. Non distribuire qualsiasi altra operazione (ad esempio, altre macchine virtuali) per la subnet del gateway. Per poter funzionare correttamente, la subnet del gateway deve essere denominata "GatewaySubnet". La subnet del gateway di denominazione 'GatewaySubnet' consente di utilizzare Azure Stack sapere che la subnet per distribuire il gateway di rete virtuale le macchine virtuali e servizi.

Quando si crea la subnet del gateway, si specifica il numero di indirizzi IP inclusi nella subnet. Gli indirizzi IP inclusi nella subnet del gateway sono allocati alle VM del gateway e ai servizi del gateway. Alcune configurazioni richiedono più indirizzi IP di altre. Esaminare le istruzioni per la configurazione da creare e verificare che la subnet del gateway che si vuole creare soddisfi tali requisiti. È anche consigliabile verificare che la subnet del gateway contenga una quantità di indirizzi IP sufficiente per supportare possibili future configurazioni aggiuntive. Anche se è possibile creare una subnet del gateway con dimensioni pari a /29, è consigliabile crearne una con dimensioni pari a /28 o superiori, ad esempio /28, /27, /26 e così via. In questo modo, se si aggiungono funzionalità in futuro, non è necessario chiudere il gateway, quindi eliminare e ricreare la subnet del gateway per consentire più indirizzi IP.

L'esempio seguente di PowerShell Resource Manager illustra una subnet del gateway denominata GatewaySubnet. La notazione CIDR specifica /27. Questa dimensione ammette un numero di indirizzi IP sufficiente per la maggior parte delle configurazioni attualmente esistenti.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Quando si usano le subnet del gateway, evitare di associare un gruppo di sicurezza di rete (NSG) alla subnet del gateway. Se si associa un gruppo di sicurezza di rete a tale subnet, il gateway VPN potrebbe smettere di funzionare come previsto. Per ulteriori informazioni sui gruppi di sicurezza di rete, vedere [che cos'è un gruppo di sicurezza di rete?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Gateway di rete locali
Quando si crea una configurazione di gateway VPN in Azure, il gateway di rete locale rappresenta spesso il percorso locale. Rappresenta tutti i dispositivi VPN remoto che si trova all'esterno di Azure Stack nello Stack di Azure.  Potrebbe trattarsi di un dispositivo VPN in Data Center, un Data Center remoto o un Gateway VPN in Azure.

Assegnare un nome, l'indirizzo IP pubblico del dispositivo VPN di gateway di rete locale e specificare i prefissi di indirizzo che si trovano nel percorso locale. Azure esamina i prefissi di indirizzo di destinazione per il traffico di rete, consulta la configurazione specificata per il gateway di rete locale e indirizza i pacchetti di conseguenza.

L'esempio seguente di PowerShell consente di creare un nuovo gateway di rete locale:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```
Talvolta è necessario modificare le impostazioni di gateway di rete locale. Ad esempio, quando si aggiunge o si modifica l'intervallo di indirizzi oppure se viene modificato l'indirizzo IP del dispositivo VPN. Vedere [Modificare le impostazioni del gateway di rete locale usando PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>Parametri IPsec/IKE
Quando si configura una connessione VPN nello Stack di Azure, è necessario configurare la connessione a entrambe le estremità.  Se si configura una connessione VPN tra Azure Stack e un dispositivo hardware, ad esempio un commutatore o un router che agisce come Gateway VPN, che il dispositivo potrebbe essere richiesto per le impostazioni aggiuntive.

A differenza di Azure, che supporta più offerte come un iniziatore e risponditore, Stack di Azure supporta sola offerta.

###  <a name="ike-phase-1-main-mode-parameters"></a>Parametri della Fase 1 di IKE (Modalità principale)
| Proprietà              | Valore|
|-|-|
| Versione IKE           | IKEv2 |
|Diffie-Hellman Group   | Gruppo 2 (1024 bit) |
| Metodo di autenticazione | Chiave precondivisa |
|Algoritmi di crittografia e di hash | AES256, SHA256 |
|Durata dell'associazione di sicurezza (tempo)     | 28.800 secondi|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametri della Fase 2 di IKE (Modalità rapida)
| Proprietà| Valore|
|-|-|
|Versione IKE |IKEv2 |
|Crittografia e hash algoritmi (crittografia)     | GCMAES256|
|Crittografia e hash algoritmi (autenticazione) | GCMAES256|
|Durata dell'associazione di sicurezza (tempo)  | 3.600 secondi |
|Durata dell'associazione di sicurezza (byte) | 819,200       |
|Perfect Forward Secrecy (PFS) |PFS2048 |
|Rilevamento peer inattivo | Supportato|  
