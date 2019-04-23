---
title: Mantenere gli indirizzi IP durante il failover delle macchine virtuali di Azure con Azure Site Recovery | Microsoft Docs
description: Viene descritto come mantenere gli indirizzi IP quando si effettua il failover di macchine virtuali di Azure per il ripristino di emergenza in un'area secondaria con Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 618d60417aa6b582eaef94bf75dcf16c74750f83
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788872"
---
# <a name="retain-ip-addresses-during-failover"></a>Mantenere gli indirizzi IP durante il failover

[Azure Site Recovery](site-recovery-overview.md) abilita il ripristino di emergenza per macchine virtuali di Azure tramite la replica delle macchine virtuali in un'altra area di Azure ed eseguendo il failover se si verifica un'interruzione del servizio ed eseguendo il failback nell'area primaria quando la situazione è tornata normale.

Durante il failover, può essere opportuno mantenere gli indirizzi IP nell'area di destinazione identica all'area di origine:

- Per impostazione predefinita, quando si abilita il ripristino di emergenza per macchine virtuali di Azure, Site Recovery crea le risorse di destinazione in base alle impostazioni di quelle di origine. Per le macchine virtuali di Azure con indirizzi IP statici, Site Recovery tenta di effettuare il provisioning dello stesso indirizzo IP per le macchine virtuali di destinazione, se non è in uso. Per una spiegazione completa su come Site Recovery gestisce l'indirizzamento [leggere questo articolo](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Per applicazioni semplici, la configurazione predefinita è sufficiente. Per le app più complessa, potrebbe essere necessario effettuare il provisioning di risorse aggiuntive per verificare che la connettività funzioni come previsto dopo il failover.


Questo articolo descrive come mantenere gli indirizzi IP negli scenari di esempio più complessi. Tra gli esempi sono inclusi:

- Failover per una società con tutte le risorse in esecuzione in Azure
- Failover per una società con una distribuzione ibrida e le risorse in esecuzione in locale e in Azure

## <a name="resources-in-azure-full-failover"></a>Risorse in Azure: failover completo

La società A ha tutte le proprie app in esecuzione in Azure.

### <a name="before-failover"></a>Prima del failover

Di seguito viene indicata l'architettura prima del failover.

- La società ha identiche reti e subnet in aree di origine e di destinazione di Azure.
- Per ridurre l'obiettivo del tempo di ripristino (RTO), l'azienda usa nodi di replica per Gruppi di disponibilità AlwaysOn di SQL Server, controller di dominio e così via. Questi nodi di replica si trovano in una rete virtuale diversa nell'area di destinazione, in modo che possano stabilire connettività una rete VPN da sito a sito tra le aree di origine e di destinazione. Ciò non è possibile se lo stesso spazio di indirizzi IP viene usato nell'origine e nella destinazione.  
- Prima del failover, l'architettura di rete è la seguente:
    - L'area primaria è Asia orientale di Azure
        - Asia orientale dispone di una rete virtuale (**Rete virtuale di origine**) con spazio degli indirizzi 10.1.0.0/16.
        - Nell'area Asia orientale i carichi di lavoro sono suddivisi tra tre subnet nella rete virtuale:
            - **Subnet 1**: 10.1.1.0/24
            - **Subnet 2**: 10.1.2.0/24,
            - **Subnet 3**: 10.1.3.0/24
    - L'area secondaria (destinazione) è Asia sud-orientale di Azure
        - Asia sud-orientale dispone di una rete virtuale di ripristino (**Rete virtuale di ripristino**) identica alla **Rete virtuale di origine**.
        - Asia sud-orientale dispone di una rete virtuale aggiuntiva (**Rete virtuale di Azure**) con spazio degli indirizzi 10.2.0.0/16.
        - La **rete virtuale di Azure** contiene una subnet (**Subnet 4**) con spazio degli indirizzi 10.2.4.0/24.
        - Nodi di replica per SQL Server Always On, a cui si trovano nel controller di dominio e così via **Subnet 4**.
    - La **rete virtuale di origine** e la **rete virtuale di Azure** sono connesse con una connessione VPN da sito a sito.
    - La **Rete virtuale di ripristino** non è connessa ad alcun'altra rete virtuale.
    - La **società A** assegna/verifica gli indirizzi IP di destinazione per gli elementi replicati. L'indirizzo IP di destinazione è lo stesso indirizzo IP di origine per ogni macchina virtuale.

![Risorse in Azure prima del failover completo](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Dopo il failover

Se si verifica un'interruzione nell'area di origine, la società A può effettuare il failover di tutte le relative risorse all'area di destinazione.

- Con gli indirizzi IP di destinazione già in uso prima del failover, la società A può orchestrare il failover e stabilire automaticamente le connessioni dopo il failover tra la **rete virtuale di ripristino** e la **rete virtuale di Azure**. Il ciclo è illustrato nel diagramma seguente:
- A seconda dei requisiti delle app, le connessioni tra le due reti virtuali (**rete virtuale di ripristino** e **rete virtuale di Azure**) nell'area di destinazione possono essere stabilite prima, durante (come passaggio intermedio) o dopo il failover.
  - La società può usare [piani di ripristino](site-recovery-create-recovery-plans.md) per specificare quando verranno stabilite connessioni.
  - Possono connettere le reti virtuali tramite peering di reti virtuali o VPN da sito a sito.
      - Il peering di reti virtuali non usa alcun gateway VPN e presenta vincoli diversi.
      - I [prezzi](https://azure.microsoft.com/pricing/details/virtual-network) per il peering di reti virtuali viene calcolato in modo diverso rispetto ai [prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway) per il gateway VPN tra reti virtuali. Per i failover, è in genere consigliabile usare lo stesso metodo di connettività delle reti di origine, incluso il tipo di connessione, per ridurre al minimo gli eventi di rete imprevisti.

    ![Risorse in Azure: failover completo](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Risorse in Azure: failover di applicazioni isolate

Potrebbe essere necessario effettuare il failover a livello di app, ad esempio di un'app specifica o di un livello di app che si trova in una subnet dedicata.

- In questo scenario è possibile mantenere gli indirizzi IP, ma non è in genere consigliabile poiché si aumenta il rischio di incoerenze nella connettività. Andrà inoltre persa la connettività della subnet con altre subnet nella stessa rete virtuale di Azure.
- Un modo migliore per effettuare il failover dell'applicazione a livello di subnet consiste nell'usare indirizzi IP di destinazione diversi per il failover (se la connettività è necessaria per altre subnet nella rete virtuale di origine) o isolare ogni applicazione nella propria rete virtuale dedicata nell'origine. Con il secondo approccio, è possibile stabilire la connettività tra reti nell'origine ed emularla durante il failover all'area di destinazione.  

In questo esempio la società A posiziona le app nell'area di origine in reti virtuali dedicate e stabilisce una connettività tra tali reti. Con questa struttura, la società può effettuare il failover di app isolate e mantenere gli indirizzi origine IP privati nella rete di destinazione.

### <a name="before-failover"></a>Prima del failover

Prima del failover, l'architettura è la seguente:

- Le macchine virtuali dell'applicazione sono ospitate nell'area Asia orientale di Azure primaria:
    - Le macchine virtuali di **App1** si trovano nella **rete virtuale di origine 1**: 10.1.0.0/16.
    - Le macchine virtuali di **App2** si trovano nella **rete virtuale di origine 2**: 10.2.0.0/16.
    - Nella **rete virtuale di origine 1** sono presenti due subnet.
    - Nella **rete virtuale di origine 2** sono presenti due subnet.
- L'area secondaria (destinazione) è Asia sud-orientale di Azure - Asia sud-orientale di Azure dispone di reti virtuali di ripristino (**Rete virtuale di ripristino 1** e **Rete virtuale di Ripristino 2**) identiche a **Rete virtuale di origine 1** e **Rete virtuale di origine 2**.
        - La **rete virtuale di ripristino 1** e la **rete virtuale di ripristino 2** dispongono ognuna di due subnet che corrispondono alle subnet in **rete virtuale di origine 1** e **rete virtuale di origine 2**. Nell'area Asia sud-orientale è presente una rete orientale aggiuntiva (**Rete virtuale di Azure**) con spazio degli indirizzi 10.3.0.0/16.
        - La **rete virtuale di Azure** contiene una subnet (**Subnet 4**) con spazio degli indirizzi 10.3.4.0/24.
        -Nodi di replica per SQL Server Always On, a cui si trovano nel controller di dominio e così via **Subnet 4**.
- Sono presenti alcune connessioni VPN da sito a sito: 
    - **Rete virtuale di origine 1** e **rete virtuale di Azure**
    - **Rete virtuale di origine 2** e **rete virtuale di Azure**
    - La **Rete virtuale di origine 1** e la **Rete virtuale di origine 2** sono connesse tramite VPN da sito a sito
- La **Rete virtuale di ripristino 1** e la **Rete virtuale di ripristino 2** non sono connesse ad alcuna rete virtuale.
- La **società A** configura gateway VPN sulla **rete virtuale di ripristino 1** e sulla **rete virtuale di ripristino 2** per ridurre l'obiettivo del tempo di ripristino (RTO).  
- La **Rete virtuale 1 di ripristino** e la **Rete virtuale 2 di ripristino** non sono connesse ad alcun'altra rete virtuale.
- Per ridurre l'obiettivo del tempo di ripristino (RTO), i gateway VPN sono configurati sulla **Rete virtuale 1 di ripristino** e sulla **Rete virtuale 2 di ripristino** prima del failover.

    ![Risorse in Azure prima del failover di app](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Dopo il failover

In caso di interruzione o di un problema che interessa una singola app (in **Rete virtuale di origine 2 nel nostro esempio), la società A può ripristinare l'app interessata come indicato di seguito:


- Le connessioni VPN tra la **Rete virtuale di origine 1** e la **Rete virtuale di origine 2** e quelle tra la **Rete virtuale di origine 2** e la **Rete virtuale di Azure** vengono disconnesse.
- Vengono stabilite le connessioni VPN tra la **Rete virtuale di origine 1** e la **Rete virtuale di ripristino 2**, così come tra la **Rete virtuale di ripristino 2** e la **Rete virtuale di Azure**.
- Viene effettuato il failover delle macchine virtuali della **Rete virtuale di origine 2** nella **Rete virtuale di ripristino 2**.

![Risorse nel failover di app di Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Questo esempio può essere espanso per includere altre applicazioni e connessioni di rete. Si consiglia, per quanto possibile, di seguire un modello di connessione analogo per il failover dall'origine alla destinazione.
- I gateway VPN usano indirizzi IP pubblici e hop di gateway per stabilire le connessioni. Se non si vuole usare indirizzi IP pubblici o si vogliono evitare hop aggiuntivi, è possibile usare il [peering di rete virtuale di Azure](../virtual-network/virtual-network-peering-overview.md) per eseguire il peering di reti virtuali tra [aree di Azure supportate](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Risorse ibride: failover completo

In questo scenario la **società B** adotta un approccio ibrido, con parte dell'infrastruttura dell'applicazione in esecuzione in Azure e il resto in locale. 

### <a name="before-failover"></a>Prima del failover

Di seguito viene indicato l'aspetto dell'architettura di rete prima del failover.

- Le macchine virtuali dell'applicazione sono ospitate nell'area Asia orientale di Azure.
- Asia orientale dispone di una rete virtuale (**Rete virtuale di origine**) con spazio degli indirizzi 10.1.0.0/16.
  - Nell'area Asia orientale i carichi di lavoro sono suddivisi tra tre subnet nella **rete virtuale di origine**:
    - **Subnet 1**: 10.1.1.0/24
    - **Subnet 2**: 10.1.2.0/24,
    - **Subnet 3**: 10.1.3.0/24 con uso di una rete virtuale di Azure con spazio degli indirizzi 10.1.0.0/16. Questa rete virtuale è denominata **Rete virtuale di origine**
      - L'area secondaria (destinazione) è Asia sud-orientale di Azure:
  - Asia sud-orientale dispone di una rete virtuale di ripristino (**Rete virtuale di ripristino**) identica alla **Rete virtuale di origine**.
- Le macchine virtuali nell'area Asia orientale vengono connesse a un data center locali con Azure ExpressRoute o VPN da sito-a-sito.
- Per ridurre l'obiettivo del tempo di ripristino (RTO), la società B effettua il provisioning del gateway nella rete virtuale di ripristino nell'area Asia sud-orientale di Azure prima del failover.
- La società B assegna/verifica gli indirizzi IP di destinazione per le macchine virtuali replicate. L'indirizzo IP di destinazione è lo stesso indirizzo IP di origine per ogni macchina virtuale.


![Connettività da locale ad Azure prima del failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Dopo il failover


Se si verifica un'interruzione nell'area di origine, la società B può effettuare il failover di tutte le relative risorse all'area di destinazione.

- Con gli indirizzi IP di destinazione già in uso prima del failover, la società B può orchestrare il failover e stabilire automaticamente le connessioni dopo il failover tra la **rete virtuale di ripristino** e la **rete virtuale di Azure**.
- A seconda dei requisiti delle app, le connessioni tra le due reti virtuali (**rete virtuale di ripristino** e **rete virtuale di Azure**) nell'area di destinazione possono essere stabilite prima, durante (come passaggio intermedio) o dopo il failover. La società può usare [piani di ripristino](site-recovery-create-recovery-plans.md) per specificare quando verranno stabilite connessioni.
- La connessione originale tra l'area Asia orientale di Azure e il data center locale dovrebbe essere disconnessa prima di stabilire la connessione tra l'area Asia sud-orientale di Azure e il data center locale.
- Il routing locale viene riconfigurato in modo da puntare ai gateway e all'area di destinazione dopo il failover.

![Connettività da locale ad Azure dopo il failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Risorse ibride: failover di applicazioni isolate

La società B non può effettuare il failover di app isolate a livello di subnet. Ciò è dovuto al fatto che lo spazio indirizzi nelle reti virtuali di origine e di ripristino è lo stesso e che la connessione originale da origine a locale è attiva.

 - Per garantire la resilienza delle app, la società B deve inserire ogni app nella propria rete virtuale di Azure dedicata.
 - Con ogni app in una rete virtuale separata, la società B può effettuare il failover delle app isolate e indirizzare le connessioni di origine all'area di destinazione.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [piani di ripristino](site-recovery-create-recovery-plans.md).
