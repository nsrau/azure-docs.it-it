---
title: Considerazioni sulla rete - Modulo di protezione hardware dedicato di Azure | Microsoft Docs
description: Panoramica delle considerazioni sulla rete applicabili alle distribuzioni del modulo di protezione hardware dedicato di Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 044930c9df7b54515b9b66426a6b05aa9517a3a1
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881278"
---
# <a name="azure-dedicated-hsm-networking"></a>Reti con HSM dedicato di Azure

HSM dedicato di Azure richiede un ambiente di rete altamente sicuro. Questo vale sia dal cloud di Azure all'ambiente IT del cliente (locale), che con l'uso delle applicazioni distribuite e per scenari a disponibilità elevata. Rete di Azure fornisce tutto questo e vi sono quattro aree distinte a cui prestare attenzione.

- Creazione di dispositivi HSM all'interno della rete virtuale in Azure
- Connessione locale a risorse basate su cloud per la configurazione e gestione di dispositivi HSM
- Creazione e connessione di reti virtuali per l'interconnessione di risorse applicative e dispositivi HSM
- Connessione di reti virtuali tra aree per l'intercomunicazione e per abilitare la disponibilità elevata

## <a name="virtual-network-for-your-dedicated-hsms"></a>Rete virtuale per HSM dedicati

Gli HSM dedicati sono integrati in una rete virtuale e inseriti nella rete privata del cliente in Azure. Ciò consente l'accesso ai dispositivi da macchine virtuali o risorse di calcolo nella rete virtuale.  
Per altre informazioni sull'integrazione dei servizi di Azure nella rete virtuale e le funzionalità che ciò offre, vedere la documentazione [Rete virtuale per servizi di Azure](../virtual-network/virtual-network-for-azure-services.md).

### <a name="virtual-networks"></a>Reti virtuali

Prima di eseguire il provisioning di un dispositivo HSM dedicato i clienti devono creare una rete virtuale in Azure o devono usarne una esistente nella loro sottoscrizione. La rete virtuale definisce il perimetro di sicurezza per il dispositivo HSM dedicato. Per altre informazioni sulla creazione di reti virtuali, vedere la [documentazione sulle reti virtuali](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Subnet

Le subnet segmentano la rete virtuale in spazi di indirizzi separati usabili dalle risorse di Azure contenute. Gli HSM dedicati sono distribuiti in una subnet all'interno di una rete virtuale. Ogni dispositivo HSM dedicato che viene distribuito nella subnet del cliente riceverà un indirizzo IP privato dalla subnet. La subnet in cui il dispositivo HSM viene distribuito deve essere delegata in modo esplicito al servizio: Microsoft.HardwareSecurityModules/dedicatedHSMs. In questo modo vengono concesse determinate autorizzazioni al servizio HSM per la distribuzione nella subnet. La delega agli HSM dedicati impone alcune limitazioni per i criteri della subnet. Nelle subnet delegate non sono supportati i gruppi di sicurezza di rete e le route definite dall'utente. Di conseguenza, dopo che una subnet è stata delegata a HSM dedicati, può essere usata solo per distribuire risorse HSM. Non sarà possibile distribuire nessun'altra risorsa del cliente nella subnet.


### <a name="expressroute-gateway"></a>Gateway ExpressRoute

Un requisito dell'architettura corrente è la configurazione di un gateway ExpressRoute nella subnet dei clienti in cui deve essere inserito un dispositivo HSM per abilitare l'integrazione del dispositivo HSM in Azure. Questo gateway ExpressRoute non può essere usato per la connessione di percorsi locali ai dispositivi HSM dei clienti in Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Connessione dell'IT locale ad Azure

Quando si creano risorse basate su cloud, un'esigenza tipica è mantenere una connessione privata a risorse IT locali. Nel caso di HSM dedicato, ciò sarà necessario soprattutto per consentire al software client HSM di configurare i dispositivi HSM e anche per attività quali il backup e il recupero dei log dai moduli di protezione hardware a scopo di analisi. Una decisione fondamentale da prendere a questo proposito riguarda la natura della connessione, per la quale esistono varie opzioni.  L'opzione più flessibile è VPN da sito a sito dato che probabilmente ci saranno più risorse locali che richiedono la comunicazione sicura con le risorse, inclusi i moduli di protezione hardware, nel cloud di Azure. Questo richiederà che l'organizzazione del cliente abbia un dispositivo VPN per facilitare la connessione. È possibile usare una connessione VPN da punto a sito se è presente un solo endpoint locale, ad esempio una sola workstation amministrativa.
Per altre informazioni sulle opzioni di connettività, vedere le [opzioni di pianificazione di Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Al momento ExpressRoute non può essere usato per la connessione a risorse locali. Si noti anche che il gateway ExpressRoute non può essere usato come descritto in precedenza per le connessioni all'infrastruttura locale.

### <a name="point-to-site-vpn"></a>VPN da punto a sito

Una rete privata virtuale da punto a sito è la forma più semplice di connessione sicura a un singolo endpoint locale. Può risultare utile se si prevede di avere una sola workstation amministrativa per HSM dedicati basati su Azure.

### <a name="site-to-site-vpn"></a>VPN da sito a sito

Una rete privata virtuale da sito a sito consente la comunicazione sicura tra HSM dedicati basati su Azure e l'IT locale. Per eseguire questa operazione, è necessario disporre di una funzionalità di backup per il modulo di protezione hardware locale e che sia necessaria una connessione tra i due per l'esecuzione del backup.

## <a name="connecting-virtual-networks"></a>Connessione di reti virtuali

Un'architettura di distribuzione tipica per HSM dedicato inizia con una singola rete virtuale e la corrispondente subnet in cui vengono creati i dispositivi HSM e ne viene eseguito il provisioning. All'interno della stessa area possono essere presenti anche altre reti virtuali e subnet per i componenti dell'applicazione che usano l'HSM dedicato. Per abilitare la comunicazione tra queste reti si usa il peering di rete virtuale.

### <a name="virtual-network-peering"></a>Peering di rete virtuale

Quando in un'area sono presenti più reti virtuali che devono accedere l'una alle risorse delle altre, si può usare il peering di rete virtuale per creare canali di comunicazione sicura tra esse.  Il peering di rete virtuale non solo consente la comunicazione sicura ma garantisce anche una connessione a bassa latenza e larghezza di banda elevata tra le risorse in Azure.

![peering di rete](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Connessione tra aree di Azure

I dispositivi HSM hanno la capacità, tramite le librerie di software, di reindirizzare il traffico a un modulo di protezione hardware alternativo. Il reindirizzamento del traffico è utile in caso di errore dei dispositivi o di perdita dell'accesso a essi. È possibile ridurre gli scenari di errore a livello di area distribuendo moduli di protezione hardware in altre aree e abilitando la comunicazione tra reti virtuali in aree diverse.

### <a name="cross-region-ha-using-vpn-gateway"></a>Disponibilità elevata tra aree mediante il gateway VPN

Per le applicazioni distribuite a livello globale o per gli scenari di failover a livello di area con disponibilità elevata è necessario connettere reti virtuali di aree diverse. Con HSM dedicato di Azure è possibile ottenere la disponibilità elevata usando un Gateway VPN che fornisce un tunnel sicuro tra le due reti virtuali. Per altre informazioni sulle connessioni da rete virtuale a rete virtuale mediante un Gateway VPN, vedere l'articolo intitolato [Che cos'è un Gateway VPN?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> Al momento il peering di rete virtuale globale in scenari di connettività fra aree non è disponibile con HSM dedicato e al suo posto si deve usare un gateway VPN. 

![rete virtuale globale](media/networking/global-vnet.png)

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti](faq.md)
- [Supporto](supportability.md)
- [Disponibilità elevata](high-availability.md)
- [Sicurezza fisica](physical-security.md)
- [Monitoraggio](monitoring.md)
- [Architettura di distribuzione](deployment-architecture.md)