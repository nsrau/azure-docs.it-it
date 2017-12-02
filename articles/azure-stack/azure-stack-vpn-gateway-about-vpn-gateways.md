---
title: Sul gateway VPN per lo Stack di Azure | Documenti Microsoft
description: "Informazioni e configurare il gateway VPN che è utilizzare con lo Stack di Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: ba9642d8c51f57623aded44b84d7127334806bc1
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2017
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Sul gateway VPN per lo Stack di Azure
*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*


Prima di poter inviare il traffico di rete tra la rete virtuale di Azure e il sito locale, è necessario creare un gateway di rete virtuale per la rete virtuale.

Un gateway VPN è un tipo di gateway di rete virtuale che invia traffico crittografato tramite una connessione pubblica. È possibile utilizzare i gateway VPN di inviare il traffico in modo sicuro tra una rete virtuale nello Stack di Azure e una rete virtuale in Azure o tra una rete virtuale e un'altra rete connessa a un dispositivo VPN.

Quando si crea un gateway di rete virtuale, si specifica il tipo di gateway che si vuole creare. Stack di Azure supporta un tipo di gateway di rete virtuale: il tipo 'Vpn'.

Ogni rete virtuale può avere due gateway di rete virtuale, ma solo uno per ogni tipo. A seconda delle impostazioni scelte, è possibile creare più connessioni a un singolo gateway VPN. Una configurazione di connessione multisito ne è un esempio.

> [!NOTE]
> In Azure, è necessario dividere la velocità effettiva della larghezza di banda per SKU di Gateway VPN si sceglie tra tutte le connessioni che sono connessi a esso.  Nello Stack di Azure, il valore della larghezza di banda per lo SKU di Gateway VPN viene applicato a ogni risorsa di connessione che è connesso.     

> Ad esempio, In Azure, la base SKU di Gateway VPN in grado di gestire circa 100 Mbps di larghezza di velocità effettiva aggregata.  Se si creano due connessioni al Gateway VPN e utilizza una connessione 50 Mbps di larghezza di banda, quindi a 50 Mbps è disponibile per l'altra connessione.   

> Nello Stack di Azure, *ogni* connessione per lo SKU di Gateway VPN base ottiene allocato 100 Mbps di velocità effettiva.

## <a name="configuring-a-vpn-gateway"></a>Configurazione di un gateway VPN
Una connessione gateway VPN si basa su più risorse configurate con impostazioni specifiche. La maggior parte delle risorse può essere configurata separatamente, anche se in alcuni casi è necessario configurarle seguendo un determinato ordine.

### <a name="settings"></a>Impostazioni
Le impostazioni scelte per ogni risorsa sono fondamentali per creare una connessione corretta. Per informazioni sulle singole risorse e le impostazioni per il Gateway VPN, vedere [impostazioni del gateway VPN per Azure Stack](azure-stack-vpn-gateway-settings.md). È possibile trovare informazioni utili per capire i tipi di gateway, tipi di VPN, i tipi di connessione, subnet gateway, gateway di rete locale e varie altre impostazioni di risorsa che è possibile prendere in considerazione.

### <a name="deployment-tools"></a>Strumenti di distribuzione
È possibile iniziare a creare e configurare le risorse usando uno strumento di configurazione, ad esempio il portale di Azure, e successivamente decidere di passare a un altro strumento, ad esempio PowerShell, per configurare risorse aggiuntive o eventualmente modificare quelle esistenti. Attualmente, non è possibile configurare tutte le risorse e le relative impostazioni nel portale di Azure. Le istruzioni riportate negli articoli per ogni topologia di connessione indicano se è necessario usare uno strumento di configurazione specifico.

## <a name="connection-topology-diagrams"></a>Diagrammi di topologia di connessione
È importante tenere presente che sono disponibili configurazioni diverse per le connessioni del gateway VPN. È necessario determinare la configurazione più adatta alle proprie esigenze. Nelle sezioni seguenti è possibile trovare informazioni e diagrammi delle topologie sulle connessioni gateway VPN seguenti. Le sezioni seguenti contengono tabelle che elencano:

- Modello di distribuzione disponibile
- Strumenti di configurazione disponibili
- Collegamenti che visualizzano direttamente un articolo, se disponibile

I diagrammi e le descrizioni riportate nelle sezioni seguenti consentono di selezionare una topologia di connessione alle proprie necessità. I diagrammi illustrano le principali topologie di base, ma è possibile creare configurazioni più complesse usando i diagrammi come riferimento.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Da sito a sito e multisito (tunnel VPN IPsec/IKE)
### <a name="site-to-site"></a>Da sito a sito
Una connessione gateway VPN da sito a sito (S2S) avviene tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico assegnato e non dietro una NAT. Le connessioni S2S possono essere usate per le configurazioni cross-premise e ibride.    
![Esempio di configurazione di connessione VPN da sito a sito](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Multisito
Questo tipo di connessione è una variante della connessione da sito a sito. È possibile creare più di una connessione VPN dal gateway di rete virtuale, che in genere connette a più siti locali. Quando si usano più connessioni, è necessario usare una rete VPN di tipo RouteBased (nota come gateway dinamico nell'ambito delle reti virtuali classiche). Poiché ogni rete virtuale può avere un solo gateway VPN, tutte le connessioni che usano il gateway condividono la larghezza di banda disponibile. Questo tipo di connessione è spesso definito "multisito".   
![Esempio di connessione gateway VPN di Azure multisito](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)



## <a name="gateway-skus"></a>SKU del gateway
Quando si crea un gateway di rete virtuale per lo Stack di Azure, specificare il gateway SKU che si desidera utilizzare. Sono supportati gli SKU di gateway VPN seguenti:
- Basic
- Standard
- HighPerformance

Quando si seleziona un gateway superiore SKU, ad esempio Standard su Basic o ad alte prestazioni su Standard o Basic, vengono allocate più CPU e della larghezza di banda di rete per il gateway. Di conseguenza, il gateway può supportare una velocità effettiva di rete alla rete virtuale.

Stack di Azure non supporta il gateway UltraPerformance SKU, che viene utilizzato esclusivamente con Express Route.

Nella scelta di uno SKU, considerare quanto segue:
- Stack di Azure non supporta i gateway basata su criteri.
- Protocollo BGP (Border Gateway) non è supportato nella SKU Basic.
- Coesistenza Gateway VPN di ExpressRoute configurazioni non sono supportate nello Stack di Azure
- Le connessioni del gateway VPN S2S attivo/attivo possono essere configurate solo nello SKU HighPerformance.

## <a name="estimated-aggregate-throughput-by-sku"></a>Velocità effettiva aggregata stimata per SKU
La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata per SKU di gateway.

|   | Velocità effettiva Gateway VPN *(1)* |Tunnel IPsec max Gateway VPN |
|-------|-------|-------|
|**SKU Basic** ***(2)***    | 100 Mbps  | 10    |
|**SKU standard**       | 100 Mbps  | 10    |
|**SKU con prestazioni elevate** | 200 Mbps    | 30    |
***(1)***  Velocità effettiva di VPN non è una velocità effettiva garantita per le connessioni cross-premise via Internet. È la misura massima possibile per la velocità effettiva.  
***(2)***  BGP non è supportato per SKU Basic.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su [delle impostazioni per il gateway VPN](azure-stack-vpn-gateway-settings.md) per lo Stack di Azure.
