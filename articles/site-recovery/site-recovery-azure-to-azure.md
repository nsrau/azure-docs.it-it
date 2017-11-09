---
title: 'Eseguire la replica di VM di Azure tra aree di Azure per esigenze di ripristino di emergenza: da Azure ad Azure| Microsoft Docs'
description: Riepiloga la procedura necessaria per la replica di VM di Azure tra aree di Azure (da Azure ad Azure) con il servizio Azure Site Recovery per esigenze di ripristino di emergenza.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: raynew
ms.openlocfilehash: 9ca33057f6030fdcc233f6053fdc392d62f8f9f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Eseguire la replica delle VM di Azure tra le aree con Azure Site Recovery

>[!NOTE]
>
> La replica di Azure Site Recovery per le macchine virtuali di Azure è attualmente in anteprima.

Questo articolo illustra come eseguire la replica delle VM di Azure tra aree di Azure mediante il servizio [Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum su Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="disaster-recovery-in-azure"></a>Ripristino di emergenza in Azure

Le funzionalità dell'infrastruttura e le funzionalità di Azure predefinite consentono di ottenere una strategia di disponibilità affidabile e resiliente per i carichi di lavoro in esecuzione nelle VM di Azure. È tuttavia necessario pianificare in modo autonomo il ripristino di emergenza tra aree di Azure per diversi motivi:

- È necessario soddisfare le indicazioni di conformità per app e carichi di lavoro specifici che richiedono una strategia BCDR (Business Continuity and Disaster Recovery).
- Si vogliono proteggere e ripristinare le VM di Azure in base alle decisioni aziendali specifiche e non solo in base alle funzionalità predefinite di Azure.
- È necessario testare il failover e il ripristino in base alle esigenze aziendali e di conformità, senza impatti sulla produzione.
- È necessario eseguire il failover nell'area di ripristino in caso di disastro ed eseguire il failback all'area di origine originale senza problemi.

Usare Site Recovery per la replica di VM da Azure ad Azure per semplificare l'esecuzione di tutte queste attività.


## <a name="why-use-site-recovery"></a>Perché usare Site Recovery?      

Site Recovery offre un modo semplice per eseguire la replica di VM di Azure tra le aree:

- **Distribuzione automatica**. A differenza di un modello di replica attivo-attivo, non è necessaria alcuna infrastruttura costosa e complessa nell'area secondaria. Quando si abilita la replica, Site Recovery crea automaticamente le risorse necessarie nell'area di destinazione, in base alle impostazioni dell'area di origine.
- **Aree di controllo**. Con Site Recovery è possibile eseguire la replica da qualsiasi area a qualsiasi area in un continente specifico. È possibile confrontare questo approccio con l'archiviazione con ridondanza geografica e accesso in lettura, che esegue la replica asincrona solo tra [aree abbinate](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). L'archiviazione con ridondanza geografica e accesso in lettura fornisce l'accesso di sola lettura ai dati nell'area di destinazione.
- **Replica automatica**. Site Recovery fornisce la replica continua automatica. È possibile attivare il failover e il failback con un singolo clic.
- **RTO e RPO**. Site Recovery sfrutta i vantaggi dell'infrastruttura di rete di Azure che connette le aree per mantenere valori di RTO e RPO molto bassi.
- **Test**. È possibile eseguire esercitazioni per il ripristino di emergenza con failover di test su richiesta, quando necessario, senza influire sui carichi di lavoro di produzione o sulla replica continua.
- **Piani di ripristino** È possibile usare i piani di ripristino per orchestrare il failover e il failback dell'intera applicazione in esecuzione in più VM. La funzionalità del piano di ripristino include l'integrazione di qualità elevata con i runbook di Automazione di Azure.


## <a name="deployment-summary"></a>Riepilogo della distribuzione

Ecco un riepilogo delle operazioni necessarie per la configurazione della replica di VM tra le aree di Azure:

1. Creare un insieme di credenziali dei servizi di ripristino. L'insieme di credenziali contiene le impostazioni di configurazione e orchestra la replica.
2. Abilitare la replica per le VM di Azure.
3. Eseguire un failover di test per verificare che tutti gli elementi funzionino come previsto.

>[!IMPORTANT]
>
> È possibile controllare la [matrice di supporto per la replica di VM di Azure](./site-recovery-support-matrix-azure-to-azure.md).

>[!IMPORTANT]
>
> Per informazioni su come configurare la connettività di rete in uscita necessaria per le VM di Azure per la replica con Site Recovery, vedere il [documento di indicazioni sulla rete](./site-recovery-azure-to-azure-networking-guidance.md).

### <a name="before-you-start"></a>Prima di iniziare

* L'account utente di Azure deve avere determinate [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) per abilitare la replica di una macchina virtuale di Azure.
* È necessario che la sottoscrizione di Azure sia abilitata per la creazione di VM nella posizione di destinazione da usare come area per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> È consigliabile creare l'insieme di credenziali dei servizi di ripristino nella posizione in cui si vuole che vengano replicate le VM. Ad esempio, se la posizione di destinazione è Stati Uniti centrali, creare l'insieme di credenziali in **Stati Uniti centrali**.

## <a name="enable-replication"></a>Abilitare la replica

In **Insiemi di credenziali dei servizi di ripristino** selezionare il nome dell'insieme di credenziali. Nell'insieme di credenziali fare clic sul pulsante **+Replica**.

### <a name="step-1-configure-the-source"></a>Passaggio 1. Configurare l'origine
1. In **Origine** selezionare **Azure - ANTEPRIMA**.
2. In **Percorso di origine** selezionare l'area di Azure di origine in cui le VM sono attualmente in esecuzione.
3. Selezionare il modello di distribuzione delle VM: **Resource Manager** o **Classica**.
4. Selezionare il **Source resource group** (Gruppo di risorse di origine) per VM di tipo Resource Manager o **Servizio cloud** per le VM classiche.

    ![Configurare l'origine](./media/site-recovery-azure-to-azure/source.png)

### <a name="step-2-select-virtual-machines"></a>Passaggio 2. Selezionare le macchine virtuali

* Selezionare le VM da replicare e quindi fare clic su **OK**.

    ![Selezionare le VM](./media/site-recovery-azure-to-azure/vms.png)

### <a name="step-3-configure-settings"></a>Passaggio 3. Configurare le impostazioni

1. Per eseguire l'override delle impostazioni di destinazione predefinite e specificare le impostazioni da usare, fare clic su **Personalizza**. Per altre informazioni, vedere [Customize target resources](site-recovery-replicate-azure-to-azure.md##customize-target-resources) (Personalizzare le risorse di destinazione).

    ![Configurare le impostazioni](./media/site-recovery-azure-to-azure/settings.png)


2. Per impostazione predefinita, Site Recovery crea un criterio di replica che crea snapshot coerenti con l'app ogni 4 ore e conserva i punti di ripristino per 24 ore. Per creare un criterio con impostazioni diverse, fare clic su **Personalizza** accanto a **Criteri di replica**.

    ![Personalizzare i criteri](./media/site-recovery-azure-to-azure/customize-policy.png)

3. Per avviare il provisioning delle risorse di destinazione, fare clic su **Create target resources** (Crea risorse di destinazione). Il provisioning richiede circa un minuto. Non chiudere il pannello durante il provisioning, altrimenti sarà necessario ripetere l'operazione.

4. Per attivare la replica della VM selezionata, fare clic su **Abilita replica**.

5. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**.

6. In **Impostazioni** > **Elementi replicati** è possibile visualizzare lo stato delle VM e lo stato della replica iniziale. Fare clic sulla VM per visualizzare nel dettaglio le rispettive impostazioni.

## <a name="run-a-test-failover"></a>Eseguire un failover di test

Dopo aver completato la configurazione, eseguire un failover di test per verificare che tutti gli elementi funzionino come previsto.

1. Per eseguire il failover di una singola macchina, in **Impostazioni** > **Elementi replicati** fare clic sulla VM e quindi sull'icona **+Failover di test**.

2. Per eseguire il failover di un piano di ripristino, in **Impostazioni** > **Piani di ripristino** fare clic con il pulsante destro del mouse sul piano e quindi scegliere **Failover di test**. Per creare un piano di ripristino, [seguire queste istruzioni](site-recovery-create-recovery-plans.md). 

3. In **Failover di test** selezionare la rete virtuale di Azure di destinazione a cui si connetteranno le VM di Azure dopo il failover.

4. Per avviare il failover, fare clic su **OK**. Per verificare lo stato dell'operazione, fare clic sulla VM per visualizzare le rispettive proprietà. In alternativa, è possibile fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali, quindi su **Impostazioni** > **Processi** > **Site Recovery jobs** (Processi di Site Recovery).

5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete giusta e che sia in esecuzione.

6. Per eliminare le VM create durante il failover di test, fare clic su **Cleanup test failover** (Pulizia failover di test) nell'elemento replicato o nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. 

[Altre informazioni](site-recovery-test-failover-to-azure.md) sui failover di test.


## <a name="next-steps"></a>Passaggi successivi

Al termine del test della distribuzione:

- Altre informazioni sui diversi tipi di failover e su come eseguirli sono disponibili [qui](site-recovery-failover.md).
- Altre informazioni sull'[uso dei piani di ripristino](site-recovery-create-recovery-plans.md) per la riduzione di RTO.
