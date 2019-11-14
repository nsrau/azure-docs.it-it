---
title: Spostare una macchina virtuale in un'altra area (Azure Site Recovery)
description: Informazioni su come eseguire la migrazione della macchina virtuale SQL Server da un'area a un'altra in Azure.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022298"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Spostare SQL Server VM in un'altra area all'interno di Azure con Azure Site Recovery Services

Questo articolo illustra come usare Azure Site Recovery per eseguire la migrazione di una macchina virtuale SQL Server da un'area a un'altra in Azure. 

Per lo trasferimento di una macchina virtuale SQL Server in un'area diversa è necessario eseguire le operazioni seguenti:
1. [**Preparazione**](#prepare-to-move): verificare che sia la macchina virtuale SQL Server di origine che l'area di destinazione siano adeguatamente preparate per lo spostamento. 
1. [**Configurazione**](#configure-azure-site-recovery-vault): lo stato di trasferimento della macchina virtuale SQL Server richiede che si tratta di un oggetto replicato all'interno dell'insieme di credenziali Azure Site Recovery. È necessario aggiungere la macchina virtuale SQL Server all'insieme di credenziali Azure Site Recovery. 
1. [**Test**](#test-move-process): la migrazione della macchina virtuale SQL Server richiede il failover dall'area di origine all'area di destinazione replicata. Per assicurarsi che il processo di spostamento abbia esito positivo, è prima di tutto necessario verificare che la macchina virtuale SQL Server possa eseguire correttamente il failover nell'area di destinazione. Questo consente di esporre eventuali problemi ed evitarli quando si esegue lo spostamento effettivo. 
1. [**Spostamento**](#move-the-sql-server-vm): dopo che il failover di test è stato superato e si è sicuri di eseguire la migrazione della macchina virtuale SQL Server, è possibile eseguire lo spostamento della macchina virtuale nell'area di destinazione. 
1. [**Pulizia**](#clean-up-source-resources): per evitare addebiti per la fatturazione, rimuovere la macchina virtuale SQL Server dall'insieme di credenziali e tutte le risorse non necessarie rimaste nel gruppo di risorse. 

## <a name="verify-prerequisites"></a>Verificare i prerequisiti 

- Verificare che lo stato di trasferimento dall'area di origine all'area di destinazione [sia supportato](../../../site-recovery/azure-to-azure-support-matrix.md#region-support).  
- Esaminare l' [architettura e i componenti dello scenario](../../../site-recovery/azure-to-azure-architecture.md) , nonché le [limitazioni e i requisiti del supporto](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Verificare le autorizzazioni dell'account. Se si è creato un account Azure gratuito, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una macchina virtuale e copiare i dati usando Azure Site Recovery, è necessario disporre di: 
    - Autorizzazioni per la creazione di una macchina virtuale. Il ruolo predefinito *Collaboratore Macchina virtuale* dispone di queste autorizzazioni, che includono: 
        - Autorizzazioni per creare una macchina virtuale nel gruppo di risorse selezionato. 
        - Autorizzazioni per la creazione di una macchina virtuale nella rete virtuale selezionata. 
        - Autorizzazioni per la scrittura nell'account di archiviazione selezionato. 
      - Autorizzazione per la gestione delle operazioni di Azure Site Recovery. Il ruolo *collaboratore Site Recovery* dispone di tutte le autorizzazioni necessarie per gestire le operazioni di Site Recovery in un insieme di credenziali di servizi di ripristino.  

## <a name="prepare-to-move"></a>Preparare lo spostamento
Preparare la macchina virtuale di origine SQL Server e l'area di destinazione per lo spostamento. 

### <a name="prepare-the-source-sql-server-vm"></a>Preparare l'origine SQL Server VM

- Verificare che tutti i certificati radice più recenti si trovino nella macchina virtuale SQL Server che si desidera spostare. Se i certificati radice più recenti non sono presenti, i vincoli di sicurezza impediranno la copia dei dati nell'area di destinazione. 
- Per le macchine virtuali Windows, installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale, in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente disconnesso, seguire il processo di aggiornamento standard di Windows e del certificato per l'organizzazione. 
- Per le VM Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella VM. 
- Assicurarsi che non si stia usando un proxy di autenticazione per controllare la connettività di rete per le macchine virtuali che si desidera spostare. 
- Se la macchina virtuale che si sta tentando di spostare non ha accesso a Internet o se usa un proxy firewall per controllare l'accesso in uscita, verificare i requisiti. 
- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, tra cui i servizi di bilanciamenti del carico, i gruppi di sicurezza di rete e gli indirizzi IP pubblici. 

### <a name="prepare-the-target-region"></a>Preparare l'area di destinazione

- Verificare che la sottoscrizione di Azure consenta di creare VM nell'area di destinazione usata per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria. 
- Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare le macchine virtuali con dimensioni corrispondenti alle VM di origine. Se si usa Site Recovery per copiare i dati nella destinazione, Site Recovery sceglie le stesse dimensioni o le dimensioni più vicine possibili per la macchina virtuale di destinazione. 
- Assicurarsi di creare una risorsa di destinazione per ogni componente identificato nel layout di rete di origine. Questo passaggio è importante affinché le VM nell'area di destinazione abbiano tutte le caratteristiche e le funzionalità disponibili nell'area di origine. 
    - Azure Site Recovery individua e crea automaticamente una rete virtuale quando si abilita la replica per la VM di origine. È anche possibile pre-creare una rete e assegnarla alla macchina virtuale nel flusso utente per abilitare la replica. È necessario creare manualmente altre risorse nell'area di destinazione.
- Per creare le risorse di rete usate più di frequente che sono pertinenti per il caso specifico in base alla configurazione della VM di origine, vedere la documentazione seguente: 
    - [Gruppi di sicurezza di rete](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Bilanciamento del carico](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Indirizzo IP pubblico](../../../virtual-network/virtual-network-public-ip-address.md)
    - Per tutti i componenti di rete aggiuntivi, vedere la [documentazione relativa alla rete](../../../virtual-network/virtual-networks-overview.md).
- Creare manualmente una rete non di produzione nell'area di destinazione se si vuole testare la configurazione prima di eseguire lo spostamento finale nell'area di destinazione. Questo passaggio è consigliato perché garantisce un'interferenza minima con la rete di produzione. 

## <a name="configure-azure-site-recovery-vault"></a>Configurare Azure Site Recovery insieme di credenziali

La procedura seguente illustra come usare Azure Site Recovery per copiare i dati nell'area di destinazione. Creare l'insieme di credenziali di servizi di ripristino in qualsiasi area diversa dall'area di origine. 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Scegliere di **creare una risorsa** nell'angolo superiore sinistro del riquadro di spostamento. 
1. Selezionarlo **& strumenti di gestione** , quindi selezionare **backup e Site Recovery**. 
1. Nella scheda **nozioni di base** , in **Dettagli progetto**, creare un nuovo gruppo di risorse nell'area di destinazione oppure selezionare un gruppo di risorse esistente nell'area di destinazione. 
1. In **Dettagli istanza**specificare un nome per l'insieme di credenziali e quindi selezionare l' **area** di destinazione dall'elenco a discesa. 
1. Selezionare **Verifica + crea** per creare l'insieme di credenziali di servizi di ripristino. 
1. Selezionare **tutti i servizi** nell'angolo superiore sinistro del riquadro di spostamento e nella casella di ricerca digitare `recovery services`. 
1. Facoltativamente Selezionare la stella accanto a insiemi di credenziali **dei servizi di ripristino** per aggiungerla alla barra di spostamento rapida. 
1. Selezionare insiemi di credenziali **dei servizi di ripristino** e quindi selezionare l'insieme di credenziali di servizi di ripristino creato. 
1. Nel riquadro **Panoramica** selezionare **replica**. 

   ![Configurare la replica](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. Selezionare **source (origine** ) e quindi selezionare **Azure** come origine. Selezionare i valori appropriati per gli altri campi a discesa, ad esempio il percorso per le macchine virtuali di origine. Solo i gruppi di risorse che si trovano nell'area del **percorso di origine** saranno visibili nel campo gruppo di **risorse di origine** . 
1. Selezionare **macchine virtuali** e quindi scegliere le macchine virtuali di cui si vuole eseguire la migrazione. Selezionare **OK** per salvare la selezione della macchina virtuale. 
1. Selezionare **Impostazioni**, quindi scegliere il **percorso di destinazione** dall'elenco a discesa. Deve corrispondere al gruppo di risorse preparato in precedenza. 
1. Dopo aver personalizzato la replica, selezionare **crea risorse di destinazione** per creare le risorse nella nuova posizione. 
1. Al termine della creazione della risorsa, selezionare **Abilita la replica** per avviare la replica della macchina virtuale SQL Server dall'origine all'area di destinazione.
1. È possibile controllare lo stato della replica passando all'insieme di credenziali di ripristino, selezionando **gli elementi replicati** e visualizzando lo **stato** della macchina virtuale SQL Server. Uno stato **protetto** indica che la replica è stata completata. 

   ![Verificare lo stato della replica](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>Processo di spostamento test
I passaggi seguenti illustrano come usare Azure Site Recovery per testare il processo di spostamento. 

1. Passare all'insieme di credenziali di **servizi di ripristino** nel [portale di Azure](https://portal.azure.com) e selezionare **elementi replicati**. 
1. Selezionare la macchina virtuale SQL Server che si desidera spostare, verificare che lo **stato di replica** sia **integro** e quindi selezionare **failover di test**. 

   ![Failover di test per la macchina virtuale](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. Nella pagina **failover di test** selezionare il punto di ripristino coerente con l' **app più recente** da usare per il failover, poiché si tratta dell'unico tipo di snapshot che può garantire la coerenza dei dati SQL Server. 
1. Selezionare la rete virtuale in **rete virtuale di Azure** e quindi fare clic su **OK** per testare il failover. 
   
   >[!IMPORTANT]
   > Si consiglia di usare una rete VM di Azure separata per il test di failover. Non usare la rete di produzione che è stata configurata quando è stata abilitata la replica e in cui si vogliono spostare infine le VM. 

1. Per monitorare lo stato di avanzamento, passare all'insieme di credenziali, selezionare **Site Recovery processi** in **monitoraggio**e quindi selezionare il processo di **failover di test** in corso.

   ![Monitorare lo stato di avanzamento del test di failover](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. Al termine del test, passare a **macchine virtuali** nel portale ed esaminare la macchina virtuale appena creata. Assicurarsi che la macchina virtuale SQL Server sia in esecuzione, sia dimensionata in modo appropriato ed è connessa alla rete appropriata. 
1. Eliminare la macchina virtuale creata come parte del test, poiché l'opzione di **failover** verrà disattivata fino a quando non viene eseguita la pulizia delle risorse di test del failover. Tornare all'insieme di credenziali, selezionare gli **elementi replicati**, selezionare la macchina virtuale SQL Server, quindi selezionare **Cleanup test failover**. Registrare e salvare le eventuali osservazioni associate al test nella sezione **Note** , quindi selezionare la casella di controllo accanto a **test completata. Elimina le macchine virtuali del failover di test**. Selezionare **OK** per pulire le risorse dopo il test. 

   ![Pulisci elementi dopo il test di failover](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Spostare la macchina virtuale SQL Server 
I passaggi seguenti illustrano come spostare la macchina virtuale SQL Server dall'area di origine all'area di destinazione. 

1. Passare all'insieme di credenziali **dei servizi di ripristino** , selezionare **elementi replicati**, selezionare la macchina virtuale e quindi fare clic su **failover**. 

   ![Avviare il failover](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. Selezionare il punto di ripristino coerente con l' **app più recente** in **punto di ripristino**. 
1. Selezionare la casella di controllo accanto a **Arresta il computer prima di avviare il failover**. Site Recovery tenterà di arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continuerà anche se l'arresto non riesce. 
1. Selezionare **OK** per avviare il failover.
1. È possibile monitorare il processo di failover dalla stessa pagina dei **processi di Site Recovery** visualizzata durante il monitoraggio del test di failover nella sezione precedente. 
1. Al termine del processo, verificare che la macchina virtuale SQL Server venga visualizzata nell'area di destinazione come previsto. 
1. Tornare all'insieme di credenziali, selezionare **elementi replicati**, selezionare la macchina virtuale SQL Server e selezionare **commit** per completare il processo di spostamento nell'area di destinazione. Attendere il completamento del processo di commit. 
1. Registrare la macchina virtuale di SQL Server con il provider di risorse VM SQL per abilitare la gestibilità delle **macchine virtuali SQL** nella portale di Azure e le funzionalità associate al provider di risorse. Per altre informazioni, vedere [registrare SQL Server VM con il provider di risorse VM SQL](virtual-machines-windows-sql-register-with-rp.md). 

  > [!WARNING]
  > SQL Server la coerenza dei dati è garantita solo con snapshot coerenti con l'app. Non è possibile usare lo snapshot **elaborato più recente** per il failover SQL Server perché uno snapshot del ripristino di arresto anomalo non può garantire la coerenza dei dati SQL Server. 

## <a name="clean-up-source-resources"></a>Pulire le risorse di origine
Per evitare addebiti per la fatturazione, rimuovere la macchina virtuale SQL Server dall'insieme di credenziali ed eliminare tutte le risorse associate non necessarie. 

1. Tornare all'insieme di credenziali **Site Recovery** , selezionare **elementi replicati**e selezionare la macchina virtuale SQL Server. 
1. Selezionare **Disabilita replica**. Selezionare un motivo per disabilitare la protezione e quindi fare clic su **OK** per disabilitare la replica. 

   >[!IMPORTANT]
   > È importante eseguire questo passaggio per evitare l'addebito di Azure Site Recovery la replica. 

1. Se non si prevede di riutilizzare le risorse nell'area di origine, eliminare tutte le risorse di rete rilevanti e gli account di archiviazione corrispondenti. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server sulle domande frequenti di una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).
* [SQL Server sulle note sulla versione della macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


