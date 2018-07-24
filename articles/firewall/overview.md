---
title: Informazioni sul firewall di Azure
description: Informazioni sulle funzionalità di Firewall di Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 7/16/2018
ms.author: victorh
ms.openlocfilehash: 3657b619dc57b994158c711c46d4db6924aa2930
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089822"
---
# <a name="what-is-azure-firewall"></a>Informazioni sul firewall di Azure

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo come servizio con disponibilità elevata incorporata e scalabilità del cloud senza restrizioni. 

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

![Panoramica del firewall](media/overview/firewall-overview.png)



È possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali. Firewall di Azure usa un indirizzo IP pubblico statico per le risorse della rete virtuale consentendo ai firewall esterni di identificare il traffico proveniente dalla rete virtuale.  Il servizio è completamente integrato con Monitoraggio di Azure per la registrazione e l'analisi.

## <a name="features"></a>Funzionalità

La versione di anteprima pubblica di Firewall di Azure offre le funzionalità seguenti:

### <a name="built-in-high-availability"></a>Disponibilità elevata predefinita
La disponibilità elevata è integrata, quindi non sono necessari servizi di bilanciamento del carico aggiuntivi e non è necessario eseguire altre configurazioni.

### <a name="unrestricted-cloud-scalability"></a>Scalabilità del cloud senza restrizioni 
È possibile aumentare le prestazioni di Firewall di Azure a seconda delle esigenze in modo da soddisfare i mutevoli flussi del traffico di rete senza dover stabilire un budget per i periodi di traffico più intenso.

### <a name="fqdn-filtering"></a>Filtro dei nomi di dominio completi 
È possibile limitare il traffico HTTP/S in uscita a un elenco specifico di nomi di dominio completo (FQDN), inclusi i caratteri jolly. Questa funzionalità non richiede la terminazione SSL.

### <a name="network-traffic-filtering-rules"></a>Regole di filtro per il traffico di rete

È possibile creare in modo centralizzato regole di filtro di rete per *consentire* o *negare* il traffico in base all'indirizzo IP di origine e di destinazione, alla porta e al protocollo. Firewall di Azure è un servizio con stato completo, quindi in grado di distinguere i pacchetti validi per diversi tipi di connessioni. Le regole vengono applicate e registrate in più sottoscrizioni e reti virtuali.

### <a name="outbound-snat-support"></a>Supporto SNAT in uscita

Tutti gli indirizzi IP del traffico di rete virtuale in uscita vengono convertiti nell'indirizzo IP pubblico di Firewall di Azure (Source Network Address Translation). È possibile identificare e consentire il traffico proveniente dalla rete virtuale a destinazioni Internet remote.

### <a name="azure-monitor-logging"></a>Registrazione di Monitoraggio di Azure

Tutti gli eventi vengono integrati con Monitoraggio di Azure, che consente di archiviare i log in un account di archiviazione, trasmettere gli eventi a Hub eventi o inviarli a Log Analytics.

## <a name="known-issues"></a>Problemi noti

La versione di anteprima pubblica di Firewall di Azure presenta i problemi noti seguenti:


|Problema  |DESCRIZIONE  |Mitigazione  |
|---------|---------|---------|
|Interoperabilità con i gruppi di sicurezza di rete     |Se un gruppo di sicurezza di rete (NSG) viene applicato nella subnet del firewall, può bloccare la connettività Internet in uscita anche se il gruppo di sicurezza di rete è configurato per consentire l'accesso a Internet in uscita. Le connessioni Internet in uscita sono contrassegnate come provenienti da una rete virtuale e la destinazione è Internet. Un gruppo di sicurezza di rete ha per impostazione predefinita una regola di *autorizzazione* per consentire il traffico dalla rete virtuale alla rete virtuale, ma non quando la destinazione è Internet.|Per attenuare il problema, aggiungere la regola in ingresso seguente al gruppo di sicurezza di rete da applicare alla subnet del firewall:<br><br>Origine: Rete virtuale Porte di origine: Qualsiasi <br><br>Destinazione: Qualsiasi destinazione Porte: Qualsiasi <br><br>Protocollo: Tutti Accesso: Consenti|
|Conflitto con la funzionalità JIT (Just-in-Time) del Centro sicurezza di Azure|Se per accedere a una macchina virtuale che si trova in una subnet con una route definita dall'utente che punta a Firewall di Azure come gateway predefinito viene usata la funzionalità JIT del Centro sicurezza di Azure, questa non funziona. Ciò è dovuto al routing asimmetrico, ovvero un pacchetto viene ricevuto tramite l'indirizzo IP della macchina virtuale (accesso aperto da JIT), ma il percorso di ritorno è tramite il firewall, che elimina il pacchetto perché nel firewall non è stata stabilita alcuna sessione.|Per risolvere questo problema, posizionare le macchine virtuali JIT in una subnet separata che non contenga una route definita dall'utente al firewall.|
|Il peering globale con hub e spoke non funziona|Il modello hub e spoke, in cui l'hub e il firewall vengono distribuiti in un'unica area di Azure con gli spoke in un'altra area di Azure connessi all'hub tramite il peering reti virtuali globale, non è supportato.|Per altre informazioni, vedere [Creare, modificare o eliminare un peering reti virtuali](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)|
Le regole di filtro di rete per i protocolli non TCP/UDP (ad esempio ICMP) non funzionano per il traffico associato a Internet|Le regole di filtro di rete per i protocolli non TCP/UDP non funzionano con SNAT per l'indirizzo IP pubblico. I protocolli non TCP/UDP sono supportati tra le subnet spoke e le reti virtuali.|Firewall di Azure usa Load Balancer Standard, [che attualmente non supporta SNAT per i protocolli IP](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-standard-overview#limitations). Sono in fase di studio opzioni per supportare questo scenario in una versione futura.



## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md)
- [Distribuire Firewall di Azure con un modello](deploy-template.md)
- [Creare un ambiente di test di Firewall di Azure](scripts/sample-create-firewall-test.md)

