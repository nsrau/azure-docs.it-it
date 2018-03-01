---
title: Mantenere gli indirizzi IP per le macchine virtuali di Azure dopo il failover in un'altra area di Azure | Microsoft Docs
description: Descrive come mantenere gli indirizzi IP per gli scenari di failover da Azure ad Azure con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 28d772df384e620c7e82812adfa2bfa148401132
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Mantenimento degli indirizzi IP per il failover delle macchine virtuali di Azure

Azure Site Recovery consente il ripristino di emergenza per le macchine virtuali di Azure. Durante il failover da un'area di Azure a un'altra, spesso i clienti richiedono di mantenere le loro configurazioni IP. Per impostazione predefinita, Site Recovery simula la struttura della subnet e della rete virtuale di origine durante la creazione di queste risorse nell'area di destinazione. Per le macchine virtuali di Azure configurate con indirizzi IP privati statici, Site Recovery tenta inoltre di eseguire il provisioning dello stesso IP privato nella macchina virtuale di destinazione, se tale IP non è già bloccato da una risorsa di Azure o da una VM replicata.

Per applicazioni semplici, la configurazione predefinita precedente è tutto ciò che serve. Per le applicazioni aziendali più complesse, i clienti potrebbero richiedere il provisioning di ulteriori risorse di rete per assicurare la connettività post-failover con altri componenti dell'infrastruttura. Questo articolo illustra i requisiti di rete per effettuare il failover delle macchine virtuali di Azure da un'area all'altra, mantenendo gli indirizzi IP delle VM.

## <a name="azure-to-azure-connectivity"></a>Connettività da Azure ad Azure

Per il primo scenario viene presa in considerazione l'**Azienda A** che ha l'intera infrastruttura dell'applicazione in esecuzione in Azure. Per ragioni di conformità e di continuità aziendale, l'**Azienda A** decide di usare Azure Site Recovery per proteggere le applicazioni.

Considerato il requisito del mantenimento degli indirizzi IP (ad esempio per i binding dell'applicazione), l'Azienda A ha la stessa struttura della subnet e della rete virtuale nell'area di destinazione. Per ridurre ulteriormente l'obiettivo del tempo di ripristino (RTO), l'**Azienda A** utilizza i nodi di replica per SQL Always ON, controller di dominio e così via e questi nodi di replica vengono inseriti in una rete virtuale diversa nell'area di destinazione. L'uso di uno spazio indirizzi diverso per i nodi di replica consente all'**Azienda A** di stabilire la connettività VPN da sito a sito tra le aree di origine e di destinazione, cosa che non sarebbe possibile se lo stesso spazio indirizzi fosse usato a entrambe le estremità.

Di seguito è riportato l'aspetto dell'architettura di rete prima del failover:
- Le macchine virtuali dell'applicazione sono ospitate nell'area Asia orientale di Azure, utilizzando una rete virtuale di Azure con spazio indirizzi 10.1.0.0/16. Questa rete virtuale è denominata **Rete virtuale di origine**.
- I carichi di lavoro dell'applicazione sono suddivisi tra tre subnet (10.1.0.0/24, 10.1.1.0/24, 10.1.2.0/24), denominate rispettivamente **Subnet 1**, **Subnet 2** e **3 Subnet**.
- L'area Asia sud-orientale di Azure è l'area di destinazione e dispone di una rete virtuale di ripristino che simula la configurazione della subnet e lo spazio indirizzi nell'origine. Questa rete virtuale è denominata **Rete virtuale di ripristino**.
- I nodi di replica, come quelli necessari per Always On, controller di dominio e così via, sono inseriti in una rete virtuale con spazio indirizzi 20.1.0.0/16 all'interno della Subnet 4 con indirizzo 20.1.0.0/24. La rete virtuale è denominata **Rete virtuale di Azure** e si trova nell'area Asia sud-orientale di Azure.
- La **Rete virtuale di origine** e la **Rete virtuale di Azure** sono connesse tramite connettività VPN da sito a sito.
- La **Rete virtuale di ripristino** non è connessa ad alcun'altra rete virtuale.
- L'**Azienda A** assegna/verifica l'indirizzo IP di destinazione per gli elementi replicati. In questo esempio l'indirizzo IP di destinazione è lo stesso dell'indirizzo IP di origine per ogni macchina virtuale.

![Connettività da Azure ad Azure prima del failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Failover completo dell'area

In caso di interruzione a livello di area, l'**Azienda A** è in grado di ripristinare l'intera distribuzione rapidamente e facilmente usando i potenti [piani di ripristino](site-recovery-create-recovery-plans.md) di Azure Site Recovery. Avendo già impostato l'indirizzo IP di destinazione per ogni macchina virtuale prima del failover, l'**Azienda A** può orchestrare il failover e automatizzare l'attivazione della connessione tra la Rete virtuale di ripristino e la Rete virtuale di Azure, come illustrato nel diagramma seguente.

![Failover completo dell'area per la connettività da Azure ad Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover.png)

A seconda dei requisiti dell'applicazione, le connessioni tra le due reti virtuali nell'area di destinazione possono essere stabilite prima, durante (come passaggio intermedio) o dopo il failover. Usare i [piani di ripristino](site-recovery-create-recovery-plans.md) per aggiungere script e definire l'ordine di failover.

L'Azienda A può inoltre scegliere di usare Peering reti virtuali o la VPN da sito a sito per stabilire la connettività tra la Rete virtuale di ripristino e la Rete virtuale di Azure. Il peering reti virtuali non usa un gateway VPN e presenta vincoli diversi. I [prezzi per il peering reti virtuali](https://azure.microsoft.com/pricing/details/virtual-network) vengono inoltre calcolati in modo diverso rispetto ai [prezzi per il gateway VPN da rete virtuale a rete virtuale](https://azure.microsoft.com/pricing/details/vpn-gateway). Per i failover, è in genere consigliabile simulare la connettività di origine, incluso il tipo di connessione, per ridurre al minimo gli eventi imprevisti derivanti da modifiche alla rete.

### <a name="isolated-application-failover"></a>Failover dell'applicazione isolato

In determinate condizioni gli utenti potrebbero dover effettuare il failover di parti dell'infrastruttura dell'applicazione. Un possibile esempio è il failover di una specifica applicazione o livello che si trova all'interno di una subnet dedicata. Se da un lato il failover sulla subnet con il mantenimento degli indirizzi IP è possibile, non è consigliabile per la maggior parte dei casi, dal momento che incrementa notevolmente le incoerenze della connettività. Andrà inoltre persa la connettività della subnet su altre subnet nella stessa rete virtuale di Azure.

Un modo migliore per soddisfare i requisiti di failover dell'applicazione a livello di subnet consiste nell'usare indirizzi IP di destinazione diversi per il failover (se la connettività è necessaria per altre subnet nella rete virtuale di origine) o isolare ogni applicazione nella propria rete virtuale dedicata nell'origine. Con il secondo approccio, è possibile stabilire la connettività tra reti nell'origine ed emularla durante il failover all'area di destinazione.

Per progettare le singole applicazioni per garantire la resilienza, è consigliabile ospitare un'applicazione nella propria rete virtuale dedicata e stabilire la connettività tra queste reti virtuali in base alle esigenze. In questo modo si garantisce il failover dell'applicazione isolato mantenendo gli indirizzi IP privati originali.

La configurazione pre-failover appare quindi come segue:
- Le macchine virtuali dell'applicazione sono ospitate nell'area Asia orientale di Azure, utilizzando una rete virtuale di Azure con spazio indirizzi 10.1.0.0/16 per la prima applicazione e 15.1.0.0/16 per la seconda applicazione. Le reti virtuali sono denominate **Rete virtuale 1 di origine** e **Rete virtuale 2 di origine**, rispettivamente per la prima e la seconda applicazione.
- Ogni rete virtuale viene ulteriormente suddivisa in due reti virtuali.
- L'area Asia sud-orientale di Azure è l'area di destinazione e dispone delle reti virtuali di ripristino Rete virtuale 1 di ripristino e Rete virtuale 2 di ripristino.
- I nodi di replica, come quelli necessari per Always On, controller di dominio e così via, sono inseriti in una rete virtuale con spazio indirizzi 20.1.0.0/16 all'interno della **Subnet 4** con indirizzo 20.1.0.0/24. La rete virtuale è denominata rete virtuale di Azure e si trova nell'area Asia sud-orientale di Azure.
- La **Rete virtuale 1 di origine** e la **Rete virtuale di Azure** sono connesse tramite connettività VPN da sito a sito. Analogamente, la **Rete virtuale 2 di origine** e la **Rete virtuale di Azure** sono connesse tramite connettività VPN da sito a sito.
- Anche la **Rete virtuale 1 di origine** e la **Rete virtuale 2 di origine** sono connesse tramite VPN S2S in questo esempio. Poiché le due reti virtuali si trovano nella stessa area, può essere usato anche Peering reti virtuali al posto della connettività VPN S2S.
- La **Rete virtuale 1 di ripristino** e la **Rete virtuale 2 di ripristino** non sono connesse ad alcun'altra rete virtuale.
- Per ridurre l'obiettivo del tempo di ripristino (RTO), i gateway VPN sono configurati sulla **Rete virtuale 1 di ripristino** e sulla **Rete virtuale 2 di ripristino** prima del failover.

![Applicazione isolata prima del failover con connettività da Azure ad Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover.png)

In caso di una situazione di emergenza che interessa solo un'applicazione (in questo esempio ospitata nella Rete virtuale 2 di origine), l'Azienda A può ripristinare l'applicazione interessata come illustrato di seguito:
- Le connessioni VPN tra la **Rete virtuale 1 di origine** e la **Rete virtuale 2 di origine**, così come tra la **Rete virtuale 2 di origine** e la **Rete virtuale di Azure** vengono disconnesse.
- Vengono stabilite le connessioni VPN tra la **Rete virtuale 1 di origine** e la **Rete virtuale 2 di ripristino**, così come tra la **Rete virtuale 2 di ripristino** e la **Rete virtuale di Azure**.
- Viene effettuato il failover delle macchine virtuali dalla **Rete virtuale 2 di origine** alla **Rete virtuale 2 di ripristino**.

![Applicazione isolata dopo il failover con connettività da Azure ad Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover.png)

L'esempio precedente di failover isolato può essere esteso in modo da includere altre applicazioni e connessioni di rete. Si consiglia, per quanto possibile, di seguire un modello di connessione analogo per il failover dall'origine alla destinazione.

### <a name="further-considerations"></a>Altre considerazioni

I gateway VPN utilizzano indirizzi IP pubblici e hop di gateway per stabilire le connessioni. Se non si vuole usare l'indirizzo IP pubblico e/o si vogliono evitare hop aggiuntivi, è ora possibile usare Peering reti virtuali globale per il peering delle reti virtuali tra le aree di Azure.

Questa funzionalità è attualmente in anteprima pubblica e verrà estesa per il supporto di più aree, abilitando la connettività diretta tra macchine virtuali senza coinvolgere la rete Internet pubblica e senza eventuali hop aggiuntivi.

Per altre informazioni, fare riferimento alla [documentazione relativa al peering](../virtual-network/virtual-network-create-peering.md#register) e ai [prezzi](https://azure.microsoft.com/en-us/pricing/details/virtual-network/).

## <a name="on-premises-to-azure-connectivity"></a>Connettività da locale ad Azure

Per il secondo scenario, viene presa in considerazione l'**Azienda B** che ha una parte dell'infrastruttura dell'applicazione in esecuzione in Azure e il resto in esecuzione in locale. Per ragioni di conformità e di continuità aziendale, l'**Azienda B** decide di usare Azure Site Recovery per proteggere le applicazioni in esecuzione in Azure.

Di seguito è riportato l'aspetto dell'architettura di rete prima del failover:
- Le macchine virtuali dell'applicazione sono ospitate nell'area Asia orientale di Azure, utilizzando una rete virtuale di Azure con spazio indirizzi 10.1.0.0/16. Questa rete virtuale è denominata **Rete virtuale di origine**.
- I carichi di lavoro dell'applicazione sono suddivisi tra tre subnet (10.1.0.0/24, 10.1.1.0/24, 10.1.2.0/24), denominate rispettivamente **Subnet 1**, **Subnet 2** e **3 Subnet**.
- L'area Asia sud-orientale di Azure è l'area di destinazione e dispone di una rete virtuale di ripristino che simula la configurazione della subnet e lo spazio indirizzi nell'origine. Questa rete virtuale è denominata **Rete virtuale di ripristino**.
- Le macchine virtuali nell'area Asia orientale di Azure sono connesse al data center locale tramite ExpressRoute o una connessione VPN da sito a sito.
- Per ridurre l'obiettivo del tempo di ripristino (RTO), l'Azienda B effettua il provisioning dei gateway nella Rete virtuale di ripristino nell'area Asia sud-orientale di Azure prima del failover.
- L'**Azienda B** assegna/verifica l'indirizzo IP di destinazione per gli elementi replicati. Per questo esempio, l'indirizzo IP di destinazione è lo stesso dell'indirizzo IP di origine per ogni macchina virtuale.

![Connettività da locale ad Azure prima del failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Failover completo dell'area

In caso di interruzione a livello di area, l'**Azienda B** è in grado di ripristinare l'intera distribuzione rapidamente e facilmente usando i potenti [piani di ripristino](site-recovery-create-recovery-plans.md) di Azure Site Recovery. Avendo già impostato l'indirizzo IP di destinazione per ogni macchina virtuale prima del failover, l'**Azienda B** può orchestrare il failover e automatizzare l'attivazione della connessione tra la Rete virtuale di ripristino e il data center locale, come illustrato nel diagramma seguente.

La connessione originale tra l'area Asia orientale di Azure e il data center locale dovrebbe essere disconnessa prima di stabilire la connessione tra l'area Asia sud-orientale di Azure e il data center locale. Anche il routing locale viene riconfigurato in modo da puntare ai gateway e all'area di destinazione dopo il failover.

![Connettività da locale ad Azure dopo il failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover.png)

### <a name="subnet-failover"></a>Failover sulla subnet

A differenza dello scenario da Azure ad Azure descritto per l'**Azienda A**, un failover a livello di subnet non è possibile in questo caso per l'**Azienda B**. Ciò è dovuto al fatto che lo spazio indirizzi nelle reti virtuali di origine e di ripristino è lo stesso e la connessione originale da origine a locale è attiva.

Per garantire la resilienza dell'applicazione, è consigliabile che ogni applicazione si trovi nella propria rete virtuale di Azure dedicata. È quindi possibile effettuare il failover delle applicazioni in isolamento e le connessioni richieste da locale a origine possono essere indirizzate all'area di destinazione come descritto in precedenza.

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.
