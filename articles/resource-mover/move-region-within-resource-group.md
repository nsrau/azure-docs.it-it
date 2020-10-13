---
title: Spostare le risorse in un'altra area con Azure Resource Mover
description: Informazioni su come spostare le risorse all'interno di un gruppo di risorse in un'altra area con Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 716928761d23c2cf04ebcc72e253ad7884408065
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061844"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Spostare le risorse tra le aree (dal gruppo di risorse)

Questo articolo illustra come spostare le risorse in un gruppo di risorse specifico in un'area di Azure diversa. Nel gruppo di risorse selezionare le risorse che si desidera spostare. Quindi, è possibile spostarli usando il [motore di risorse di Azure](overview.md).

> [!IMPORTANT]
> Spostamento risorse di Azure è attualmente disponibile in anteprima pubblica.


## <a name="prerequisites"></a>Prerequisiti

- È necessario l'accesso del *proprietario* per la sottoscrizione in cui si trovano le risorse che si desidera spostare.
    - La prima volta che si aggiunge una risorsa per un mapping di origine e destinazione specifico in una sottoscrizione di Azure, il motore risorse crea un' [identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (precedentemente nota come identità del servizio gestito) considerata attendibile dalla sottoscrizione.
    - Per creare l'identità e assegnarle il ruolo richiesto (Collaboratore o Amministratore Accesso utenti nella sottoscrizione di origine), l'account usato per aggiungere le risorse deve avere le autorizzazioni di *Proprietario* nella sottoscrizione. [Altre informazioni](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sui ruoli di Azure.
- La sottoscrizione richiede una quota sufficiente per creare le risorse di origine nell'area di destinazione. In caso contrario, richiedere limiti aggiuntivi. [Altre informazioni](/azure/azure-resource-manager/management/azure-subscription-service-limits)
- Verificare i prezzi e gli addebiti associati all'area di destinazione in cui si intende spostare le macchine virtuali. Per facilitare l'operazione, usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/).
- Controllare che le risorse che si vuole spostare siano supportate da Resource Mover:
    - Macchine virtuali di Azure e dischi associati
    - Schede di interfaccia di rete
    - Set di disponibilità
    - Reti virtuali di Azure
    - Indirizzi IP pubblici
    - Gruppi di sicurezza di rete:
    - Servizi di bilanciamento del carico interni e pubblici
    - Database SQL di Azure e pool elastici


## <a name="check-vm-requirements"></a>Controllare i requisiti delle macchine virtuali

1. Controllare che le macchine virtuali da spostare siano supportate.

    - [Verificare](support-matrix-move-region-azure-vm.md#windows-vm-support) le macchine virtuali Windows supportate.
    - [Verificare](support-matrix-move-region-azure-vm.md#linux-vm-support) le macchine virtuali Linux e le versioni del kernel supportate.
    - Controllare le impostazioni di [calcolo](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [archiviazione](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) e [rete](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) supportate.
2. Verificare che le macchine virtuali dispongano dei certificati radice trusted più recenti e di un elenco di revoche di certificati (CRL) aggiornato. 
    - Nelle VM di Azure che eseguono Windows installare gli aggiornamenti di Windows più recenti.
    - Nelle macchine virtuali che eseguono Linux, seguire le indicazioni per il server di distribuzione Linux per assicurarsi che il computer disponga dei certificati più recenti e del CRL. 
3. Consentire la connettività in uscita dalle macchine virtuali:
    - Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso a questi [URL](support-matrix-move-region-azure-vm.md#url-access)
    - Se si usano le regole del gruppo di sicurezza di rete per controllare la connettività in uscita, creare queste [regole del tag di servizio](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selezionare le risorse da spostare

Selezionare le risorse da spostare. Si spostano le risorse in un'area di destinazione nella sottoscrizione dell'area di origine. Se si vuole cambiare la sottoscrizione, è possibile eseguire questa operazione dopo lo spostamento delle risorse.

1. Nel portale di Azure aprire il gruppo di risorse pertinente.
2. Nella pagina gruppo di risorse selezionare le risorse che si desidera spostare.
3. Selezionare **Sposta**  >  **spostamento in un'altra area**.

    ![Selezione per lo trasferimento di risorse in un'area diversa](./media/move-region-within-resource-group/select-move-region.png)
    
4. In **origine e destinazione**selezionare l'area di destinazione in cui si desidera spostare le risorse. Fare quindi clic su **Avanti**.


    ![Pagina di origine e destinazione per selezionare l'area di destinazione](./media/move-region-within-resource-group/source-target.png)


7. In **risorse da spostare**selezionare **Avanti**.  
8. In **Seleziona risorse**selezionare la risorsa che si desidera spostare. È possibile aggiungere solo le risorse supportate per lo spostamento. Al termine selezionare **Done** (Fine).
9. In **Sposta risorse**selezionare **Avanti**. 
10. In verifica **+ Aggiungi**, controllare i dettagli di origine e destinazione.
11. Verificare di aver compreso che i metadati relativi alle risorse spostate verranno archiviati in un gruppo di risorse creato a questo scopo e consentire a Resource Mover di creare un'identità gestita dal sistema per accedere alle risorse della sottoscrizione.
1. Selezionare **procedi** per iniziare ad aggiungere le risorse.

    ![Pagina Riepilogo per controllare i dettagli e procedere con lo spostamento](./media/move-region-within-resource-group/summary.png)    

11. Viene avviata l'operazione Aggiungi risorsa. Al termine dell'operazione, le notifiche indicano che le risorse sono state aggiunte e la distribuzione è riuscita.
13. Nelle notifiche selezionare **aggiunta di risorse per lo spostamento**.

    ![Messaggio visualizzato nelle notifiche](./media/move-region-within-resource-group/notification.png)    


14. Dopo aver selezionato la notifica, le risorse selezionate vengono aggiunte a una raccolta di spostamento nell'hub di Azure Resource Mover.  Il motore di risorse consente di controllare le dipendenze e quindi di iniziare a trasferire le risorse nell'area di destinazione.

## <a name="resolve-dependencies"></a>Risolvere gli errori relativi alle risorse di Azure non trovate

Le risorse che si stanno muovendo vengono visualizzate nella pagina **tra aree** , in uno stato di *preparazione in sospeso* . Avviare la convalida come segue:

1. Se le risorse mostrano un messaggio di *convalida delle dipendenze* nella colonna **problemi** , selezionare il pulsante **convalida dipendenze** . Viene avviato il processo di convalida.

    ![Pulsante per convalidare le dipendenze](./media/move-region-within-resource-group/validate-dependencies.png)

2. Se vengono trovate dipendenze, selezionare **Aggiungi dipendenze**. 
3. In **Aggiungi dipendenze** selezionare le risorse dipendenti > **Aggiungi dipendenze**. Monitorare lo stato di avanzamento nelle notifiche.

    ![Pulsante per aggiungere le dipendenze](./media/move-region-within-resource-group/add-dependencies.png)

3. Se necessario, aggiungere altre dipendenze e convalidare le dipendenze in base alle esigenze. Selezionare **Aggiorna** per assicurarsi che le risorse mostrino uno stato aggiornato.

4. Nella pagina **Tra aree** verificare che le risorse si trovino ora in uno stato *Preparazione in sospeso* e che non vengano segnalati problemi.

    ![Pagina per visualizzare lo stato di preparazione in sospeso per tutte le risorse](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine 

Prima di poter preparare e spostare le risorse, il gruppo di risorse di origine deve essere presente nell'area di destinazione. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparare lo spostamento del gruppo di risorse di origine

Eseguire la preparazione nel modo seguente:

1. In **Tra aree** selezionare il gruppo di risorse di origine > **Prepara**.
2. In **preparare le risorse**selezionare **prepara**.
1. 
    ![Pulsante per preparare il gruppo di risorse di origine](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Durante il processo di preparazione Spostamento risorse genera modelli di Azure Resource Manager usando le impostazioni del gruppo di risorse. Le risorse all'interno di un gruppo di risorse non sono interessate.
    
> [!NOTE]
>  Dopo la preparazione il gruppo di risorse si trova nello stato *Avvio spostamento in sospeso*. Aggiornare per visualizzare lo stato più recente.

![Stato che mostra lo stato di avvio in sospeso](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine

Avviare lo spostamento come descritto di seguito:

1. In **Tra aree** selezionare il gruppo di risorse di origine > **Avvia spostamento**.
2. in **spostare le risorse**selezionare **Avvia spostamento**. Il gruppo di risorse passa allo stato *Avvio spostamento in corso*.
3. Dopo l'avvio dello spostamento, viene creato il gruppo di risorse di destinazione in base al modello di Resource Manager generato. Il gruppo di risorse di origine passa allo stato *Commit spostamento in sospeso*.

![Stato che mostra lo spostamento del commit](./media/move-region-availability-zone/commit-move-pending.png)

Per eseguire il commit e terminare il processo di spostamento:

1. In **aree diverse**selezionare il gruppo di risorse > **Sposta commit**
2. in **spostare le risorse**selezionare **commit**.

> [!NOTE]
> Dopo il commit dello spostamento il gruppo di risorse di origine si trova nello stato *Eliminazione origine in sospeso*.

## <a name="modify-target-settings"></a>Modificare le impostazioni di destinazione

Se non si desidera spostare una risorsa di origine, è possibile eseguire una delle operazioni seguenti:

- Creare una risorsa nell'area di destinazione con lo stesso nome e le stesse impostazioni della risorsa nell'area di origine.
- Creare una nuova risorsa equivalente nell'area di destinazione. Ad eccezione delle impostazioni specificate, la risorsa di destinazione viene creata con le stesse impostazioni dell'origine.
- Usare una risorsa esistente nell'area di destinazione.

Modificare un'impostazione come indicato di seguito:

1. Per modificare un'impostazione, selezionare la voce nella colonna **configurazione di destinazione** per la risorsa.
2. Nella pagina **Configurazione destinazione** specificare le impostazioni di destinazione che si desidera utilizzare.
    Le modifiche vengono apportate solo per la risorsa che si sta modificando. È necessario aggiornare le risorse dipendenti separatamente.   
    
Le impostazioni esatte modificate dipendono dal tipo di risorsa. [Altre](modify-target-settings.md) informazioni sulla modifica delle impostazioni di destinazione.

## <a name="prepare-resources-to-move"></a>Preparare le risorse da spostare

Dopo aver spostato il gruppo di risorse di origine è possibile preparare lo spostamento delle altre risorse.

1. In **Tra aree** selezionare le risorse da preparare. 

    ![Pagina per selezionare le altre risorse da preparare](./media/move-region-availability-zone/prepare-other.png)

2. Selezionare **Prepara**.

> [!NOTE]
> - Durante il processo di preparazione, l'agente di Azure Site Recovery Mobility viene installato nelle macchine virtuali per la replica.
> - I dati delle macchine virtuali vengono replicati periodicamente nell'area di destinazione. Tale operazione non ha effetto sulla macchina virtuale di origine.
> - Spostamento risorse genera modelli di Resource Manager per le altre risorse di origine.
> - Dopo la preparazione le risorse si trovano nello stato *Avvio spostamento in sospeso*.

![Pagina che mostra le risorse nello stato Avvio spostamento in sospeso](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Avviare lo spostamento

Dopo aver preparato le risorse è ora possibile avviare lo spostamento.

1. In **Tra aree** selezionare le risorse con lo stato *Avvio spostamento in sospeso*. Quindi selezionare **Avvia spostamento**.
2. In **Sposta risorse**selezionare **Avvia spostamento**.

    ![Selezionare per il pulsante Avvia spostamento](./media/move-region-within-resource-group/initiate-move.png)

3. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.


> [!NOTE]
> - Nel caso delle macchine virtuali vengono create macchine virtuali di replica nell'area di destinazione. La macchina virtuale di origine viene arrestata e rimane inattiva per qualche minuto.<br/>
> - Spostamento risorse ricrea altre risorse usando i modelli di Resource Manager che sono stati preparati. Questa operazione non comporta in genere tempi di inattività.<br/> 
> - Per i bilanciamenti del carico, le regole NAT non vengono copiate. Crearli nell'area di destinazione dopo aver eseguito il commit dello spostamento.
> - Per gli indirizzi IP pubblici, l'etichetta del nome DNS non viene copiata. Ricreare l'etichetta dopo aver eseguito il commit dello spostamento.
> - Dopo la preparazione delle risorse, si trovano in uno stato di *spostamento in sospeso di commit* .


## <a name="discard-or-commit"></a>Rimozione o commit?

Dopo lo spostamento iniziale è possibile decidere se si vuole rimuovere lo spostamento o eseguirne il commit. 

- **Rimozione**: è possibile eliminare uno spostamento se si sta eseguendo un test e non si vuole realmente spostare la risorsa di origine. In seguito alla rimozione dello spostamento, la risorsa torna allo stato *Avvio spostamento in sospeso*.
- **Commit**: il commit consente di completare lo spostamento nell'area di destinazione. Dopo il commit una risorsa di origine si troverà nello stato *Eliminazione origine in sospeso* e sarà possibile decidere se eliminarla.


## <a name="discard-the-move"></a>Rimuovere lo spostamento 

È possibile rimuovere lo spostamento come descritto di seguito:

1. In **aree diverse**selezionare risorse con *spostamento commit*stato in sospeso e selezionare **Ignora spostamento**.
2. In Rimuovi **spostamento**selezionare **Ignora**.
3. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.
4. Quando le notifiche indicano che lo spostamento ha avuto esito positivo, selezionare **Aggiorna**. 

> [!NOTE]
> Per le macchine virtuali, dopo l'eliminazione delle risorse, si trovano in uno stato di *spostamento iniziale in sospeso* .

## <a name="commit-the-move"></a>Eseguire il commit dello spostamento

Se si vuole completare il processo di spostamento, eseguire il commit dello spostamento. 


1. In **aree diverse**selezionare risorse con *spostamento commit*stato in sospeso e selezionare **commit sposta**.
2. In **risorse commit**selezionare **commit**.

    ![Pagina per eseguire il commit delle risorse e finalizzare lo spostamento](./media/move-region-within-resource-group/commit-resources.png)

3. Tenere traccia dello stato di avanzamento del commit nella barra delle notifiche.

> [!NOTE]
> - Dopo il commit dello spostamento la replica delle macchine virtuali viene arrestata. Il commit non influisce sulla macchina virtuale di origine.
> - Il commit non influisce sulle risorse di rete di origine.
> - Dopo il commit dello spostamento le risorse si trovano nello stato *Eliminazione origine in sospeso*.

## <a name="configure-settings-after-the-move"></a>Configurare le impostazioni dopo lo spostamento

1. Poiché le etichette dei nomi DNS non vengono copiate per gli indirizzi IP pubblici, al termine dello spostamento passare alle risorse di destinazione e aggiornare l'etichetta. 
2. Per i servizio di bilanciamento del carico interno, poiché le regole NAT non vengono copiate, passare alle risorse create nell'area di destinazione e aggiornare le regole NAT.
3. Il servizio di mobilità non viene disinstallato automaticamente dalle macchine virtuali. Disinstallarlo manualmente oppure lasciarlo installato se si prevede di spostare nuovamente il server.
## <a name="delete-source-resources-after-commit"></a>Eliminare le risorse di origine dopo il commit

Facoltativamente, dopo lo spostamento è possibile eliminare le risorse nell'area di origine. 

1. In **aree diverse**selezionare il nome di ogni risorsa di origine che si desidera eliminare.
2. Nella pagina delle proprietà di ogni risorsa selezionare **Elimina**.

## <a name="delete-additional-resources-created-for-move"></a>Eliminare le risorse aggiuntive create per lo spostamento

Dopo lo spostamento è possibile eliminare manualmente la raccolta di spostamento e le risorse di Site Recovery create.

- La raccolta di spostamento è nascosta per impostazione predefinita. Per visualizzarla, è necessario attivare le risorse nascoste.
- Prima di eliminare l'archiviazione cache è necessario eliminare il relativo blocco.

La procedura di eliminazione è la seguente: 

1. Individuare le risorse nel gruppo ```RegionMoveRG-<sourceregion>-<target-region>``` di risorse nell'area di origine.
2. Verificare che tutte le macchine virtuali e le altre risorse di origine nella raccolta di spostamento siano state spostate o eliminate. per assicurarsi che non vi siano risorse in sospeso che le usano.
2. Eliminare le risorse seguenti:

    - Il nome della raccolta di spostamento è ```movecollection-<sourceregion>-<target-region>```.
    - Il nome dell'account di archiviazione cache è ```resmovecache<guid>```.
    - Il nome dell'insieme di credenziali è ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Passaggi successivi


Informazioni [sul processo di](about-move-process.md) spostamento.
