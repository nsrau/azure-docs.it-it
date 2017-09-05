---
title: Eseguire la replica di applicazioni con SQL Server e Azure Site Recovery | Microsoft Docs
description: "Questo articolo descrive come eseguire la replica di SQL Server con Azure Site Recovery per ottenere le funzionalità di ripristino di emergenza di SQL Server."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: pratshar
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: a1e5461ef2188a42a3edd6cc35827874ddd6e3f3
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteggere SQL Server con il ripristino di emergenza di SQL Server e Azure Site Recovery

Questo articolo descrive come proteggere il back-end SQL Server di un'applicazione usando una combinazione di [Azure Site Recovery](site-recovery-overview.md) e tecnologie di continuità aziendale e ripristino di emergenza di SQL Server.

Prima di iniziare, acquisire familiarità con le funzionalità di ripristino di emergenza di SQL Server, come il clustering di failover, i gruppi di disponibilità AlwaysOn, il mirroring del database e il log shipping.


## <a name="sql-server-deployments"></a>Distribuzioni di SQL Server

Molti carichi di lavoro usano come base SQL Server, che può essere integrato con app come SharePoint, Dynamics e SAP per implementare servizi dati.  È possibile distribuire SQL Server in diversi modi.

* **SQL Server autonomo**: SQL Server e tutti i database sono ospitati in un singolo computer (fisico o una macchina virtuale). Se è virtualizzato, il clustering dell'host viene usato per la disponibilità elevata. La disponibilità elevata a livello di guest non viene implementata.
* **Istanze di clustering di failover di SQL Server (AlwaysOn)**: due o più nodi che eseguono istanze di SQL Server con dischi condivisi vengono configurati in un cluster di failover di Windows. Se un nodo è inattivo, il cluster può effettuare il failover di SQL Server in un'altra istanza. Questa configurazione viene in genere usata per implementare la disponibilità elevata in un sito primario. Questa distribuzione non offre la protezione da errori o interruzioni nel livello dell'archiviazione condivisa. Un disco condiviso può essere implementato con iSCSI, Fibre Channel o VHDX condiviso.
* **Gruppi di disponibilità SQL AlwaysOn**: due o più nodi vengono configurati in un cluster non condiviso con i database SQL Server configurati in un gruppo di disponibilità con replica sincrona e failover automatico.

 Per ripristinare i database in un sito remoto, in questo articolo vengono sfruttate le tecnologie di ripristino di emergenza native di SQL riportate di seguito:

* Gruppi di disponibilità SQL AlwaysOn, per supportare il ripristino di emergenza per SQL Server 2012 o 2014 Enterprise Edition.
* Mirroring del database SQL in modalità protezione elevata, per SQL Server Standard Edition (qualsiasi versione) o SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Supporto di Site Recovery

### <a name="supported-scenarios"></a>Scenari supportati
Site Recovery può proteggere SQL Server, come riepilogato nella tabella.

**Scenario** | **In un sito secondario** | **In Azure**
--- | --- | ---
**Hyper-V** | Sì | Sì
**VMware** | Sì | Sì
**Server fisico** | Sì | Sì

### <a name="supported-sql-server-versions"></a>Versioni di SQL Server supportate
Per gli scenari supportati, sono supportate le versioni di SQL Server seguenti:

* SQL Server 2016 Enterprise e Standard
* SQL Server 2014 Enterprise e Standard
* SQL Server 2012 Enterprise e Standard
* SQL Server 2008 R2 Enterprise e Standard

### <a name="supported-sql-server-integration"></a>Supporto dell'integrazione con SQL Server

Site Recovery può essere integrato con le tecnologie di continuità aziendale e ripristino di emergenza native di SQL Server riepilogate nella tabella per fornire una soluzione di ripristino di emergenza.

**Funzionalità** | **Dettagli** | **SQL Server** |
--- | --- | ---
**Gruppo di disponibilità AlwaysOn** | Ognuna delle istanze autonome multiple di SQL Server viene eseguita in un cluster di failover con più nodi.<br/><br/>I database possono essere raggruppati in gruppi di failover che possono essere copiati (con mirroring) in istanze di SQL Server in modo che non sia necessaria alcuna archiviazione condivisa.<br/><br/>Fornisce il ripristino di emergenza tra un sito primario e uno o più siti secondari. Due nodi possono essere configurati in un cluster non condiviso con i database di SQL Server configurati in un gruppo di disponibilità con replica sincrona e failover automatico. | SQL Server 2014 e 2012 Enterprise
**Clustering di failover (istanza del cluster di failover AlwaysOn)** | SQL Server usa il clustering di failover di Windows per l'elevata disponibilità dei carichi di lavoro SQL Server locali.<br/><br/>I nodi che eseguono le istanze di SQL Server con dischi condivisi sono configurati in un cluster di failover. Se un'istanza non è attiva, il cluster esegue il failover in un'altra istanza.<br/><br/>Il cluster non protegge da errori o interruzioni nell'archiviazione condivisa. Il disco condiviso può essere implementato con iSCSI, fibre channel o VHDX condivisi. | Edizioni SQL Server Enterprise<br/><br/>Edizione SQL Server Standard (limitata a soli due nodi)
**Mirroring del database (modalità di protezione elevata)** | Protegge un singolo database in una singola copia secondaria. Disponibile nelle modalità di replica a protezione elevata (sincrona) e a prestazione elevata (asincrona). Non richiede un cluster di failover. | SQL Server 2008 R2<br/><br/>Tutte le edizioni di SQL Server Enterprise
**SQL Server autonomo** | SQL Server e il database si trovano in un singolo server (fisico o virtuale). Il clustering dell'host viene usato per la disponibilità elevata se il server è virtuale. Nessuna disponibilità elevata a livello di guest. | Edizione Enterprise o Standard

## <a name="deployment-recommendations"></a>Indicazioni di distribuzione

Nella tabella seguente vengono riepilogate le indicazioni per l'integrazione delle tecnologie di continuità aziendale e ripristino di emergenza (BCDR) di SQL Server con Site Recovery.

| **Versione** | **Edizione** | **Distribuzione** | **Da locale a locale** | **Da locale ad Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 o 2012 |Enterprise |Istanza del cluster di failover |Gruppi di disponibilità AlwaysOn |Gruppi di disponibilità AlwaysOn |
|| Enterprise |Gruppo di disponibilità AlwaysOn per la disponibilità elevata |Gruppi di disponibilità AlwaysOn |Gruppi di disponibilità AlwaysOn | |
|| Standard |Istanza del cluster di failover (FCI) |Replica di Site Recovery con mirror locale |Replica di Site Recovery con mirror locale | |
|| Enterprise o Standard |Autonoma |Replica di Site Recovery |Replica di Site Recovery | |
| SQL Server 2008 R2 o 2008 |Enterprise o Standard |Istanza del cluster di failover (FCI) |Replica di Site Recovery con mirror locale |Replica di Site Recovery con mirror locale |
|| Enterprise o Standard |Autonoma |Replica di Site Recovery |Replica di Site Recovery | |
| SQL Server (qualsiasi versione) |Enterprise o Standard |Istanza del cluster di failover: applicazione DTC |Replica di Site Recovery |Non supportato |

## <a name="deployment-prerequisites"></a>Prerequisiti di distribuzione

* Distribuzione di SQL Server locale che esegue una versione supportata di SQL Server. In genere è necessario anche Active Directory per il server SQL.
* Requisiti per lo scenario che si vuole distribuire. Per altre informazioni sui requisiti per il supporto, vedere gli articoli relativi alla [replica in Azure](site-recovery-support-matrix-to-azure.md) e [in locale](site-recovery-support-matrix.md) e ai [prerequisiti per la distribuzione](site-recovery-prereq.md).
* Per configurare il ripristino in Azure, eseguire lo strumento [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) nelle macchine virtuali di SQL Server per verificare che siano compatibili con Azure e Site Recovery.

## <a name="set-up-active-directory"></a>Configurare Active Directory

Per garantire la corretta esecuzione di SQL Server, configurare Active Directory nel sito di ripristino secondario.

* **Organizzazione di piccole dimensioni**: in presenza di un numero ridotto di applicazioni e di un singolo controller di dominio per il sito locale, se si vuole effettuare il failover dell'intero sito è consigliabile usare la replica di Site Recovery per replicare il controller di dominio nel data center secondario o in Azure.
* **Organizzazione di medie o grandi dimensioni**: se si hanno un numero elevato di applicazioni e una foresta Active Directory e si vuole effettuare il failover per applicazione o carico di lavoro, è consigliabile configurare un controller di dominio aggiuntivo nel data center secondario o in Azure. Se si usano gruppi di disponibilità AlwaysOn per il ripristino in un sito remoto, è consigliabile configurare un altro controller di dominio aggiuntivo nel sito secondario o in Azure, da usare per l'istanza di SQL Server ripristinata.

Le istruzioni in questo articolo presuppongono che sia disponibile un controller di dominio nella posizione secondaria. [Altre informazioni](site-recovery-active-directory.md) sulla protezione di Active Directory con Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Eseguire l'integrazione con SQL Server AlwaysOn per la replica in Azure

È necessario eseguire queste operazioni:

1. Importare gli script nell'account di Automazione di Azure. Sono inclusi gli script per il failover del gruppo di disponibilità SQL in una [macchina virtuale di Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) e una [macchina virtuale classica](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Distribuzione in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Aggiungere ASR-SQL-FailoverAG come azione preliminare del primo gruppo del piano di ripristino.

1. Seguire le istruzioni disponibili nello script per creare una variabile di automazione in modo da specificare il nome dei gruppi di disponibilità.

### <a name="steps-to-do-a-test-failover"></a>Passaggi per eseguire un failover di test

SQL AlwaysOn non supporta il failover di test in modo nativo. È quindi consigliabile eseguire queste operazioni:

1. Configurare [Backup di Azure](../backup/backup-azure-vms.md) nella macchina virtuale che ospita la replica del gruppo di disponibilità in Azure.

1. Prima di attivare il failover di test del piano di ripristino, ripristinare la macchina virtuale dal backup creato nel passaggio precedente.

    ![Eseguire il ripristino da Backup di Azure ](./media/site-recovery-sql/restore-from-backup.png)

1. [Forzare un quorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) nella macchina virtuale ripristinata dal backup.

1. Aggiornare l'indirizzo IP del listener a un indirizzo IP disponibile nella rete di failover di test.

    ![Aggiornare l'indirizzo IP del listener](./media/site-recovery-sql/update-listener-ip.png)

1. Portare online il listener.

    ![Portare online il listener](./media/site-recovery-sql/bring-listener-online.png)

1. Creare un servizio di bilanciamento del carico con un indirizzo IP creato nel pool di indirizzi IP front-end corrispondente al listener di ogni gruppo di disponibilità e con la macchina virtuale SQL aggiunta nel pool back-end.

     ![Creazione del servizio di bilanciamento del carico - Pool di indirizzi front-end ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Creazione del servizio di bilanciamento del carico - Pool back-end ](./media/site-recovery-sql/create-load-balancer2.png)

1. Effettuare un failover di test del piano di ripristino.

### <a name="steps-to-do-a-failover"></a>Passaggi per eseguire un failover

Dopo aver aggiunto lo script nel piano di ripristino e aver convalidato il piano di ripristino tramite un failover di test, è possibile eseguire il failover del piano di ripristino.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Eseguire l'integrazione con SQL Server AlwaysOn per la replica in un sito secondario locale

Se SQL Server usa gruppi di disponibilità per la disponibilità elevata o un'istanza di clustering di failover, è consigliabile usare gruppi di disponibilità anche nel sito di ripristino. Si noti che ciò si applica alle app che non usano transazioni distribuite.

1. [Configurare i database](https://msdn.microsoft.com/library/hh213078.aspx) in gruppi di disponibilità.
1. Creare una rete virtuale nel sito secondario.
1. Configurare una connessione VPN da sito a sito tra la rete virtuale e il sito primario.
1. Creare una macchina virtuale nel sito di ripristino e installarvi SQL Server.
1. Estendere i gruppi di disponibilità AlwaysOn esistenti alla nuova VM di SQL Server. Configurare l'istanza di SQL Server come una copia di replica asincrona.
1. Creare un listener del gruppo di disponibilità o aggiornare il listener esistente per includere la macchina virtuale di replica asincrona.
1. Verificare che la farm dell'applicazione sia configurata con il listener. Se è configurata con il nome del server di database, aggiornarla per usare il listener in modo che non debba essere riconfigurata dopo il failover.

Per le applicazioni che usano transazioni distribuite, è consigliabile distribuire Site Recovery con la [replica da sito a sito di VM VMware/server fisici](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considerazioni sul piano di ripristino
1. Aggiungere questo script di esempio alla libreria VMM nei siti primari e secondari.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Quando si crea un piano di ripristino per l'applicazione, aggiungere un'azione preliminare al passaggio di script Group-1 che richiami lo script per eseguire il failover dei gruppi di disponibilità.

## <a name="protect-a-standalone-sql-server"></a>Proteggere un'istanza di SQL Server autonoma

In questo scenario, è consigliabile usare la replica di Site Recovery per proteggere il computer SQL Server. La procedura esatta varia a seconda che SQL Server sia configurato come VM o server fisico e che si voglia eseguire la replica in Azure o in un sito secondario locale. Vedere l'articolo relativo agli [scenari di Site Recovery](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Proteggere un cluster SQL Server (Standard Edition/Windows Server 2008 R2)

Per un cluster che esegue SQL Server Standard Edition o SQL Server 2008 R2, è consigliabile usare la replica di Site Recovery per proteggere SQL Server.

### <a name="on-premises-to-on-premises"></a>Da sito locale a sito locale

* Se l'app usa transazioni distribuite, è consigliabile distribuire [Site Recovery con la replica SAN](site-recovery-vmm-san.md) per un ambiente Hyper-V oppure la replica da [server fisico/VMware a VMware](site-recovery-vmware-to-vmware.md) per un ambiente VMware.
* Per le applicazioni non DTC, usare l'approccio precedente per ripristinare il cluster come un server autonomo sfruttando un mirror del database a protezione elevata locale.

### <a name="on-premises-to-azure"></a>Da sito locale ad Azure

Site Recovery non offre il supporto di cluster guest durante la replica in Azure. Inoltre, SQL Server non fornisce una soluzione di ripristino di emergenza a costo contenuto per l'edizione Standard. In questo scenario, è consigliabile proteggere il cluster SQL Server locale in un'istanza di SQL Server autonoma e ripristinarlo in Azure.

1. Configurare un'istanza di SQL Server autonoma aggiuntiva nel sito locale.
1. Configurare l'istanza in modo che funga da mirror per i database da proteggere. Configurare il mirroring in modalità protezione elevata.
1. Configurare Site Recovery nel sito locale, per [Hyper-V](site-recovery-hyper-v-site-to-azure.md) o [VM VMware/server fisici](site-recovery-vmware-to-azure-classic.md).
1. Usare la replica di Site Recovery per eseguire la replica della nuova istanza di SQL Server in Azure. Trattandosi di una copia mirror a protezione elevata, verrà sincronizzata con il cluster primario, ma verrà replicata in Azure con la replica di Site Recovery.


![Cluster standard](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerazioni sul failback

Per i cluster SQL Server Standard, il failback dopo un failover non pianificato richiede un backup e un ripristino di SQL Server dall'istanza del mirror al cluster originale, con ridefinizione del mirror.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni](site-recovery-components.md) sull'architettura di Site Recovery.

