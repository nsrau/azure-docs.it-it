---
title: Configurare il ripristino di emergenza per SQL Server con SQL Server e Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come configurare il ripristino di emergenza per SQL Server usando SQL Server e Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 7ee7d6434058da63883f8db0eae6a3f91c778338
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325125"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Configurare il ripristino di emergenza per SQL Server

Questo articolo descrive come proteggere il SQL Server back-end di un'applicazione. A tale scopo, è possibile usare una combinazione di SQL Server tecnologie di continuità aziendale e ripristino di emergenza (BCDR) e [Azure Site Recovery](site-recovery-overview.md).

Prima di iniziare, assicurarsi di comprendere SQL Server funzionalità di ripristino di emergenza. Queste funzionalità includono:

* Clustering di failover
* Gruppi di disponibilità AlwaysOn
* Mirroring del database
* Log shipping
* Replica geografica attiva
* Gruppi di failover automatico

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Combinazione di tecnologie BCDR con Site Recovery

La scelta di una tecnologia BCDR per il ripristino delle istanze di SQL Server deve essere basata sull'obiettivo del tempo di ripristino (RTO) e sulle esigenze dell'obiettivo del punto di ripristino (RPO), come descritto nella tabella seguente. Combinare Site Recovery con l'operazione di failover della tecnologia scelta per orchestrare il ripristino dell'intera applicazione.

Tipo di distribuzione | Tecnologia BCDR | Previsto RTO per SQL Server | Previsto RPO per SQL Server |
--- | --- | --- | ---
SQL Server in una macchina virtuale (VM) di infrastruttura distribuita come servizio (IaaS) di Azure o in locale.| [Gruppo di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | Tempo impiegato per rendere la replica secondaria come primaria. | Poiché la replica nella replica secondaria è asincrona, si verifica una perdita di dati.
SQL Server in una macchina virtuale IaaS di Azure o in locale.| [Clustering di failover (istanza del cluster di failover AlwaysOn)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | Tempo impiegato per eseguire il failover tra i nodi. | Poiché Always On FCI usa lo spazio di archiviazione condiviso, la stessa visualizzazione dell'istanza di archiviazione è disponibile in caso di failover.
SQL Server in una macchina virtuale IaaS di Azure o in locale.| [Mirroring del database (modalità a prestazioni elevate)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | Tempo impiegato per forzare il servizio, che utilizza il server mirror come server warm standby. | La replica è asincrona. È possibile che il database mirror sia in ritardo rispetto al database principale. Il ritardo è in genere di piccole dimensioni. Tuttavia, può diventare grande se il sistema del server principale o del server mirror è sottoposto a un carico elevato.<br/><br/>Il log shipping può essere un supplemento al mirroring del database. Si tratta di un'alternativa favorevole al mirroring asincrono del database.
SQL come piattaforma distribuita come servizio (PaaS) in Azure.<br/><br/>Questo tipo di distribuzione include i pool elastici e i server del database SQL di Azure. | Replica geografica attiva | 30 secondi dopo l'attivazione del failover.<br/><br/>Quando si attiva il failover per uno dei database secondari, tutti gli altri database secondari vengono collegati automaticamente al nuovo database primario. | RPO di cinque secondi.<br/><br/>La replica geografica attiva Usa la tecnologia Always On di SQL Server. Replica in modo asincrono le transazioni di cui è stato eseguito il commit nel database primario in un database secondario tramite l'isolamento dello snapshot.<br/><br/>Per i dati secondari si garantisce che non siano mai presenti transazioni parziali.
SQL come PaaS configurato con la replica geografica attiva in Azure.<br/><br/>Questo tipo di distribuzione include un'istanza gestita di database SQL, pool elastici e server di database SQL. | Gruppi di failover automatico | RTO di un'ora. | RPO di cinque secondi.<br/><br/>I gruppi di failover automatico forniscono la semantica del gruppo sulla replica geografica attiva. Viene tuttavia utilizzato lo stesso meccanismo di replica asincrona.
SQL Server in una macchina virtuale IaaS di Azure o in locale.| Replica con Azure Site Recovery | RTO è in genere inferiore a 15 minuti. Per altre informazioni, leggere il [contratto di RTO fornito da Site Recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Un'ora per la coerenza dell'applicazione e cinque minuti per la coerenza dell'arresto anomalo.

> [!NOTE]
> Di seguito sono riportate alcune considerazioni importanti per la protezione dei carichi di lavoro SQL con Site Recovery:
> * Site Recovery è indipendente dall'applicazione. Site Recovery consentono di proteggere qualsiasi versione di SQL Server distribuita in un sistema operativo supportato. Per altre informazioni, vedere la [matrice di supporto per il ripristino](vmware-physical-azure-support-matrix.md#replicated-machines) di computer replicati.
> * È possibile scegliere di usare Site Recovery per qualsiasi distribuzione in Azure, Hyper-V, VMware o nell'infrastruttura fisica. Per [informazioni su come proteggere un cluster di SQL Server](#how-to-help-protect-a-sql-server-cluster) con Site Recovery, seguire le istruzioni disponibili alla fine di questo articolo.
> * Verificare che la frequenza di modifica dei dati osservata nel computer sia entro [Site Recovery limiti](vmware-physical-azure-support-matrix.md#churn-limits). La frequenza di modifica viene misurata in byte scritti al secondo. Per i computer che eseguono Windows, è possibile visualizzare questa frequenza di modifica selezionando la scheda **prestazioni** in Gestione attività. Osservare la velocità di scrittura per ogni disco.
> * Site Recovery supporta la replica delle istanze del cluster di failover su Spazi di archiviazione diretta. Per ulteriori informazioni, vedere [come abilitare la replica spazi di archiviazione diretta](azure-to-azure-how-to-enable-replication-s2d-vms.md).

## <a name="disaster-recovery-of-an-application"></a>Ripristino di emergenza di un'applicazione

Site Recovery orchestra il failover di test e il failover dell'intera applicazione con l'ausilio dei piani di ripristino.

Ci sono alcuni prerequisiti per garantire che il piano di ripristino sia completamente personalizzato in base alle esigenze. Qualsiasi distribuzione di SQL Server richiede in genere una distribuzione di Active Directory. È necessaria anche la connettività per il livello applicazione.

### <a name="step-1-set-up-active-directory"></a>Passaggio 1: Configurare Active Directory

Configurare Active Directory nel sito di ripristino secondario affinché SQL Server venga eseguito correttamente.

* **Small Enterprise**: Sono presenti un numero ridotto di applicazioni e un singolo controller di dominio per il sito locale. Se si desidera eseguire il failover dell'intero sito, utilizzare Site Recovery replica. Questo servizio replica il controller di dominio nel data center secondario o in Azure.
* **Da medio a grande impresa**: Potrebbe essere necessario configurare controller di dominio aggiuntivi.
  - Se si dispone di un numero elevato di applicazioni, avere una foresta Active Directory e si desidera eseguire il failover in base all'applicazione o al carico di lavoro, configurare un altro controller di dominio nel data center secondario o in Azure.
  -  Se si usa Gruppi di disponibilità Always On per eseguire il ripristino in un sito remoto, configurare un altro controller di dominio nel sito secondario o in Azure. Questo controller di dominio viene utilizzato per l'istanza di SQL Server ripristinata.

Le istruzioni in questo articolo presuppongono che un controller di dominio sia disponibile nella località secondaria. Per ulteriori informazioni, vedere le procedure [che consentono di proteggere Active Directory con Site Recovery](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Passaggio 2: Garantire la connettività con altri livelli

Quando il livello del database è in esecuzione nell'area di Azure di destinazione, assicurarsi di disporre della connettività con i livelli applicazione e Web. Eseguire i passaggi necessari in anticipo per convalidare la connettività con il failover di test.

Per informazioni su come progettare le applicazioni per le considerazioni sulla connettività, vedere gli esempi seguenti:

* [Progettare un'applicazione per il ripristino di emergenza cloud](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Strategie di ripristino di emergenza del pool elastico](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Passaggio 3: Interoperare con Always On, la replica geografica attiva e i gruppi di failover automatico

Le tecnologie BCDR Always On, la replica geografica attiva e i gruppi di failover automatico hanno repliche secondarie di SQL Server in esecuzione nell'area di Azure di destinazione. Il primo passaggio del failover dell'applicazione consiste nel specificare questa replica come primaria. Questo passaggio presuppone che sia già presente un controller di dominio nel database secondario. Il passaggio potrebbe non essere necessario se si sceglie di eseguire un failover automatico. Eseguire il failover dei livelli Web e applicazione solo dopo il completamento del failover del database.

> [!NOTE]
> Se è stato aiutato a proteggere i computer SQL con Site Recovery, è sufficiente creare un gruppo di ripristino di questi computer e aggiungere il failover nel piano di ripristino.

[Creare un piano di ripristino](site-recovery-create-recovery-plans.md) con macchine virtuali di livello applicazione e Web. Nei passaggi seguenti viene illustrato come aggiungere il failover del livello database:

1. Importare gli script per eseguire il failover del gruppo di disponibilità SQL sia in una macchina virtuale [Gestione risorse](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) che in una [macchina virtuale classica](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Importare gli script nell'account di automazione di Azure.

    [![Immagine di un logo "Distribuisci in Azure"](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Aggiungere lo script ASR-SQL-FailoverAG come pre-azione del primo gruppo del piano di ripristino.

1. Seguire le istruzioni disponibili nello script per creare una variabile di automazione. Questa variabile fornisce il nome dei gruppi di disponibilità.

### <a name="step-4-conduct-a-test-failover"></a>Passaggio 4: Eseguire un failover di test

Alcune tecnologie BCDR come SQL Always On non supportano in modo nativo il failover di test. Si consiglia l'approccio seguente *solo quando si utilizzano tali tecnologie*.

1. Configurare [backup di Azure](../backup/backup-azure-arm-vms.md) nella macchina virtuale che ospita la replica del gruppo di disponibilità in Azure.

1. Prima di attivare il failover di test del piano di ripristino, ripristinare la macchina virtuale dal backup effettuato nel passaggio precedente.

    ![Screenshot che mostra la finestra per il ripristino di una configurazione da backup di Azure](./media/site-recovery-sql/restore-from-backup.png)

1. [Forzare un quorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) nella macchina virtuale ripristinata dal backup.

1. Aggiornare l'indirizzo IP del listener in modo che sia un indirizzo disponibile nella rete di failover di test.

    ![Screenshot della finestra delle regole e della finestra di dialogo delle proprietà dell'indirizzo IP](./media/site-recovery-sql/update-listener-ip.png)

1. Portare online il listener.

    ![Screenshot della finestra con etichetta Content_AG che mostra i nomi e gli Stati dei server](./media/site-recovery-sql/bring-listener-online.png)

1. Creare un servizio di bilanciamento del carico. Per ogni listener del gruppo di disponibilità, creare un indirizzo IP dal pool di indirizzi IP front-end. Aggiungere anche la macchina virtuale SQL Server al pool back-end.

     ![Screenshot della finestra intitolata "SQL-AlwaysOn-LB-front-end pool IP](./media/site-recovery-sql/create-load-balancer1.png)

    ![Screenshot della finestra intitolata "SQL-AlwaysOn-LB-back-end pool IP](./media/site-recovery-sql/create-load-balancer2.png)

1. In gruppi di ripristino successivi aggiungere il failover del livello applicazione seguito dal livello Web per questo piano di ripristino.

1. Eseguire un failover di test del piano di ripristino per testare il failover end-to-end dell'applicazione.

## <a name="steps-to-do-a-failover"></a>Passaggi per eseguire un failover

Dopo aver aggiunto lo script nel passaggio 3 e averlo convalidato nel passaggio 4, è possibile eseguire un failover del piano di ripristino creato nel passaggio 3.

I passaggi di failover per i livelli applicazione e Web devono essere gli stessi nei piani di failover di test e di ripristino del failover.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Come proteggere un cluster di SQL Server

Per un cluster che esegue SQL Server Standard Edition o SQL Server 2008 R2, è consigliabile utilizzare Site Recovery replica per proteggere SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Da Azure ad Azure e da sito locale ad Azure

Site Recovery non fornisce il supporto del cluster guest durante la replica in un'area di Azure. SQL Server Standard Edition non fornisce inoltre una soluzione di ripristino di emergenza a basso costo. In questo scenario, è consigliabile proteggere il cluster di SQL Server in un'istanza di SQL Server autonoma nella posizione primaria e ripristinarlo nel database secondario.

1. Configurare un'istanza di SQL Server autonoma aggiuntiva nell'area di Azure primaria o nel sito locale.

1. Configurare l'istanza di in modo che funga da mirror per i database che si desidera proteggere. Configurare il mirroring in modalità a protezione elevata.

1. Configurare Site Recovery nel sito primario per macchine virtuali di [Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md)o [VMware e server fisici](site-recovery-vmware-to-azure-classic.md).

1. Utilizzare Site Recovery replica per replicare la nuova istanza di SQL Server nel sito secondario. Poiché si tratta di una copia mirror a protezione elevata, viene sincronizzata con il cluster primario, ma replicata usando Site Recovery replica.

   ![Immagine di un cluster standard che mostra la relazione e il flusso tra un sito primario, Site Recovery e Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerazioni sul failback

Per i cluster SQL Server Standard, il failback dopo un failover non pianificato richiede una SQL Server backup e ripristino. Questa operazione viene eseguita dall'istanza di mirror al cluster originale con la ridefinizione del mirror.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>In che modo SQL Server ottenere una licenza quando viene usata con Site Recovery?

Site Recovery replica per SQL Server è coperta dal vantaggio del ripristino di emergenza di Software Assurance. Questa copertura si applica a tutti gli scenari di Site Recovery: ripristino di emergenza da sito locale ad Azure e ripristino di emergenza tra aree di Azure IaaS. Per ulteriori informazioni, vedere [Azure Site Recovery prezzi](https://azure.microsoft.com/pricing/details/site-recovery/) .

### <a name="will-site-recovery-support-my-sql-server-version"></a>Site Recovery supporterà la versione SQL Server?

Site Recovery è indipendente dall'applicazione. Site Recovery consentono di proteggere qualsiasi versione di SQL Server distribuita in un sistema operativo supportato. Per ulteriori informazioni, vedere la [matrice di supporto per il ripristino](vmware-physical-azure-support-matrix.md#replicated-machines) di computer replicati.

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sull' [architettura Site Recovery](site-recovery-components.md).
* Per SQL Server in Azure, vedere altre informazioni sulle [soluzioni a disponibilità elevata](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) per il ripristino in un'area di Azure secondaria.
* Per database SQL, vedere altre informazioni sulle opzioni di [continuità aziendale](../sql-database/sql-database-business-continuity.md) e [disponibilità elevata](../sql-database/sql-database-high-availability.md) per il ripristino in un'area di Azure secondaria.
* Per SQL Server computer in locale, vedere altre informazioni sulle opzioni di [disponibilità elevata](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) per il ripristino in macchine virtuali di Azure.
