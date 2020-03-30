---
title: Configurare il ripristino di emergenza per SQL Server con Azure Site RecoverySet up disaster recovery for SQL Server with Azure Site Recovery
description: In questo articolo viene descritto come configurare il ripristino di emergenza per SQL Server utilizzando SQL Server e Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084746"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Configurare il ripristino di emergenza per SQL Server

In questo articolo viene descritto come proteggere il back-end di SQL Server di un'applicazione. A tale scopo, utilizzare una combinazione di tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server e [Azure Site Recovery](site-recovery-overview.md).

Prima di iniziare, assicurarsi di aver compreso le funzionalità di ripristino di emergenza di SQL Server.Before you start, make sure you understand SQL Server disaster recovery capabilities. Queste funzionalità includono:

* Clustering di failover
* Gruppi di disponibilità AlwaysOn
* Mirroring del database
* Log shipping
* Replica geografica attiva
* Gruppi di failover automatico

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Combinazione delle tecnologie BCDR con Site Recovery

La scelta di una tecnologia BCDR per il ripristino delle istanze di SQL Server deve essere basata sulle esigenze dell'obiettivo del tempo di ripristino (RTO) e dell'obiettivo del punto di ripristino (RPO), come descritto nella tabella seguente. Combinare Site Recovery con l'operazione di failover della tecnologia scelta per orchestrare il ripristino dell'intera applicazione.

Tipo di distribuzione | Tecnologia BCDR | RTO previsto per SQL Server | SPO previsto per SQL Server |
--- | --- | --- | ---
SQL Server in un'infrastruttura di Azure come macchina virtuale (IaaS) o in locale.| [Gruppo di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | Tempo impiegato per impostare la replica secondaria come primaria. | Poiché la replica nella replica secondaria è asincrona, si è verificato una perdita di dati.
SQL Server in una macchina virtuale IaaS di Azure o in locale.| [Clustering di failover (istanza del cluster di failover AlwaysOn)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | Tempo necessario per eseguire il failover tra i nodi. | Poiché Always On FCI utilizza l'archiviazione condivisa, la stessa visualizzazione dell'istanza di archiviazione è disponibile in caso di failover.
SQL Server in una macchina virtuale IaaS di Azure o in locale.| [Mirroring del database (modalità a prestazioni elevate)Database mirroring (high-performance mode)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | Tempo impiegato per forzare il servizio, che utilizza il server mirror come server warm standby. | La replica è asincrona. Il database mirror potrebbe avere un certo ritardo rispetto al database principale. Il ritardo è in genere piccolo. Ma può diventare di grandi dimensioni se il sistema del server principale o mirror è sotto un carico elevato.<br/><br/>La distribuzione dei log può essere un supplemento al mirroring del database. È un'alternativa favorevole al mirroring asincrono del database.
SQL as platform as a service (PaaS) on Azure.<br/><br/>Questo tipo di distribuzione include pool elastici e server di database SQL di Azure.This deployment type includes elastic pools and Azure SQL Database servers. | Replica geografica attiva | 30 secondi dopo l'attivazione del failover.<br/><br/>Quando il failover viene attivato per uno dei database secondari, tutti gli altri database secondari vengono collegati automaticamente al nuovo database primario. | RPO di cinque secondi.<br/><br/>La replica geografica attiva utilizza la tecnologia Always On di SQL ServerSQL Server.Active geo-replication uses the Always On technology of SQL ServerSQL Server. Replica in modo asincrono le transazioni di cui è stato eseguito il commit nel database primario in un database secondario utilizzando l'isolamento dello snapshot.<br/><br/>I dati secondari sono garantiti per non avere mai transazioni parziali.
SQL as PaaS configured with active geo-replication on Azure.<br/><br/>Questo tipo di distribuzione include un'istanza gestita del database SQL, pool elastici e server di database SQL. | Gruppi di failover automatico | RTO di un'ora. | RPO di cinque secondi.<br/><br/>I gruppi di failover automatico forniscono la semantica del gruppo in cima alla replica geografica attiva. Ma viene utilizzato lo stesso meccanismo di replica asincrona.
SQL Server in una macchina virtuale IaaS di Azure o in locale.| Replica con Ripristino siti di AzureReplication with Azure Site Recovery | RTO è in genere inferiore a 15 minuti. Per ulteriori informazioni, leggere il [servizio di servizio RTO fornito da Site Recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Un'ora per la coerenza dell'applicazione e cinque minuti per la coerenza dell'arresto anomalo. Se si sta cercando Un RPO inferiore, utilizzare altre tecnologie BCDR.

> [!NOTE]
> Alcune considerazioni importanti quando si aiuta a proteggere i carichi di lavoro SQL con Site Recovery:Some important considerations when you're helping to protect SQL workloads with Site Recovery:
> * Site Recovery è indipendente dall'applicazione. Site Recovery consente di proteggere qualsiasi versione di SQL Server distribuita in un sistema operativo supportato. Per altre informazioni, vedere la matrice di [supporto per](vmware-physical-azure-support-matrix.md#replicated-machines) il ripristino dei computer replicati.
> * È possibile scegliere di usare Site Recovery per qualsiasi distribuzione in Azure, Hyper-V, VMware o infrastruttura fisica. Seguire le indicazioni fornite alla fine di questo articolo su [come proteggere un cluster](#how-to-help-protect-a-sql-server-cluster) di SQL Server con Site Recovery.
> * Assicurarsi che la frequenza di modifica dei dati osservata nel computer rientri nei limiti di [Site Recovery.](vmware-physical-azure-support-matrix.md#churn-limits) La frequenza di modifica viene misurata in byte scritti al secondo. Per i computer che eseguono Windows, è possibile visualizzare questa frequenza di modifica selezionando la scheda **Prestazioni** in Task Manager. Osservare la velocità di scrittura per ogni disco.
> * Site Recovery supporta la replica delle istanze del cluster di failover in Spazi di archiviazione diretti. Per ulteriori informazioni, vedere [come abilitare](azure-to-azure-how-to-enable-replication-s2d-vms.md)la replica diretta di Spazi di archiviazione .

## <a name="disaster-recovery-of-an-application"></a>Ripristino di emergenza di un'applicazioneDisaster recovery of an application

Site Recovery orchestra il failover di test e il failover dell'intera applicazione con l'aiuto dei piani di ripristino.

Esistono alcuni prerequisiti per garantire che il piano di ripristino sia completamente personalizzato in base alle proprie esigenze. Qualsiasi distribuzione di SQL Server richiede in genere una distribuzione di Active Directory.Any SQL Server deployment typically needs an Active Directory deployment. Richiede inoltre connettività per il livello applicazione.

### <a name="step-1-set-up-active-directory"></a>Passaggio 1: Configurare Active Directory

Configurare Active Directory nel sito di ripristino secondario per la corretta esecuzione di SQL Server.

* **Piccola azienda**: si dispone di un numero ridotto di applicazioni e un singolo controller di dominio per il sito locale. Se si desidera eseguire il failover dell'intero sito, utilizzare la replica di Site Recovery. Questo servizio replica il controller di dominio nel data center secondario o in Azure.This service replicates the domain controller to the secondary datacenter or to Azure.
* **Azienda di medie e grandi dimensioni**: Potrebbe essere necessario configurare controller di dominio aggiuntivi.
  - Se si dispone di un numero elevato di applicazioni, si dispone di una foresta di Active Directory e si vuole eseguire il failover in base all'applicazione o al carico di lavoro, configurare un altro controller di dominio nel data center secondario o in Azure.If you have a large number of applications, have an Active Directory forest, and want to fail over by application or workload, set up another domain controller in the secondary datacenter or in Azure.
  -  Se si usano gruppi di disponibilità AlwaysOnAlways On availability groups per il ripristino in un sito remoto, configurare un altro controller di dominio nel sito secondario o in Azure.If you're using Always On availability groups to recover to a remote site, set up another domain controller on the secondary site or in Azure. Questo controller di dominio viene utilizzato per l'istanza di SQL Server recuperata.

Le istruzioni contenute in questo articolo presuppongono che un controller di dominio sia disponibile nella posizione secondaria. Per ulteriori informazioni, vedere le procedure per [proteggere Active Directory con Site Recovery](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Passaggio 2: Verificare la connettività con altri livelliStep 2: Ensure connectivity with other tiers

Dopo aver eseguito il livello di database nell'area di Azure di destinazione, assicurarsi di disporre della connettività con i livelli applicazione e Web.After the database tier is running in the target Azure region, ensure that you have connectivity with the application and web tiers. Eseguire in anticipo i passaggi necessari per convalidare la connettività con il failover di test.

Per informazioni su come progettare applicazioni per considerazioni sulla connettività, vedere questi esempi:To understand how you can design applications for connectivity considerations, see these examples:

* [Progettare un'applicazione per il ripristino di emergenza cloud](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Strategie di ripristino di emergenza per pool elastico](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Passaggio 3: Interoperabilità con I gruppi Di tipo Always On, replica geografica attiva e failover automaticoStep 3: Interoperate with Always On, active geo-replication, and auto-failover groups

Tecnologie BCDR Always On, i gruppi di replica geografica attiva e di failover automatico hanno repliche secondarie di SQL Server in esecuzione nell'area di Azure di destinazione. Il primo passaggio per il failover dell'applicazione consiste nello specificare questa replica come primaria. Questo passaggio presuppone che tu abbia già un controller di dominio nel database secondario. Il passaggio potrebbe non essere necessario se si sceglie di eseguire un failover automatico. Eseguire il failover dei livelli Web e dell'applicazione solo dopo il completamento del failover del database.

> [!NOTE]
> Se è stato contribuito a proteggere le macchine SQL con Site Recovery, è sufficiente creare un gruppo di ripristino di questi computer e aggiungerne il failover nel piano di ripristino.

[Creare un piano di ripristino](site-recovery-create-recovery-plans.md) con macchine virtuali di livello applicazione e Web.Create a recovery plan with application and web tier virtual machines. I passaggi seguenti illustrano come aggiungere il failover del livello di database:

1. Importare gli script per eseguire il failover del gruppo di disponibilità SQL in una [macchina virtuale di Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) e in una macchina virtuale [classica.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1) Importare gli script nell'account di Automazione di Azure.Import the scripts into your Azure Automation account.

    [![Immagine del logo "Distribuisci in Azure"Image of a "Deploy to Azure" logo](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Aggiungere lo script ASR-SQL-FailoverAG come pre-azione del primo gruppo del piano di ripristino.

1. Seguire le istruzioni disponibili nello script per creare una variabile di automazione. Questa variabile fornisce il nome dei gruppi di disponibilità.

### <a name="step-4-conduct-a-test-failover"></a>Passaggio 4: Eseguire un failover di testStep 4: Conduct a test failover

Alcune tecnologie BCDR, ad esempio SQL Always On, non supportano in modo nativo il failover di test. Si consiglia l'approccio seguente solo quando si *utilizzano tali tecnologie*.

1. Configurare Backup di Azure nella macchina virtuale che ospita la replica del gruppo di disponibilità in Azure.Set up [Azure Backup](../backup/backup-azure-arm-vms.md) on the VM that hosts the availability group replica in Azure.

1. Prima di attivare il failover di test del piano di ripristino, ripristinare la macchina virtuale dal backup eseguito nel passaggio precedente.

    ![Screenshot della finestra per il ripristino di una configurazione da Backup di Azure](./media/site-recovery-sql/restore-from-backup.png)

1. [Forzare un quorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) nella macchina virtuale ripristinata dal backup.

1. Aggiornare l'indirizzo IP del listener in modo che sia un indirizzo disponibile nella rete di failover di test.

    ![Screenshot della finestra Delle regole e della finestra di dialogo delle proprietà dell'indirizzo IP](./media/site-recovery-sql/update-listener-ip.png)

1. Portare il listener online.

    ![Screenshot della finestra con etichetta Content_AG che mostra i nomi e gli stati dei server](./media/site-recovery-sql/bring-listener-online.png)

1. Verificare che il servizio di bilanciamento del carico nella rete di failover disponga di un indirizzo IP, dal pool di indirizzi IP front-end corrispondente a ogni listener del gruppo di disponibilità e con la macchina virtuale di SQL Server nel pool back-end.

     ![Screenshot della finestra intitolata "SQL-AlwaysOn-LB - Pool IP front-end](./media/site-recovery-sql/create-load-balancer1.png)

    ![Screenshot della finestra intitolata "SQL-AlwaysOn-LB - Pool IP back-end](./media/site-recovery-sql/create-load-balancer2.png)

1. Nei gruppi di ripristino successivi aggiungere il failover del livello applicazione seguito dal livello Web per questo piano di ripristino.

1. Eseguire un failover di test del piano di ripristino per testare il failover end-to-end dell'applicazione.

## <a name="steps-to-do-a-failover"></a>Passaggi per eseguire un failover

Dopo aver aggiunto lo script nel passaggio 3 e convalidarlo nel passaggio 4, è possibile eseguire un failover del piano di ripristino creato nel passaggio 3.After you add the script in Step 3 and validate it in Step 4, you can do a failover of the recovery plan created in Step 3.

I passaggi di failover per i livelli di applicazione e Web devono essere gli stessi sia nei piani di failover di test che nei piani di ripristino del failover.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Come proteggere un cluster di SQL ServerHow to help protect a SQL Server cluster

Per un cluster che esegue SQL Server Standard Edition o SQL Server 2008 R2, è consigliabile usare la replica di Site Recovery per proteggere SQL Server.For a cluster running SQL Server Standard edition or SQL Server 2008 R2, we recommend use Site Recovery replication to help protect SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure to Azure and On-premises to Azure

Site Recovery non fornisce il supporto del cluster guest durante la replica in un'area di Azure.Site Recovery doesn't provide guest cluster support when replicating to an Azure region. L'edizione Standard di SQL Server non fornisce inoltre una soluzione di ripristino di emergenza a basso costo. In questo scenario, è consigliabile proteggere il cluster di SQL Server in un'istanza autonoma di SQL Server nella posizione primaria e ripristinarla nel database secondario.

1. Configurare un'istanza autonoma di SQL Server aggiuntiva nell'area primaria di Azure o nel sito locale.

1. Configurare l'istanza in modo che funga da mirror per i database che si desidera proteggere. Configurare il mirroring in modalità a protezione elevata.

1. Configurare Site Recovery nel sito primario per le macchine virtuali e i server fisici [di Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md)o [VMware.](site-recovery-vmware-to-azure-classic.md)

1. Utilizzare la replica di Site Recovery per replicare la nuova istanza di SQL Server nel sito secondario. Poiché si tratta di una copia mirror ad alta sicurezza, verrà sincronizzata con il cluster primario ma replicata tramite la replica di Site Recovery.

   ![Immagine di un cluster standard che mostra la relazione e il flusso tra un sito primario, Site Recovery e Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerazioni sul failback

Per i cluster SQL Server Standard, il failback dopo un failover non pianificato richiede un backup e un ripristino di SQL Server.For SQL Server Standard clusters, failback after an unplanned failover requires a SQL Server backup and restore. Questa operazione viene eseguita dall'istanza mirror al cluster originale con il ristabilimento del mirror.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>In che modo SQL Server ottiene la licenza quando viene utilizzato con Site Recovery?

La replica di Site Recovery per SQL Server è coperta dai vantaggi del ripristino di emergenza di Software Assurance. Questa copertura si applica a tutti gli scenari di Site Recovery: dal locale al ripristino di emergenza di Azure e ripristino di emergenza di Azure IaaS tra aree. Per altre informazioni, vedere Prezzi di [Azure Site Recovery.See Azure Site Recovery pricing](https://azure.microsoft.com/pricing/details/site-recovery/) for more.

### <a name="will-site-recovery-support-my-sql-server-version"></a>Site Recovery supporterà la mia versione di SQL Server?

Site Recovery è indipendente dall'applicazione. Site Recovery consente di proteggere qualsiasi versione di SQL Server distribuita in un sistema operativo supportato. Per altre informazioni, vedere la matrice di [supporto per](vmware-physical-azure-support-matrix.md#replicated-machines) il ripristino dei computer replicati.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni [sull'architettura](site-recovery-components.md)di Site Recovery .
* Per SQL Server in Azure, altre informazioni sulle [soluzioni](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) a disponibilità elevata per il ripristino in un'area di Azure secondaria.
* Per il database SQL, altre informazioni sulla [continuità aziendale](../sql-database/sql-database-business-continuity.md) e sulle opzioni di [disponibilità elevata](../sql-database/sql-database-high-availability.md) per il ripristino in un'area di Azure secondaria.
* Per i computer SQL Server in locale, vedere altre informazioni sulle opzioni di disponibilità elevata per il ripristino nelle macchine virtuali di Azure.For SQL Server machines at on-premises, learn more about the [high availability options](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) for recovery in Azure Virtual Machines.
