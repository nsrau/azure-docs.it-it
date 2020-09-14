---
title: StorSimple Snapshot Manager gruppi di volume | Microsoft Docs
description: Viene descritto come utilizzare lo snap-in MMC StorSimple Snapshot Manager per creare e gestire i gruppi di volumi.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 805ab0e78f0679ce483af3a73203b41132be2a59
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054773"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Usare StorSimple Snapshot Manager per creare e gestire gruppi di volumi
## <a name="overview"></a>Panoramica
È possibile usare il nodo **Gruppi di volumi** nel riquadro **Ambito** per assegnare volumi a gruppi di volumi, visualizzare informazioni su un gruppo di volumi, pianificare backup e modificare gruppi di volumi.

I gruppi di volumi sono pool di volumi correlati utilizzati per garantire che i backup siano coerenti con l'applicazione. Per altre informazioni, vedere [Volumi e gruppi di volumi](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [Integrazione con il servizio Copia Shadow del volume di Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Tutti i volumi in un gruppo di volumi devono provenire da un singolo provider di servizi cloud.
> * Quando si configurano gruppi di volumi, non combinare volumi condivisi cluster (CSV) e volumi non condivisi cluster nello stesso gruppo di volumi. StorSimple Snapshot Manager non supporta una combinazione di volumi condivisi cluster e volumi non condivisi cluster nello stesso snapshot.

![Nodo Gruppi di volumi](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: Nodo Gruppi di volumi di StorSimple Snapshot Manager** 

Questa esercitazione illustra come usare StorSimple Snapshot Manager per:

* Visualizzare le informazioni sui gruppi di volumi
* Creare un gruppo di volumi
* Eseguire il backup di un gruppo di volumi
* Modificare un gruppo di volumi
* Eliminare un gruppo di volumi

Tutte queste azioni sono disponibili anche nel riquadro **Azioni** .

## <a name="view-volume-groups"></a>Visualizzare i gruppi di volumi
Se si fa clic sul nodo **Gruppi di volumi**, nel riquadro **Risultati** vengono mostrate le informazioni seguenti su ciascun gruppo di volumi, a seconda delle colonne selezionate. Le colonne nel riquadro **risultati** sono configurabili. Fare clic con il pulsante destro del mouse sul nodo **Volumi**, selezionare **Visualizza**, quindi scegliere **Aggiungi/Rimuovi colonne**.

| Colonna risultati | Descrizione |
|:--- |:--- |
| Nome |La colonna **Nome** contiene il nome del gruppo di volumi. |
| Applicazione |La colonna **Applicazioni** mostra il numero di writer VSS attualmente installati e in esecuzione nell'host Windows. |
| Opzione selezionata |La colonna **Selezionato** mostra il numero di volumi contenuti nel gruppo di volumi. Zero (0) indica che nessuna applicazione è associata ai volumi nel gruppo di volumi. |
| Importati |La colonna **Importato** mostra il numero di volumi importati. Se impostata su **True**, questa colonna indica che un gruppo di volumi è stato importato dal portale di Azure e non è stato creato in StorSimple Snapshot Manager. |

> [!NOTE]
> I gruppi di volumi di StorSimple Snapshot Manager vengono inoltre visualizzati nella scheda **Criteri di backup** del portale di Azure.
> 
> 

## <a name="create-a-volume-group"></a>Creare un gruppo di volumi
Utilizzare la procedura seguente per creare un gruppo di volumi.

#### <a name="to-create-a-volume-group"></a>Per creare un gruppo di volumi
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su **Gruppi di volumi**, quindi scegliere **Crea gruppo di volumi**.
   
    ![Crea gruppo di volumi](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Viene visualizzata la finestra di dialogo **Crea un gruppo di volumi**.
   
    ![Finestra di dialogo Crea un gruppo di volumi](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Immettere le informazioni seguenti:
   
   1. Nella casella **Nome** digitare un nome univoco per il nuovo gruppo di volumi.
   2. Nella casella **Applicazioni** selezionare le applicazioni associate ai volumi che verranno aggiunte al gruppo di volumi.
      
       Nella casella **Applicazioni** vengono elencate solo le applicazioni che usano volumi StorSimple e per le quali è abilitato il componente VSS writer. VSS writer è abilitato solo se tutti i volumi di cui è a conoscenza sono volumi StorSimple. Se la casella Applicazioni è vuota, non è stata installata alcuna applicazione che utilizza volumi StorSimple di Azure e che dispone di writer del Servizio snapshot del volume. Attualmente, Azure StorSimple supporta Microsoft Exchange e SQL Server. Per ulteriori informazioni sui writer VSS, vedere [integrazione con Windows servizio Copia Shadow del volume](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Se si seleziona un'applicazione, vengono selezionati automaticamente tutti i volumi associati ad essa. Viceversa, se si selezionano i volumi associati a un'applicazione specifica, l'applicazione viene automaticamente selezionata nella casella **Applicazioni** . 
   3. Nella casella **Volumi** selezionare i volumi StorSimple da aggiungere al gruppo di volumi. 
      
      * È possibile includere volumi con una o più partizioni. (Più volumi di partizione possono essere dischi dinamici o dischi di base con più partizioni). Un volume che contiene più partizioni viene considerato come una singola unità. Di conseguenza, se si aggiunge solo una delle partizioni a un gruppo di volumi, tutte le altre partizioni vengono automaticamente aggiunte a tale gruppo contemporaneamente. Dopo aver aggiunto un volume con più partizioni a un gruppo di volumi, il volume con più partizioni continua a essere considerato come una singola unità.
      * È possibile creare gruppi di volumi vuoti non assegnando alcun volume a essi. 
      * Non combinare volumi condivisi cluster (CSV) e volumi non condivisi cluster nello stesso gruppo di volumi. StorSimple Snapshot Manager non supporta una combinazione di volumi condivisi cluster e volumi non condivisi cluster nello stesso snapshot.
4. Fare clic su **OK** per salvare il gruppo.

## <a name="back-up-a-volume-group"></a>Eseguire il backup di un gruppo di volumi
Utilizzare la procedura seguente per eseguire il backup di un gruppo di volumi.

#### <a name="to-back-up-a-volume-group"></a>Per eseguire il backup di un gruppo di volumi
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** espandere il nodo **Gruppi di volumi**, fare clic con il pulsante destro del mouse sul nome di un gruppo di volumi, quindi scegliere **Esegui backup**.
   
    ![Esecuzione immediata del backup del gruppo di volumi](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Nella finestra di dialogo **Esegui backup** selezionare **Snapshot locale** o **Snapshot nel cloud**, quindi fare clic su **Crea**.
   
    ![Finestra di dialogo Esegui backup](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Per verificare che il backup sia in esecuzione, espandere il nodo **Processi**, quindi fare clic su **In esecuzione**. Il backup dovrebbe essere elencato.
5. Per visualizzare lo snapshot completato, espandere il nodo **Catalogo di backup**, espandere il nome del gruppo di volumi, quindi fare clic su **Snapshot locale** o **Snapshot nel cloud**. Il backup verrà elencato se è stato completato correttamente.

## <a name="edit-a-volume-group"></a>Modificare un gruppo di volumi
Utilizzare la procedura seguente per modificare un gruppo di volumi.

#### <a name="to-edit-a-volume-group"></a>Per modificare un gruppo di volumi
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** espandere il nodo **Gruppi di volumi**, fare clic con il pulsante destro del mouse sul nome di un gruppo di volumi, quindi scegliere **Modifica**.
3. Viene visualizzata la finestra di dialogo Crea un gruppo di volumi. È possibile modificare le voci dei campi **Nome**, **Applicazioni** e **Volumi**.
4. Fare clic su **OK** per salvare le modifiche.

## <a name="delete-a-volume-group"></a>Eliminare un gruppo di volumi
Utilizzare la procedura seguente per eliminare un gruppo di volumi. 

> [!WARNING]
> Vengono eliminati anche tutti i backup associati al gruppo di volumi.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Per eliminare un gruppo di volumi
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** espandere il nodo **Gruppi di volumi**, fare clic con il pulsante destro del mouse sul nome di un gruppo di volumi, quindi scegliere **Elimina**.
3. Viene visualizzata la finestra di dialogo **Elimina gruppo di volumi**. Nella casella di testo digitare **Conferma**, quindi fare clic su **OK**.
   
    Il gruppo di volumi eliminato non è più presente nell'elenco del riquadro **Risultati** e tutti i backup associati a tale gruppo vengono eliminati dal catalogo di backup.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare StorSimple Snapshot Manager per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).
* Informazioni su come [usare StorSimple Snapshot Manager per creare e gestire criteri di backup](storsimple-snapshot-manager-manage-backup-policies.md).

