---
title: Spostare VM IaaS di Azure in un'altra area di Azure con il servizio Azure Site Recovery | Microsoft Docs
description: Usare Site Recovery per lo spostamento di macchine virtuali IaaS di Azure da un'area di Azure a un'altra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 7619b8831d75ce639c6f6c773c7c7d491abc93e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122030"
---
# <a name="move-azure-vms-to-another-region"></a>Spostare macchine virtuali di Azure in un'altra area

Esistono vari scenari in cui può essere opportuno spostare le macchine virtuali (VM) IaaS di Azure esistenti da un'area a un'altra, ad esempio per aumentare l'affidabilità e la disponibilità delle VM esistenti, per migliorare la gestibilità oppure per motivi di governance. Per altre informazioni, vedere la [panoramica dello spostamento di VM di Azure](azure-to-azure-move-overview.md). 

È possibile usare il servizio [Azure Site Recovery](site-recovery-overview.md) per gestire e orchestrare il ripristino di emergenza di computer locali e VM di Azure ai fini della continuità aziendale e del ripristino di emergenza, nonché per gestire lo spostamento di VM di Azure in un'area secondaria.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> 
> * Verificare i prerequisiti per lo spostamento
> * Preparare le VM di origine e l'area di destinazione
> * Copiare i dati e abilitare la replica
> * Testare la configurazione ed eseguire lo spostamento
> * Eliminare le risorse nell'area di origine
> 
> [!NOTE]
> Questa esercitazione illustra come spostare VM di Azure da un'area a un'altra così come sono. Se è necessario migliorare la disponibilità spostando le VM di un set di disponibilità in VM aggiunte a una zona in un'altra area, vedere l'esercitazione [Spostare macchine virtuali di Azure nelle zone di disponibilità](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Prerequisiti

- Verificare che le VM di Azure si trovino nell'area di Azure da cui si vuole eseguire lo spostamento.
- Verificare che la [combinazione di area di origine-area di destinazione scelta sia supportata](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) e prendere una decisione basata su informazioni aggiornate in merito all'area di destinazione.
- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](azure-to-azure-architecture.md).
- Rivedere le [limitazioni e i requisiti del supporto](azure-to-azure-support-matrix.md).
- Verificare le autorizzazioni dell'account. Se si è creato un account Azure gratuito, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una VM ed essenzialmente copiare i dati con Azure Site Recovery, è necessario quanto segue:

    * Autorizzazioni per creare una macchina virtuale nelle risorse di Azure. Il ruolo predefinito Collaboratore Macchina virtuale ha tali autorizzazioni, che includono:
        - Autorizzazione per la creazione di una macchina virtuale nel gruppo di risorse selezionato
        - Autorizzazione per la creazione di una macchina virtuale nella rete virtuale selezionata
        - Autorizzazione per la scrittura nell'account di archiviazione selezionato

    * Autorizzazione per la gestione delle operazioni di Azure Site Recovery. Il ruolo Collaboratore di Site Recovery ha tutte le autorizzazioni necessarie per gestire le operazioni di Site Recovery in un insieme di credenziali di Servizi di ripristino.

## <a name="prepare-the-source-vms"></a>Preparare le VM di origine

1. Verificare che nelle VM di Azure da spostare siano presenti tutti i certificati radice più recenti. In caso contrario, i vincoli di sicurezza impediranno la copia dei dati nell'area di destinazione.

    - Per le macchine virtuali di Windows, installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente non connesso, seguire i processi di aggiornamento di Windows e di aggiornamento dei certificati standard per l'organizzazione.
    - Per le VM Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella VM.
1. Assicurarsi di non usare un proxy di autenticazione per controllare la connettività di rete per le VM da spostare.
1. Se la VM che si intende spostare non ha accesso a Internet o usa un proxy firewall per controllare l'accesso in uscita, [controllare i requisiti](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
1. Identificare il layout di rete di origine e tutte le risorse attualmente in uso, tra cui i servizi di bilanciamenti del carico, i gruppi di sicurezza di rete e gli indirizzi IP pubblici.

## <a name="prepare-the-target-region"></a>Preparare l'area di destinazione

1. Verificare che la sottoscrizione di Azure consenta di creare VM nell'area di destinazione usata per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria.

1. Verificare che la sottoscrizione abbia risorse sufficienti per supportare VM di dimensioni corrispondenti alle VM di origine. Se si usa Site Recovery per copiare i dati nella destinazione, Site Recovery sceglie per la VM di destinazione la stessa dimensione o la dimensione più vicina possibile.

1. Assicurarsi di creare una risorsa di destinazione per ogni componente identificato nel layout di rete di origine. Questo passaggio è importante affinché le VM nell'area di destinazione abbiano tutte le caratteristiche e le funzionalità disponibili nell'area di origine.

    > [!NOTE]
    > Azure Site Recovery individua e crea automaticamente una rete virtuale quando si abilita la replica per la VM di origine. È anche possibile creare preventivamente una rete e assegnarla alla VM nel flusso utente per l'abilitazione della replica. Tutte le altre risorse devono essere create manualmente nell'area di destinazione, come indicato più avanti.

     Per creare le risorse di rete usate più di frequente che sono pertinenti per il caso specifico in base alla configurazione della VM di origine, vedere la documentazione seguente:

   - [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Servizi di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [IP pubblico](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
     Per qualsiasi altro componente di rete, vedere la [documentazione relativa alle reti](https://docs.microsoft.com/azure/#pivot=products&panel=network).

1. Se si vuole testare la configurazione prima dello spostamento finale nell'area di destinazione, [creare manualmente una rete non di produzione](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) nell'area di destinazione. Questo passaggio è consigliato perché garantisce un'interferenza minima con la rete di produzione.

## <a name="copy-data-to-the-target-region"></a>Copiare i dati nell'area di destinazione
La procedura seguente illustra come usare Azure Site Recovery per copiare i dati nell'area di destinazione.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Creare l'insieme di credenziali in qualsiasi area tranne quella di origine

1. Accedere a [portale di Azure](https://portal.azure.com) > **Servizi di ripristino**.
1. Selezionare **Crea una risorsa** > **Strumenti di gestione** > **Backup e Site Recovery (OMS)**.
1. In **Nome** specificare il nome descrittivo **ContosoVMVault**. Se è disponibile più di una sottoscrizione, selezionare quella appropriata.
1. Creare il gruppo di risorse **ContosoRG**.
1. Specificare un'area di Azure. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. In **Insiemi di credenziali dei servizi di ripristino** selezionare **Panoramica** > **ContosoVMVault** > **+Replica**.
1. In **Source** (Origine) selezionare **Azure**.
1. In **Percorso di origine** selezionare l'area di Azure di origine in cui le VM sono attualmente in esecuzione.
1. Selezionare il modello di distribuzione Resource Manager. Selezionare quindi la **sottoscrizione di origine** e il **gruppo di risorse di origine**.
1. Scegliere **OK** per salvare le impostazioni.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Abilitare la replica per le VM di Azure e avviare la copia dei dati

Site Recovery recupera un elenco delle VM associate alla sottoscrizione e al gruppo di risorse.

1. Nel passaggio successivo selezionare la VM da spostare e quindi **OK**.
1. In **Impostazioni** selezionare **Ripristino di emergenza**.
1. In **Configura ripristino di emergenza** >  **Area di destinazione** selezionare l'area di destinazione in cui si eseguirà la replica.
1. Per questa esercitazione accettare le altre impostazioni predefinite.
1. Selezionare **Abilita replica**. Questo passaggio avvia un processo per abilitare la replica per la VM.

    ![Abilitare la replica](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="test-the-configuration"></a>Testare la configurazione

1. Passare all'insieme di credenziali. In **Impostazioni** > **Elementi replicati** selezionare la VM da spostare nell'area di destinazione e quindi **+Failover di test**.
1. In **Failover di test** selezionare un punto di ripristino da usare per il failover:

   - **Elaborato più recente**: viene eseguito il failover della macchina virtuale al punto di recupero più recente elaborato dal servizio Site Recovery. Viene visualizzato il timestamp. Con questa opzione non viene impiegato tempo per l'elaborazione dei dati e si ottiene quindi un valore RTO (Recovery Time Objective) basso.
   - **Coerente con l'app più recente**: questa opzione esegue il failover di tutte le macchine virtuali al più recente punto di recupero coerente con l'app. Viene visualizzato il timestamp.
   - **Personalizzato**: selezionare qualsiasi punto di recupero.

1. Selezionare la rete virtuale di Azure di destinazione in cui si vogliono spostare le VM di Azure per testare la configurazione.
    > [!IMPORTANT]
    > Per il failover di test è consigliabile usare una rete di macchine virtuali di Azure separata. Non usare la rete di produzione che è stata configurata quando è stata abilitata la replica e in cui si vogliono spostare infine le VM.
1. Per avviare il test dello spostamento, fare clic su **OK**. Per verificare lo stato dell'operazione, fare clic sulla VM per visualizzare le rispettive proprietà. In alternativa, è possibile fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali, quindi su **Impostazioni** > **Processi** > **Site Recovery jobs** (Processi di Site Recovery).
1. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la VM sia in esecuzione, che sia di dimensioni appropriate e che sia connessa alla rete corretta.
1. Se si vuole eliminare la VM creata nell'ambito del test dello spostamento, fare clic su **Pulisci failover di test** per l'elemento replicato. In **Note** registrare e salvare eventuali osservazioni associate al test.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Eseguire lo spostamento nell'area di destinazione e confermare

1. Passare all'insieme di credenziali. In **Impostazioni** > **Elementi replicati** selezionare la VM e quindi **Failover**.
1. In **Failover** selezionare **Più recente**.
1. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
1. Al termine del processo, controllare che la VM si trovi nell'area di Azure di destinazione come previsto.
1. In **Elementi replicati** fare clic con il pulsante destro del mouse sulla VM e quindi scegliere **Esegui commit**. Questo passaggio completa il processo di spostamento nell'area di destinazione. Attendere il completamento del processo di commit.

## <a name="delete-the-resource-in-the-source-region"></a>Eliminare la risorsa nell'area di origine

Passare alla VM. Selezionare **Disabilita replica**. Questo passaggio arresta il processo di copia dei dati per la VM.

> [!IMPORTANT]
> È importante eseguire questo passaggio per evitare addebiti per la replica di Azure Site Recovery.

Se non si prevede di riusare alcuna risorsa di origine, seguire questa procedura:

1. Eliminare tutte le risorse di rete pertinenti dell'area di origine annotate nel passaggio 4 di [Preparare le VM di origine](#prepare-the-source-vms).
1. Eliminare l'account di archiviazione corrispondente nell'area di origine.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata spostata una VM di Azure in un'area diversa di Azure. È ora possibile configurare il ripristino di emergenza per la VM spostata.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza dopo la migrazione](azure-to-azure-quickstart.md)

