---
title: Informazioni sul gateway VPN di Azure Stack | Microsoft Docs
description: Scopri e configurare i gateway VPN che si usa con Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/02/2018
ms.author: sethm
ms.openlocfilehash: 8f45535b0e75c3f95a56148ea92a343bb0d8a9f5
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079260"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Informazioni sul gateway VPN di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Prima di poter inviare il traffico di rete tra rete virtuale di Azure e il sito locale, è necessario creare un gateway di rete virtuale per la rete virtuale.

Un gateway VPN è un tipo di gateway di rete virtuale che invia traffico crittografato tramite una connessione pubblica. È possibile usare i gateway VPN per inviare il traffico in modo sicuro tra una rete virtuale in Azure Stack e una rete virtuale in Azure. È anche possibile inviare il traffico in modo sicuro tra una rete virtuale e un'altra rete che è connesso a un dispositivo VPN.

Quando si crea un gateway di rete virtuale, si specifica il tipo di gateway che si vuole creare. Azure Stack supporta un tipo di gateway di rete virtuale, il **Vpn** tipo.

Ogni rete virtuale può avere due gateway di rete virtuale, ma solo uno per ogni tipo. A seconda delle impostazioni scelte, è possibile creare più connessioni a un singolo gateway VPN. Un esempio è una configurazione di connessione multisito.

Prima di creare e configurare i gateway VPN di Azure Stack, rivedere le [considerazioni per la rete di Azure Stack](/articles/azure-stack/user/azure-stack-network-differences.md) per informazioni su come le configurazioni per Azure Stack sono diversi da Azure.

>[!NOTE]
>In Azure, la velocità effettiva della larghezza di banda per il gateway VPN SKU scelto deve essere divise tra tutte le connessioni che sono connessi al gateway. Ma in Azure Stack, il valore della larghezza di banda per lo SKU del gateway VPN viene applicato a ogni risorsa di connessione che è connesso al gateway.
>
> Ad esempio: 
> * In Azure, lo SKU di Gateway VPN Basic può supportare circa 100 Mbps di velocità effettiva aggregata. Se si creano due connessioni a Gateway VPN e una connessione Usa 50 Mbps di larghezza di banda, quindi 50 Mbps è disponibile per l'altra connessione.
> * In Azure Stack *ogni* connessione per lo SKU del Gateway VPN base ottiene allocato 100 Mbps di velocità effettiva.

## <a name="configuring-a-vpn-gateway"></a>Configurazione di un gateway VPN

Una connessione gateway VPN si basa su diverse risorse che sono configurate con impostazioni specifiche. La maggior parte di queste risorse può essere configurata separatamente, ma in alcuni casi devono essere configurati in un ordine specifico.

### <a name="settings"></a>Impostazioni

Le impostazioni che si è scelto per ogni risorsa sono fondamentali per la creazione di una connessione riuscita.

Per informazioni sulle singole risorse e le impostazioni per un gateway VPN, vedere [impostazioni del gateway VPN per Azure Stack](azure-stack-vpn-gateway-settings.md). Questo articolo aiuterà a comprendere:

* Tipi di gateway, tipi di VPN e i tipi di connessione.
* Subnet del gateway, gateway di rete locale e altre impostazioni delle risorse che è possibile prendere in considerazione.

### <a name="deployment-tools"></a>Strumenti di distribuzione

È possibile creare e configurare le risorse usando uno strumento di configurazione, ad esempio il portale di Azure. In un secondo momento è possibile passare a un altro strumento come PowerShell per configurare risorse aggiuntive o modificare le risorse esistenti, se applicabile. Attualmente, non è possibile configurare tutte le risorse e le relative impostazioni nel portale di Azure. Le istruzioni riportate negli articoli per ogni topologia di connessione indicano se è necessario usare uno strumento di configurazione specifico.

## <a name="connection-topology-diagrams"></a>Diagrammi delle topologie di connessione

È importante tenere presente che sono disponibili configurazioni diverse per le connessioni del gateway VPN. Determinare la configurazione più adatta alle proprie esigenze. Nelle sezioni seguenti, è possibile visualizzare le informazioni e diagrammi delle topologie sulle connessioni gateway VPN seguenti:

* Modello di distribuzione disponibile
* Strumenti di configurazione disponibili
* Collegamenti che visualizzano direttamente un articolo, se disponibile

I diagrammi e le descrizioni nelle sezioni seguenti consentono di selezionare una topologia di connessione alle proprie esigenze. I diagrammi mostrano le principali topologie di base, ma è possibile compilare configurazioni più complesse usando i diagrammi come guida.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Da sito a sito e multisito (tunnel VPN IPsec/IKE)

### <a name="site-to-site"></a>Da sito a sito

Una connessione gateway VPN da sito a sito (S2S) avviene tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale e viene assegnato un indirizzo IP pubblico. Questo dispositivo non può trovarsi dietro un NAT. Le connessioni S2S possono essere usate per le configurazioni cross-premise e ibride.

![Esempio di configurazione di connessione VPN Site-to-site](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Multisito

Questo tipo di connessione è una variante della connessione da sito a sito. È possibile creare più di una connessione VPN dal gateway di rete virtuale, che in genere connette a più siti locali. Quando si usano più connessioni, è necessario usare un tipo di VPN RouteBased (noto come un gateway dinamico quando si lavora con reti virtuali classiche.) Poiché ogni rete virtuale può avere un solo gateway VPN, tutte le connessioni che usano il gateway condividono la larghezza di banda disponibile. Questo tipo di connessione è spesso definito "multisito".

![Esempio di connessione gateway VPN di Azure multisito](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>SKU del gateway

Quando si crea un gateway di rete virtuale per Azure Stack, si specifica il gateway SKU che si desidera utilizzare. Sono supportati gli SKU di gateway VPN seguenti:

* Basic
* Standard
* HighPerformance

Quando si seleziona un versione successiva SKU del gateway, come Standard su Basic o HighPerformance su Standard o Basic, numero di CPU e della larghezza di banda di rete vengono allocate al gateway. Di conseguenza, il gateway può supportare una velocità effettiva di rete alla rete virtuale.

Azure Stack non supporta lo SKU, che viene usato esclusivamente con Express Route del gateway UltraPerformance.

Quando si seleziona lo SKU, considerare quanto segue:

* Azure Stack non supporta i gateway basati su criteri.
* Protocollo BGP (Border Gateway) non è supportato nello SKU di base.
* Coesistenza Gateway VPN ExpressRoute configurazioni non supportate in Azure Stack.
* Le connessioni del gateway VPN S2S attivo/attivo possono essere configurate solo nello SKU HighPerformance.

## <a name="estimated-aggregate-throughput-by-sku"></a>Velocità effettiva aggregata stimata per SKU

La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata per SKU di gateway.

|   | Velocità effettiva del Gateway VPN *(1)* | Tunnel IPsec massimi del Gateway VPN *(2)* |
|-------|-------|-------|
|**Lo SKU Basic** ***(3)***    | 100 Mbps  | 10    |
|**SKU standard**       | 100 Mbps  | 10    |
|**SKU prestazioni elevate** | 200 Mbps    | 5 |

**Note sulla tabella:**

*Nota (1)* -velocità effettiva della VPN non è una velocità effettiva garantita per le connessioni cross-premise tramite Internet. È la misurazione della massima velocità effettiva possibile.  
*Nota (2)* -Max tunnel è il totale per ogni distribuzione di Azure Stack per tutte le sottoscrizioni.  
*Nota (3)* -routing BGP non è supportato per lo SKU Basic.

## <a name="next-steps"></a>Passaggi successivi

[Impostazioni di configurazione di gateway VPN di Azure Stack](azure-stack-vpn-gateway-settings.md)
