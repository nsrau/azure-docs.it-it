---
title: Uso di ExpressRoute con il ripristino di emergenza di macchine virtuali di Azure | Microsoft Docs
description: Procedura di uso di Azure ExpressRoute con Azure Site Recovery per il ripristino di emergenza di macchine virtuali di Azure
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 44ecbcc51cb53f4d7b68f5c5e24e7d81c5a4208c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071588"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Uso di ExpressRoute con il ripristino di emergenza di macchine virtuali di Azure

Microsoft Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Questo articolo descrive come usare ExpressRoute con Site Recovery per il ripristino di emergenza di macchine virtuali di Azure.

## <a name="prerequisites"></a>prerequisiti

Prima di iniziare, assicurarsi di avere compreso:
-   I [circuiti](../expressroute/expressroute-circuit-peerings.md) di ExpressRoute
-   I [domini di routing](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains) di ExpressRoute
-   L'[architettura di replica](azure-to-azure-architecture.md) delle macchine virtuali di Azure
-   L'[impostazione della replica](azure-to-azure-tutorial-enable-replication.md) per le macchine virtuali di Azure
-   La [procedura di failover](azure-to-azure-tutorial-failover-failback.md) delle macchine virtuali di Azure

## <a name="expressroute-and-azure-virtual-machine-replication"></a>ExpressRoute e la replica delle macchine virtuali di Azure

Quando si proteggono macchine virtuali di Azure con Site Recovery, i dati di replica vengono inviati a un account di archiviazione di Azure o a un disco gestito di replica nell'area di destinazione di Azure, a seconda del fatto che le macchine virtuali di Azure usano [dischi gestiti di Azure](../virtual-machines/windows/managed-disks-overview.md) oppure no. Anche se gli endpoint di replica sono pubblici, per impostazione predefinita il traffico di replica per la replica delle macchine virtuali di Azure non attraversa Internet, indipendentemente dall'area di Azure in cui è presente la rete virtuale di origine.

Per il ripristino di emergenza delle macchine virtuali di Azure, non è necessario ExpressRoute per la replica poiché i dati di replica non lasciano il limite di Azure. Dopo il failover nell'area di Azure di destinazione, è possibile accedere alle macchine virtuali tramite [peering privato](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Replica delle distribuzioni di Azure

In un [articolo](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity) precedente viene descritta una configurazione semplice con una sola rete virtuale di Azure connessa al data center locale del cliente tramite ExpressRoute. Le distribuzioni aziendali tipo presentano carichi di lavoro suddivisi tra più reti virtuali di Azure, mentre un hub di connettività centrale stabilisce la connettività esterna sia a Internet che alle distribuzioni locali.

Questo esempio illustra una topologia hub-spoke, comune nelle distribuzioni aziendali:
-   La distribuzione si colloca nell'area dell'**Asia orientale di Azure** e il data center locale dispone di una connessione al circuito ExpressRoute tramite un arco partner a Hong Kong.
-   Le applicazioni vengono distribuite tra due reti virtuali spoke: **Source VNet1**, con spazio degli indirizzi 10.1.0.0/24, e **Source VNet2**, con spazio degli indirizzi 10.2.0.0/24.
-   La rete virtuale hub, **Source Hub VNet**, con spazio degli indirizzi 10.10.10.0/24, funge da gatekeeper. Tutte le comunicazioni tra le subnet passano attraverso l'hub.
-   La rete virtuale hub ha due subnet: **NVA Subnet**, con spazio degli indirizzi 10.10.10.0/25, e **Gateway Subnet**, con spazio degli indirizzi 10.10.10.128/25.
-   **NVA Subnet** dispone di un'appliance virtuale di rete con indirizzo IP 10.10.10.10.
-   **Gateway Subnet** dispone di un gateway ExpressRoute collegato a una connessione ExpressRoute che indirizza al data center locale del cliente tramite un dominio di routing di peering privato.
-   Ogni rete virtuale spoke è connessa alla rete virtuale hub e tutte le operazioni di routing all'interno di questa topologia di rete sono controllate tramite tabelle di route di Azure (UDR). Tutto il traffico in uscita da una rete virtuale all'altra o al data center locale viene instradato tramite NVA Subnet.

![Dall'ambiente locale ad Azure con ExpressRoute prima del failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>Peering hub-spoke

Il peering da spoke a hub ha la configurazione seguente:
-   Allow virtual network address (Consenti indirizzo rete virtuale): Abilitato
-   Consenti traffico inoltrato: Abilitato
-   Consenti transito gateway: Disabilitato
-   Use remove gateways (Usa rimozione gateway): Abilitato

 ![Configurazione peering da spoke a hub](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

Il peering da hub a spoke ha la configurazione seguente:
-   Allow virtual network address (Consenti indirizzo rete virtuale): Abilitato
-   Consenti traffico inoltrato: Abilitato
-   Consenti transito gateway: Abilitato
-   Use remove gateways (Usa rimozione gateway): Disabilitato

 ![Configurazione peering da hub a spoke](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Abilitazione della replica per la distribuzione

Per la configurazione precedente, innanzitutto [configurare il ripristino di emergenza](azure-to-azure-tutorial-enable-replication.md) per ogni macchina virtuale mediante Site Recovery. Site Recovery è in grado di creare le reti virtuali di replica (incluse subnet e subnet gateway) nell'area di destinazione e creare i mapping necessari tra le reti virtuali di origine e di destinazione. È anche possibile creare precedentemente le reti e le subnet lato destinazione e usarle durante l'abilitazione della replica.

Site Recovery non replica tabelle di route, gateway di rete virtuale, connessioni di gateway di rete virtuale, peering di rete virtuale o alcuna altra connessione o risorsa di rete. Queste e altre risorse che non fanno parte del [processo di replica](azure-to-azure-architecture.md#replication-process) devono essere create prima o durante il failover e connesse alle risorse pertinenti. È possibile usare i potenti [piani di ripristino](site-recovery-create-recovery-plans.md) di Azure Site Recovery per automatizzare la creazione e la connessione di risorse aggiuntive tramite script di automazione.

Per impostazione predefinita, il traffico di replica non lascia il limite di Azure. In genere le distribuzioni NVA stabiliscono anche una route predefinita (0.0.0.0/0) che forza il flusso del traffico Internet in uscita attraverso NVA. In questo caso, se tutto il traffico di replica passa attraverso NVA, l'appliance può essere soggetta a limitazioni. Lo stesso vale anche quando si usano route predefinite per il routing di tutto il traffico delle macchine virtuali di Azure verso distribuzioni locali. È consigliabile [creare un endpoint di servizio di rete virtuale](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) nella rete virtuale per "Archiviazione" in modo che il traffico di replica non lasci il limite di Azure.

## <a name="failover-models-with-expressroute"></a>Modelli di failover con ExpressRoute

Quando in una macchina virtuale di Azure viene eseguito il failover a un'area diversa, la connessione ExpressRoute esistente alla rete virtuale di origine non viene trasferita automaticamente alla rete virtuale di destinazione nell'area di ripristino. Per connettere ExpressRoute alla rete virtuale di destinazione è necessaria una nuova connessione.

È possibile replicare macchine virtuali di Azure in qualsiasi area di Azure all'interno dello stesso cluster geografico, come descritto in dettaglio [qui](azure-to-azure-support-matrix.md#region-support). Se l'area di Azure scelta come destinazione non si trova nella stessa area geopolitica dell'origine, è necessario abilitare ExpressRoute Premium se si usa un circuito ExpressRoute unico per la connettività dell'area di origine e di destinazione. Per altre informazioni, vedere le [località](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e i [prezzi di ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Due circuiti ExpressRoute in due diverse località di peering di ExpressRoute
-   Questa configurazione è utile come assicurazione contro errori del circuito ExpressRoute primario ed emergenze su larga scala a livello di area, che potrebbero anche compromettere le località di peering di ExpressRoute e interrompere il circuito ExpressRoute primario.
-   Il circuito connesso all'ambiente di produzione viene in genere usato come circuito primario, mentre il circuito secondario è un operatore alternativo, in genere con larghezza di banda inferiore. È possibile aumentare la larghezza di banda del circuito secondario in un evento di emergenza, quando il circuito secondario deve assumere il ruolo di primario.
-   Con questa configurazione è possibile stabilire connessioni dal circuito ExpressRoute secondario alla rete virtuale di destinazione dopo l'esecuzione del failover o disporre di connessioni stabilite e pronte per una dichiarazione di emergenza, riducendo il tempo di ripristino complessivo. Con connessioni simultanee alle reti virtuali dell'area di origine e di destinazione, verificare che il routing locale usi il circuito e la connessione secondari solo dopo il failover.
-   Le reti virtuali di origine e di destinazione per le macchine virtuali protette con Site Recovery possono avere indirizzi IP uguali o diversi in caso di failover, a seconda delle preferenze dell'utente. In entrambi i casi, è possibile stabilire le connessioni secondarie prima di eseguire il failover.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Due circuiti ExpressRoute nella stessa località di peering di ExpressRoute
-   Questa configurazione assicura contro errori del circuito ExpressRoute primario, ma non contro emergenze su larga scala a livello di area, che potrebbero compromettere le località di peering di ExpressRoute. Con quest'ultima configurazione, possono risultare compromessi sia il circuito primario che quello secondario.
-   Le altre condizioni per indirizzi IP e connessioni rimangono uguali a quelle del caso precedente. È possibile disporre di connessioni simultanee da un data center locale alla rete virtuale di origine con il circuito primario e alla rete virtuale di destinazione con il circuito secondario. Con connessioni simultanee alle reti virtuali dell'area di origine e di destinazione, verificare che il routing locale usi il circuito e la connessione secondari solo dopo il failover.
-   Se invece i circuiti vengono creati nella stessa località di peering, non è possibile collegarli alla stessa rete virtuale.

### <a name="single-expressroute-circuit"></a>Circuito ExpressRoute singolo
-   Questa configurazione non assicura contro emergenze su larga scala a livello di area, che possono compromettere la località di peering ExpressRoute.
-   Con un singolo circuito ExpressRoute non è possibile connettere simultaneamente le reti virtuali di origine e di destinazione al circuito se nell'area di destinazione viene usato lo stesso spazio indirizzi IP.
-   Quando viene usato lo stesso spazio indirizzi IP nell'area di destinazione, la connessione lato origine deve essere disconnessa, per poi stabilire la connessione lato destinazione. Questa modifica della connessione può essere inserita in uno script come parte di un piano di ripristino.
-   In caso di errore a livello di area, se l'area primaria non è accessibile, l'operazione di disconnessione può avere esito negativo. Tale interruzione del servizio può compromettere la creazione di una connessione all'area di destinazione quando lo stesso spazio indirizzi IP viene usato nella rete virtuale di destinazione.
-   Se la creazione della connessione ha esito positivo nella destinazione e l'area primaria viene ripristinata in un secondo momento, è possibile che alcuni pacchetti vengano ignorati se due connessioni simultanee provano a connettersi allo stesso spazio indirizzi. Per impedire che un pacchetto venga ignorato, la connessione primaria deve essere interrotta immediatamente. Dopo il failback delle macchine virtuali nell'area primaria, la connessione primaria può essere nuovamente stabilita dopo avere disconnesso la connessione secondaria.
-   Se viene usato uno spazio indirizzi diverso nella rete virtuale di destinazione, è possibile connettersi contemporaneamente alle reti virtuali di origine e di destinazione dallo stesso circuito ExpressRoute.

## <a name="recovering-azure-deployments"></a>Ripristino di distribuzioni di Azure
Prendere in considerazione il modello di failover con due circuiti ExpressRoute diversi in due località di peering diverse e la conservazione di indirizzi IP privati per le macchine virtuali di Azure protette. L'area di ripristino di destinazione è Asia sud-orientale di Azure e viene stabilita una connessione al circuito ExpressRoute secondario tramite un arco partner a Singapore.

Per automatizzare il ripristino dell'intera distribuzione, oltre alla replica di macchine virtuali e reti virtuali devono essere create anche altre connessioni e risorse di rete rilevanti. Per le versioni precedenti della topologia di rete di hub-spoke, è necessario attenersi alla procedura seguente durante o dopo l'operazione di [failover](azure-to-azure-tutorial-failover-failback.md):
1.  Creare il gateway ExpressRoute di Azure nella rete virtuale dell'hub dell'area di destinazione. Il gateway ExpressRoute è necessario per connettere la rete virtuale dell'hub di destinazione al circuito ExpressRoute.
2.  Creare la connessione di rete virtuale dalla rete virtuale dell'hub di destinazione al circuito ExpressRoute di destinazione.
3.  Configurare i peering delle reti virtuali tra l'hub dell'area di destinazione e le reti virtuali spoke. Le proprietà di peering nell'area di destinazione saranno identiche a quelle dell'area di origine.
4.  Configurare gli UDR nella rete virtuale hub e nelle due reti virtuali spoke. Quando si usano gli stessi indirizzi IP, le proprietà degli UDR lato destinazione sono identiche a quelle dal lato origine. Con indirizzi IP di destinazione diversi, gli UDR devono essere modificati di conseguenza.

La procedura precedente può essere inserita in uno script come parte di un [piano di ripristino](site-recovery-create-recovery-plans.md). A seconda dei requisiti di connettività dell'applicazione e tempo di ripristino, la procedura precedente può anche essere completata prima di avviare il failover.

Dopo il ripristino delle macchine virtuali e il completamento degli altri passaggi di connettività, l'ambiente di ripristino è simile al seguente: ![Da locale ad Azure con ExpressRoute dopo il failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

Un semplice esempio di topologia per il ripristino di emergenza delle macchine virtuali di Azure con un singolo circuito ExpressRoute, con lo stesso IP in macchine virtuali di destinazione, è descritto in dettaglio [qui](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>Considerazioni relative all'obiettivo del tempo di ripristino (RTO)
Per ridurre il tempo di ripristino complessivo per la distribuzione, si consiglia di eseguire il provisioning e la distribuzione dei [componenti di rete](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) aggiuntivi dell'area di destinazione, come i gateway di rete virtuale descritti in precedenza. La distribuzione di risorse aggiuntive comporta un breve tempo di inattività che, se non preso in considerazione durante la pianificazione, può compromettere il tempo di ripristino complessivo.

È consigliabile eseguire periodicamente [analisi di ripristino di emergenza](azure-to-azure-tutorial-dr-drill.md) per le distribuzioni protette. Un'analisi consente di convalidare la strategia di replica senza tempi di inattività o perdite di dati e non compromette in alcun modo l'ambiente di produzione. L'esecuzione di un'analisi consente anche di evitare problemi di configurazione dell'ultimo minuto che possono compromettere l'obiettivo del tempo di ripristino. È consigliabile usare la rete di una macchina virtuale di Azure separata per il failover di test e non la rete predefinita che è stata configurata al momento dell'abilitazione della replica.

Se si usa un singolo circuito ExpressRoute, è consigliabile usare uno spazio indirizzi IP diverso per la rete virtuale di destinazione per evitare problemi di attivazione della connessione in caso di emergenze a livello di area. Se non è possibile usare indirizzi IP diversi per l'ambiente di produzione recuperato, è necessario eseguire il failover di test di analisi del ripristino di emergenza in una rete di test distinta con indirizzi IP diversi, dato che non è possibile connettere due reti virtuali con spazi indirizzi IP sovrapposti allo stesso circuito ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [circuiti di ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Altre informazioni sui [domini di routing di ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Altre informazioni sulle [località di ExpressRoute](../expressroute/expressroute-locations.md).
- Leggere altre informazioni sui [piani di ripristino](site-recovery-create-recovery-plans.md) per automatizzare il failover delle applicazioni.
