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
ms.openlocfilehash: 5597f3c017ccf2dbb58b7b6b046720c8f49803c5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312288"
---
# <a name="move-azure-vms-into-availability-zones"></a>Spostare macchine virtuali di Azure nelle zone di disponibilità
Le zone di disponibilità di Azure proteggono le applicazioni e i dati dai guasti del data center. Ogni zona di disponibilità è costituita da uno o più data center dotati di impianti indipendenti per l'energia, il raffreddamento e la rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. Con le zone di disponibilità, Azure offre un contratto di servizio con tempo di attività delle VM del 99,99% tra i migliori del settore. La zona di disponibilità è supportata in aree selezionate, come indicato [qui](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). 

In uno scenario in cui le macchine virtuali sono state distribuite come "istanza singola" in un'area specifica e si vuole migliorare la disponibilità spostandole nella zona di disponibilità, è possibile farlo usando Azure Site Recovery. Ciò può essere ulteriormente classificato in:

- Spostare macchine virtuali a istanza singola nelle zone di disponibilità in un'area di destinazione
- Spostare macchine virtuali in un set di disponibilità nelle zone di disponibilità in un'area di destinazione

> [!IMPORTANT]
> Attualmente Azure Site Recovery supporta lo spostamento delle VM da un'area a un'altra, ma non all'interno della stessa area. 

## <a name="verify-prerequisites"></a>Verificare i prerequisiti

- Verificare se l'area di destinazione prevede il [supporto per la zona di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones): verificare se la combinazione di [area di origine-area di destinazione scelta è supportata](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) e prendere una decisione basata su informazioni aggiornate in merito all'area di destinazione.
- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](azure-to-azure-architecture.md).
- Rivedere le [limitazioni e i requisiti del supporto](azure-to-azure-support-matrix.md).
- Verificare le autorizzazioni dell'account: Se è appena stato creato l'account gratuito di Azure, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della propria sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una VM e infine copiare i dati nella destinazione con Azure Site Recovery, è necessario quanto segue:

    1. Autorizzazioni per creare una macchina virtuale nelle risorse di Azure. Al ruolo predefinito 'Collaboratore Macchina virtuale' sono assegnate le autorizzazioni seguenti, che includono:
        - Autorizzazione per la creazione di una macchina virtuale nel gruppo di risorse selezionato
        - Autorizzazione per la creazione di una macchina virtuale nella rete virtuale selezionata
        - Autorizzazione per la scrittura nell'account di archiviazione selezionato

    2. È necessario anche avere l'autorizzazione per la gestione delle operazioni di Azure Site Recovery. Il ruolo 'Collaboratore di Site Recovery' ha tutte le autorizzazioni necessarie per la gestione delle operazioni di Site Recovery in un insieme di credenziali dei servizi di ripristino.

## <a name="prepare-the-source-vms"></a>Preparare le VM di origine

1. Le macchine virtuali devono usare i dischi gestiti se si vuole spostarli nella zona di disponibilità usando Site Recovery. È possibile convertire le macchine virtuali (VM) Windows esistenti che usano dischi non gestiti all'uso di dischi gestiti seguendo le procedure descritte [qui](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). A tale scopo, assicurarsi che il set di disponibilità sia configurato come "gestito". 
2. Controllare che nelle VM di Azure da spostare siano presenti tutti i certificati radice più recenti. In caso contrario, a causa dei vincoli di sicurezza non è possibile abilitare la copia dei dati nell'area di destinazione.

3. Per le macchine virtuali di Windows, installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente non connesso, seguire i processi di aggiornamento di Windows e di aggiornamento dei certificati standard per l'organizzazione.

4. Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella macchina virtuale.
5. Assicurarsi di non usare un proxy di autenticazione per controllare la connettività di rete per le macchine virtuali da spostare.

6. Se la VM che si sta provando a spostare non ha accesso a Internet e usa un proxy firewall per controllare l'accesso in uscita, controllare i requisiti [qui](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. Identificare il layout di rete di origine e tutte le risorse attualmente in uso, tra cui i servizi di bilanciamento del carico, i gruppi di sicurezza, l'indirizzo IP pubblico e così via per la verifica.

## <a name="prepare-the-target-region"></a>Preparare l'area di destinazione

1. Verificare che la sottoscrizione di Azure in uso consenta la creazione di macchine virtuali nell'area di destinazione usata per il ripristino di emergenza. Se necessario, contattare il supporto per abilitare la quota richiesta.

2. Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare macchine virtuali con dimensioni corrispondenti alle macchine virtuali di origine. Se si usa Site Recovery per copiare i dati nella destinazione, Site Recovery sceglie per la VM di destinazione la stessa dimensione o la dimensione più vicina possibile.

3. Assicurarsi di creare una risorsa di destinazione per ogni componente identificato nel layout di rete di origine. Questo aspetto è importante affinché le VM abbiano tutte le caratteristiche e le funzionalità presenti nell'origine, dopo il cutover nell'area di destinazione.

    > [!NOTE]
    > Azure Site Recovery individua e crea automaticamente una rete virtuale e un account di archiviazione quando si abilita la replica per la VM di origine. In alternativa, è possibile creare preventivamente queste risorse e assegnarle alla VM nel passaggio di abilitazione della replica. Tutte le altre risorse devono invece essere create manualmente nell'area di destinazione, come indicato di seguito.

     Per creare le risorse di rete usate più di frequente che sono pertinenti per il caso specifico in base alla configurazione della VM di origine, vedere i documenti seguenti.

    - [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Servizi di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials
        
     - [IP pubblico ](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Per qualsiasi altro componente di rete, vedere la [documentazione](https://docs.microsoft.com/azure/#pivot=products&panel=network) relativa alle reti. 

> [!IMPORTANT]
> Assicurarsi di usare un servizio di bilanciamento del carico con ridondanza della zona nella destinazione. Per altre informazioni, leggere [qui](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Se si vuole testare la configurazione prima di eseguire il cutover finale nell'area di destinazione, [creare manualmente una rete non di produzione](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) nell'area di destinazione. Questo passaggio creerà un'interferenza minima con l'ambiente di produzione ed è consigliato.


> [!NOTE]
> La procedura seguente si riferisce a una singola VM, ma è possibile estenderla a più VM passando all'insieme di credenziali di Servizi di ripristino e facendo clic su + Replica, quindi selezionando insieme le VM pertinenti.

## <a name="enable-replication"></a>Abilitare la replica
La procedura seguente illustra come usare Azure Site Recovery per abilitare la replica dei dati nell'area di destinazione, prima di infine spostarle nelle zone di disponibilità.

1. Nel portale di Azure fare clic su **Macchine virtuali** e selezionare la macchina virtuale che si vuole spostare nelle zone di disponibilità.
2. In **Operazioni** fare clic su **Ripristino di emergenza**.
3. In **Configure disaster recovery** (Configura ripristino di emergenza)  >  **Area di destinazione** selezionare l'area di destinazione in cui si vuole eseguire la replica. Assicurarsi che quest'area [supporti](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) le zone di disponibilità.
![enable-rep-1.PNG](media/azure-vms-to-zones/enable-rep-1.PNG)

1. Selezionare **Avanti: Impostazioni avanzate**
2. Selezionare i valori appropriati per sottoscrizione di destinazione, gruppo di risorse della macchina virtuale di destinazione e rete virtuale.
3. Nella sezione **Disponibilità** scegliere la zona di disponibilità in cui si vuole spostare la VM. 
> [!NOTE]
> Se non è presente un'opzione per set o zona di disponibilità, verificare che i [prerequisiti](#prepare-the-source-vms) siano soddisfatti e che la [preparazione](#prepare-the-source-vms) delle VM di origine sia stata completata.

   ![enable-rep-2.PNG](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Fare clic su abilitare la replica. Verrà avviato un processo per abilitare la replica per la macchina virtuale.

## <a name="verify-settings"></a>Verificare le impostazioni

Al termine del processo di replica, è possibile controllare lo stato di replica, modificare le impostazioni di replica e testare la distribuzione.

1. Nel menu della macchina virtuale fare clic su **Ripristino di emergenza**.
2. È possibile verificare l'integrità della replica, i punti di ripristino creati, nonché le aree di origine e di destinazione sulla mappa.

   ![Stato della replica](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testare la configurazione

1. Nel menu della macchina virtuale fare clic su **Ripristino di emergenza**.
2. Fare clic sull'icona **Failover di test**.
3. In **Failover di test** selezionare un punto di ripristino in cui eseguire il failover:

   - **Elaborato più recente**: viene eseguito il failover della macchina virtuale al punto di recupero più recente elaborato dal servizio Site Recovery. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati e viene quindi fornito un valore RTO (Recovery Time Objective) basso.
   - **Coerente con l'app più recente**: questa opzione esegue il failover di tutte le macchine virtuali al più recente punto di recupero coerente con l'app. Viene visualizzato il timestamp.
   - **Personalizzato**: selezionare qualsiasi punto di recupero.

3. Selezionare la rete virtuale di Azure di destinazione di test in cui si vogliono spostare le VM di Azure per testare la configurazione. 

> [!IMPORTANT]
> Per il failover di test è consigliabile usare una rete di macchine virtuali di Azure separata, non la rete di produzione dell'area di destinazione in cui si vogliono spostare infine le VM.

4. Per avviare il test dello spostamento, fare clic su **OK**. Per verificare lo stato dell'operazione, fare clic sulla VM per visualizzare le rispettive proprietà. In alternativa, è possibile fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali, quindi su **Impostazioni** > **Processi** > **Site Recovery jobs** (Processi di Site Recovery).
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia in esecuzione, che sia stata dimensionata in modo adeguato e che sia connessa alla rete appropriata.
6. Se si vuole eliminare la VM creata nell'ambito del test dello spostamento, fare clic su **Pulisci failover di test** per l'elemento replicato. In **Note** registrare e salvare eventuali osservazioni associate al test.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Eseguire lo spostamento nell'area di destinazione e confermare.

1.  Nel menu della macchina virtuale fare clic su **Ripristino di emergenza**.
2. Fare clic sull'icona **Failover**.
3. In **Failover** selezionare **Più recente**. 
4. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover. 
5. Al termine del processo, controllare che la VM si trovi nell'area di Azure di destinazione come previsto.
6. In **Elementi replicati** fare clic con il pulsante destro del mouse su Macchina virtuale > **Esegui commit**. Questa operazione completa il processo di spostamento nell'area di destinazione. Attendere la fine del processo di commit.

## <a name="discard-the-resource-in-the-source-region"></a>Rimuovere la risorsa nell'area di origine 

1. Passare alla VM.  Fare clic su **Disabilita replica**.  Il processo di copia dei dati per la VM viene arrestato.  

> [!IMPORTANT]
> Il passaggio riportato sopra è importante per evitare addebiti per la replica di Site Recovery dopo lo spostamento. Le impostazioni della replica di origine vengono automaticamente pulite. Si noti che l'estensione Site Recovery installata come parte della replica non viene rimossa e deve essere rimossa manualmente. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aumentata la disponibilità di una VM di Azure spostandola in un set di disponibilità o una zona di disponibilità. Ora è possibile configurare il ripristino di emergenza per le macchine virtuali spostate.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza dopo la migrazione](azure-to-azure-quickstart.md)


