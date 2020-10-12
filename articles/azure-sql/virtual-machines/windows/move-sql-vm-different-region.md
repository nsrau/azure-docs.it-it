---
title: Spostare una macchina virtuale in un'altra area (Azure Site Recovery)
description: Informazioni su come eseguire la migrazione di una macchina virtuale di SQL Server da un'area a un'altra in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4252528020dde731dd7bf14ae8f7a03467ba953a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91298577"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>Spostare una macchina virtuale SQL Server in un'altra area all'interno di Azure con Azure Site Recovery
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo illustra come usare Azure Site Recovery per eseguire la migrazione di una macchina virtuale (VM) di SQL Server da un'area a un'altra in Azure. 

Lo spostamento di una VM di SQL Server in un'altra area richiede le operazioni seguenti:
1. [Preparazione](#prepare-to-move): verificare che sia la VM di SQL Server di origine sia l'area di destinazione siano adeguatamente preparate per lo spostamento. 
1. [Configurazione](#configure-azure-site-recovery-vault): Lo spostamento della VM di SQL Server richiede che sia un oggetto replicato all'interno dell'insieme di credenziali di Azure Site Recovery. È necessario aggiungere la VM di SQL Server all'insieme di credenziali di Azure Site Recovery. 
1. [Test](#test-move-process): per eseguire la migrazione della VM di SQL Server è necessario eseguirne il failover dall'area di origine nell'area di destinazione replicata. Per assicurarsi che il processo di spostamento abbia esito positivo, è prima di tutto necessario verificare che la macchina virtuale SQL Server riesca a eseguire correttamente il failover nell'area di destinazione. Questo consente di esporre eventuali problemi e di evitarli quando si esegue lo spostamento effettivo. 
1. [Spostamento](#move-the-sql-server-vm): superato il failover di test, se si è certi di poter eseguire la migrazione della VM di SQL Server, è possibile eseguire lo spostamento della VM nell'area di destinazione. 
1. [Pulizia](#clean-up-source-resources): per evitare addebiti nella fatturazione, rimuovere dall'insieme di credenziali la VM di SQL Server e tutte le risorse non necessarie rimaste nel gruppo di risorse. 

## <a name="verify-prerequisites"></a>Verificare i prerequisiti 

- Verificare che lo spostamento dall'area di origine all'area di destinazione [sia supportato](../../../site-recovery/azure-to-azure-support-matrix.md#region-support).  
- Esaminare l'[architettura e i componenti dello scenario](../../../site-recovery/azure-to-azure-architecture.md), nonché le [limitazioni e i requisiti relativi all'assistenza](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Verificare le autorizzazioni dell'account. Se si è creato un account Azure gratuito, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una VM e copiare i dati usando Azure Site Recovery, è necessario quanto segue: 
    - Autorizzazioni per creare una VM. Il ruolo predefinito *Collaboratore Macchina virtuale* dispone di queste autorizzazioni, che includono: 
        - Autorizzazioni per creare una VM nel gruppo di risorse selezionato. 
        - Autorizzazioni per creare una VM nella rete virtuale selezionata. 
        - Autorizzazioni per scrivere nell'account di archiviazione selezionato. 
      - Autorizzazione per la gestione delle operazioni di Azure Site Recovery. Il ruolo *Collaboratore di Site Recovery* ha tutte le autorizzazioni necessarie per gestire le operazioni di Site Recovery in un insieme di credenziali di Servizi di ripristino.  

## <a name="prepare-to-move"></a>Preparare lo spostamento
Preparare sia la VM di SQL Server di origine, sia l'area di destinazione per lo spostamento. 

### <a name="prepare-the-source-sql-server-vm"></a>Preparare la VM di SQL Server di origine

- Verificare che nella VM di SQL Server da spostare siano presenti tutti i certificati radice più recenti. In caso contrario, i vincoli di sicurezza impediranno la copia dei dati nell'area di destinazione. 
- Per le macchine virtuali di Windows, installare tutti gli aggiornamenti di Windows più recenti nella VM in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente disconnesso, seguire le Windows Update standard e il processo di aggiornamento del certificato per l'organizzazione. 
- Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella macchina virtuale. 
- Assicurarsi di non usare un proxy di autenticazione per controllare la connettività di rete per le VM da spostare. 
- Se la VM che si intende spostare non ha accesso a Internet o usa un proxy firewall per controllare l'accesso in uscita, verificare i requisiti. 
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, tra cui i servizi di bilanciamenti del carico, i gruppi di sicurezza di rete e gli indirizzi IP pubblici. 

### <a name="prepare-the-target-region"></a>Preparare l'area di destinazione

- Verificare che la sottoscrizione di Azure consenta di creare VM nell'area di destinazione usata per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria. 
- Verificare che la sottoscrizione abbia risorse sufficienti per supportare VM di dimensioni corrispondenti alle VM di origine. Se si usa Site Recovery per copiare i dati nella destinazione, Site Recovery sceglie per la VM di destinazione la stessa dimensione o la dimensione più vicina possibile. 
- Assicurarsi di creare una risorsa di destinazione per ogni componente identificato nel layout di rete di origine. Questo passaggio è importante affinché le VM nell'area di destinazione abbiano tutte le caratteristiche e le funzionalità disponibili nell'area di origine. 
    - Azure Site Recovery individua e crea automaticamente una rete virtuale quando si abilita la replica per la VM di origine. È anche possibile creare preventivamente una rete e assegnarla alla VM nel flusso utente per abilitare la replica. Tutte le altre risorse devono essere create manualmente nell'area di destinazione.
- Per creare le risorse di rete usate più di frequente che sono pertinenti per il caso specifico in base alla configurazione della VM di origine, vedere la documentazione seguente: 
    - [Gruppi di sicurezza di rete](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Bilanciamento del carico](../../../load-balancer/tutorial-load-balancer-standard-internal-portal.md)
    - [Indirizzo IP pubblico](../../../virtual-network/virtual-network-public-ip-address.md)
    - Per gli eventuali componenti di rete aggiuntivi, vedere la [documentazione relativa alle reti](../../../virtual-network/virtual-networks-overview.md).
- Se si vuole testare la configurazione prima dello spostamento finale nell'area di destinazione, creare manualmente una rete non di produzione nell'area di destinazione. Questo passaggio è consigliato perché garantisce un'interferenza minima con la rete di produzione. 

## <a name="configure-azure-site-recovery-vault"></a>Configurare l'insieme di credenziali di Azure Site Recovery

La procedura seguente illustra come usare Azure Site Recovery per copiare i dati nell'area di destinazione. Creare l'insieme di credenziali di Servizi di ripristino in qualsiasi area che non sia quella di origine. 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Scegliere di **creare una risorsa** dall'angolo superiore sinistro del riquadro di spostamento. 
1. Selezionare **IT e strumenti di gestione** e quindi **Backup e Site Recovery**. 
1. Nella scheda **Informazioni di base**, in **Dettagli del progetto** creare un nuovo gruppo di risorse nell'area di destinazione oppure selezionare un gruppo di risorse esistente nell'area di destinazione. 
1. In **Dettagli istanza** specificare un nome per l'insieme di credenziali e quindi selezionare l'**area** di destinazione dall'elenco a discesa. 
1. Fare clic su **Rivedi e crea** per creare l'insieme di credenziali di Servizi di ripristino. 
1. Selezionare **Tutti i servizi** nell'angolo superiore sinistro del riquadro di spostamento e nella casella di ricerca digitare `recovery services`. 
1. (Facoltativo) Selezionare la stella accanto a **insiemi di credenziali di Servizi di ripristino** per aggiungere l'opzione alla barra di spostamento rapido. 
1. Selezionare **Insiemi di credenziali di Servizi di ripristino** quindi selezionare l'insieme di credenziali di Servizi di ripristino creato. 
1. Nel riquadro **Panoramica** selezionare **Replica**. 

   ![Configurare la replica](./media/move-sql-vm-different-region/configure-replication.png)

1. Selezionare **Origine**, quindi selezionare **Azure** come origine. Selezionare i valori appropriati per gli altri campi a discesa, ad esempio il percorso per le macchine virtuali di origine. Solo i gruppi di risorse che si trovano nell'area **Percorso di origine** saranno visibili nel campo **Gruppo di risorse di origine**. 
1. Selezionare **Macchine virtuali** e quindi scegliere le macchine virtuali di cui eseguire la migrazione. Selezionare **OK** per salvare la selezione di VM. 
1. Selezionare **Impostazioni**, quindi scegliere il **percorso di destinazione** dall'elenco a discesa. Deve essere il gruppo di risorse preparato in precedenza. 
1. Dopo aver personalizzato la replica, selezionare **Crea risorse di destinazione** per creare le risorse nel nuovo percorso. 
1. Completata la creazione delle risorse, selezionare **Abilita replica** per avviare la replica della VM di SQL Server dall'area di origine a quella di destinazione.
1. È possibile controllare lo stato della replica passando all'insieme di credenziali di ripristino, selezionando **Elementi replicati** e visualizzando lo **stato** della VM di SQL Server. Lo stato **Protetto** indica che la replica è stata completata. 

   ![Verificare lo stato della replica](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>Testare il processo di spostamento
La procedura seguente illustra come usare Azure Site Recovery per testare il processo di spostamento. 

1. Passare all'**insieme di credenziali di Servizi di ripristino** nel [portale di Azure](https://portal.azure.com) e selezionare **Elementi replicati**. 
1. Selezionare la VM di SQL Server da spostare, verificare che in **Integrità della replica** sia indicato **Integra** e quindi selezionare **Failover di test**. 

   ![Testare il failover per la macchina virtuale](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. Nella pagina **Failover di test** selezionare il punto di ripristino **coerente con l'app più recente** per usarlo per il failover, poiché si tratta dell'unico tipo di snapshot che può garantire la coerenza dei dati di SQL Server. 
1. Selezionare la rete virtuale in **Rete virtuale di Azure** e quindi selezionare **OK** per testare il failover. 
   
   >[!IMPORTANT]
   > Per testare il failover è consigliabile usare una rete di macchine virtuali di Azure separata. Non usare la rete di produzione che è stata configurata quando è stata abilitata la replica e in cui si vogliono spostare infine le VM. 

1. Per monitorare lo stato di avanzamento, passare all'insieme di credenziali, selezionare **Processi di Site Recovery** in **Monitoraggio**, quindi selezionare il processo di **failover di test** in corso.

   ![Monitorare lo stato di avanzamento del test di failover](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. Al termine del test, passare a **Macchine virtuali** nel portale ed esaminare la macchina virtuale appena creata. Verificare che la VM di SQL Server sia in esecuzione, sia di dimensioni appropriate e sia connessa alla rete corretta. 
1. Eliminare la VM creata come parte del test, poiché l'opzione **Failover** sarà disabilitata finché non viene eseguita la pulizia delle risorse di test del failover. Tornare all'insieme di credenziali, selezionare **Elementi replicati**, selezionare la VM di SQL Server e quindi selezionare **Pulisci failover di test**. Registrare e salvare eventuali osservazioni associate al test nella sezione **Note** e selezionare la casella di controllo accanto a **Il test è stato completato. Eliminare le macchine virtuali del failover di test**. Selezionare **OK** per pulire le risorse dopo il test. 

   ![Pulire gli elementi dopo il test di failover](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Spostare la VM di SQL Server 
I passaggi seguenti illustrano come spostare la VM di SQL Server dall'area di origine all'area di destinazione. 

1. Passare all'insieme di credenziali di **Servizi di ripristino**, selezionare **Elementi replicati**, selezionare la VM e quindi selezionare **Failover**. 

   ![Avviare il failover](./media/move-sql-vm-different-region/initiate-failover.png)

1. Selezionare il punto di ripristino **coerente con l'app più recente** in **Punto di ripristino**. 
1. Selezionare la casella di controllo accanto a **Arresta il computer prima di avviare il failover**. Site Recovery tenta di arrestare la VM di origine prima di attivare il failover. Il failover continuerà anche se l'arresto ha esito negativo. 
1. Selezionare **OK** per avviare il failover.
1. È possibile monitorare il processo di failover dalla stessa pagina **Processi di Site Recovery** visualizzata durante il monitoraggio del test di failover nella sezione precedente. 
1. Al termine del processo, verificare che la VM di SQL Server si trovi nell'area di destinazione come previsto. 
1. Tornare all'insieme di credenziali, selezionare **Elementi replicati**, selezionare la VM di SQL Server e selezionare **Commit** per terminare il processo di spostamento nell'area di destinazione. Attendere il completamento del processo di commit. 
1. Registrare la VM di SQL Server con il provider di risorse per VM SQL per abilitare la gestibilità delle **macchine virtuali SQL** nel portale di Azure e le funzionalità associate al provider di risorse. Per altre informazioni, vedere [registrare SQL Server VM con il provider di risorse della macchina virtuale SQL](sql-vm-resource-provider-register.md). 

  > [!WARNING]
  > La coerenza dei dati di SQL Server è garantita solo con snapshot coerenti con l'app. Non è possibile usare lo snapshot **elaborato più recente** per il failover di SQL Server perché uno snapshot per il ripristino a seguito dell'arresto anomalo del sistema non può garantire la coerenza dei dati di SQL Server. 

## <a name="clean-up-source-resources"></a>Pulire le risorse di origine
Per evitare addebiti nella fatturazione, rimuovere dall'insieme di credenziali la VM di SQL Server ed eliminare eventuali risorse associate non necessarie. 

1. Tornare all'insieme di credenziali di **Site Recovery**, selezionare **Elementi replicati** e selezionare la VM di SQL Server. 
1. Selezionare **Disabilita replica**. Selezionare un motivo per disabilitare la protezione, quindi selezionare **OK** per disabilitare la replica. 

   >[!IMPORTANT]
   > È importante eseguire questo passaggio per evitare addebiti per la replica di Azure Site Recovery. 

1. Se non si prevede di usare di nuovo le risorse nell'area di origine, eliminare tutte le risorse di rete rilevanti e gli account di archiviazione corrispondenti. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server sulle domande frequenti di una macchina virtuale Windows](frequently-asked-questions-faq.md)
* [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).
* [SQL Server sulle note sulla versione della macchina virtuale Windows](doc-changes-updates-release-notes.md)


