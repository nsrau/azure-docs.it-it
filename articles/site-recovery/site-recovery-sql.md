---
title: Configurare il ripristino di emergenza per SQL Server con SQL Server e Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come configurare il ripristino di emergenza per SQL Server tramite SQL Server e Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491788"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Configurare il ripristino di emergenza per SQL Server

Questo articolo descrive come proteggere il back-end SQL Server di un'applicazione usando una combinazione di [Azure Site Recovery](site-recovery-overview.md) e tecnologie di continuità aziendale e ripristino di emergenza di SQL Server.

Prima di iniziare, assicurarsi di che aver compreso a funzionalità di ripristino di emergenza SQL Server, tra cui clustering di failover, i gruppi di disponibilità AlwaysOn, mirroring del database, log shipping, replica geografica attiva e gruppi di failover automatico.

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>Consiglio di ripristino di emergenza per l'integrazione di tecnologie BCDR di SQL Server con Site Recovery

Scelta di una tecnologia di BCDR per istanze di SQL Server recovery deve basarsi sulle esigenze RTO e RPO definiti in base la tabella seguente. Una volta effettuata questa scelta, Site Recovery può essere integrato con l'operazione di failover di tale tecnologia per orchestrare il ripristino dell'intera applicazione.

**Tipo di distribuzione** | **Tecnologie BCDR** | **RTO previsto per SQL** | **Previsto valore RPO per SQL** |
--- | --- | --- | ---
SQL Server in VM IaaS di Azure o in locale| **[Always On Availability Group](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | Equivalente per il tempo impiegato per effettuare la replica secondaria come database primario | La replica è asincrona per la replica secondaria, e pertanto una perdita di dati.
SQL Server in VM IaaS di Azure o in locale| **[Failover (FCI AlwaysOn) di clustering](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | Equivalente per il tempo impiegato per eseguire il failover tra i nodi | Usa l'archiviazione condivisa, di conseguenza la stessa visualizzazione dell'istanza di archiviazione è disponibile in caso di failover.
SQL Server in VM IaaS di Azure o in locale| **[Mirroring del database (modalità a prestazioni elevate)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | Equivale al tempo impiegato per l'utilizzo forzato del servizio, che usa il server mirror come server warm standby. | La replica è asincrona. Il database mirror potrebbe rimanere indietro rispetto al database principale. Lo spazio vuoto è tipicamente piccolo, howvever, può diventare significativo se il sistema del server principale o mirror è in un carico eccessivo.<br></br>Il log shipping può essere un'integrazione al mirroring del database e offre un'alternativa favorevole al mirroring del database asincrono
SQL come PaaS in Azure<br></br>(I pool elastici, i server di database SQL) | **Replica geografica attiva** | 30 secondi dopo viene attivato<br></br>Una volta attivato il failover su uno dei database secondari, tutti gli altri database secondari vengono automaticamente collegati al nuovo database primario. | Un valore RPO di 5 secondi<br></br>La replica geografica attiva sfrutta la tecnologia di Always On di SQL Server per replicare in modo asincrono le transazioni sottoposte a commit nel database primario a un database secondario tramite l'isolamento dello snapshot. <br></br>I dati secondari sono sempre garantiti transazioni complete.
SQL come PaaS configurato con replica geografica attiva in Azure<br></br>(SQL istanza gestita di Database, pool elastici, i server di database SQL) | **Gruppi di failover automatico** | Obiettivo del tempo di 1 ora | Un valore RPO di 5 secondi<br></br>I gruppi di failover automatico forniscono la semantica del gruppo sulla replica geografica attiva, ma viene usato lo stesso meccanismo di replica asincrona.
SQL Server in VM IaaS di Azure o in locale| **Replica con Azure Site Recovery** | In genere meno di 15 minuti. [Scopri di più](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) per apprendere il contratto di servizio di RTO fornita da Azure Site Recovery. | 1 ora per coerenza con l'applicazione e 5 minuti per la coerenza di arresto anomalo del sistema. 

> [!NOTE]
> Alcune importanti considerazioni quando si proteggono carichi di lavoro SQL con Azure Site Recovery:
> * Azure Site Recovery è indipendente dall'applicazione e di conseguenza, qualsiasi versione di SQL server in cui viene distribuito in un sistema operativo può essere protetti da Azure Site Recovery. [Altre informazioni](vmware-physical-azure-support-matrix.md#replicated-machines)
> * È possibile scegliere di usare Site Recovery per la distribuzione in Azure, Hyper-V, VMware o fisico dell'infrastruttura. Seguire le [materiale sussidiario](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2) alla fine del documento su come proteggere i Cluster di SQL Server con Azure Site Recovery.
> * Assicurarsi che i dati di frequenza (byte scrittura / sec) osservato nel computer di modifica si trova all'interno [limiti di Site Recovery](vmware-physical-azure-support-matrix.md#churn-limits). Per i computer windows, è possibile visualizzare nella scheda delle prestazioni in Gestione attività. Osservare scrittura velocità per ogni disco.
> * Azure Site Recovery supporta la replica delle istanze del Cluster di Failover su spazi di archiviazione diretta. [Altre informazioni](azure-to-azure-how-to-enable-replication-s2d-vms.md)
 

## <a name="disaster-recovery-of-application"></a>Ripristino di emergenza dell'applicazione

**Azure Site Recovery Orchestra i failover di test e il failover dell'intera applicazione con l'aiuto di piani di ripristino.** 

Esistono alcuni prerequisiti per verificare che il piano di ripristino è completamente personalizzato in base alle proprie esigenze. Qualsiasi distribuzione di SQL Server deve in genere un server Active Directory. Deve anche la connettività per il livello di applicazione.

### <a name="step-1-set-up-active-directory"></a>Passaggio 1: Configurare Active Directory

Per garantire la corretta esecuzione di SQL Server, configurare Active Directory nel sito di ripristino secondario.

* **Organizzazione di piccole dimensioni**: in presenza di un numero ridotto di applicazioni e di un singolo controller di dominio per il sito locale, se si vuole effettuare il failover dell'intero sito è consigliabile usare la replica di Site Recovery per replicare il controller di dominio nel data center secondario o in Azure.
* **Organizzazione di medie o grandi dimensioni**: se si hanno un numero elevato di applicazioni e una foresta Active Directory e si vuole effettuare il failover per applicazione o carico di lavoro, è consigliabile configurare un controller di dominio aggiuntivo nel data center secondario o in Azure. Se si usano gruppi di disponibilità AlwaysOn per il ripristino in un sito remoto, è consigliabile configurare un altro controller di dominio aggiuntivo nel sito secondario o in Azure, da usare per l'istanza di SQL Server ripristinata.

Le istruzioni in questo articolo presuppongono che sia disponibile un controller di dominio nella posizione secondaria. [Altre informazioni](site-recovery-active-directory.md) sulla protezione di Active Directory con Site Recovery.

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>Passaggio 2: Verificare la connettività con altri livelli dell'applicazione e il livello web

Assicurarsi che il livello di database è in esecuzione nell'area di Azure di destinazione, di avere la connettività con l'applicazione e il livello web. Devono effettuare i passaggi necessari per convalidare la connettività con il failover di test in anticipo.

Comprendere come progettare applicazioni per considerazioni relative alla connettività con un paio di esempi:
* [Progettare un'applicazione per il ripristino di emergenza cloud](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Strategie di ripristino di emergenza di pool elastico](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>Passaggio 3: Integrazione con AlwaysOn, replica geografica attiva o i gruppi di failover automatico per il failover dell'applicazione

Le tecnologie BCDR Always On, i gruppi di replica e failover automatico-Geo Capabilities attivi hanno repliche secondarie di SQL server in esecuzione nell'area di Azure di destinazione. Di conseguenza, il primo passaggio per il failover dell'applicazione è rendere questa replica primaria (presupponendo che si dispone già di un controller di dominio secondario). Questo passaggio potrebbe non essere necessario se si sceglie di eseguire un failover automatico. Solo una volta completato il failover del database, è necessario il failover i livelli web o un'applicazione.

> [!NOTE] 
> Se si hanno protetto le macchine SQL con Azure Site Recovery, è sufficiente creare un gruppo di ripristino di tali macchine e aggiungere il failover del piano di ripristino.

[Creare un piano di ripristino](site-recovery-create-recovery-plans.md) con applicazioni e macchine virtuali del livello web. Seguire i passaggi seguenti per aggiungere un failover del livello di database:

1. Importare gli script nell'account di Automazione di Azure. Sono inclusi gli script per il failover del gruppo di disponibilità SQL in una [macchina virtuale di Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) e una [macchina virtuale classica](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Distribuzione in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Aggiungere ASR-SQL-FailoverAG come azione preliminare del primo gruppo del piano di ripristino.

1. Seguire le istruzioni disponibili nello script per creare una variabile di automazione in modo da specificare il nome dei gruppi di disponibilità.

### <a name="step-4-conduct-a-test-failover"></a>Passaggio 4: Eseguire un failover di test

Alcune tecnologie BCDR come SQL Always On in modo nativo non supportano il failover di test. Pertanto, è consigliabile l'approccio seguente **solo quando si integra con queste tecnologie**:

1. Configurare [Backup di Azure](../backup/backup-azure-arm-vms.md) nella macchina virtuale che ospita la replica del gruppo di disponibilità in Azure.

1. Prima di attivare il failover di test del piano di ripristino, ripristinare la macchina virtuale dal backup creato nel passaggio precedente.

    ![Eseguire il ripristino da Backup di Azure](./media/site-recovery-sql/restore-from-backup.png)

1. [Forzare un quorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) nella macchina virtuale ripristinata dal backup.

1. Aggiornare l'indirizzo IP del listener a un indirizzo IP disponibile nella rete di failover di test.

    ![Aggiornare l'indirizzo IP del listener](./media/site-recovery-sql/update-listener-ip.png)

1. Portare online il listener.

    ![Portare online il listener](./media/site-recovery-sql/bring-listener-online.png)

1. Creare un servizio di bilanciamento del carico con un indirizzo IP creato nel pool di indirizzi IP front-end corrispondente al listener di ogni gruppo di disponibilità e con la macchina virtuale SQL aggiunta nel pool back-end.

     ![Creazione del servizio di bilanciamento del carico - Pool di indirizzi front-end](./media/site-recovery-sql/create-load-balancer1.png)

    ![Creazione del servizio di bilanciamento del carico - Pool back-end](./media/site-recovery-sql/create-load-balancer2.png)

1. Aggiungere il failover del livello applicazione, seguito dal livello web in questo piano di ripristino in gruppi di ripristino successive. 
1. Eseguire un failover di test del piano di ripristino per eseguire il test failover end-to-end dell'applicazione.

## <a name="steps-to-do-a-failover"></a>Passaggi per eseguire un failover

Dopo aver aggiunto lo script nel piano di ripristino nel passaggio 3 e convalidato eseguendo un failover di test con un approccio specializzato nel passaggio 4, è possibile eseguire il failover del piano di ripristino creato nel passaggio 3.

Si noti che i passaggi del failover per i livelli web e dell'applicazione devono essere lo stesso in entrambi i failover di test e piani di ripristino di failover.

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>Come proteggere un cluster di SQL Server (standard edition o SQL Server 2008 R2)

Per un cluster che esegue SQL Server Standard Edition o SQL Server 2008 R2, è consigliabile usare la replica di Site Recovery per proteggere SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure ad Azure e da locale ad Azure

Site Recovery non offre guest cluster supporto durante la replica in un'area di Azure. Inoltre, SQL Server non fornisce una soluzione di ripristino di emergenza a costo contenuto per l'edizione Standard. In questo scenario, è consigliabile proteggere il cluster di SQL Server per SQL Server nel percorso primario autonomo e ripristinarlo nel server secondario.

1. Configurare un'istanza di SQL Server autonoma aggiuntiva principale area di Azure o nel sito locale.
1. Configurare l'istanza in modo che funga da mirror per i database da proteggere. Configurare il mirroring in modalità protezione elevata.
1. Configurare Site Recovery nel sito primario ([Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md) oppure [VM VMware/server fisici)](site-recovery-vmware-to-azure-classic.md).
1. Usare la replica di Site Recovery per replicare la nuova istanza di SQL Server di sito secondario. Poiché si tratta di una copia mirror a protezione elevata, tale elemento verrà sincronizzato con il cluster primario, ma verrà replicata tramite replica di Site Recovery.


![Cluster standard](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerazioni sul failback

Per i cluster SQL Server Standard, il failback dopo un failover non pianificato richiede un backup e un ripristino di SQL Server dall'istanza del mirror al cluster originale, con ridefinizione del mirror.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>La modalità SQL ottenere una licenza quando protetti con Azure Site Recovery?
La replica di Azure Site Recovery per SQL Server è compresa nel vantaggio Ripristino di emergenza di Software Assurance, per tutti gli scenari di Azure Site Recovery (ripristino di emergenza da locale ad Azure oppure ripristino di emergenza IaaS di Azure tra diverse aree). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>Azure Site Recovery supporterà la versione SQL?
Azure Site Recovery è indipendente dall'applicazione. Di conseguenza, qualsiasi versione di SQL server in cui viene distribuito in un sistema operativo può essere protetti da Azure Site Recovery. [Altre informazioni](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni](site-recovery-components.md) sull'architettura di Site Recovery.
* Per SQL Server in Azure, altre informazioni sulle [soluzioni a disponibilità elevata](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) per il ripristino nell'area secondaria di Azure.
* Per Database SQL di Azure, altre informazioni sul [continuità](../sql-database/sql-database-business-continuity.md) e [disponibilità elevata](../sql-database/sql-database-high-availability.md) opzioni per il ripristino nell'area secondaria di Azure.
* Per le macchine SQL server in locale, [altre informazioni](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) sulle opzioni di disponibilità elevata per il ripristino in macchine virtuali di Azure.
