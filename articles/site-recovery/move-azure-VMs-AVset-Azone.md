---
title: Spostare macchine virtuali IaaS di Azure in un'altra area di Azure come macchine virtuali aggiunte alla zona usando il servizio Azure Site Recovery | Microsoft Docs
description: Usare Site Recovery per lo spostamento di macchine virtuali IaaS di Azure da un'altra area di Azure come macchine virtuali aggiunte alla zona.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 6e26f9d344a4c11c335d1ea34998c848cbe3598a
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753758"
---
# <a name="move-azure-vms-into-availability-zones"></a>Spostare macchine virtuali di Azure nelle zone di disponibilità
Le zone di disponibilità di Azure consentono di proteggere le applicazioni e i dati da eventuali guasti del data center. Ogni zona di disponibilità è costituita da uno o più data center dotati di impianti indipendenti per l'energia, il raffreddamento e la rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati dai guasti del data center. Con le zone di disponibilità, Azure offre un Contratto di servizio con tempo di attività delle macchine virtuali del 99,99%. Le zone di disponibilità sono supportate in aree selezionate, come indicato in [Informazioni sulle zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region).

Se le macchine virtuali sono distribuite come *istanza singola* in un'area specifica e si vuole migliorare la disponibilità spostandole in una zona di disponibilità, è possibile eseguire questa operazione usando Azure Site Recovery. Questa operazione può essere ulteriormente classificata in:

- Spostare macchine virtuali a istanza singola nelle zone di disponibilità in un'area di destinazione
- Spostare macchine virtuali in un set di disponibilità nelle zone di disponibilità in un'area di destinazione

> [!IMPORTANT]
> Attualmente Azure Site Recovery supporta lo spostamento delle macchine virtuali da un'area a un'altra, ma non all'interno della stessa area.

## <a name="check-prerequisites"></a>Verificare i prerequisiti

- Verificare se l'area di destinazione dispone del [supporto per le zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region). Controllare che la [combinazione di area di origine e area di destinazione sia supportata](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support). Prendere una decisione consapevole riguardo all'area di destinazione.
- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](azure-to-azure-architecture.md).
- Rivedere le [limitazioni e i requisiti del supporto](azure-to-azure-support-matrix.md).
- Controllare le autorizzazioni dell'account. Se è appena stato creato l'account Azure gratuito, si è amministratori della propria sottoscrizione. In caso contrario, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una macchina virtuale e infine copiare i dati nella destinazione con Azure Site Recovery, è necessario avere quanto segue:

    1. Autorizzazione per la creazione di una macchina virtuale nelle risorse di Azure. Il ruolo predefinito *Collaboratore Macchina virtuale* dispone di queste autorizzazioni, che includono:
        - Autorizzazione per la creazione di una macchina virtuale nel gruppo di risorse selezionato
        - Autorizzazione per la creazione di una macchina virtuale nella rete virtuale selezionata
        - Autorizzazione per la scrittura nell'account di archiviazione selezionato

    2. Autorizzazione per la gestione delle attività di Azure Site Recovery. Il ruolo *Collaboratore di Site Recovery* dispone di tutte le autorizzazioni necessarie per la gestione delle operazioni di Site Recovery in un insieme di credenziali di Servizi di ripristino.

## <a name="prepare-the-source-vms"></a>Preparare le VM di origine

1. Le macchine virtuali devono usare dischi gestiti se si vuole spostarle in una zona di disponibilità usando Site Recovery. È possibile convertire le macchine virtuali Windows esistenti che usano dischi non gestiti in modo da usare quelli gestiti. Seguire la procedura descritta in [Convertire i dischi non gestiti di una VM Windows in dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Assicurarsi che il set di disponibilità sia configurato come *gestito*.
2. Controllare che nelle VM di Azure da spostare siano presenti tutti i certificati radice più recenti. In caso contrario, non è possibile abilitare la copia dei dati nell'area di destinazione a causa dei vincoli di sicurezza.

3. Per le macchine virtuali di Windows, installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente non connesso, seguire i processi di aggiornamento di Windows e di aggiornamento dei certificati standard per l'organizzazione.

4. Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella macchina virtuale.
5. Assicurarsi di non usare un proxy di autenticazione per controllare la connettività di rete per le macchine virtuali da spostare.

6. Se la macchina virtuale che si intende spostare non ha accesso a Internet o usa un proxy firewall per controllare l'accesso in uscita, controllare i requisiti in [Configurare la connettività di rete in uscita](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

7. Identificare il layout di rete di origine e le risorse attualmente in uso per la verifica, tra cui i servizi di bilanciamento del carico, i gruppi di sicurezza di rete e gli indirizzi IP pubblici.

## <a name="prepare-the-target-region"></a>Preparare l'area di destinazione

1. Verificare che la sottoscrizione di Azure in uso consenta la creazione di macchine virtuali nell'area di destinazione usata per il ripristino di emergenza. Se necessario, contattare il supporto per abilitare la quota richiesta.

2. Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare macchine virtuali con dimensioni corrispondenti alle macchine virtuali di origine. Se si usa Site Recovery per copiare i dati nella destinazione, Site Recovery sceglie la stessa dimensione o la dimensione più vicina possibile per la macchina virtuale di destinazione.

3. Creare una risorsa di destinazione per ogni componente identificato nel layout di rete di origine. Questa operazione consente di assicurarsi che, dopo il cutover nell'area di destinazione, le macchine virtuali abbiano tutte le caratteristiche e le funzionalità presenti nell'origine.

    > [!NOTE]
    > Azure Site Recovery individua e crea automaticamente una rete virtuale e un account di archiviazione quando si abilita la replica per la macchina virtuale di origine. È anche possibile creare prima queste risorse e assegnarle alla macchina virtuale come parte del passaggio di abilitazione della replica, ma tutte le altre risorse devono essere create manualmente nell'area di destinazione, come indicato di seguito.

     Per informazioni su come creare le risorse di rete usate più di frequente che sono pertinenti per il caso specifico in base alla configurazione della macchina virtuale di origine, vedere i documenti seguenti.

    - [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Servizi di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [IP pubblico](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Per qualsiasi altro componente di rete, vedere la [documentazione](https://docs.microsoft.com/azure/#pivot=products&panel=network) relativa alle reti.

    > [!IMPORTANT]
    > Assicurarsi di usare un servizio di bilanciamento del carico con ridondanza della zona nella destinazione. Per altre informazioni, vedere [Load Balancer Standard e zone di disponibilità](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Se si vuole testare la configurazione prima di eseguire il cutover nell'area di destinazione, [creare manualmente una rete non di produzione](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) nell'area di destinazione. È consigliabile adottare questo approccio perché garantisce un'interferenza minima con l'ambiente di produzione.

## <a name="enable-replication"></a>Abilitare la replica
La procedura seguente illustra come usare Azure Site Recovery per abilitare la replica dei dati nell'area di destinazione prima di spostarli nelle zone di disponibilità.

> [!NOTE]
> Questi passaggi riguardano una singola macchina virtuale. È possibile estendere la procedura a più macchine virtuali. Passare all'insieme di credenziali di Servizi di ripristino, selezionare **+ Replica** e selezionare insieme le macchine virtuali pertinenti.

1. Nel portale di Azure selezionare **Macchine virtuali** e quindi la macchina virtuale che si vuole spostare nelle zone di disponibilità.
2. In **Operazioni** selezionare **Ripristino di emergenza**.
3. In **Configura ripristino di emergenza** > **Area di destinazione** selezionare l'area di destinazione in cui si eseguirà la replica. Assicurarsi che quest'area [supporti](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region) le zone di disponibilità.

    ![Selezione dell'area di destinazione](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Selezionare **Avanti: Impostazioni avanzate**.
5. Scegliere i valori appropriati per la sottoscrizione di destinazione, il gruppo di risorse della macchina virtuale di destinazione e la rete virtuale.
6. Nella sezione **Disponibilità** scegliere la zona di disponibilità in cui si vuole spostare la macchina virtuale. 
   > [!NOTE]
   > Se non è presente un'opzione per il set o la zona di disponibilità, verificare che i [prerequisiti](#prepare-the-source-vms) siano soddisfatti e che la [preparazione](#prepare-the-source-vms) delle macchine virtuali di origine sia stata completata.
  
    ![Selezioni per la scelta di una zona di disponibilità](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Selezionare **Abilita replica**. Verrà avviato un processo per abilitare la replica per la macchina virtuale.

## <a name="check-settings"></a>Verificare le impostazioni

Al termine del processo di replica, è possibile controllare lo stato di replica, modificare le impostazioni di replica e testare la distribuzione.

1. Nel menu della macchina virtuale selezionare **Ripristino di emergenza**.
2. È possibile verificare l'integrità della replica, i punti di ripristino creati, nonché le aree di origine e di destinazione sulla mappa.

   ![Stato della replica](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testare la configurazione

1. Nel menu della macchina virtuale selezionare **Ripristino di emergenza**.
2. Selezionare l'icona **Failover di test**.
3. In **Failover di test** selezionare un punto di ripristino da usare per il failover:

   - **Elaborato più recente**: viene eseguito il failover della macchina virtuale al punto di recupero più recente elaborato dal servizio Site Recovery. Viene visualizzato il timestamp. Con questa opzione, non viene dedicato alcun tempo all'elaborazione dei dati, quindi si ottiene un RTO (Recovery Time Objective) basso.
   - **Coerente con l'app più recente**: questa opzione esegue il failover di tutte le macchine virtuali al più recente punto di recupero coerente con l'app. Viene visualizzato il timestamp.
   - **Personalizzato**: selezionare qualsiasi punto di recupero.

3. Selezionare la rete virtuale di Azure di destinazione di test in cui si vogliono spostare le VM di Azure per testare la configurazione. 

    > [!IMPORTANT]
    > Per il failover di test è consigliabile usare una rete di macchine virtuali di Azure separata, non la rete di produzione dell'area di destinazione in cui si vogliono spostare le macchine virtuali.

4. Per avviare il test dello spostamento, selezionare **OK**. Per monitorare l'avanzamento dell'operazione, selezionare la macchina virtuale per visualizzarne le proprietà. In alternativa, è possibile selezionare il processo **Failover di test** nel nome dell'insieme di credenziali e quindi **Impostazioni** > **Processi** > **Processi di Site Recovery**.
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia in esecuzione, che sia stata dimensionata in modo adeguato e che sia connessa alla rete appropriata.
6. Se si vuole eliminare la macchina virtuale creata nell'ambito del test dello spostamento, selezionare **Pulisci failover di test** per l'elemento replicato. In **Note** registrare e salvare eventuali osservazioni associate al test.

## <a name="move-to-the-target-region-and-confirm"></a>Eseguire lo spostamento nell'area di destinazione e confermare

1.  Nel menu della macchina virtuale selezionare **Ripristino di emergenza**.
2. Selezionare l'icona **Failover**.
3. In **Failover** selezionare **Più recente**. 
4. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover. 
5. Al termine del processo, controllare che la macchina virtuale si trovi nell'area di Azure di destinazione come previsto.
6. In **Elementi replicati** fare clic con il pulsante destro del mouse su Macchina virtuale > **Esegui commit**. Questa operazione completa il processo di spostamento nell'area di destinazione. Attendere il completamento del processo di commit.

## <a name="discard-the-resource-in-the-source-region"></a>Rimuovere la risorsa nell'area di origine

Passare alla macchina virtuale. Selezionare **Disabilita replica**. Il processo di copia dei dati per la macchina virtuale viene arrestato.  

> [!IMPORTANT]
> Eseguire il passaggio precedente per evitare addebiti per la replica di Site Recovery dopo lo spostamento. Le impostazioni della replica di origine vengono automaticamente pulite. Si noti che l'estensione Site Recovery installata come parte della replica non viene rimossa automaticamente. L'operazione di rimozione deve essere eseguita manualmente.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aumentata la disponibilità di una macchina virtuale di Azure spostandola in un set di disponibilità o in una zona di disponibilità. È ora possibile impostare il ripristino di emergenza per la macchina virtuale spostata.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza dopo la migrazione](azure-to-azure-quickstart.md)


