---
title: Funzionalità di Firewall di Azure
description: Informazioni sulle funzionalità del firewall di Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: victorh
ms.openlocfilehash: 16e128fd61c8b0aeae017e5298ae1d8aed6e97e9
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408508"
---
# <a name="azure-firewall-features"></a>Funzionalità di Firewall di Azure

Il [firewall di Azure](overview.md) è un servizio di sicurezza di rete gestito e basato sul cloud che protegge le risorse della rete virtuale di Azure.

![Panoramica del firewall](media/overview/firewall-threat.png)

Il firewall di Azure include le funzionalità seguenti:

- [Disponibilità elevata incorporata](#built-in-high-availability)
- [Zone di disponibilità](#availability-zones)
- [Scalabilità illimitata del cloud](#unrestricted-cloud-scalability)
- [Regole di filtro del nome di dominio completo dell'applicazione](#application-fqdn-filtering-rules)
- [Regole di filtro per il traffico di rete](#network-traffic-filtering-rules)
- [Tag FQDN](#fqdn-tags)
- [Tag di servizio](#service-tags)
- [Intelligence per le minacce](#threat-intelligence)
- [Supporto SNAT in uscita](#outbound-snat-support)
- [Supporto DNAT in ingresso](#inbound-dnat-support)
- [Più indirizzi IP pubblici](#multiple-public-ip-addresses)
- [Registrazione di monitoraggio di Azure](#azure-monitor-logging)
- [Tunneling forzato](#forced-tunneling)
- [Certificazioni](#certifications)

## <a name="built-in-high-availability"></a>Disponibilità elevata predefinita

La disponibilità elevata è integrata, quindi non sono necessari servizi di bilanciamento del carico aggiuntivi e non è necessario eseguire altre configurazioni.

## <a name="availability-zones"></a>Zone di disponibilità

Firewall di Azure può essere configurato durante la distribuzione con più zone di disponibilità per una maggiore disponibilità. Con le zone di disponibilità, la disponibilità viene aumentata a un tempo di attività pari al 99,99%. Per altre informazioni, vedere il [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) per Firewall di Azure. Il contratto di servizio con un tempo di attività del 99,99% è disponibile quando si selezionano due o più zone di disponibilità.

È anche possibile associare Firewall di Azure a una zona specifica solo per motivi di prossimità, tramite il contratto di servizio standard pari al 99,95%.

La distribuzione di un firewall in una zona di disponibilità non comporta costi aggiuntivi. Sono tuttavia previsti costi aggiuntivi per i trasferimenti di dati in ingresso e in uscita associati alle zone di disponibilità. Per altre informazioni, vedere [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Le zone di disponibilità di Firewall di Azure sono disponibili nelle aree che supportano le zone di disponibilità. Per altre informazioni, vedere [Aree che supportano le zone di disponibilità in Azure](../availability-zones/az-region.md)

> [!NOTE]
> Le zone di disponibilità possono essere configurate solo durante la distribuzione. Non è possibile configurare un firewall esistente per includere le zone di disponibilità.

Per altre informazioni sulle zone di disponibilità, vedere [Aree e zone di disponibilità in Azure](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Scalabilità del cloud senza restrizioni

È possibile aumentare le prestazioni di Firewall di Azure a seconda delle esigenze in modo da soddisfare i mutevoli flussi del traffico di rete senza dover stabilire un budget per i periodi di traffico più intenso.

## <a name="application-fqdn-filtering-rules"></a>Regole di filtro del nome di dominio completo dell'applicazione

È possibile limitare il traffico HTTP/S in uscita o il traffico SQL di Azure a un elenco specificato di nomi di dominio completi (FQDN), inclusi i caratteri jolly. Questa funzionalità non richiede la terminazione TLS.

## <a name="network-traffic-filtering-rules"></a>Regole di filtro per il traffico di rete

È possibile creare in modo centralizzato regole di filtro di rete per *consentire* o *negare* il traffico in base all'indirizzo IP di origine e di destinazione, alla porta e al protocollo. Firewall di Azure è un servizio con stato completo, quindi in grado di distinguere i pacchetti validi per diversi tipi di connessioni. Le regole vengono applicate e registrate in più sottoscrizioni e reti virtuali.

## <a name="fqdn-tags"></a>Tag FQDN

I [tag FQDN](fqdn-tags.md) rendono più semplice consentire il traffico di rete noto del servizio di Azure attraverso il firewall. Ad esempio, si supponga di voler consentire il traffico di rete di Windows Update attraverso il firewall. Creare una regola di applicazione e includere il tag di Windows Update. A questo punto il traffico di rete da Windows Update può attraversare il firewall.

## <a name="service-tags"></a>Tag di servizio

Un [tag di servizio](service-tags.md) rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza. Non è possibile creare tag di servizio personalizzati, né specificare gli indirizzi IP da includere in un tag. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

## <a name="threat-intelligence"></a>Intelligence per le minacce

I filtri basati sull'[intelligence per le minacce](threat-intel.md) possono essere abilitati per il firewall per la creazione di avvisi e il rifiuto del traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

## <a name="outbound-snat-support"></a>Supporto SNAT in uscita

Tutti gli indirizzi IP del traffico di rete virtuale in uscita vengono convertiti nell'indirizzo IP pubblico di Firewall di Azure (Source Network Address Translation). È possibile identificare e consentire il traffico proveniente dalla rete virtuale a destinazioni Internet remote. Firewall di Azure non esegue la conversione degli indirizzi IP di origine (SNAT, Source Network Address Translation) quando l'indirizzo IP di destinazione è un intervallo di indirizzi IP privati in base allo standard [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Se l'organizzazione usa un intervallo di indirizzi IP pubblici per le reti private, Firewall di Azure invierà il traffico tramite SNAT a uno degli indirizzi IP privati firewall in AzureFirewallSubnet. È possibile configurare Firewall di Azure in modo da **non** usare SNAT per l'intervallo di indirizzi IP pubblici. Per altre informazioni, vedere [Intervalli di indirizzi IP privati SNAT di Firewall di Azure](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Supporto DNAT in ingresso

Il traffico di rete Internet in ingresso all'indirizzo IP pubblico del firewall viene convertito (Destination Network Address Translation) e filtrato per gli indirizzi IP nelle reti virtuali.

## <a name="multiple-public-ip-addresses"></a>Più indirizzi IP pubblici

È possibile associare al firewall [più indirizzi IP pubblici](deploy-multi-public-ip-powershell.md) (fino a 250).

Questo supporta gli scenari seguenti:

- **DNAT** - È possibile convertire più istanze di porta standard per i server back-end. Se ad esempio si dispone di due indirizzi IP pubblici, è possibile convertire la porta TCP 3389 (RDP) per entrambi gli indirizzi IP.
- **SNAT** - Sono disponibili porte aggiuntive per le connessioni SNAT in uscita, riducendo il rischio di esaurimento delle porte SNAT. Al momento, Firewall di Azure seleziona in modo casuale l'indirizzo IP pubblico di origine da usare per una connessione. Se sono presenti filtri downstream nella rete, è necessario consentire tutti gli indirizzi IP pubblici associati al firewall. Per semplificare questa configurazione, provare a usare un [prefisso di indirizzo IP pubblico](../virtual-network/public-ip-address-prefix.md).

## <a name="azure-monitor-logging"></a>Registrazione di Monitoraggio di Azure

Tutti gli eventi vengono integrati con Monitoraggio di Azure, per consentire di archiviare i log in un account di archiviazione e di trasmettere i flussi di eventi all'hub eventi o inviarli ai log di Monitoraggio di Azure. Per altre informazioni, vedere [Esercitazione: Monitorare i log e le metriche di Firewall di Azure](tutorial-diagnostics.md).

## <a name="forced-tunneling"></a>Tunneling forzato

È possibile configurare Firewall di Azure per instradare tutto il traffico associato a Internet a un hop successivo designato anziché passare direttamente a Internet. Ad esempio, è possibile che si disponga di un firewall perimetrale locale o di un'altra appliance virtuale di rete per elaborare il traffico di rete prima che venga passato a Internet. Per altre informazioni, vedere [Tunneling forzato di Firewall di Azure](forced-tunneling.md).

## <a name="certifications"></a>Certificazioni

Firewall di Azure è conforme a PCI (Payment Card Industry), SOC (Service Organization Controls), ISO (International Organization for Standardization) e ICSA Labs. Per altre informazioni, vedere [Certificazioni di conformità di Firewall di Azure](compliance-certifications.md).

## <a name="next-steps"></a>Passaggi successivi

- [Logica di elaborazione delle regole del Firewall di Azure](rule-processing.md)