---
title: Procedure consigliate per Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra le procedure consigliate per la distribuzione di Azure Site Recovery
services: site-recovery
documentationCenter: 
author: rayne-wiselman"
manager: cfreeman
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-support-matrix-to-azure
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a53e2a09fb2aabe96dd5347ea7509815d92bfdb8
ms.lasthandoff: 03/15/2017

---

# <a name="get-ready-to-deploy-azure-site-recovery"></a>Preparare la distribuzione di Azure Site Recovery

Questo articolo illustra come preparare la distribuzione di Azure Site Recovery.

## <a name="protecting-hyper-v-virtual-machines"></a>Protezione di macchine virtuali Hyper-V

Per proteggere le macchine virtuali Hyper-V, sono disponibili due scelte a livello di distribuzione. È possibile replicare le macchine virtuali Hyper-V locali in Azure o in un centro dati secondario. Esistono requisiti diversi per ogni distribuzione.

**Requisito** | **Eseguire la replica in Azure (con VMM)** | **Eseguire la replica di macchine virtuali Hyper-V in Azure (senza VMM)** | **Eseguire la replica di macchine virtuali Hyper-V in un sito secondario (con VMM)** | **Dettagli**
---|---|---|---|---
**VMM** | VMM in esecuzione su System Center 2012 R2. <br/><br/>Almeno un cloud VMM contenente uno o più gruppi host VMM. | ND | Server VMM nei siti primari e secondari in esecuzione almeno in System Center 2012 SP1 con gli aggiornamenti più recenti. <br/><br/> Almeno un cloud in ogni server VMM. I cloud devono avere il profilo di capacità Hyper-V impostato.<br/><br/> Il cloud di origine deve avere almeno un gruppo host VMM. | Facoltativo. Non è necessario distribuire System Center VMM per replicare le macchine virtuali Hyper-V in Azure, ma, se lo si distribuisce, sarà necessario verificare che il server VMM sia configurato correttamente. Verificare anche che sia in esecuzione la versione di VMM corretta e che i cloud siano configurati.
**Hyper-V** | Almeno un server host Hyper-V nel sito locale che esegue Windows Server 2012 R2 o versione successiva. | Almeno un server Hyper-V nei siti di origine e di destinazione che esegue almeno Windows Server 2012 con gli aggiornamenti più recenti installati e connesso a Internet.<br/><br/> I server Hyper-V devono essere in un gruppo host in un cloud VMM. | Almeno un server Hyper-V nei siti di origine e di destinazione che esegue almeno Windows Server 2012 con gli aggiornamenti più recenti installati e connesso a Internet.<br/><br/> I server Hyper-V devono trovarsi in un gruppo host in un cloud VMM. |
**Macchine virtuali** | Almeno una VM nel server host Hyper-V di origine | Almeno una VM nel server host Hyper-V nel cloud VMM di origine | Almeno una VM nel server host Hyper-V nel cloud VMM di origine. |  Le VM che eseguono la replica in Azure devono essere conformi ai prerequisiti delle macchine virtuali di Azure.
**Account di Azure** | È necessario un account Azure e una sottoscrizione per il servizio Site Recovery. | È necessario un account Azure e una sottoscrizione per il servizio Site Recovery. | ND | Nel caso in cui non sia disponibile un account, iniziare con una versione di valutazione gratuita.
**Archiviazione di Azure** | È necessaria una sottoscrizione per un account di archiviazione di Azure con la replica geografica abilitata. | È necessaria una sottoscrizione per un account di archiviazione di Azure con la replica geografica abilitata. | ND | L'account deve trovarsi nella stessa area dell'insieme di credenziali di Azure Site Recovery ed essere associato alla stessa sottoscrizione.
**Rete** | Configurare il mapping di rete per verificare che tutte le macchine virtuali per cui viene eseguito il failover nella stessa rete di Azure possono connettersi le une alle altre, indipendentemente dal piano di ripristino in cui si trovano. Se nella rete di Azure di destinazione è configurato un gateway di rete, le macchine virtuali possono connettersi ad altre macchine virtuali locali. Se non si configura il mapping di rete, solo le macchine di cui si esegue il failover nello stesso piano di ripristino possono connettersi. | ND |  <br/><br/>Configurare il mapping di rete per verificare che le macchine virtuali siano connesse a reti appropriate dopo il failover e che le macchine virtuali di replica siano correttamente posizionate su server host Hyper-V. Se non si configura il mapping di rete, le macchine replicate non verranno connesse ad alcuna rete VM dopo il failover. |  Per configurare il mapping di rete con VMM, sarà necessario verificare che le reti VM e logiche VMM siano configurate correttamente.
**Provider e agenti** | Durante la distribuzione si installerà il provider di Azure Site Recovery nei server VMM. Nei server Hyper-V nei cloud VMM verrà installato l'agente Servizi di ripristino di Azure. | Durante la distribuzione verranno installati sia il provider Azure Site Recovery che l'agente Servizi di ripristino di Azure nel cluster o nel server host Hyper-V.| Durante la distribuzione si installerà il provider di Azure Site Recovery nei server VMM. Nei server Hyper-V nei cloud VMM verrà installato l'agente Servizi di ripristino di Azure. | La connessione dei provider e degli agenti a Site Recovery viene effettuata tramite Internet, mediante una connessione HTTPS crittografata. Non è necessario aggiungere le eccezioni firewall o creare un proxy specifico per la connessione del provider.
**Connettività Internet** | La connessione Internet è necessaria solo per i server VMM. | La connessione Internet è necessaria solo per i server host Hyper-V. | La connessione Internet è necessaria solo per i server VMM. | Non è necessario installare nulla nelle macchine virtuali, che non si connettono direttamente a internet.



## <a name="protect-vmware-virtual-machines-or-physical-servers"></a>Proteggere macchine virtuali VMware o server fisici

Per proteggere le macchine virtuali VMware o i server fisici Windows/Linux, sono disponibili due scelte a livello di distribuzione. È possibile replicarli in Azure o in un data center secondario. Esistono requisiti diversi per ogni distribuzione.

**Requisito** | **Eseguire la replica di VM VMware/server fisici in Azure** | * **Eseguire la replica di VM VMware/server fisici in un sito secondario**  
---|---|---
**Sito primario** | **Server di elaborazione**: server Windows dedicato, fisico o virtuale. | **Server di elaborazione**: server Windows dedicato, fisico o VM VMware.<br/><br/>  
**Sito locale secondario** | ND | **Server di configurazione**: server Windows dedicato, fisico o virtuale. <br/><br/> **Server di destinazione master**: server dedicato, fisico o virtuale. Eseguire la configurazione con Windows per proteggere computer Windows o con Linux per proteggere computer Linux.
**Azzurro** | **Sottoscrizione**: è necessaria una sottoscrizione per il servizio Site Recovery. <br/><br/> **Account di archiviazione**: è necessario un account di archiviazione con la replica geografica abilitata. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Site Recovery ed essere associato alla stessa sottoscrizione. <br/><br/> **Server di configurazione**: è necessario configurare il server di configurazione come una macchina virtuale di Azure. <br/><br/> **Server di destinazione master**: è necessario configurare il server di destinazione master come una macchina virtuale di Azure. <br/><br/> Eseguire la configurazione con Windows per proteggere computer Windows o con Linux per proteggere computer Linux.<br/><br/> **Rete virtuale di Azure**: è necessaria una rete virtuale di Azure in cui distribuire il server di configurazione e il server di destinazione master. La rete deve trovarsi nella stessa sottoscrizione e nella stessa area dell'insieme di credenziali di Azure Site Recovery. | ND  
**Macchine virtuali/server fisici** | Almeno una macchina virtuale VMware o un server fisico Windows/Linux.<br/><br/>Durante la distribuzione, in ogni computer viene installato il servizio Mobility.| Almeno una VM VMware o un server fisico Windows/Linux.<br/><br/> Durante la distribuzione in ogni computer viene installato Unified Agent.




## <a name="azure-virtual-machine-requirements"></a>Requisiti delle macchine virtuali di Azure

È possibile distribuire Site Recovery per replicare macchine virtuali e server fisici in esecuzione su qualsiasi sistema operativo supportato da Azure, vale a dire la maggior parte delle versioni di Windows e Linux. Sarà necessario verificare che le macchine virtuali locali da proteggere siano conformi ai requisiti di Azure.


## <a name="optimizing-your-deployment"></a>Ottimizzazione della distribuzione

Usare i suggerimenti seguenti per ottimizzare e ridimensionare la distribuzione.

- **Dimensioni del volume del sistema operativo**: quando si replica una macchina virtuale in Azure, il volume del sistema operativo deve essere inferiore a 1 TB. Se si dispone di volumi superiori, è possibile spostarli manualmente in un altro disco prima di iniziare la distribuzione.
- **Dimensioni del disco dati**: se si esegue la replica ad Azure è possibile avere fino a 32 dischi dati in una macchina virtuale, ciascuno con un massimo di 1 TB. È possibile replicare in modo efficiente una macchina virtuale di ~32 TB ed eseguirne il failover.
- **Limiti del piano di ripristino**: Site Recovery è in grado di supportare migliaia di macchine virtuali. I piani di ripristino sono progettati come modello per le applicazioni devono eseguire il failover insieme. Pertanto, il numero di macchine in un piano di ripristino viene limitato a 50.
- **Limiti dei servizi di Azure**: ogni sottoscrizione di Azure include un set di limiti predefiniti su core, servizi cloud e così via. È consigliabile eseguire un failover di test per convalidare la disponibilità di risorse nella sottoscrizione. È possibile modificare questi limiti tramite il supporto di Azure.
- **Pianificazione della capacità**: pianificare la scalabilità e le prestazioni.
- **Larghezza di banda della replica**: in caso di larghezza di banda insufficiente, è opportuno notare quanto indicato di seguito.
    - **ExpressRoute**: Site Recovery funziona con Azure ExpressRoute e con gli ottimizzatori WAN, ad esempio Riverbed.
    - **Traffico di replica**: Site Recovery esegue una replica iniziale intelligente utilizzando solo i blocchi di dati, non l'intero VHD. Solo le modifiche vengono replicate durante la replica in corso.
    - **Traffico di rete**: è possibile controllare il traffico di rete usato per la replica configurando QoS di Windows con criteri basati sull'indirizzo IP di destinazione e la porta.  Inoltre, se si esegue la replica a Site Recovery di Azure utilizzando l'agente do backup di Azure, è possibile configurare la limitazione per tale agente.
- **RTO**: per misurare l'obiettivo del tempo di ripristino (RTO) che è possibile prevedere con Site Recovery, è consigliabile eseguire un failover di test e visualizzare i processi di Site Recovery per analizzare il tempo necessario per completare le operazioni. Se si sta eseguendo il failover ad Azure, per l'obiettivo RTO ottimale è consigliabile automatizzare tutte le azioni manuali mediante l'integrazione con i piani di automazione e di ripristino di Azure.
- **RPO**: Site Recovery supporta un obiettivo del punto di ripristino (RPO) quasi sincrono quando si esegue la replica ad Azure. Ciò presuppone sufficiente larghezza di banda tra il datacenter e Azure.

