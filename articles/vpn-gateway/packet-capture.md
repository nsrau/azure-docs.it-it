---
title: Acquisizioni di pacchetti del gateway VPN di Azure | Microsoft Docs
description: Informazioni sulle funzionalità di acquisizione di pacchetti che è possibile usare nei gateway VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 0957fabf58a68efe9e215b390d28dbf160574963
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517436"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Configurare le acquisizioni di pacchetti per i gateway VPN

La connettività e i problemi relativi alle prestazioni sono spesso complessi ed è necessario impegnarsi per limitare la causa del problema. La possibilità di acquisire pacchetti consente di ridurre notevolmente il tempo necessario per limitare l'ambito del problema a determinate parti della rete, ad esempio se il problema si trova sul lato cliente della rete, sul lato Azure della rete o in un punto qualsiasi. Una volta che il problema è stato ridotto, è molto più efficiente eseguire il debug e intraprendere azioni correttive.

Sono disponibili alcuni strumenti comuni per l'acquisizione di pacchetti. Tuttavia, ottenere le acquisizioni di pacchetti rilevanti usando questi strumenti è spesso molto complesso, soprattutto quando si lavora con scenari di traffico con volumi elevati. Il filtraggio delle funzionalità fornite da un'acquisizione di pacchetti del gateway VPN diventa un fattore di differenziazione principale. È possibile usare un'acquisizione di pacchetti del gateway VPN oltre agli strumenti di acquisizione di pacchetti comunemente disponibili.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Funzionalità di filtro di acquisizione pacchetti del gateway VPN

Le acquisizioni di pacchetti del gateway VPN possono essere eseguite nel gateway o in una connessione specifica a seconda delle esigenze del cliente. È anche possibile eseguire acquisizioni di pacchetti su più tunnel contemporaneamente. È possibile acquisire il traffico singolo o bidirezionale, il traffico IKE e ESP e i pacchetti interni insieme al filtro in un gateway VPN.

L'uso di 5 filtri di Tuple (subnet di origine, subnet di destinazione, porta di origine, porta di destinazione, protocollo) e flag TCP (SYN, ACK, pinna, URG, PSH, RST) è utile quando si isolano i problemi in un traffico di volume elevato.

## <a name="setup-packet-capture-using-powershell"></a>Configurare l'acquisizione di pacchetti con PowerShell

Vedere gli esempi seguenti per i comandi di PowerShell per avviare e arrestare le acquisizioni di pacchetti. Per ulteriori informazioni sulle opzioni dei parametri, ad esempio su come creare filtri, vedere questo [documento](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)di PowerShell.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Avviare l'acquisizione pacchetti per un gateway VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Parametro facoltativo **:** è possibile utilizzare FilterData per applicare filtri.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Arrestare l'acquisizione di pacchetti per un gateway VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Avviare l'acquisizione pacchetti per una connessione gateway VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Parametro facoltativo **:** è possibile utilizzare FilterData per applicare filtri.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Arrestare l'acquisizione di pacchetti in una connessione gateway VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Considerazioni sulle chiavi

- L'esecuzione di acquisizioni di pacchetti può influire sulle prestazioni. Ricordarsi di arrestare l'acquisizione di pacchetti quando non è necessaria.
- La durata di acquisizione pacchetti minima suggerita è di 600 secondi. La durata dell'acquisizione di pacchetti più breve potrebbe non fornire dati completi a causa della sincronizzazione di problemi tra più componenti del percorso.
- I file di dati di acquisizione di pacchetti vengono generati in formati PCAP o ETL. Per comprendere i dati, potrebbe essere necessario un parser Netmon.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul gateway VPN, vedere [informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md)