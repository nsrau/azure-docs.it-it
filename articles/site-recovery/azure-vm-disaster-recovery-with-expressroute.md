---
title: Integrare Azure ExpressRoute con il ripristino di emergenza per le macchine virtuali di Azure con il servizio Azure Site Recovery | Microsoft Docs
description: Viene descritto come configurare il ripristino di emergenza per le macchine virtuali di Azure tramite Azure Site Recovery e Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 16b4031c0242d79b6d866d612a4d4f594dc608fa
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821950"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrare Azure ExpressRoute con il ripristino di emergenza per le macchine virtuali di Azure


Questo articolo descrive come integrare Azure ExpressRoute con [Azure Site Recovery](site-recovery-overview.md), quando si configura il ripristino di emergenza per le macchine virtuali di Azure in un'area di Azure secondaria.

Site Recovery consente il ripristino di emergenza delle macchine virtuali di Azure tramite la replica in Azure dei dati delle macchine virtuali di Azure.

- Se le macchine virtuali di Azure usano [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), i dati delle macchine virtuali vengono replicati in un disco gestito replicato nell'area secondaria.
- Se le macchine virtuali di Azure non usano dischi gestiti, i dati delle macchine virtuali vengono replicati in un account di archiviazione di Azure.
- Gli endpoint di replica sono pubblici, ma il traffico di replica per le macchine virtuali di Azure non attraversa Internet.

ExpressRoute consente di estendere le reti locali nel cloud Microsoft Azure tramite una connessione privata fornita da un provider di connettività. Se è stato configurato ExpressRoute, questo si integra con Site Recovery nel modo seguente:

- **Durante la replica tra aree di Azure**: il traffico di replica per il ripristino di emergenza delle macchine virtuali di Azure è solo all'interno di Azure; ExpressRoute non è necessario o usato per la replica. Tuttavia, se ci si connette da un sito locale alle macchine virtuali di Azure nel sito di Azure primario, è necessario tenere presenti alcuni aspetti durante la configurazione del ripristino di emergenza per le macchine virtuali di Azure.
- **Failover tra aree di Azure**: quando si verificano interruzioni, eseguire il failover delle macchine virtuali di Azure dal server primario all'area di Azure secondaria. Dopo il failover in un'area secondaria, esistono una serie di passaggi da effettuare per poter accedere alle macchine virtuali di Azure nell'area secondaria tramite ExpressRoute.


## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare, è necessario comprendere i concetti illustrati di seguito:

- I [circuiti](../expressroute/expressroute-circuit-peerings.md) di ExpressRoute
- I [domini di routing](../expressroute/expressroute-circuit-peerings.md#routingdomains) di ExpressRoute
- [Località](../expressroute/expressroute-locations.md) per ExpressRoute.
- [Architettura della replica](azure-to-azure-architecture.md) per le macchine virtuali di Azure
- Come [configurare la replica](azure-to-azure-tutorial-enable-replication.md) per le macchine virtuali di Azure.
- Come eseguire il [failover](azure-to-azure-tutorial-failover-failback.md) delle macchine virtuali di Azure.


## <a name="general-recommendations"></a>Raccomandazioni generali

Come procedura consigliata e per garantire obiettivi del punto di ripristino (RPO) efficienti per il ripristino di emergenza, è consigliabile effettuare le operazioni seguenti al momento della configurazione di Site Recovery per l'integrazione con ExpressRoute:

- Effettuare il provisioning di componenti di rete prima del failover in un'area secondaria:
    - Quando si abilita la replica per le macchine virtuali di Azure, Site Recovery può distribuire automaticamente le risorse di rete, ad esempio reti, subnet e gateway, nell'area di destinazione Azure in base alle impostazioni della rete di origine.
    - Site Recovery non è in grado di configurare automaticamente risorse di rete come i gateway di rete virtuale.
    - È consigliabile effettuare il provisioning di queste risorse di rete aggiuntive prima di eseguire il failover. A questa distribuzione è associato un breve tempo di inattività, che può influire sul tempo di ripristino complessivo, se non ne viene tenuto conto durante la pianificazione della distribuzione.
- Eseguire esercitazioni periodiche sul ripristino di emergenza:
    - Un'analisi consente di convalidare la strategia di replica senza tempi di inattività o perdite di dati e non ha alcun impatto sull'ambiente di produzione. Permette inoltre di evitare problemi di configurazione dell'ultimo minuto che possono compromettere l'obiettivo del tempo di ripristino.
    - Quando si esegue un failover di test per l'esercitazione, è consigliabile usare la rete di una macchina virtuale di Azure separata e non la rete predefinita che è stata configurata al momento dell'abilitazione della replica.
- Usare spazi di indirizzi IP diversi se si dispone di un singolo circuito ExpressRoute.
    - È consigliabile usare un altro spazio di indirizzi IP per la rete virtuale di destinazione. Questo consente di evitare problemi quando si stabiliscono connessioni durante le interruzioni a livello di area.
    - Se non è possibile usare uno spazio di indirizzi distinto, assicurarsi di eseguire il failover di test per l'esercitazione sul ripristino di emergenza in una rete di test separata con indirizzi IP diversi. Non è possibile connettere due reti virtuali con uno spazio di indirizzi IP sovrapposto allo stesso circuito ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replicare le macchine virtuali di Azure durante l'uso di ExpressRoute


Se si vuole configurare la replica delle macchine virtuali di Azure in un sito primario e ci si connette a queste macchine virtuali dal sito locale tramite ExpressRoute, ecco come procedere:

1. [Abilitare la replica](azure-to-azure-tutorial-enable-replication.md) per ogni macchina virtuale di Azure.
2. Facoltativamente, consentire a Site Recovery di configurare la rete:
    - Quando si configura e si abilita la replica, Site Recovery configura le reti, le subnet e le subnet del gateway nell'area di Azure di destinazione in modo che corrispondano a quelle nell'area di origine. Site Recovery esegue anche il mapping tra le reti virtuali di origine e di destinazione.
    - Se non si vuole che questa operazione venga eseguita automaticamente da Site Recovery, creare le risorse di rete sul lato di destinazione prima di abilitare la replica.
3. Creare gli altri elementi di rete:
    - Site Recovery non crea le tabelle di route, i gateway di rete virtuale, le connessioni dei gateway di rete virtuale, il peering reti virtuali o altre risorse e connessioni di rete nell'area secondaria.
    - È necessario creare questi elementi di rete aggiuntivi nell'area secondaria, in qualsiasi momento prima di eseguire un failover dall'area primaria.
    - È possibile usare i [piani di ripristino](site-recovery-create-recovery-plans.md) e gli script di automazione per configurare e connettere le risorse di rete.
1. Se si dispone di un'appliance virtuale di rete distribuita per controllare il flusso del traffico di rete, tenere presente che:
    - La route di sistema predefinita di Azure per la replica delle macchine virtuali di Azure è 0.0.0.0/0.
    - In genere le distribuzioni di appliance virtuali di rete (NVA) stabiliscono anche una route predefinita (0.0.0.0/0) che forza il flusso del traffico Internet in uscita attraverso tali appliance. La route predefinita viene usata quando non è possibile trovare un'altra configurazione di route specifica.
    - In questo caso, l'appliance virtuale di rete potrebbe risultare sovraccarica se tutto il traffico di replica la attraversa.
    - La stessa limitazione vale anche quando si usano route predefinite per il routing di tutto il traffico delle macchine virtuali di Azure verso distribuzioni locali.
    - In questo scenario, è consigliabile [creare un endpoint servizio di rete](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) nella rete virtuale per il servizio Microsoft.Storage, in modo che il traffico di replica non lasci il limite di Azure.

## <a name="replication-example"></a>Esempio di replica

In genere, le distribuzioni aziendali presentano carichi di lavoro suddivisi tra più reti virtuali di Azure, con un hub di connettività centrale per la connettività esterna sia a Internet che ai siti locali. Solitamente, viene usata una topologia hub-spoke insieme a ExpressRoute.

![Dall'ambiente locale ad Azure con ExpressRoute prima del failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Area**. Le app sono distribuite nell'area Asia orientale di Azure.
- **Reti virtuali spoke**. Le app sono distribuite in due reti virtuali spoke:
    - **Rete virtuale 1 di origine**: 10.1.0.0/24.
    - **Rete virtuale 2 di origine**: 10.2.0.0/24.
    - Ogni rete virtuale spoke è connessa alla **rete virtuale dell'hub**.
- **Rete virtuale dell'hub**. È presente la rete virtuale hub **Source Hub vNet**: 10.10.10.0/24.
    - Questa rete virtuale hub opera come gatekeeper.
    - Tutte le comunicazioni tra le subnet passano attraverso questo hub.
 - ****Subnet della rete virtuale hub**. La rete virtuale hub comprende due subnet:
     - **NVA subnet**: 10.10.10.0/25. Questa subnet contiene un'appliance virtuale di rete (10.10.10.10).
     - **Subnet del gateway**: 10.10.10.128/25. Questa subnet contiene un gateway ExpressRoute collegato a una connessione ExpressRoute che indirizza al sito locale tramite un dominio di routing di peering privato.
- Il data center locale dispone di una connessione al circuito ExpressRoute tramite un'appliance perimetrale partner a Hong Kong.
- Tutto il routing è controllato tramite le tabelle di route di Azure (routing definito dall'utente).
- Tutto il traffico in uscita tra le reti virtuali o al data center locale viene instradato tramite NVA Subnet.

### <a name="hub-and-spoke-peering-settings"></a>Impostazioni di peering hub-spoke

#### <a name="spoke-to-hub"></a>Da spoke a hub

**Direzione** | **Impostazione** | **State**
--- | --- | ---
Da spoke a hub | Allow virtual network address (Consenti indirizzo rete virtuale) | Attivato
Da spoke a hub | Consenti traffico inoltrato | Attivato
Da spoke a hub | Consenti transito gateway | Disabled
Da spoke a hub | Usa gateway remoti | Attivato

 ![Configurazione peering da spoke a hub](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Da hub a spoke

**Direzione** | **Impostazione** | **State**
--- | --- | ---
Da hub a spoke | Allow virtual network address (Consenti indirizzo rete virtuale) | Attivato
Da hub a spoke | Consenti traffico inoltrato | Attivato
Da hub a spoke | Consenti transito gateway | Attivato
Da hub a spoke | Usa gateway remoti | Disabled

 ![Configurazione peering da hub a spoke](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Procedure di esempio

In questo esempio, dovrebbe verificarsi quanto segue quando si abilita la replica delle macchine virtuali di Azure nella rete di origine:

1. [Abilitare la replica](azure-to-azure-tutorial-enable-replication.md) per una macchina virtuale.
2. Site Recovery crea reti virtuali, subnet e subnet del gateway di replica nell'area di destinazione.
3. Site Recovery crea i mapping tra le reti di origine e le reti di destinazione di replica create.
4. Creare manualmente gateway di rete virtuale, connessioni di gateway di rete virtuale, peering di rete virtuale o qualsiasi altra connessione o risorsa di rete.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Eseguire il failover delle macchine virtuali di Azure durante l'uso di ExpressRoute

Dopo aver eseguito il failover delle macchine virtuali di Azure nell'area di Azure di destinazione tramite Site Recovery, è possibile accedervi mediante il [peering privato](../expressroute/expressroute-circuit-peerings.md#privatepeering) di ExpressRoute.

- È necessario connettere ExpressRoute alla rete virtuale di destinazione con una nuova connessione. La connessione ExpressRoute esistente non viene trasferita automaticamente.
- Il modo in cui deve essere configurata la connessione ExpressRoute alla rete virtuale di destinazione dipende dalla topologia di ExpressRoute.


### <a name="access-with-two-circuits"></a>Accesso con due circuiti

#### <a name="two-circuits-with-two-peering-locations"></a>Due circuiti con due località di peering

Questa configurazione consente di proteggere i circuiti ExpressRoute da situazioni di emergenza a livello di area. Se la località di peering primaria diventa inattiva, le connessioni possono continuare dall'altra località.

- Il circuito connesso all'ambiente di produzione in genere è quello primario. Il circuito secondario solitamente ha una larghezza di banda inferiore, che può essere incrementata in caso di emergenza.
- Dopo il failover, è possibile stabilire connessioni dal circuito ExpressRoute secondario alla rete virtuale di destinazione. In alternativa, è possibile avere le connessioni già configurate e pronte all'uso in caso di emergenza, per ridurre il tempo di ripristino complessivo.
- Con connessioni simultanee alle reti virtuali di origine e di destinazione, verificare che il routing locale usi il circuito e la connessione secondari solo dopo il failover.
- Dopo il failover, le reti virtuali di origine e destinazione possono ricevere nuovi indirizzi IP oppure mantenere quelli precedenti. In entrambi i casi, è possibile stabilire le connessioni secondarie prima di eseguire il failover.


#### <a name="two-circuits-with-single-peering-location"></a>Due circuiti con una singola località di peering

Questa configurazione garantisce la protezione dagli errori del circuito ExpressRoute primario, ma non se l'unica località di peering ExpressRoute diventa inattiva, compromettendo entrambi i circuiti.

- È possibile disporre di connessioni simultanee dal data center locale alla rete virtuale di origine con il circuito primario e alla rete virtuale di destinazione con il circuito secondario.
- Con connessioni simultanee alle reti virtuali di origine e di destinazione, verificare che il routing locale usi il circuito e la connessione secondari solo dopo il failover.
-   Se invece i circuiti vengono creati nella stessa località di peering, non è possibile collegarli alla stessa rete virtuale.

### <a name="access-with-a-single-circuit"></a>Accesso con un singolo circuito

In questa configurazione è presente un solo circuito ExpressRoute. Anche se il circuito dispone di una connessione ridondante nel caso in cui una connessione diventi inattiva, un circuito con una singola route non fornisce resilienza se l'area di peering diventa inattiva. Si noti che:

- È possibile eseguire la replica delle macchine virtuali di Azure in qualsiasi area di Azure nella [stessa posizione geografica](azure-to-azure-support-matrix.md#region-support). Se l'area di Azure di destinazione non è nella stessa posizione di quella di origine, è necessario abilitare ExpressRoute Premium se si usa un singolo circuito ExpressRoute. Per informazioni, vedere le [località](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e i [prezzi di ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Non è possibile connettere simultaneamente le reti virtuali di origine e di destinazione al circuito se nell'area di destinazione viene usato lo stesso spazio di indirizzi IP. In questo scenario:    
    -  Interrompere la connessione sul lato di origine e quindi stabilire la connessione sul lato di destinazione. Questa modifica della connessione può essere inserita in uno script come parte di un piano di ripristino di Site Recovery. Si noti che:
        - In caso di errore a livello di area, se l'area primaria non è accessibile, l'operazione di disconnessione può avere esito negativo. Ciò potrebbe influire sulla creazione della connessione all'area di destinazione.
        - Se è stata creata la connessione nell'area di destinazione e l'area primaria viene ripristinata in un secondo momento, è possibile che alcuni pacchetti vengano ignorati se due connessioni simultanee provano a connettersi allo stesso spazio di indirizzi.
        - Per evitare il problema, terminare immediatamente la connessione primaria.
        - Dopo il failback delle macchine virtuali nell'area primaria, la connessione primaria può essere nuovamente stabilita dopo avere disconnesso la connessione secondaria.
-   Se vengono usati spazi di indirizzi diversi nella rete virtuale di destinazione, è possibile connettersi contemporaneamente alle reti virtuali di origine e di destinazione dallo stesso circuito ExpressRoute.


## <a name="failover-example"></a>Esempio di failover

In questo esempio viene usata la topologia seguente:

- Due diversi circuiti ExpressRoute in due località di peering differenti.
- Mantenimento degli indirizzi IP privati per le macchine virtuali di Azure dopo il failover.
- L'area di ripristino di destinazione è l'area di Azure Asia sud-orientale.
- Viene stabilita una connessione al circuito ExpressRoute secondario tramite un'appliance perimetrale partner a Singapore.

Per una semplice topologia che usa un singolo circuito ExpressRoute con lo stesso indirizzo IP dopo il failover, [leggere questo articolo](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Procedure di esempio
Per automatizzare il ripristino in questo esempio, ecco come procedere:

1. Seguire i passaggi per configurare la replica.
2. [Eseguire il failover delle macchine virtuali di Azure](azure-to-azure-tutorial-failover-failback.md), con questi passaggi aggiuntivi durante o dopo il failover.

    a. Creare il gateway ExpressRoute di Azure nella rete virtuale dell'hub dell'area di destinazione. Questa operazione è necessaria per connettere la rete virtuale dell'hub di destinazione al circuito ExpressRoute.

    b. Creare la connessione dalla rete virtuale dell'hub di destinazione al circuito ExpressRoute di destinazione.

    c. Configurare i peering delle reti virtuali tra l'hub dell'area di destinazione e le reti virtuali spoke. Le proprietà di peering nell'area di destinazione saranno identiche a quelle dell'area di origine.

    d. Configurare gli UDR nella rete virtuale hub e nelle due reti virtuali spoke.

    - Quando si usano gli stessi indirizzi IP, le proprietà degli UDR lato destinazione sono identiche a quelle dal lato origine.
    - Con indirizzi IP di destinazione diversi, gli UDR devono essere modificati di conseguenza.


La procedura precedente può essere inserita in uno script come parte di un [piano di ripristino](site-recovery-create-recovery-plans.md). A seconda dei requisiti di connettività dell'applicazione e tempo di ripristino, la procedura precedente può anche essere completata prima di avviare il failover.

#### <a name="after-recovery"></a>Dopo il ripristino

Dopo aver ripristinato le macchine virtuali e aver completato la connettività, l'ambiente di ripristino è il seguente.

![Dall'ambiente locale ad Azure con ExpressRoute dopo il failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sull'uso dei [piani di ripristino](site-recovery-create-recovery-plans.md) per automatizzare il failover delle app.
