---
title: Spostare macchine virtuali IaaS di Azure tra aree per enti pubblici e aree pubbliche con il servizio Azure Site Recovery | Microsoft Docs
description: Usare Azure Site Recovery per lo spostamento di macchine virtuali IaaS di Azure tra aree per enti pubblici e aree pubbliche.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: bff6268507c0d2ec0aa1eac0c7e2e9d2513ded58
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376132"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Spostare le VM di Azure tra aree per enti pubblici e aree pubbliche 

È possibile spostare le VM IaaS tra le aree per enti pubblici e le aree pubbliche di Azure per aumentare la disponibilità delle macchine virtuali esistenti, migliorare la gestibilità o ancora per motivi di governance, come descritto nel dettaglio [qui](azure-to-azure-move-overview.md).

Oltre a usare il servizio [Azure Site Recovery](site-recovery-overview.md) per gestire e orchestrare il ripristino di emergenza di computer locali e di macchine virtuali di Azure ai fini della continuità aziendale e del ripristino di emergenza (BCDR), è anche possibile usare Site Recovery per gestire lo spostamento di macchine virtuali di Azure in un'area secondaria.       

Questa esercitazione illustra come spostare VM di Azure tra aree per enti pubblici e aree pubbliche con Azure Site Recovery. La stessa procedura può essere utilizzata per spostare VM tra coppie di aree non presenti nello stesso cluster geografico. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Verificare i prerequisiti
> * Preparare le VM di origine
> * Preparare l'area di destinazione
> * Copiare i dati nell'area di destinazione
> * Testare la configurazione
> * Eseguire lo spostamento
> * Rimuovere le risorse dall'area di origine

> [!IMPORTANT]
> Questa esercitazione illustra come spostare macchine virtuali di Azure tra aree per enti pubblici e aree pubbliche o tra coppie di aree che non sono supportate dalla normale soluzione per il ripristino di emergenza per le VM di Azure. Nel caso in cui le coppie di aree di origine e destinazione siano [supportate](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), consultare questo [documento](azure-to-azure-tutorial-migrate.md) per lo spostamento. Se il requisito è migliorare la disponibilità spostando le VM di un set di disponibilità nelle VM aggiunte a una zona in un'area diversa, vedere questa [esercitazione](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Non è consigliabile usare questo metodo per configurare il ripristino di emergenza tra coppie di aree non supportate in quanto queste sono definite tenendo presente la latenza dei dati, di fondamentale importanza per uno scenario di ripristino di emergenza.

## <a name="verify-prerequisites"></a>Verificare i prerequisiti

> [!NOTE]
> Assicurarsi di aver compreso [l'architettura e i componenti](physical-azure-architecture.md) di questo scenario. Questa architettura verrà usata per spostare le macchine virtuali di Azure, **trattandole come server fisici**.

- Verificare i [requisiti di supporto](vmware-physical-secondary-support-matrix.md) per tutti i componenti.
- Assicurarsi che i server da replicare siano conformi ai [requisiti di Azure per le macchine virtuali](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Preparare un account per l'installazione automatica del servizio Mobility su ogni server da replicare.

- Si noti che dopo il failover nell'area di destinazione in Azure non sarà possibile effettuare direttamente un failback nell'area di origine. Sarà necessario configurare nuovamente la replica alla destinazione.

### <a name="verify-azure-account-permissions"></a>Verificare le autorizzazioni dell'account di Azure

Verificare che l'account di Azure disponga delle autorizzazioni per la replica di macchine virtuali in Azure.

- Rivedere le [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necessarie per la replica di macchine in Azure.
- Verificare e modificare le autorizzazioni per gli [accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md). 

### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Configurare una [Rete Azure](../virtual-network/quick-create-portal.md) di destinazione.

- Le VM di Azure create dopo il failover verranno inserite in questa rete.
- La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.


### <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure

Configurare un [account di archiviazione di Azure](../storage/common/storage-quickstart-create-account.md).

- Site Recovery replica le macchine locali in Archiviazione di Azure. Le VM di Azure vengono create dalla risorsa di archiviazione dopo il failover.
- L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino.


## <a name="prepare-the-source-vms"></a>Preparare le VM di origine

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato in ogni server da replicare. Site Recovery installa il servizio automaticamente quando si abilita la replica per il server. Per l'installazione automatica è necessario preparare un account che Site Recovery userà per accedere al server.

- È possibile usare un account di dominio o locale
- Per le macchine virtuali Windows, se non si usa un account di dominio, disabilitare il Controllo dell'accesso utente remoto nel computer locale. A questo scopo, aggiungere la voce DWORD **LocalAccountTokenFilterPolicy** con un valore di 1 nel Registro di sistema in **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**.
- Per aggiungere la voce di registro per disabilitare l'impostazione da CLI, digitare:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Per Linux, l'account deve essere radice nel server Linux di origine.


## <a name="prepare-the-target-region"></a>Preparare l'area di destinazione

1. Verificare che la sottoscrizione di Azure in uso consenta la creazione di macchine virtuali nell'area di destinazione usata per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria.

2. Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare macchine virtuali con dimensioni corrispondenti alle macchine virtuali di origine. Se si usa Site Recovery per copiare i dati nella destinazione, Site Recovery sceglie per la VM di destinazione la stessa dimensione o la dimensione più vicina possibile.

3. Assicurarsi di creare una risorsa di destinazione per ogni componente identificato nel layout di rete di origine. Questo aspetto è importante affinché le VM abbiano tutte le caratteristiche e le funzionalità presenti nell'origine, dopo il cutover nell'area di destinazione.

    > [!NOTE]
    > Azure Site Recovery individua e crea automaticamente una rete virtuale quando si abilita la replica per la VM di origine. In alternativa, è possibile creare preventivamente una rete e assegnarla alla VM nel flusso utente per l'abilitazione della replica. Ma tutte le altre risorse devono invece essere create manualmente nell'area di destinazione, come indicato di seguito.

     Per creare le risorse di rete usate più di frequente che sono pertinenti per il caso specifico in base alla configurazione della VM di origine, vedere i documenti seguenti.

    - [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Servizi di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer)
    - [IP pubblico](../virtual-network/virtual-network-public-ip-address.md)
    
    Per qualsiasi altro componente di rete, vedere la [documentazione](https://docs.microsoft.com/azure/#pivot=products&panel=network) relativa alle reti. 

4. Se si vuole testare la configurazione prima di eseguire il cutover finale nell'area di destinazione, [creare manualmente una rete non di produzione](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) nell'area di destinazione. Questo passaggio creerà un'interferenza minima con l'ambiente di produzione ed è consigliato.

## <a name="copy-data-to-the-target-region"></a>Copiare i dati nell'area di destinazione
La procedura seguente illustra come usare Azure Site Recovery per copiare i dati nell'area di destinazione.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Creare l'insieme di credenziali in tutte le aree, ad eccezione dell'area di origine.

1. Accedere a [portale di Azure](https://portal.azure.com) > **Servizi di ripristino**.
2. Fare clic su **Crea una risorsa** > **Strumenti di gestione** > **Backup e Site Recovery (OMS)** .
3. In **Nome** specificare il nome descrittivo **ContosoVMVault**. Se si hanno più sottoscrizioni, selezionare quella appropriata.
4. Creare un gruppo di risorse **ContosoRG**.
5. Specificare un'area di Azure. Per verificare le aree supportate, vedere la sezione relativa alla disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. In Insiemi di credenziali dei servizi di ripristino fare clic su **Panoramica** > **ContosoVMVault** >  **+Replica**
7. Selezionare **In Azure** > **Non virtualizzato/Altro**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Configurare il server di configurazione per rilevare le VM.


Configurare il server di configurazione, registrarlo nell'insieme di credenziali e individuare le VM.

1. Fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Origine**.
2. Se non è disponibile un server di configurazione, fare clic su **+Server di configurazione**.
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione**.
4. Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
5. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria quando si esegue l'Installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.

   ![Impostare l'origine](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrare il server di configurazione nell'insieme di credenziali

Prima di iniziare procedere come segue: 

#### <a name="verify-time-accuracy"></a>Verificare l'accuratezza dell'ora
Nel server di configurazione verificare che il clock di sistema sia sincronizzato con un [server di riferimento ora](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deve corrispondere. Se è avanti o indietro di 15 minuti, l'installazione potrebbe avere esito negativo.

#### <a name="verify-connectivity"></a>Verificare la connettività
Assicurarsi che la macchina possa accedere a questi URL in base all'ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Le regole del firewall basate sull'indirizzo IP dovrebbero consentire la comunicazione con tutti gli URL di Azure elencati in precedenza tramite la porta HTTPS (443). Per semplificare e limitare gli intervalli IP, è consigliabile eseguire il filtraggio degli URL.

- **IP commerciali**: è necessario consentire gli [intervalli IP del data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443). Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione per supportare gli URL di Azure Active Directory, backup, replica e archiviazione.  
- **IP di enti pubblici**: consentire gli [intervalli IP del data center di Azure per enti pubblici](https://www.microsoft.com/en-us/download/details.aspx?id=57063) e la porta HTTPS (443) per tutte le aree US Gov (Virginia, Texas, Arizona e Iowa) per supportare gli URL di Azure Active Directory, backup, replica e archiviazione.  

#### <a name="run-setup"></a>Eseguire l'installazione.
Eseguire l'installazione unificata come amministratore locale per installare il server di configurazione. Per impostazione predefinita, sul server di configurazione vengono installati anche il server di elaborazione e il server di destinazione master.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Al termine della registrazione, il server di configurazione viene visualizzato sulla pagina **Impostazioni** > **Server** nell'insieme di credenziali.

### <a name="configure-target-settings-for-replication"></a>Configurare le impostazioni di destinazione per la replica

Selezionare e verificare le risorse di destinazione.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare il modello di distribuzione di destinazione.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

   ![Destinazione](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Creare un criterio di replica

1. Per creare nuovi criteri di replica, fare clic su **Infrastruttura di Site Recovery** > **Criteri di replica** >  **+Criteri di replica**.
2. In **Creare i criteri di replica** specificare un nome per i criteri.
3. In **Soglia RPO**, specificare il limite dell'obiettivo del punto di ripristino (RPO). Questo valore specifica la frequenza con cui vengono creati punti di ripristino dei dati. Se la replica continua supera questo limite, viene generato un avviso.
4. In **Conservazione del punto di recupero**, specificare la durata in ore dell'intervallo di conservazione per ogni punto di recupero. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo. È supportata la conservazione fino a 24 ore per le macchine replicate in Archiviazione Premium e fino a 72 ore per Archiviazione Standard.
5. In **Frequenza snapshot coerenti con l'app**specificare la frequenza, in minuti, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Fare clic su **OK** per creare i criteri.

    ![Criteri di replica](./media/physical-azure-disaster-recovery/replication-policy.png)


I criteri vengono automaticamente associati al server di configurazione. Per impostazione predefinita vengono creati automaticamente criteri corrispondenti per il failback. Se, ad esempio, il criterio di replica è **rep-policy**, il criterio di failback creato sarà **rep-policy-failback**. Questi criteri non vengono usati fino a quando non si avvia un failback da Azure.

### <a name="enable-replication"></a>Abilitare la replica

- Quando viene abilitata la replica, Site Recovery installa il servizio Mobility.
- Quando viene abilitata la replica per un server, le modifiche diventeranno effettive e verranno visualizzate nel portale dopo 15 minuti o più.

1. Fare clic su **Eseguire la replica dell'applicazione** > **Origine**.
2. In **Origine** selezionare il server di configurazione.
3. In **Tipo di computer**, selezionare **Computer fisici**.
4. Selezionare il server di elaborazione (server di configurazione). Fare quindi clic su **OK**.
5. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si vuole creare le VM di Azure dopo il failover. Scegliere il modello di distribuzione (classica o Resource Manager) da usare in Azure.
6. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati. 
7. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le VM di Azure create dopo il failover.
8. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. 
9. In **Computer fisici**, fare clic su **+Computer fisico**. Specificare il nome e l'indirizzo IP. Selezionare il sistema operativo del computer da replicare. Sono necessari alcuni minuti per individuare ed elencare i server. 

   > [!WARNING]
   > È necessario immettere l'indirizzo IP della VM di Azure che si intende spostare

10. In **Proprietà** > **Configura proprietà**selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.
11. In **Impostazioni della replica** > **Configura impostazioni di replica** verificare che siano selezionati i criteri di replica corretti. 
12. Fare clic su **Abilita la replica**. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.


Per monitorare i server aggiunti, è possibile controllare l'ora dell'ultima individuazione in **Server di configurazione** > **Ora ultimo contatto**. Per aggiungere computer senza attendere l'individuazione pianificata, evidenziare il server di configurazione, senza selezionarlo, e fare clic su **Aggiorna**.

## <a name="test-the-configuration"></a>Testare la configurazione


1. Passare all'insieme di credenziali e in **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale che si intende spostare nell'area di destinazione e quindi sull'icona **+Failover di Test**.
2. In **Failover di test** selezionare un punto di ripristino in cui eseguire il failover:

   - **Elaborato più recente**: viene eseguito il failover della macchina virtuale al punto di recupero più recente elaborato dal servizio Site Recovery. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati e viene quindi fornito un valore RTO (Recovery Time Objective) basso.
   - **Coerente con l'app più recente**: questa opzione esegue il failover di tutte le macchine virtuali al più recente punto di recupero coerente con l'app. Viene visualizzato il timestamp.
   - **Personalizzato**: selezionare qualsiasi punto di recupero.

3. Selezionare la rete virtuale di Azure di destinazione in cui si vogliono spostare le VM di Azure per testare la configurazione. 

   > [!IMPORTANT]
   > Per il failover di test è consigliabile usare una rete di macchine virtuali di Azure separata, non la rete di produzione in cui si vogliono spostare infine le VM, configurata quando è stata abilitata la replica.

4. Per avviare il test dello spostamento, fare clic su **OK**. Per verificare lo stato dell'operazione, fare clic sulla VM per visualizzare le rispettive proprietà. In alternativa, è possibile fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali, quindi su **Impostazioni** > **Processi** > **Site Recovery jobs** (Processi di Site Recovery).
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia in esecuzione, che sia stata dimensionata in modo adeguato e che sia connessa alla rete appropriata.
6. Se si vuole eliminare la VM creata nell'ambito del test dello spostamento, fare clic su **Pulisci failover di test** per l'elemento replicato. In **Note** registrare e salvare eventuali osservazioni associate al test.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Eseguire lo spostamento nell'area di destinazione e confermare.

1. Passare all'insieme di credenziali e in **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale e quindi su **Failover**.
2. In **Failover** selezionare **Più recente**. 
3. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover. 
4. Al termine del processo, controllare che la VM si trovi nell'area di Azure di destinazione come previsto.
5. In **Elementi replicati** fare clic con il pulsante destro del mouse su Macchina virtuale > **Esegui commit**. Questa operazione completa il processo di spostamento nell'area di destinazione. Attendere la fine del processo di commit.

## <a name="discard-the-resource-in-the-source-region"></a>Rimuovere la risorsa nell'area di origine 

- Passare alla VM.  Fare clic su **Disabilita replica**.  Il processo di copia dei dati per la VM viene arrestato.  

   > [!IMPORTANT]
   > È importante eseguire questo passaggio per evitare di incorrere nei costi associati alla replica ASR.

Se non si prevede di riusare alcuna risorsa di origine, procedere con il successivo set di passaggi.

1. Procedere all'eliminazione di tutte le risorse di rete pertinenti dell'area di origine annotate nel passaggio 4 di [Preparare le VM di origine](#prepare-the-source-vms). 
2. Eliminare l'account di archiviazione corrispondente nell'area di origine.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata spostata una macchina virtuale di Azure in un'area diversa di Azure. Ora è possibile configurare il ripristino di emergenza per le macchine virtuali spostate.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza dopo la migrazione](azure-to-azure-quickstart.md)
