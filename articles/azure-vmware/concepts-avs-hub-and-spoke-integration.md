---
title: "Concetto: integrare una distribuzione della soluzione VMware di Azure in un'architettura Hub e spoke"
description: Informazioni sulle raccomandazioni per l'integrazione di una distribuzione della soluzione VMware di Azure in una nuova architettura di hub e spoke in Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: a1ba75369a1d6f19ca7ae1f36107170f219f72d0
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649585"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integrare la soluzione VMware di Azure in un'architettura Hub e spoke

Questo articolo fornisce consigli per l'integrazione di una distribuzione della soluzione VMware di Azure in una nuova [architettura di hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/shared-services) in Azure. 

Lo scenario Hub e spoke presuppone un ambiente cloud ibrido con carichi di lavoro in:

* Azure nativo con servizi IaaS o PaaS
* Soluzione Azure VMware 
* vSphere locale

## <a name="architecture"></a>Architecture

L' *Hub* è una rete virtuale di Azure che funge da punto centrale di connettività al cloud privato della soluzione VMware locale e di Azure. I *spoke* sono reti virtuali con peering con l'hub per abilitare la comunicazione tra reti virtuali.

Il traffico tra il Data Center locale, il cloud privato della soluzione VMware di Azure e l'hub passa attraverso le connessioni ExpressRoute. Le reti virtuali spoke contengono in genere carichi di lavoro basati su IaaS, ma possono avere servizi PaaS come [ambiente del servizio app](../app-service/environment/intro.md), che ha l'integrazione diretta con la rete virtuale o altri servizi PaaS con [collegamento privato di Azure](../private-link/index.yml) abilitato. 

Il diagramma mostra un esempio di una distribuzione Hub e spoke in Azure connessa alla soluzione locale e VMware di Azure tramite ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Distribuzione dell'hub della soluzione VMware e dell'integrazione spoke di Azure":::




L'architettura include i componenti principali seguenti:

-   **Sito locale:** I data center locali del cliente sono connessi ad Azure tramite una connessione ExpressRoute.

-   **Cloud privato della soluzione VMware di Azure:** Soluzione VMware di Azure SDDC formato da uno o più cluster vSphere, ognuno con un massimo di 16 nodi.

-   **Gateway ExpressRoute:** Abilita la comunicazione tra il cloud privato della soluzione VMware di Azure, la rete locale, i servizi condivisi nella rete virtuale Hub e i carichi di lavoro in esecuzione su reti virtuali spoke.

    > [!NOTE]
    > **Considerazioni sulla VPN S2S:** Per le distribuzioni di produzione di soluzioni VMware di Azure, Azure S2S non è supportato a causa dei requisiti di rete per HCX. Tuttavia, per una distribuzione PoC o non di produzione che non richiede HCX, può essere usata.

-   **Rete virtuale Hub:** Funge da punto centrale di connettività alla rete locale e al cloud privato della soluzione VMware di Azure.

-   **Rete virtuale spoke**

    -   **IaaS spoke:** Un IaaS spoke ospita carichi di lavoro basati su IaaS di Azure, inclusi i set di disponibilità di VM e i set di scalabilità di macchine virtuali e i componenti di rete corrispondenti.

    -   **PaaS spoke:** Un PaaS spoke ospita i servizi PaaS di Azure usando l'indirizzamento privato grazie all' [endpoint privato](../private-link/private-endpoint-overview.md) e al [collegamento privato](../private-link/private-link-overview.md).

-   **Firewall di Azure:** Funge da componente centrale per segmentare il traffico tra i spoke, l'ambiente locale e la soluzione VMware di Azure.

-   **Gateway applicazione:** Espone e protegge le app Web che vengono eseguite in macchine virtuali (VM) Azure IaaS/PaaS o Azure VMware Solution. Si integra con altri servizi, ad esempio gestione API.

## <a name="network-and-security-considerations"></a>Considerazioni sulla rete e sulla sicurezza

Le connessioni ExpressRoute consentono il flusso del traffico tra l'ambiente locale, la soluzione VMware di Azure e l'infrastruttura di rete di Azure. La soluzione VMware di Azure usa [ExpressRoute copertura globale](../expressroute/expressroute-global-reach.md) per implementare questa connettività.

La connettività locale può usare anche ExpressRoute Copertura globale, ma non è obbligatoria.

* **Flusso del traffico della soluzione VMware da sito locale ad Azure**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Flusso del traffico della soluzione VMware da sito locale ad Azure":::


* **Flusso del traffico di VNET di Azure da soluzione VMware a hub**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Flusso di traffico della rete virtuale di Azure da soluzione VMware a hub":::


Per ulteriori informazioni sui concetti relativi alla rete e alla connettività della soluzione VMware di Azure, vedere la [documentazione del prodotto della soluzione VMware di Azure](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentazione del traffico

Il [firewall di Azure](../firewall/index.yml) è la parte centrale della topologia hub e spoke, distribuita nella rete virtuale dell'hub. Usare il firewall di Azure o un'altra appliance virtuale di rete supportata da Azure per stabilire regole di traffico e segmentare la comunicazione tra i diversi spoke, in locale e i carichi di lavoro della soluzione VMware di Azure.

Creare tabelle di route per indirizzare il traffico al firewall di Azure.  Per le reti virtuali spoke, creare una route che imposta la route predefinita per l'interfaccia interna del firewall di Azure, in questo modo quando un carico di lavoro nella rete virtuale deve raggiungere lo spazio di indirizzi della soluzione VMware di Azure che il firewall può valutare e applicare la regola di traffico corrispondente per consentire o negare il problema.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Creare tabelle di route per indirizzare il traffico al firewall di Azure":::


> [!IMPORTANT]
> Una route con prefisso dell'indirizzo 0.0.0.0/0 nell'impostazione **GatewaySubnet** non è supportata.

Impostare le route per reti specifiche nella tabella di route corrispondente. Ad esempio, route per raggiungere i prefissi IP di gestione delle soluzioni VMware di Azure e i carichi di lavoro da locale e viceversa, indirizzando tutto il traffico dal cloud privato alla soluzione VMware locale al cloud di Azure tramite il firewall di Azure.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Impostare le route per reti specifiche nella tabella di route corrispondente":::

Un secondo livello di segmentazione del traffico che usa i gruppi di sicurezza di rete nei spoke e nell'hub per creare criteri di traffico più granulari. 


### <a name="application-gateway"></a>Gateway applicazione

Applicazione Azure gateway V1 e V2 sono stati testati con app Web eseguite in macchine virtuali della soluzione VMware di Azure come pool back-end. Il gateway applicazione è attualmente l'unico metodo supportato per esporre le app Web in esecuzione su macchine virtuali della soluzione VMware di Azure a Internet. Può inoltre esporre le app agli utenti interni in modo sicuro.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Secondo livello di segmentazione del traffico con i gruppi di sicurezza di rete":::


### <a name="jumpbox-and-azure-bastion"></a>JumpBox e Azure Bastion

Accedere all'ambiente della soluzione VMware di Azure con JumpBox, ovvero una macchina virtuale Windows 10 o Windows Server distribuita nella subnet del servizio condiviso all'interno della rete virtuale dell'hub.

Come procedura di sicurezza consigliata, distribuire [Microsoft Azure servizio Bastion](../bastion/index.yml) all'interno della rete virtuale dell'hub. Azure Bastion offre accesso RDP e SSH semplice alle macchine virtuali distribuite in Azure senza la necessità di effettuare il provisioning di indirizzi IP pubblici a tali risorse. Dopo aver effettuato il provisioning del servizio Azure Bastion, è possibile accedere alla macchina virtuale selezionata dalla portale di Azure. Dopo aver stabilito la connessione, viene visualizzata una nuova scheda, che mostra il desktop di JumpBox e da tale desktop, è possibile accedere al piano di gestione del cloud privato della soluzione VMware di Azure.

> [!IMPORTANT]
> Non assegnare un indirizzo IP pubblico alla VM JumpBox o esporre la porta 3389/TCP alla rete Internet pubblica. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Rete virtuale dell'hub Bastion di Azure":::


## <a name="azure-dns-resolution-considerations"></a>Considerazioni sulla risoluzione DNS di Azure

Per la risoluzione DNS di Azure sono disponibili due opzioni:

-   Usare i controller di dominio Azure Active Directory (Azure AD) distribuiti nell'hub (descritto in [considerazioni sulle identità](#identity-considerations)) come server dei nomi.

-   Distribuire e configurare una zona privata di DNS di Azure.

L'approccio migliore consiste nel combinare entrambi per offrire una risoluzione dei nomi affidabile per la soluzione VMware di Azure, in locale e in Azure.

Come raccomandazione di progettazione generale, usare l'infrastruttura DNS di Azure esistente (in questo caso, Active Directory DNS integrato) distribuita in almeno due macchine virtuali di Azure distribuite nella rete virtuale dell'hub e configurate nelle reti virtuali spoke per usare i server DNS di Azure nelle impostazioni DNS.

Azure DNS privato può ancora essere usato in cui la zona di DNS privato di Azure è collegata alle reti virtuali e i server DNS vengono usati come sistemi di risoluzione ibrido con l'invio condizionale alla soluzione VMware locale/Azure che esegue DNS sfruttando l'infrastruttura di Azure DNS privato del cliente.

Esistono diverse considerazioni da considerare per le zone private di DNS di Azure:

* È necessario abilitare la registrazione automatica per DNS di Azure per gestire automaticamente il ciclo di vita dei record DNS per le macchine virtuali distribuite in reti virtuali spoke.
* Il numero massimo di zone DNS private a cui è possibile collegare una rete virtuale con autoregistration Enabled è solo uno.
* Il numero massimo di zone DNS private a cui è possibile collegare una rete virtuale è 1000 senza che sia abilitata la registrazione.

I server della soluzione VMware locale e di Azure possono essere configurati con i server di trasmissione condizionali per le macchine virtuali resolver in Azure per la zona DNS privato di Azure.

## <a name="identity-considerations"></a>Considerazioni sull'identità

Per motivi di identità, l'approccio migliore consiste nel distribuire almeno un controller di dominio di Active Directory nell'hub, usando la subnet del servizio condiviso, idealmente due di essi in modalità distribuita in zone o in un set di disponibilità di VM. Vedere [centro architetture di Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain) per estendere il dominio di Active Directory locale ad Azure.

Inoltre, distribuire un altro controller di dominio nel lato della soluzione VMware di Azure per fungere da identità e origine DNS nell'ambiente vSphere.

Per vCenter e SSO, impostare un'origine identità nel portale di Azure, in **Gestisci \> \> origini identità**Identity.

Come procedura consigliata, integrare [dominio ad con Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
