---
title: File di inclusione
description: File di inclusione
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121284"
---
> [!NOTE]
> Questi esempi non sono validi per la configurazione di connessioni con coesistenza S2S/ExpressRoute.
> Per altre informazioni sull'uso di gateway in una configurazione di connessioni con coesistenza, vedere [Configurare connessioni coesistenti](../articles/expressroute/expressroute-howto-coexist-classic.md#gw).

## <a name="add-a-gateway"></a>Aggiungere un gateway

Quando si aggiunge un gateway a una rete virtuale usando il modello di risorsa classico, il file di configurazione di rete viene modificato direttamente prima della creazione del gateway. I valori riportati negli esempi seguenti devono essere presenti nel file per creare un gateway. Se alla rete virtuale in precedenza era associato un gateway, alcuni di questi valori saranno già presenti. Modificare il file in base ai valori riportati di seguito.

### <a name="download-the-network-configuration-file"></a>Scaricare il file di configurazione di rete

1. Scaricare il file di configurazione di rete tramite la procedura descritta nell'articolo [File di configurazione di rete](../articles/virtual-network/virtual-networks-using-network-configuration-file.md). Aprire il file in un editor di testo.
2. Aggiungere al file un sito di rete locale. È possibile usare qualsiasi prefisso di indirizzo valido. È possibile aggiungere qualsiasi indirizzo IP valido per il gateway VPN. I valori di indirizzo riportati in questa sezione non vengono usati per le operazioni di ExpressRoute, ma sono necessari per la convalida dei file. Nell'esempio, "branch1" è il nome del sito. È possibile usare un nome diverso, ma è necessario assicurarsi che venga usato lo stesso valore nella sezione Gateway del file.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Passare a VirtualNetworkSites e modificare i campi.

   * Verificare che per la rete virtuale sia presente la subnet del gateway. In caso contrario, è possibile aggiungerne una in questa fase. Il nome deve essere "GatewaySubnet".
   * Verificare che esista la sezione Gateway del file. In caso contrario, aggiungerla. Questa operazione è necessaria per associare la rete virtuale al sito di rete locale (che rappresenta la rete a cui ci si connette).
   * Verificare che il tipo di connessione sia impostato su Dedicated. Questa impostazione è obbligatoria per le connessioni ExpressRoute.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Salvare il file e caricarlo in Azure.

### <a name="create-the-gateway"></a>Creare il gateway

Usare il comando seguente per creare un gateway. Sostituire i valori presenti con quelli desiderati.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Verificare che il gateway sia stato creato

Utilizzare il comando seguente per verificare che il gateway sia stato creato. Questo comando recupera l'ID del gateway, necessario per le altre operazioni.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Ridimensionare un gateway

Esistono diversi [SKU del gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). È possibile usare il comando seguente per modificare la SKU del gateway in qualsiasi momento.

> [!IMPORTANT]
> Questo comando non funziona per il gateway UltraPerformance. Per modificare il gateway in un gateway UltraPerformance, innanzitutto rimuovere il gateway ExpressRoute esistente, quindi creare un nuovo gateway UltraPerformance. Per effettuare il downgrade del gateway da un gateway UltraPerformance, innanzitutto rimuovere il gateway UltraPerformance, quindi creare un nuovo gateway.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Rimuovere un gateway

Usare il comando seguente per rimuovere un gateway

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```