---
title: Spostare la macchina virtuale in un'altra area (Azure Site Recovery)
description: Informazioni su come eseguire la migrazione della macchina virtuale di SQL Server da un'area a un'altra all'interno di Azure.Learn how you can migrate your SQL Server virtual machine from one region to another within Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022298"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Spostare la macchina virtuale di SQL Server in un'altra area all'interno di Azure con i servizi di Azure Site RecoveryMove SQL Server VM to another region within Azure with Azure Site Recovery services

Questo articolo illustra come usare Azure Site Recovery per eseguire la migrazione della macchina virtuale (VM) di SQL Server da un'area all'altra all'interno di Azure.This article teaches you how to use Azure Site Recovery to migrate your SQL Server virtual machine (VM) from one region to another within Azure. 

Lo spostamento di una macchina virtuale di SQL Server in un'area diversa richiede l'esecuzione delle operazioni seguenti:Move a SQL Server VM to a different region requires doing the following:
1. [**Preparazione**](#prepare-to-move): Verificare che sia la macchina virtuale DI SQL Server di origine che l'area di destinazione siano adeguatamente preparate per lo spostamento. 
1. [**Configurazione:**](#configure-azure-site-recovery-vault)lo spostamento della macchina virtuale di SQL Server richiede che si tratti di un oggetto replicato all'interno dell'insieme di credenziali di Azure Site Recovery.Configuring : Moving your SQL Server VM requires that it is a replicated object within the Azure Site Recovery vault. È necessario aggiungere la macchina virtuale di SQL Server all'insieme di credenziali di Azure Site Recovery.You need to add your SQL Server VM to the Azure Site Recovery vault. 
1. [**Test:**](#test-move-process)la migrazione della macchina virtuale di SQL Server richiede il failover dall'area di origine all'area di destinazione replicata. Per assicurarsi che il processo di spostamento abbia esito positivo, è innanzitutto necessario verificare che la macchina virtuale di SQL Server possa eseguire correttamente il failover nell'area di destinazione. Questo aiuterà a esporre eventuali problemi e evitarli quando si esegue lo spostamento effettivo. 
1. [**Spostamento:**](#move-the-sql-server-vm)dopo il failover di test superato e la sicurezza di cui è possibile eseguire la migrazione della macchina virtuale di SQL Server, è possibile eseguire lo spostamento della macchina virtuale nell'area di destinazione. 
1. [**Pulizia:**](#clean-up-source-resources)per evitare addebiti per la fatturazione, rimuovere la macchina virtuale di SQL Server dall'insieme di credenziali e tutte le risorse non necessarie rimaste nel gruppo di risorse. 

## <a name="verify-prerequisites"></a>Verificare i prerequisiti 

- Verificare che sia [supportata](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)lo spostamento dall'area di origine all'area di destinazione.  
- Esaminare l'architettura e i componenti dello [scenario,](../../../site-recovery/azure-to-azure-architecture.md) nonché [le limitazioni e i requisiti](../../../site-recovery/azure-to-azure-support-matrix.md)del supporto. 
- Verificare le autorizzazioni dell'account. Se si è creato un account Azure gratuito, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una macchina virtuale e copiare i dati tramite Azure Site Recovery, è necessario disporre di:To enable replication for a VM and copy data using Azure Site Recovery, you must have: 
    - Autorizzazioni per creare una macchina virtuale. Il ruolo predefinito *Collaboratore macchina virtuale* dispone di queste autorizzazioni, che includono:The Virtual Machine Contributor built-in role has these permissions, which include: 
        - Autorizzazioni per creare una macchina virtuale nel gruppo di risorse selezionato. 
        - Autorizzazioni per creare una macchina virtuale nella rete virtuale selezionata. 
        - Autorizzazioni per scrivere nell'account di archiviazione selezionato. 
      - Autorizzazione per la gestione delle operazioni di Azure Site Recovery. Il ruolo *Collaboratore di Site Recovery* dispone di tutte le autorizzazioni necessarie per gestire le operazioni di Site Recovery in un insieme di credenziali di Servizi di ripristino.  

## <a name="prepare-to-move"></a>Preparati a muoverti
Preparare sia la macchina virtuale SQL Server di origine che l'area di destinazione per lo spostamento. 

### <a name="prepare-the-source-sql-server-vm"></a>Preparare la macchina virtuale di SQL Server di originePrepare the source SQL Server VM

- Verificare che tutti i certificati radice più recenti si trovino nella macchina virtuale di SQL Server che si vuole spostare. Se i certificati radice più recenti non sono presenti, i vincoli di sicurezza impediranno la copia dei dati nell'area di destinazione. 
- Per le macchine virtuali Windows, installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale, in modo che tutti i certificati radice attendibili siano presenti nel computer. In un ambiente disconnesso, seguire il processo standard di aggiornamento dei certificati e delle date UP di Windows per l'organizzazione. 
- Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella macchina virtuale. 
- Assicurarsi di non usare un proxy di autenticazione per controllare la connettività di rete per le macchine virtuali che si desidera spostare. 
- Se la VM che si intende spostare non ha accesso a Internet o usa un proxy firewall per controllare l'accesso in uscita, controllare i requisiti. 
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, tra cui i servizi di bilanciamenti del carico, i gruppi di sicurezza di rete e gli indirizzi IP pubblici. 

### <a name="prepare-the-target-region"></a>Preparare l'area di destinazione

- Verificare che la sottoscrizione di Azure consenta di creare VM nell'area di destinazione usata per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria. 
- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare le macchine virtuali con dimensioni corrispondenti alle macchine virtuali di origine. Se si usa Site Recovery per copiare i dati nella destinazione, Site Recovery sceglie la stessa dimensione o la dimensione più vicina possibile per la macchina virtuale di destinazione. 
- Assicurarsi di creare una risorsa di destinazione per ogni componente identificato nel layout di rete di origine. Questo passaggio è importante affinché le VM nell'area di destinazione abbiano tutte le caratteristiche e le funzionalità disponibili nell'area di origine. 
    - Azure Site Recovery individua e crea automaticamente una rete virtuale quando si abilita la replica per la VM di origine. È anche possibile precreare una rete e assegnarla alla macchina virtuale nel flusso utente per l'abilitazione della replica. È necessario creare manualmente eventuali altre risorse nell'area di destinazione.
- Per creare le risorse di rete usate più di frequente che sono pertinenti per il caso specifico in base alla configurazione della VM di origine, vedere la documentazione seguente: 
    - [Gruppi di sicurezza di rete](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Servizio di bilanciamento del carico](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Indirizzo IP pubblico](../../../virtual-network/virtual-network-public-ip-address.md)
    - Per altri componenti di rete, vedere la [documentazione relativa](../../../virtual-network/virtual-networks-overview.md)alla rete .
- Se si vuole testare la configurazione prima dello spostamento finale nell'area di destinazione, creare manualmente una rete non di produzione nell'area di destinazione. Questo passaggio è consigliato perché garantisce un'interferenza minima con la rete di produzione. 

## <a name="configure-azure-site-recovery-vault"></a>Configurare l'insieme di credenziali di Azure Site Recovery

La procedura seguente illustra come usare Azure Site Recovery per copiare i dati nell'area di destinazione. Creare l'insieme di credenziali dei servizi di ripristino in qualsiasi area diversa dall'area di origine. 

1. Accedere al portale di [Azure](https://portal.azure.com). 
1. Scegliere **Crea una risorsa** nell'angolo superiore sinistro del riquadro di spostamento. 
1. Selezionare Strumenti di **gestione del & IT,** quindi **selezionare Backup e Ripristino sito**. 
1. Nella scheda **Nozioni di base,** in **Dettagli progetto,** creare un nuovo gruppo di risorse nell'area di destinazione oppure selezionare un gruppo di risorse esistente nell'area di destinazione. 
1. In **Dettagli istanza**, specificare un nome per l'insieme di credenziali, quindi selezionare l'area di destinazione dall'elenco a discesa. **Region** 
1. Selezionare **Revisione e Crea** per creare l'insieme di credenziali dei servizi di ripristino. 
1. Selezionare **Tutti i servizi** nell'angolo superiore sinistro del riquadro di spostamento e nella casella di ricerca digitare `recovery services`. 
1. (Opzionalmente) Selezionare la stella accanto a **Vault di Servizi di ripristino** per aggiungerla alla barra di navigazione rapida. 
1. Selezionare **I vault dei servizi** di ripristino e quindi selezionare l'insieme di credenziali di Servizi di ripristino creato. 
1. Nel riquadro **Panoramica** selezionare **Replica**. 

   ![Configurare la replica](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. Selezionare **Origine** e quindi **Azure** come origine. Selezionare i valori appropriati per gli altri campi a discesa, ad esempio il percorso per le macchine virtuali di origine. Solo i gruppi di risorse che si trovano nell'area **Ubicazione** di origine saranno visibili nel campo Gruppo di risorse di **origine.** 
1. Selezionare **Macchine virtuali** e quindi scegliere le macchine virtuali di cui eseguire la migrazione. Selezionare **OK** per salvare la selezione della macchina virtuale. 
1. Selezionare **Impostazioni**, quindi scegliere la **posizione di destinazione** dall'elenco a discesa. Deve essere il gruppo di risorse preparato in precedenza. 
1. Dopo aver personalizzato la replica, selezionare **Crea risorse di destinazione** per creare le risorse nella nuova posizione. 
1. Al termine della creazione delle risorse, selezionare **Abilita replica** per avviare la replica della macchina virtuale di SQL Server dall'origine all'area di destinazione.
1. È possibile controllare lo stato della replica passando all'insieme di credenziali di ripristino, selezionando **Elementi replicati** e visualizzando **lo stato** della macchina virtuale di SQL Server. Lo stato **Protetto** indica che la replica è stata completata. 

   ![Verificare lo stato della replica](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>Testare il processo di spostamento
I passaggi seguenti illustrano come usare Azure Site Recovery per testare il processo di spostamento. 

1. Passare all'insieme di **credenziali di Servizi di ripristino** nel portale di [Azure](https://portal.azure.com) e selezionare **Elementi replicati**. 
1. Selezionare la macchina virtuale di SQL Server che si desidera spostare, verificare che **l'integrità** della replica sia **visualizzata** come Integro e quindi selezionare **Failover di test**. 

   ![Testare il failover per la macchina virtualeTest failover for your VM](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. Nella pagina **Failover di test** selezionare il punto di ripristino più recente coerente con **l'app** da utilizzare per il failover, in quanto è l'unico tipo di snapshot in grado di garantire la coerenza dei dati di SQL Server. 
1. Selezionare la rete virtuale in **Rete virtuale** di Azure e quindi selezionare **OK** per testare il failover. 
   
   >[!IMPORTANT]
   > È consigliabile usare una rete VM di Azure separata per il test di failover. Non usare la rete di produzione che è stata configurata quando è stata abilitata la replica e in cui si vogliono spostare infine le VM. 

1. Per monitorare lo stato di avanzamento, passare all'insieme di credenziali, selezionare Processi di **Site Recovery** in **Monitoraggio**, quindi selezionare il processo di failover di **test** in corso.

   ![Monitorare lo stato di avanzamento del test di failoverMonitor failover test](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. Al termine del test, passare a **Macchine virtuali** nel portale ed esaminare la macchina virtuale appena creata. Assicurarsi che la macchina virtuale di SQL Server sia in esecuzione, ridimensionata in modo appropriato e connessa alla rete appropriata. 
1. Eliminare la macchina virtuale creata come parte del test, poiché l'opzione **Failover** verrà visualizzata in grigio fino a quando non vengono eliminate le risorse di test di failover. Tornare all'insieme di credenziali, selezionare **Elementi replicati**, selezionare la macchina virtuale di SQL Server e quindi selezionare **Pulizia failover**di test . Registrare e salvare eventuali osservazioni associate al test nella sezione **Note** e selezionare la casella di controllo accanto a **Test completata. Eliminare le macchine virtuali di failover**di test . Selezionare **OK** per pulire le risorse dopo il test. 

   ![pulire gli elementi dopo il test di failover](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Spostare la macchina virtuale di SQL ServerMove the SQL Server VM 
I passaggi seguenti illustrano come spostare la macchina virtuale di SQL Server dall'area di origine all'area di destinazione. 

1. Passare all'insieme di credenziali **Servizi di ripristino,** selezionare **Elementi replicati**, selezionare la macchina virtuale e quindi selezionare **Failover**. 

   ![Avviare il failover](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. Selezionare il punto di ripristino **più recente coerente con l'app** in Punto di **ripristino**. 
1. Selezionare la casella di controllo accanto a **Arresta il computer prima**di avviare il failover . Site Recovery tenterà di arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continuerà anche se l'arresto non riesce. 
1. Selezionare **OK** per avviare il failover.
1. È possibile monitorare il processo di failover dalla stessa pagina Processi di **Site Recovery** visualizzati durante il monitoraggio del test di failover nella sezione precedente. 
1. Al termine del processo, verificare che la macchina virtuale di SQL Server venga visualizzata nell'area di destinazione come previsto. 
1. Tornare all'insieme di credenziali, selezionare **Elementi replicati**, selezionare la macchina virtuale di SQL Server e selezionare **Commit** per completare il processo di spostamento nell'area di destinazione. Attendere il completamento del processo di commit. 
1. Registrare la macchina virtuale di SQL Server con il provider di risorse DELLA macchina virtuale SQL per abilitare la gestibilità della **macchina virtuale SQL** nel portale di Azure e le funzionalità associate al provider di risorse. Per altre informazioni, vedere [Registrare la macchina virtuale di SQL Server con](virtual-machines-windows-sql-register-with-rp.md)il provider di risorse della macchina virtuale SQL. 

  > [!WARNING]
  > La coerenza dei dati di SQL Server sql Server è garantita solo con snapshot coerenti con le app. Lo snapshot **elaborato più recente** non può essere utilizzato per il failover di SQL Server come snapshot di recupero da arresto anomalo del sistema non è in grado di garantire la coerenza dei dati di SQL Server.The latest processed snapshot can't be used for SQL Server failover as a crash recovery snapshot can't guarantee SQL Server data consistency. 

## <a name="clean-up-source-resources"></a>Pulire le risorse di origineClean up source resources
Per evitare addebiti per la fatturazione, rimuovere la macchina virtuale di SQL Server dall'insieme di credenziali ed eliminare le risorse associate non necessarie. 

1. Tornare all'insieme di credenziali di **Site Recovery,** selezionare **Elementi replicati**e selezionare la macchina virtuale di SQL Server. 
1. Selezionare **Disabilita replica**. Selezionare un motivo per disabilitare la protezione e quindi scegliere **OK** per disabilitare la replica. 

   >[!IMPORTANT]
   > È importante eseguire questo passaggio per evitare di ricevere addebiti per la replica di Azure Site Recovery.It is important to perform this step to avoid being charged for Azure Site Recovery replication. 

1. Se non si prevede di riutilizzare le risorse nell'area di origine, eliminare tutte le risorse di rete pertinenti e gli account di archiviazione corrispondenti. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale WindowsOverview of SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server sulle domande frequenti di una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida per la determinazione dei prezzi di SQL Server in una macchina virtuale WindowsSQL Server on a Windows VM pricing guidance](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server sulle note sulla versione della macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


