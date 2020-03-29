---
title: 'Gateway VPN di Azure: configurare le acquisizioni di pacchettiAzure VPN Gateway: Configure packet captures'
description: Informazioni sulle funzionalità di acquisizione pacchetti che è possibile utilizzare nei gateway VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353510"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Configurare le acquisizioni di pacchetti per i gateway VPN

I problemi relativi alla connettività e alle prestazioni sono spesso complessi e richiedono una notevole quantità di tempo e fatica solo per restringere la causa del problema. La possibilità di acquisizione di pacchetti consente di ridurre notevolmente il tempo necessario per ridurre l'ambito del problema a determinate parti della rete, ad esempio se il problema si trova sul lato cliente della rete, sul lato Azure della rete o in un punto intermedio. Una volta che il problema è stato ridotto, è molto più efficiente eseguire il debug e intraprendere azioni correttive.

Esistono alcuni strumenti comunemente disponibili per l'acquisizione di pacchetti. Tuttavia, ottenere acquisizioni di pacchetti rilevanti utilizzando questi strumenti è spesso ingombrante, soprattutto quando si lavora con scenari di traffico ad alto volume. Le funzionalità di filtro fornite da un'acquisizione di pacchetti gateway VPN diventano un importante fattore di differenziazione. È possibile utilizzare un'acquisizione di pacchetti gateway VPN oltre agli strumenti di acquisizione pacchetti comunemente disponibili.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Funzionalità di filtro dell'acquisizione pacchetti del gateway VPN

Le acquisizioni di pacchetti del gateway VPN possono essere eseguite sul gateway o su una connessione specifica a seconda delle esigenze del cliente. È inoltre possibile eseguire acquisizioni di pacchetti su più tunnel contemporaneamente. È possibile acquisire il traffico singolo o bidirezionale, il traffico IKE ed ESP e i pacchetti interni insieme al filtro su un gateway VPN.

L'utilizzo di 5 tuple filter (subnet di origine, subnet di destinazione, porta di origine, porta di destinazione, protocollo) e flag TCP (SYN, ACK, FIN, URG, PSH, RST) è utile quando si isolano i problemi su un traffico di volumi elevati.

È possibile utilizzare una sola opzione per proprietà durante l'esecuzione dell'acquisizione di pacchetti.

## <a name="setup-packet-capture-using-powershell"></a>Configurare l'acquisizione di pacchetti tramite PowerShellSetup packet capture using PowerShell

Vedere gli esempi seguenti per i comandi di PowerShell per avviare e arrestare le acquisizioni di pacchetti. Per ulteriori informazioni sulle opzioni dei parametri, ad esempio su come creare un filtro, vedere questo [documento](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)di PowerShell .

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Avviare l'acquisizione di pacchetti per un gateway VPNStart packet capture for a VPN gateway

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Il parametro facoltativo **-FilterData** può essere utilizzato per applicare il filtro.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Interrompere l'acquisizione di pacchetti per un gateway VPNStop packet capture for a VPN gateway

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Avviare l'acquisizione di pacchetti per una connessione gateway VPNStart packet capture for a VPN gateway connection

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Il parametro facoltativo **-FilterData** può essere utilizzato per applicare il filtro.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Interrompere l'acquisizione di pacchetti in una connessione gateway VPNStop packet capture on a VPN gateway connection

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Considerazioni sulle chiavi

- L'esecuzione di acquisizioni di pacchetti può influire sulle prestazioni. Ricordarsi di interrompere l'acquisizione del pacchetto quando non è necessaria.
- La durata minima di acquisizione dei pacchetti consigliata è 600 secondi. Avere una durata di acquisizione dei pacchetti più breve potrebbe non fornire dati completi a causa della sincronizzazione dei problemi tra più componenti nel percorso.
- I file di dati di acquisizione pacchetti vengono generati in formato PCAP. Utilizzare Wireshark o altre applicazioni comunemente disponibili per aprire i file PCAP.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md)
