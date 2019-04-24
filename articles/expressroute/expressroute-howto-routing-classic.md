---
title: 'Configurare il peering per un circuito - ExpressRoute: Azure: classico | Microsoft Docs'
description: Questo articolo descrive come creare ed eseguire il provisioning di un circuito ExpressRoute per il peering privato, il peering pubblico e il peering Microsoft. Questo articolo mostra anche come controllare lo stato e aggiornare o eliminare i peering per un circuito.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 01486edce5ac5881ff192021a459b9c6d006e25b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370257"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Creare e modificare il peering per un circuito ExpressRoute (versione classica)
> [!div class="op_single_selector"]
> * [Portale di Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Interfaccia della riga di comando di Azure](howto-routing-cli.md)
> * [Video - Peering privato](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - Peering pubblico](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Peering Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-routing-classic.md) (PowerShell (classico))
> 

Questo articolo descrive le procedure per creare e gestire la configurazione di peering/routing per un circuito ExpressRoute usando PowerShell e il modello di distribuzione classico. La procedura seguente mostra anche come controllare lo stato e aggiornare, eliminare o effettuare il deprovisioning dei peering per un circuito ExpressRoute. Per un circuito ExpressRoute è possibile configurare uno, due o tutti e tre i peering (peering privato di Azure, peering pubblico di Azure e peering Microsoft). È possibile configurare i peering nell'ordine desiderato. assicurandosi, tuttavia, di completare la configurazione di un peering per volta. 

Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (in genere una VPN IP, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Informazioni sui modelli di distribuzione di AzureAbout Azure deployment models**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione

* Prima di iniziare la configurazione, assicurarsi di aver letto le pagine relative ai [prerequisiti](expressroute-prerequisites.md), ai [requisiti per il routing](expressroute-routing.md) e ai [flussi di lavoro](expressroute-workflows.md).
* È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. Per poter eseguire i cmdlet descritti di seguito deve essere stato effettuato il provisioning del circuito ExpressRoute e lo stato del circuito deve essere abilitato.

### <a name="download-the-latest-powershell-cmdlets"></a>Scaricare i cmdlet di PowerShell più recenti

Installare le versioni più recenti dei moduli di PowerShell per Gestione dei servizi di Azure e il modulo ExpressRoute. Quando si usa l'esempio seguente, si noti che il numero di versione (in questo esempio, 5.1.1) verrà modificato quando vengono rilasciate versioni più recenti dei cmdlet.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Per istruzioni dettagliate sulla configurazione del computer per l'uso con i moduli di Azure PowerShell, vedere [Getting started with Azure PowerShell cmdlets](/powershell/azure/overview) (Introduzione ai cmdlet di Azure PowerShell).

### <a name="sign-in"></a>Accesso

Per accedere al proprio account di Azure, usare gli esempi seguenti:

1. Aprire la console di PowerShell con diritti elevati e connettersi all'account.

   ```powershell
   Connect-AzAccount
   ```
2. Controllare le sottoscrizioni per l'account.

   ```powershell
   Get-AzSubscription
   ```
3. Se sono disponibili più sottoscrizioni, selezionare la sottoscrizione da usare.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. Successivamente, utilizzare il cmdlet seguente per aggiungere la sottoscrizione di Azure a PowerShell per il modello di distribuzione classico.

   ```powershell
   Add-AzureAccount
   ```

## <a name="azure-private-peering"></a>Peering privato di Azure

Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering privato di Azure per un circuito ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Per creare un peering privato di Azure

1. **Creare un circuito ExpressRoute.**

   Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura.
2. **Verificare che sia stato effettuato il provisioning del circuito ExpressRoute.**
   
   È necessario verificare se è stato effettuato il provisioning del circuito ExpressRoute e se il circuito è abilitato.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Valore restituito:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Assicurarsi che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. In caso contrario, rivolgersi al provider di connettività affinché imposti gli stati necessari per il circuito.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Configurare il peering privato di Azure per il circuito.**

   Prima di procedere con i passaggi successivi, verificare che siano presenti gli elementi seguenti:
   
   * Una subnet /30 per il collegamento primario. Non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
   * Una subnet /30 per il collegamento secondario. Non deve far parte di alcuno spazio indirizzi riservato per le reti virtuali.
   * Un ID VLAN valido su cui stabilire questo peering. Verificare che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte. È possibile usare il numero AS privato per questo peering. Verificare di non usare il numero 65515.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo**.
     
   Per configurare il peering privato di Azure per il circuito, è possibile usare l'esempio seguente:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Se si vuole usare un hash MD5, usare l'esempio seguente per configurare il peering privato di Azure per il circuito:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Verificare di specificare il numero AS come ASN di peering e non come ASN cliente.
   > 

### <a name="to-view-azure-private-peering-details"></a>Per visualizzare i dettagli relativi al peering privato di Azure

Per visualizzare i dettagli di configurazione, usare il cmdlet seguente:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Valore restituito:

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>Per aggiornare la configurazione del peering privato di Azure

Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente. Nell'esempio seguente il valore dell'ID VLAN del circuito viene aggiornato da 100 a 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Per eliminare un peering privato di Azure

Per rimuovere la configurazione di peering, eseguire il cmdlet seguente. Assicurarsi che tutte le reti virtuali siano scollegate dal circuito ExpressRoute prima di eseguire questo cmdlet.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Peering pubblico di Azure

Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering pubblico di Azure per un circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Per creare un peering pubblico di Azure

1. **Creare un circuito ExpressRoute**

   Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura.
2. **Verificare che sia stato eseguito il provisioning del circuito ExpressRoute**

   In primo luogo, è necessario verificare se è stato effettuato il provisioning del circuito ExpressRoute e se il circuito è abilitato.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Valore restituito:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Verificare che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. In caso contrario, rivolgersi al provider di connettività affinché imposti gli stati necessari per il circuito.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Configurare il peering pubblico di Azure per il circuito**
   
   Prima di continuare, verificare che siano presenti gli elementi seguenti:
   
   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido.
   * Un ID VLAN valido su cui stabilire questo peering. Verificare che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo**.

   > [!IMPORTANT]
   > Assicurarsi di specificare il numero AS come ASN di peering e non come ASN cliente.
   >  
     
   Per configurare il peering pubblico di Azure per il circuito, è possibile usare l'esempio seguente:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Se si vuole usare un hash MD5, usare l'esempio seguente per configurare il circuito:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Per visualizzare i dettagli relativi al peering pubblico di Azure

Per visualizzare i dettagli di configurazione, usare il cmdlet seguente:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Valore restituito:

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>Per aggiornare la configurazione del peering pubblico di Azure

Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente. In questo esempio il valore dell'ID VLAN del circuito viene aggiornato da 200 a 600.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Verificare che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. 
### <a name="to-delete-azure-public-peering"></a>Per eliminare un peering pubblico di Azure

Per rimuovere la configurazione di peering, eseguire il cmdlet seguente:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Peering Microsoft

Questa sezione fornisce le istruzioni per creare, ottenere, aggiornare ed eliminare la configurazione del peering Microsoft per un circuito ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Per creare il peering Microsoft

1. **Creare un circuito ExpressRoute**
  
   Seguire le istruzioni per creare un [circuito ExpressRoute](expressroute-howto-circuit-classic.md) e chiedere al provider di connettività di effettuarne il provisioning. Se il provider di connettività offre servizi gestiti di livello 3, è possibile chiedere al provider di connettività di abilitare il peering privato di Azure. In questo caso, non sarà necessario seguire le istruzioni riportate nelle sezioni seguenti. Se invece il provider di connettività non gestisce il routing per conto dell'utente, dopo aver creato il circuito, seguire questa procedura.
2. **Verificare che sia stato eseguito il provisioning del circuito ExpressRoute**

   Verificare che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Valore restituito:
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Verificare che lo stato visualizzato per il circuito sia Provisioning eseguito e Abilitato. In caso contrario, rivolgersi al provider di connettività affinché imposti gli stati necessari per il circuito.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Configurare il peering Microsoft per il circuito**
   
    Prima di procedere, verificare quanto segue:
   
   * Una subnet /30 per il collegamento primario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Una subnet /30 per il collegamento secondario. Deve essere un prefisso IPv4 pubblico valido di proprietà dell'utente e registrato presso un registro RIR o IRR.
   * Un ID VLAN valido su cui stabilire questo peering. Verificare che nessun altro peering nel circuito usi lo stesso ID VLAN.
   * Numero AS per il peering. È possibile usare numeri AS a 2 e a 4 byte.
   * Prefissi annunciati: è necessario fornire un elenco di tutti i prefissi che si intende annunciare nella sessione BGP. Sono accettati solo prefissi di indirizzi IP pubblici. Per inviare un set di prefissi, è possibile creare un elenco con valori delimitati da virgole. Questi prefissi devono essere intestati all'utente in un registro RIR o IRR.
   * ASN cliente: se si annunciano prefissi non registrati nel numero AS del peering, è possibile specificare il numero AS in cui sono registrati. **Facoltativo**.
   * Nome del registro di routing: è possibile specificare il registro RIR/IRR in cui sono registrati il numero AS e i prefissi.
   * Un hash MD5, se si sceglie di usarne uno. **Facoltativo.**
     
   Per configurare il peering Microsoft per il circuito, eseguire il cmdlet seguente:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Per visualizzare i dettagli del peering Microsoft

Per visualizzare i dettagli di configurazione, usare il cmdlet seguente:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Valore restituito:

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>Per aggiornare la configurazione del peering Microsoft

Per aggiornare qualsiasi parte della configurazione, usare il cmdlet seguente:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Per eliminare il peering Microsoft

Per rimuovere la configurazione di peering, eseguire il cmdlet seguente:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Passaggi successivi

Successivamente, [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)

* Per ulteriori informazioni sui flussi di lavoro, vedere [Flussi di lavoro ExpressRoute](expressroute-workflows.md).
* Per altre informazioni sul peering del circuito, vedere l'articolo relativo ai [circuiti ExpressRoute e domini di routing](expressroute-circuit-peerings.md)
