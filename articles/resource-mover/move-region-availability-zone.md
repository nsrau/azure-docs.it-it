---
title: Spostare le macchine virtuali di Azure in zone di disponibilità in un'altra area con Azure Resource Mover
description: Informazioni su come spostare le macchine virtuali di Azure in zone di disponibilità con il motore di risorse di Azure.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543001"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Spostare le macchine virtuali di Azure in una zona di disponibilità in un'altra area

Questo articolo illustra come spostare le VM di Azure (e le risorse di rete/archiviazione correlate) in una zona di disponibilità in un'area di Azure diversa, usando il [motore di risorse di Azure](overview.md).

Le [zone di disponibilità di Azure](../availability-zones/az-overview.md#availability-zones) consentono di proteggere la distribuzione di Azure da errori dei data center. Ogni zona di disponibilità è costituita da uno o più Data Center dotati di impianti indipendenti per l'energia, il raffreddamento e la rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le [aree abilitate](../availability-zones/az-region.md). Con il motore di risorse è possibile spostare:

- Una macchina virtuale a istanza singola per una zona di disponibilità o un set di disponibilità nell'area di destinazione.
- Una macchina virtuale in un set di disponibilità a una zona di disponibilità o un set di disponibilità nell'area di destinazione.
- Una macchina virtuale in una zona di disponibilità dell'area di origine a una zona di disponibilità nell'area di destinazione.


> [!IMPORTANT]
> Spostamento risorse di Azure è attualmente disponibile in anteprima pubblica.

Se si vuole spostare le macchine virtuali in una zona di disponibilità diversa nella stessa area, [vedere questo articolo](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Prerequisiti

- Accesso *proprietario* per la sottoscrizione in cui si trovano le risorse che si desidera spostare.
    - La prima volta che si aggiunge una risorsa per un mapping di origine e destinazione specifico in una sottoscrizione di Azure, il motore risorse crea un' [identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (precedentemente nota come identità del servizio gestito) considerata attendibile dalla sottoscrizione.
    - Per creare l'identità e assegnarle il ruolo richiesto (Collaboratore o Amministratore Accesso utenti nella sottoscrizione di origine), l'account usato per aggiungere le risorse deve avere le autorizzazioni di *Proprietario* nella sottoscrizione. [Altre informazioni](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sui ruoli di Azure.
- La sottoscrizione richiede una quota sufficiente per creare le risorse di origine nell'area di destinazione. In caso contrario, richiedere limiti aggiuntivi. [Altre informazioni](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Verificare i prezzi e gli addebiti associati all'area di destinazione in cui si intende spostare le macchine virtuali. Per facilitare l'operazione, usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/).
    


## <a name="check-vm-requirements"></a>Controllare i requisiti delle macchine virtuali

1. Controllare che le macchine virtuali da spostare siano supportate.

    - [Verificare](support-matrix-move-region-azure-vm.md#windows-vm-support) le macchine virtuali Windows supportate.
    - [Verificare](support-matrix-move-region-azure-vm.md#linux-vm-support) le macchine virtuali Linux e le versioni del kernel supportate.
    - Controllare le impostazioni di [calcolo](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [archiviazione](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) e [rete](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) supportate.
2. Controllare che le macchine virtuali da spostare siano accese.
3. Verificare che le macchine virtuali dispongano dei certificati radice trusted più recenti e di un elenco di revoche di certificati (CRL) aggiornato. 
    - Nelle VM di Azure che eseguono Windows installare gli aggiornamenti di Windows più recenti.
    - Nelle macchine virtuali che eseguono Linux, seguire le indicazioni per il server di distribuzione Linux per assicurarsi che il computer disponga dei certificati più recenti e del CRL. 
4. Consentire la connettività in uscita dalle macchine virtuali:
    - Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso a questi [URL](support-matrix-move-region-azure-vm.md#url-access)
    - Se si usano le regole del gruppo di sicurezza di rete per controllare la connettività in uscita, creare queste [regole del tag di servizio](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selezionare le risorse da spostare

Selezionare le risorse da spostare.

- È possibile selezionare qualsiasi tipo di risorsa supportato tra i gruppi di risorse nell'area di origine selezionata.
- Si spostano le risorse in un'area di destinazione nella sottoscrizione dell'area di origine. Se si vuole cambiare la sottoscrizione, è possibile eseguire questa operazione dopo lo spostamento delle risorse.

1. Nel portale di Azure cercare spostamento risorse. In **Servizi** selezionare quindi **Spostamento risorse di Azure**.

    ![Cercare il motore delle risorse](./media/move-region-availability-zone/search.png)

2. In **Panoramica** selezionare **inizia**.

    ![Pulsante per iniziare](./media/move-region-availability-zone/get-started.png)

3. In **Sposta risorse** > **Origine e destinazione** selezionare l'area e la sottoscrizione di origine.
4. In **Destinazione** selezionare l'area in cui spostare le macchine virtuali. Quindi fare clic su **Next**.

     ![Pagina per compilare l'area/sottoscrizione di origine e di destinazione](./media/move-region-availability-zone/source-target.png)

6. In **Risorse da spostare** fare clic su **Selezionare le risorse**.
7. In **Selezionare le risorse** selezionare la macchina virtuale. È possibile aggiungere solo le risorse supportate per lo spostamento. Fare quindi clic su **Done**. In **Risorse da spostare** fare clic su **Avanti**.

    ![Pagina per selezionare le macchine virtuali da spostare](./media/move-region-availability-zone/select-vm.png)
8. In **Rivedi e aggiungi** verificare le impostazioni relative a origine e destinazione.

    ![Pagina per rivedere le impostazioni e procedere allo spostamento](./media/move-region-availability-zone/review.png)

9. Per iniziare ad aggiungere le risorse, fare clic su **Continua**.
10. Dopo il completamento del processo di aggiunta fare clic su **Aggiunta di risorse per lo spostamento** nell'icona di notifica.

    ![Messaggio nelle notifiche](./media/move-region-availability-zone/notification.png)

Dopo aver fatto clic sulla notifica, le risorse vengono visualizzate nella pagina **tra le aree**

> [!NOTE]
> Dopo aver fatto clic sulla notifica, le risorse vengono visualizzate nella pagina **tra aree** , in uno stato di *preparazione in sospeso* .
> - Il metodo per rimuovere una risorsa da una raccolta di spostamento dipende da punto in cui ci si trova nel processo di spostamento. [Altre informazioni](remove-move-resources.md)

## <a name="resolve-dependencies"></a>Risolvere gli errori relativi alle risorse di Azure non trovate

1. Se per le risorse viene visualizzato un messaggio *Convalida dipendenze* nella colonna **Problemi**, fare clic sul pulsante **Convalida dipendenze**. Gli esseri del processo di convalida.
2. Se vengono trovate dipendenze, fare clic su **Aggiungi dipendenze**. 
3. In **Aggiungi dipendenze** selezionare le risorse dipendenti > **Aggiungi dipendenze**. Monitorare lo stato di avanzamento nelle notifiche.

    ![Pulsante per aggiungere le dipendenze](./media/move-region-availability-zone/add-dependencies.png)

3. Se necessario, aggiungere altre dipendenze e convalidare di nuovo le dipendenze. 

    ![Pagina per aggiungere altre dipendenze](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Nella pagina **Tra aree** verificare che le risorse si trovino ora in uno stato *Preparazione in sospeso* e che non vengano segnalati problemi.

    ![Pagina che mostra le risorse nello stato Preparazione in sospeso](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine 

Prima di poter preparare e spostare le macchine virtuali, il gruppo di risorse di origine deve essere presente nell'area di destinazione. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparare lo spostamento del gruppo di risorse di origine

Eseguire la preparazione nel modo seguente:

1. In **Tra aree** selezionare il gruppo di risorse di origine > **Prepara**.
2. In **Prepara risorse** fare clic su **Prepara**.

    ![Pulsante per preparare il gruppo di risorse di origine](./media/move-region-availability-zone/prepare-resource-group.png)

    Durante il processo di preparazione Spostamento risorse genera modelli di Azure Resource Manager usando le impostazioni del gruppo di risorse. Le risorse all'interno di un gruppo di risorse non sono interessate.

> [!NOTE]
>  Dopo la preparazione il gruppo di risorse si trova nello stato *Avvio spostamento in sospeso*. 

![Stato che mostra lo stato di avvio in sospeso](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine

Avviare lo spostamento come descritto di seguito:

1. In **Tra aree** selezionare il gruppo di risorse di origine > **Avvia spostamento**.
2. In **Sposta risorse** fare clic su **Avvia spostamento**. Il gruppo di risorse passa allo stato *Avvio spostamento in corso*.
3. Dopo l'avvio dello spostamento, viene creato il gruppo di risorse di destinazione in base al modello di Resource Manager generato. Il gruppo di risorse di origine passa allo stato *Commit spostamento in sospeso*.

![Stato che mostra lo spostamento del commit](./media/move-region-availability-zone/commit-move-pending.png)

Per eseguire il commit e terminare il processo di spostamento:

1. In **aree diverse** selezionare il gruppo di risorse > **Sposta commit**
2. In **Sposta risorse** fare clic su **Commit**.

> [!NOTE]
> Dopo il commit dello spostamento il gruppo di risorse di origine si trova nello stato *Eliminazione origine in sospeso*.


## <a name="add-a-target-availability-zone"></a>Aggiungere una zona di disponibilità di destinazione

Prima di spostare il resto delle risorse, si imposterà una zona di disponibilità di destinazione per la macchina virtuale.

1. Nella pagina **tra aree** fare clic sul collegamento nella colonna **configurazione di destinazione** della macchina virtuale che si sta muovendo.

    ![Proprietà VM](./media/move-region-availability-zone/select-vm-settings.png)

3. In **impostazioni di configurazione** specificare l'impostazione per la macchina virtuale di destinazione. È possibile configurare una macchina virtuale nell'area di destinazione nel modo seguente:
    -  Creare una nuova risorsa equivalente nell'area di destinazione. Ad eccezione delle impostazioni specificate, la risorsa di destinazione viene creata con le stesse impostazioni dell'origine.
    - Selezionare una macchina virtuale esistente nell'area di destinazione. 

4. In **zone** selezionare la zona in cui si desidera posizionare la VM quando viene spostata.

    Le modifiche vengono apportate solo per la risorsa che si sta modificando. È necessario aggiornare le risorse dipendenti separatamente.

5. In **SKU** specificare il [livello di Azure](..//virtual-machines/sizes.md) che si vuole assegnare alla macchina virtuale di destinazione.
6. In **set di disponibilità** selezionare un set di disponibilità se si vuole che la macchina virtuale di destinazione venga eseguita all'interno di un set di disponibilità nella zona di disponibilità.
7. Selezionare **Save changes** (Salva modifiche).

    ![Impostazioni della VM](./media/move-region-availability-zone/vm-settings.png)


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

1. In **Tra aree** selezionare le risorse con lo stato *Avvio spostamento in sospeso*. Quindi fare clic su **Avvia spostamento**
2. In **Sposta risorse** fare clic su **Avvia spostamento**.

    ![Pagina per avviare lo spostamento delle risorse](./media/move-region-availability-zone/initiate-move.png)

3. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.


> [!NOTE]
> - Nel caso delle macchine virtuali vengono create macchine virtuali di replica nell'area di destinazione. La macchina virtuale di origine viene arrestata e rimane inattiva per qualche minuto.
> - Spostamento risorse ricrea altre risorse usando i modelli di Resource Manager che sono stati preparati. Questa operazione non comporta in genere tempi di inattività.
> - Dopo la preparazione delle risorse, si trovano in uno stato di *spostamento in sospeso di commit* .


![Pagina per visualizzare le risorse in uno stato di spostamento in sospeso del commit](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>Rimozione o commit?

Dopo lo spostamento iniziale è possibile decidere se si vuole rimuovere lo spostamento o eseguirne il commit. 

- **Rimozione**: è possibile eliminare uno spostamento se si sta eseguendo un test e non si vuole realmente spostare la risorsa di origine. In seguito alla rimozione dello spostamento, la risorsa torna allo stato *Avvio spostamento in sospeso*.
- **Commit**: il commit consente di completare lo spostamento nell'area di destinazione. Dopo il commit una risorsa di origine si troverà nello stato *Eliminazione origine in sospeso* e sarà possibile decidere se eliminarla.

## <a name="discard-the-move"></a>Rimuovere lo spostamento 

È possibile rimuovere lo spostamento come descritto di seguito:

1. In **Tra aree** selezionare le risorse con lo stato *Commit spostamento in sospeso* e fare clic su **Rimuovi spostamento**.
2. In **Rimuovi spostamento** fare clic su **Rimuovi**.
3. Tenere traccia dello stato di avanzamento dello spostamento nella barra delle notifiche.
 

> [!NOTE]
> Per le macchine virtuali, dopo l'eliminazione delle risorse, si trovano in uno stato di *spostamento iniziale in sospeso* .

## <a name="commit-the-move"></a>Eseguire il commit dello spostamento

Se si vuole completare il processo di spostamento, eseguire il commit dello spostamento. 

1. In **Tra aree** selezionare le risorse con lo stato *Commit spostamento in sospeso* e fare clic su **Commit spostamento**.
2. In **Commit delle risorse** fare clic su **Commit**.

    ![Pagina per eseguire il commit delle risorse e finalizzare lo spostamento](./media/move-region-availability-zone/commit-resources.png)

3. Tenere traccia dello stato di avanzamento del commit nella barra delle notifiche.

> [!NOTE]
> - Dopo il commit dello spostamento la replica delle macchine virtuali viene arrestata. Il commit non influisce sulla macchina virtuale di origine.
> - Il commit non influisce sulle risorse di rete di origine.
> - Dopo il commit dello spostamento le risorse si trovano nello stato *Eliminazione origine in sospeso*.

![Pagina che mostra le risorse nello stato *Eliminazione origine in sospeso*](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Configurare le impostazioni dopo lo spostamento

Il servizio di mobilità non viene disinstallato automaticamente dalle macchine virtuali. Disinstallarlo manualmente oppure lasciarlo installato se si prevede di spostare nuovamente il server.


## <a name="delete-source-resources-after-commit"></a>Eliminare le risorse di origine dopo il commit

Facoltativamente, dopo lo spostamento è possibile eliminare le risorse nell'area di origine.

1. In **Tra aree** fare clic sul nome di ogni risorsa di origine da eliminare.
2. Nella pagina delle proprietà di ogni risorsa selezionare **Elimina**.

## <a name="delete-additional-resources-created-for-move"></a>Eliminare le risorse aggiuntive create per lo spostamento

Dopo lo spostamento è possibile eliminare manualmente la raccolta di spostamento e le risorse di Site Recovery create.

- La raccolta di spostamento è nascosta per impostazione predefinita. Per visualizzarla, è necessario attivare le risorse nascoste.
- Prima di eliminare l'archiviazione cache è necessario eliminare il relativo blocco.

La procedura di eliminazione è la seguente: 

1. Individuare le risorse nel gruppo di risorse ```RegionMoveRG-<sourceregion>-<target-region>```.
2. Controllare che tutte le macchine virtuali e le altre risorse di origine nell'area di origine siano state spostate o eliminate, per assicurarsi che non vi siano risorse in sospeso che le usano.
2. Eliminare le risorse seguenti:

    - Il nome della raccolta di spostamento è ```movecollection-<sourceregion>-<target-region>```.
    - Il nome dell'account di archiviazione cache è ```resmovecache<guid>```.
    - Il nome dell'insieme di credenziali è ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Passaggi successivi

Informazioni [sul processo di](about-move-process.md) spostamento.