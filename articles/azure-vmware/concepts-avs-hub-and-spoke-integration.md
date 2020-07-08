---
title: "Concetto: integrare una distribuzione della soluzione VMware di Azure (AVS) in un'architettura Hub e spoke"
description: Informazioni sulle raccomandazioni per l'integrazione di una distribuzione della soluzione VMware di Azure (AVS) in una nuova architettura di hub e spoke in Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 82937e04fc0a5101c353702b92b6b068d027d7ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85374974"
---
# <a name="integrate-azure-vmware-solution-avs-in-a-hub-and-spoke-architecture"></a>Integrare la soluzione VMware di Azure (AVS) in un'architettura Hub e spoke

In questo articolo vengono forniti consigli per l'integrazione di una distribuzione di soluzione VMware di Azure (AVS) in una nuova [architettura Hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/shared-services) di Azure. 

Lo scenario Hub e spoke presuppone un ambiente cloud ibrido con carichi di lavoro in:

* Azure nativo con servizi IaaS o PaaS
* AVS 
* vSphere locale

## <a name="architecture"></a>Architecture

L' *Hub* è una rete virtuale di Azure che funge da punto centrale di connettività al cloud privato locale e AVS. I *spoke* sono reti virtuali con peering con l'hub per abilitare la comunicazione tra reti virtuali.

Il traffico tra il Data Center locale, AVS private cloud e l'hub passa attraverso le connessioni ExpressRoute. Le reti virtuali spoke contengono in genere carichi di lavoro basati su IaaS, ma possono avere servizi PaaS come [ambiente del servizio app](../app-service/environment/intro.md), che ha l'integrazione diretta con la rete virtuale o altri servizi PaaS con [collegamento privato di Azure](https://docs.microsoft.com/azure/private-link/) abilitato. 

Il diagramma mostra un esempio di una distribuzione Hub e spoke in Azure connessa a locale e da AVS a ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Distribuzione di AVS Hub e spoke Integration":::




L'architettura include i componenti principali seguenti:

-   **Sito locale:** I data center locali del cliente sono connessi ad Azure tramite una connessione Express route.

-   **Cloud privato AVS:** AVS SDDC formato da uno o più cluster vSphere, ognuno con un massimo di 16 nodi.

-   **Gateway ExpressRoute:** Consente la comunicazione tra AVS private cloud, la rete locale, i servizi condivisi nella rete virtuale Hub e i carichi di lavoro in esecuzione su reti virtuali spoke.

    > [!NOTE]
    > **Considerazioni sulla VPN S2S:** Per le distribuzioni di produzione AVS, Azure S2S non è supportato a causa dei requisiti di rete per HCX. Tuttavia, per una distribuzione PoC o non di produzione che non richiede HCX, può essere usata.

-   **Rete virtuale Hub:** Funge da punto centrale di connettività alla rete locale e al cloud privato AVS.

-   **Rete virtuale spoke**

    -   **IaaS spoke:** Un IaaS spoke ospiterà i carichi di lavoro basati su IaaS di Azure, inclusi i set di disponibilità delle VM e i set di scalabilità di macchine virtuali e i componenti di rete corrispondenti.

    -   **PaaS spoke:** Un PaaS spoke ospita i servizi PaaS di Azure usando l'indirizzamento privato grazie all' [endpoint privato](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) e al [collegamento privato](https://docs.microsoft.com/azure/private-link/private-link-overview).

-   **Firewall di Azure:** Funge da elemento centrale per segmentare il traffico tra spoke, locale e AVS.

-   **Gateway applicazione:** Espone e protegge le app Web che vengono eseguite in macchine virtuali (VM) Azure IaaS/PaaS o AVS. Si integra con altri servizi, ad esempio gestione API.

## <a name="network-and-security-considerations"></a>Considerazioni sulla rete e sulla sicurezza

Le connessioni ExpressRoute consentono il flusso del traffico tra l'ambiente locale, AVS e l'infrastruttura di rete di Azure. AVS USA [ExpressRoute copertura globale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) per implementare questa connettività.

La connettività locale può usare anche ExpressRoute Copertura globale, ma non è obbligatoria.

* **Flusso del traffico da locale a AVS**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Flusso del traffico da locale a AVS":::


* **Flusso del traffico VNET da AVS a hub**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Flusso del traffico di rete virtuale da AVS a hub":::


Per ulteriori informazioni sui concetti relativi alla rete e alla connettività AVS, vedere la [documentazione del prodotto AVS](https://docs.microsoft.com/azure/azure-vmware/concepts-networking).

### <a name="traffic-segmentation"></a>Segmentazione del traffico

Il [firewall di Azure](https://docs.microsoft.com/azure/firewall/) è la parte centrale della topologia hub e spoke, distribuita nella rete virtuale dell'hub. Usare il firewall di Azure o un'altra appliance virtuale di rete supportata da Azure per stabilire le regole di traffico e segmentare la comunicazione tra i diversi spoke, locali e i carichi di lavoro AVS.

Creare tabelle di route per indirizzare il traffico al firewall di Azure.  Per le reti virtuali spoke, creare una route che imposta la route predefinita per l'interfaccia interna del firewall di Azure, in questo modo quando un carico di lavoro nella rete virtuale deve raggiungere lo spazio degli indirizzi AVS che il firewall può valutare e applicare la regola di traffico corrispondente per consentire o negare il problema.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Creare tabelle di route per indirizzare il traffico al firewall di Azure":::


> [!IMPORTANT]
> Una route con prefisso dell'indirizzo 0.0.0.0/0 nell'impostazione **GatewaySubnet** non è supportata.

Impostare le route per reti specifiche nella tabella di route corrispondente. Ad esempio, le route per raggiungere i prefissi IP di gestione e dei carichi di lavoro AVS dall'ambiente locale e viceversa, il routing di tutto il traffico da locale a AVS private cloud tramite il firewall di Azure.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Impostare le route per reti specifiche nella tabella di route corrispondente":::

Un secondo livello di segmentazione del traffico che usa i gruppi di sicurezza di rete nei spoke e nell'hub per creare criteri di traffico più granulari. 


### <a name="application-gateway"></a>Gateway applicazione

Applicazione Azure gateway V1 e V2 sono stati testati con app Web eseguite in macchine virtuali AVS come pool back-end. Il gateway applicazione è attualmente l'unico metodo supportato per esporre le app Web in esecuzione su macchine virtuali AVS a Internet. Può inoltre esporre le app agli utenti interni in modo sicuro.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Secondo livello di segmentazione del traffico con i gruppi di sicurezza di rete":::


### <a name="jumpbox-and-azure-bastion"></a>JumpBox e Azure Bastion

Accedere all'ambiente AVS con JumpBox, ovvero una macchina virtuale Windows 10 o Windows Server distribuita nella subnet del servizio condiviso all'interno della rete virtuale dell'hub.

Come procedura di sicurezza consigliata, distribuire [Microsoft Azure servizio Bastion](https://docs.microsoft.com/azure/bastion/) all'interno della rete virtuale dell'hub. Azure Bastion offre accesso RDP e SSH semplice alle macchine virtuali distribuite in Azure senza la necessità di effettuare il provisioning di indirizzi IP pubblici a tali risorse. Dopo aver effettuato il provisioning del servizio Azure Bastion, è possibile accedere alla macchina virtuale selezionata dalla portale di Azure. Dopo aver stabilito la connessione, viene visualizzata una nuova scheda, che mostra il desktop di JumpBox e da tale desktop, è possibile accedere al piano di gestione del cloud privato AVS.

> [!IMPORTANT]
> Non assegnare un indirizzo IP pubblico alla VM JumpBox o esporre la porta 3389/TCP alla rete Internet pubblica. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Rete virtuale dell'hub Bastion di Azure":::


## <a name="azure-dns-resolution-considerations"></a>Considerazioni sulla risoluzione DNS di Azure

Per la risoluzione DNS di Azure sono disponibili due opzioni:

-   Usare i controller di dominio Azure Active Directory (Azure AD) distribuiti nell'hub (descritto in [considerazioni sulle identità](#identity-considerations)) come server dei nomi.

-   Distribuire e configurare una zona privata di DNS di Azure.

L'approccio migliore consiste nel combinare entrambi per fornire una risoluzione dei nomi affidabile per AVS, in locale e Azure.

Come raccomandazione di progettazione generale, usare l'infrastruttura DNS di Azure esistente (in questo caso, Active Directory DNS integrato) distribuita in almeno due macchine virtuali di Azure distribuite nella rete virtuale dell'hub e configurate nelle reti virtuali spoke per usare i server DNS di Azure nelle impostazioni DNS.

Azure DNS privato può ancora essere usato in cui la zona di DNS privato di Azure è collegata alle reti virtuali e i server DNS vengono usati come sistemi di risoluzione ibrido con l'invio condizionale ai nomi DNS in locale o AVS che sfruttano l'infrastruttura di Azure DNS privato del cliente.

Esistono diverse considerazioni da considerare per le zone private di DNS di Azure:

* È necessario abilitare la registrazione automatica per DNS di Azure per gestire automaticamente il ciclo di vita dei record DNS per le macchine virtuali distribuite in reti virtuali spoke.
* Il numero massimo di zone DNS private a cui è possibile collegare una rete virtuale con autoregistration Enabled è solo uno.
* Il numero massimo di zone DNS private a cui è possibile collegare una rete virtuale è 1000 senza che sia abilitata la registrazione.

I server locali e AVS possono essere configurati con i server d'inoltri condizionali per le macchine virtuali resolver in Azure per la zona DNS privato di Azure.

## <a name="identity-considerations"></a>Considerazioni sull'identità

Per motivi di identità, l'approccio migliore consiste nel distribuire almeno un controller di dominio di Active Directory nell'hub, usando la subnet del servizio condiviso, idealmente due di essi in modalità distribuita in zone o in un set di disponibilità di VM. Vedere [centro architetture di Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain) per estendere il dominio di Active Directory locale ad Azure.

Inoltre, distribuire un altro controller di dominio sul lato AVS per fungere da identità e origine DNS all'interno dell'ambiente vSphere.

Per vCenter e SSO, impostare un'origine identità nel portale di Azure, in **Gestisci \> \> origini identità**Identity.

Come procedura consigliata, integrare [dominio ad con Azure Active Directory](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: https://docs.microsoft.com/azure/architecture/

[Hub & Spoke topology]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: https://docs.microsoft.com/azure/networking/

<!-- LINKS - internal -->


