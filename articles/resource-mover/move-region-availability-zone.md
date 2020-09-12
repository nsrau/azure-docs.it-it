---
title: Spostare le macchine virtuali di Azure in zone di disponibilità in un'altra area con Azure Resource Mover
description: Informazioni su come spostare le macchine virtuali di Azure in zone di disponibilità con il motore di risorse di Azure.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: fdd564618232ce7fde5a76fb9c37937113f179b2
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670622"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Spostare le macchine virtuali di Azure in una zona di disponibilità in un'altra area

Questo articolo illustra come spostare le VM di Azure (e le risorse di rete/archiviazione correlate) in una zona di disponibilità in un'area di Azure diversa, usando il [motore di risorse di Azure](overview.md).

Le [zone di disponibilità di Azure](../availability-zones/az-overview.md#availability-zones) consentono di proteggere la distribuzione di Azure da errori dei data center. Ogni zona di disponibilità è costituita da uno o più Data Center dotati di impianti indipendenti per l'energia, il raffreddamento e la rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le [aree abilitate](../availability-zones/az-region.md). Con il motore di risorse è possibile spostare:

- Una macchina virtuale a istanza singola per una zona di disponibilità o un set di disponibilità nell'area di destinazione.
- Una macchina virtuale in un set di disponibilità a una zona di disponibilità o un set di disponibilità nell'area di destinazione.
- Una macchina virtuale in una zona di disponibilità dell'area di origine a una zona di disponibilità nell'area di destinazione.


> [!IMPORTANT]
> Il motore di risorse di Azure è attualmente disponibile in anteprima pubblica.

Se si vuole spostare le macchine virtuali in una zona di disponibilità diversa nella stessa area, [vedere questo articolo](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Prerequisiti

- Accesso *proprietario* per la sottoscrizione in cui si trovano le risorse che si desidera spostare.
    - La prima volta che si aggiunge una risorsa per un mapping di origine e destinazione specifico in una sottoscrizione di Azure, il motore risorse crea un' [identità gestita assegnata dal sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (precedentemente nota come identità del servizio gestito) considerata attendibile dalla sottoscrizione.
    - Per creare l'identità e assegnarle il ruolo richiesto (collaboratore o amministratore accesso utenti nella sottoscrizione di origine), l'account usato per aggiungere risorse richiede le autorizzazioni di *proprietario* per la sottoscrizione. [Altre](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) informazioni sui ruoli di Azure.
- La sottoscrizione richiede una quota sufficiente per creare le risorse di origine nell'area di destinazione. In caso contrario, richiedere limiti aggiuntivi. [Altre informazioni](/azure/azure-resource-manager/management/azure-subscription-service-limits).
- Verificare i prezzi e gli addebiti associati all'area di destinazione in cui si stanno spostandoli le VM. Usa il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) per aiutarti.
    


## <a name="check-vm-requirements"></a>Verificare i requisiti delle macchine virtuali

1. Verificare che le macchine virtuali da spostare siano supportate.

    - [Verificare](support-matrix-move-region-azure-vm.md#windows-vm-support) le VM Windows supportate.
    - [Verificare](support-matrix-move-region-azure-vm.md#linux-vm-support) le VM Linux e le versioni del kernel supportate.
    - Controllare le impostazioni di [calcolo](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [archiviazione](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rete](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) supportate.
2. Verificare che le macchine virtuali che si desidera spostare siano attivate.
3. Verificare che le macchine virtuali dispongano dei certificati radice trusted più recenti e di un elenco di revoche di certificati (CRL) aggiornato. 
    - Nelle VM di Azure che eseguono Windows installare gli aggiornamenti di Windows più recenti.
    - Nelle macchine virtuali che eseguono Linux, seguire le indicazioni per il server di distribuzione Linux per assicurarsi che il computer disponga dei certificati più recenti e del CRL. 
4. Consenti connettività in uscita dalle macchine virtuali:
    - Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso a questi [URL](support-matrix-move-region-azure-vm.md#url-access)
    - Se si usano regole del gruppo di sicurezza di rete (NSG) per controllare la connettività in uscita, creare queste [regole dei tag del servizio](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selezionare le risorse da spostare

Selezionare le risorse che si desidera spostare.

- È possibile selezionare qualsiasi tipo di risorsa supportato tra i gruppi di risorse nell'area di origine selezionata.
- Si spostano le risorse in un'area di destinazione nella sottoscrizione dell'area di origine. Se si desidera modificare la sottoscrizione, è possibile eseguire questa operazione dopo lo spostamento delle risorse.

1. Nella portale di Azure cercare Resource Mover. Quindi, in **Servizi**, selezionare **Azure Resource Mover**.

    ![Cercare il motore delle risorse](./media/move-region-availability-zone/search.png)

2. In **Panoramica**selezionare **inizia**.

    ![Pulsante per iniziare](./media/move-region-availability-zone/get-started.png)

3. In **Move Sources**  >  **Source + Destination**selezionare la sottoscrizione e l'area di origine.
4. In **destinazione**selezionare l'area in cui si desidera spostare le macchine virtuali. 
5. In **area metadati**selezionare la posizione in cui si vogliono archiviare i metadati relativi alle risorse che si stanno muovendo. Un gruppo di risorse viene creato in modo specifico a questo scopo. Quindi fare clic su **Next**.

     ![Pagina per compilare l'area/sottoscrizione di origine e di destinazione](./media/move-region-availability-zone/source-target.png)

6. In **risorse da spostare**fare clic su **Seleziona risorse**.
7. In **Seleziona risorse**selezionare la macchina virtuale. È possibile aggiungere solo le risorse supportate per lo spostamento. Quindi fare clic su **fine**. In **risorse da spostare**fare clic su **Avanti**.

    ![Pagina per selezionare le macchine virtuali da spostare](./media/move-region-availability-zone/select-vm.png)
8. In verifica **+ Aggiungi**, controllare le impostazioni di origine e di destinazione.

    ![Pagina per rivedere le impostazioni e procedere con lo spostamento](./media/move-region-availability-zone/review.png)

9. Fare clic su **continua**per iniziare ad aggiungere le risorse.
10. Una volta completato il processo di aggiunta, fare clic su **aggiunta di risorse per sposta** nell'icona di notifica.

    ![Messaggio nelle notifiche](./media/move-region-availability-zone/notification.png)

Dopo aver fatto clic sulla notifica, le risorse vengono visualizzate nella pagina **tra le aree**

> [!NOTE]
> Dopo aver fatto clic sulla notifica, le risorse vengono visualizzate nella pagina **tra aree** , in uno stato di *preparazione in sospeso* .
> - Se si vuole rimuovere una risorsa da una raccolta di spostamento, il metodo per eseguire questa operazione dipende dalla posizione in cui si trova il processo di spostamento. [Altre informazioni](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Risolvere gli errori relativi alle risorse di Azure non trovate

1. Se le risorse mostrano un messaggio di *convalida delle dipendenze* nella colonna **problemi** , fare clic sul pulsante **convalida dipendenze** . Gli esseri del processo di convalida.
2. Se vengono trovate dipendenze, fare clic su **Aggiungi dipendenze**. 
3. In **Aggiungi dipendenze**selezionare le risorse dipendenti > **Aggiungi dipendenze**. Monitorare lo stato di avanzamento nelle notifiche.

    ![Pulsante per aggiungere dipendenze](./media/move-region-availability-zone/add-dependencies.png)

3. Aggiungere altre dipendenze, se necessario, e convalidare di nuovo le dipendenze. 

    ![Pagina per l'aggiunta di dipendenze aggiuntive](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Nella pagina **tra le aree** verificare che le risorse siano ora in stato di *preparazione in sospeso* , senza problemi.

    ![Pagina che mostra le risorse in stato di preparazione in sospeso](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine 

Prima di poter preparare e spostare le macchine virtuali, il gruppo di risorse di origine deve essere presente nell'area di destinazione. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparare lo spostamento del gruppo di risorse di origine

Preparare come segue:

1. In **aree diverse**selezionare il gruppo di risorse di origine > **preparare**.
2. In **preparare le risorse**fare clic su **prepara**.

    ![Pulsante per preparare il gruppo di risorse di origine](./media/move-region-availability-zone/prepare-resource-group.png)

    Durante il processo di preparazione, il motore delle risorse genera modelli di Azure Resource Manager (ARM) usando le impostazioni del gruppo di risorse. Le risorse all'interno del gruppo di risorse non sono interessate.

> [!NOTE]
>  Dopo aver preparato il gruppo di risorse, è nello stato di *spostamento iniziale in sospeso* . 

![Stato che mostra lo stato di avvio in sospeso](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Spostare il gruppo di risorse di origine

Avviare lo spostamento come segue:

1. In **aree diverse**selezionare il gruppo di risorse > **avviare lo spostamento**
2. in **spostare le risorse**fare clic su **Avvia spostamento**. Il gruppo di risorse si sposta in uno stato *di avanzamento del processo di avvio* .
3. Dopo l'avvio dello spostamento, viene creato il gruppo di risorse di destinazione, in base al modello ARM generato. Il gruppo di risorse di origine si sposta in uno stato di *spostamento in sospeso di commit* .

![Stato che mostra lo spostamento del commit](./media/move-region-availability-zone/commit-move-pending.png)

Per eseguire il commit e terminare il processo di spostamento:

1. In **aree diverse**selezionare il gruppo di risorse > **Sposta commit**
2. in **spostare le risorse**fare clic su **commit**.

> [!NOTE]
> Dopo aver eseguito il commit dello spostamento, il gruppo di risorse di origine si trova in uno stato di *eliminazione origine in sospeso* .


## <a name="add-a-target-availability-zone"></a>Aggiungere una zona di disponibilità di destinazione

Prima di spostare il resto delle risorse, si imposterà una zona di disponibilità di destinazione per la macchina virtuale.

1. Nella pagina **tra aree** fare clic sul collegamento nella colonna **configurazione di destinazione** della macchina virtuale che si sta muovendo.

    ![Proprietà VM](./media/move-region-availability-zone/select-vm-settings.png)

3. In **impostazioni di configurazione**specificare l'impostazione per la macchina virtuale di destinazione. È possibile configurare una macchina virtuale nell'area di destinazione nel modo seguente:
    -  Creare una nuova risorsa equivalente nell'area di destinazione. Ad eccezione delle impostazioni specificate, la risorsa di destinazione viene creata con le stesse impostazioni dell'origine.
    - Selezionare una macchina virtuale esistente nell'area di destinazione. 

4. In **zone**selezionare la zona in cui si desidera posizionare la VM quando viene spostata.

    Le modifiche vengono apportate solo per la risorsa che si sta modificando. È necessario aggiornare le risorse dipendenti separatamente.

5. In **SKU**specificare il [livello di Azure](..//virtual-machines/sizes.md) che si vuole assegnare alla macchina virtuale di destinazione.
6. In **set di disponibilità**selezionare un set di disponibilità se si vuole che la macchina virtuale di destinazione venga eseguita all'interno di un set di disponibilità nella zona di disponibilità.
7. Selezionare **Save changes** (Salva modifiche).

    ![Impostazioni della VM](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>Preparare le risorse per lo spostamento

Ora che il gruppo di risorse di origine è stato spostato, è possibile prepararsi a spostare le altre risorse.

1. In **aree diverse**selezionare le risorse che si desidera preparare. 

    ![Pagina per selezionare prepara per altre risorse](./media/move-region-availability-zone/prepare-other.png)

2. Selezionare **prepara**. 

> [!NOTE]
> - Durante il processo di preparazione, l'agente di Azure Site Recovery Mobility viene installato nelle macchine virtuali per la replica.
> - I dati della VM vengono replicati periodicamente nell'area di destinazione. Questa operazione non influisce sulla VM di origine.
> - Lo spostamento delle risorse genera modelli ARM per le altre risorse di origine.
> - Dopo la preparazione delle risorse, si trovano in uno stato di *spostamento iniziale in sospeso* .

![Pagina che mostra le risorse nello stato di inizio spostamento in sospeso](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Avviare lo spostamento

Con le risorse preparate, è ora possibile avviare lo spostamento. 

1. In **aree diverse**selezionare risorse con *spostamento avvio*stato in sospeso. Quindi fare clic su **Avvia spostamento**
2. In **Sposta risorse**fare clic su **Avvia spostamento**.

    ![Pagina per avviare lo spostamento delle risorse](./media/move-region-availability-zone/initiate-move.png)

3. Tenere traccia dello stato di avanzamento nella barra di notifica.


> [!NOTE]
> - Per le macchine virtuali, le macchine virtuali di replica vengono create nell'area di destinazione. La macchina virtuale di origine viene arrestata e si verifica un tempo di inattività (in genere minuti).
> - Il motore risorse ricrea altre risorse usando i modelli ARM preparati. In genere non si verifica alcun tempo di inattività.
> - Dopo la preparazione delle risorse, si trovano in uno stato di *spostamento in sospeso di commit* .


![Pagina per visualizzare le risorse in uno stato di spostamento in sospeso del commit](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>Rimuovere o eseguire il commit?

Dopo lo spostamento iniziale, è possibile decidere se si desidera eseguire il commit dello spostamento o per ignorarlo. 

- **Scarto**: è possibile eliminare uno spostamento se si sta eseguendo il test e non si vuole spostare effettivamente la risorsa di origine. Se si ignora lo spostamento, la risorsa viene restituita a uno stato di *spostamento di avvio in sospeso*.
- **Commit**: il commit completa lo spostamento nell'area di destinazione. Dopo il commit, una risorsa di origine si troverà in uno stato di *eliminazione origine in sospeso*ed è possibile decidere se eliminarla.

## <a name="discard-the-move"></a>Ignora lo spostamento 

È possibile rimuovere lo spostamento come indicato di seguito:

1. In **aree diverse**selezionare risorse con *spostamento commit*stato in sospeso, quindi fare clic su **Ignora spostamento**.
2. In Rimuovi **spostamento**fare clic su **Ignora**.
3. Tenere traccia dello stato di avanzamento nella barra di notifica.
 

> [!NOTE]
> Per le macchine virtuali, dopo l'eliminazione delle risorse, si trovano in uno stato di *spostamento iniziale in sospeso* .

## <a name="commit-the-move"></a>Esegui commit dello spostamento

Se si desidera completare il processo di spostamento, eseguire il commit dello spostamento. 

1. In **aree diverse**selezionare risorse con *spostamento commit*stato in sospeso, quindi fare clic su **commit sposta**.
2. In **risorse commit**fare clic su **commit**.

    ![Pagina per eseguire il commit delle risorse per finalizzare lo spostamento](./media/move-region-availability-zone/commit-resources.png)

3. Tenere traccia dello stato di avanzamento del commit nella barra notifiche.

> [!NOTE]
> - Dopo aver eseguito il commit dello spostamento, le VM interrompono la replica. Il commit non è influenzato dalla macchina virtuale di origine.
> - Il commit non influisca sulle risorse di rete di origine.
> - Dopo il commit dello spostamento, le risorse si trovano in uno stato di *eliminazione origine in sospeso* .

![Pagina in cui sono visualizzate le risorse in * Delete source Pending * state](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Configurare le impostazioni dopo lo spostamento

Il servizio Mobility non viene disinstallato automaticamente dalle macchine virtuali. Disinstallarlo manualmente oppure lasciarlo se si prevede di spostare nuovamente il server.


## <a name="delete-source-resources-after-commit"></a>Elimina risorse di origine dopo il commit

Dopo lo spostamento, è possibile eliminare facoltativamente le risorse nell'area di origine.

1. In **aree diverse**fare clic sul nome di ogni risorsa di origine che si desidera eliminare.
2. Nella pagina proprietà per ogni risorsa selezionare **Elimina**.

## <a name="delete-additional-resources-created-for-move"></a>Elimina risorse aggiuntive create per lo spostamento

Dopo lo spostamento, è possibile eliminare manualmente la raccolta di spostamento e Site Recovery risorse create.

- La raccolta di spostamento è nascosta per impostazione predefinita. Per visualizzarlo, è necessario attivare le risorse nascoste.
- Per poter eliminare l'archiviazione della cache è necessario eliminare un blocco.

Elimina come segue: 

1. Individuare le risorse nel gruppo di risorse ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Verificare che tutte le macchine virtuali e le altre risorse di origine nell'area di origine siano state spostate o eliminate. In questo modo si garantisce che non vi siano risorse in sospeso che le utilizzano.
2. Eliminare le risorse:

    - Il nome della raccolta di spostamento è ```movecollection-<sourceregion>-<target-region>``` .
    - Il nome dell'account di archiviazione della cache è ```resmovecache<guid>```
    - Il nome dell'insieme di credenziali è ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Passaggi successivi

Informazioni [sul processo di](about-move-process.md) spostamento.
