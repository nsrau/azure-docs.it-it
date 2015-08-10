<properties 
   pageTitle="Utilizzo di Gestione snapshot StorSimple per creare e gestire gruppi di volumi | Microsoft Azure"
   description="Viene descritto come utilizzare lo snap-in MMC di Gestione snapshot StorSimple per creare e gestire i gruppi di volumi."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Utilizzo di Gestione snapshot StorSimple per creare e gestire gruppi di volumi

## Panoramica

È possibile utilizzare il nodo **Gruppi di volumi** nel riquadro **Ambito** per assegnare i volumi a gruppi di volumi, visualizzare le informazioni relative a un gruppo di volumi, pianificare i backup e modificare i gruppi di volumi.

I gruppi di volumi sono pool di volumi correlati utilizzati per garantire che i backup siano coerenti con l'applicazione. Per ulteriori informazioni, vedere [Volumi e gruppi di volumi](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) e [Integrazione con il servizio Copia Shadow del volume di Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

>[AZURE.IMPORTANT]Quando si configurano gruppi di volumi, non combinare volumi condivisi cluster (CSV) e volumi non condivisi cluster nello stesso gruppo di volumi. Gestione snapshot StorSimple non supporta una combinazione di volumi condivisi cluster e volumi non condivisi cluster nello stesso snapshot.
 
![Nodo Gruppi di volumi](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: Nodo Gruppi di volumi di Gestione snapshot StorSimple**

In questa esercitazione viene illustrato come utilizzare Gestione snapshot StorSimple per:

- Visualizzare le informazioni sui gruppi di volumi 
- Creare un gruppo di volumi
- Eseguire il backup di un gruppo di volumi
- Modificare un gruppo di volumi
- Eliminare un gruppo di volumi

Tutte queste azioni sono disponibili anche nel riquadro **Azioni**.
 
## Visualizzazione dei gruppi di volumi

Se si fa clic sul nodo **Gruppi di volumi**, nel riquadro **Risultati** vengono mostrate le informazioni seguenti su ciascun gruppo di volumi, a seconda delle colonne selezionate. (Le colonne nel riquadro **Risultati** sono configurabili. Fare clic on il pulsante destro del mouse sul nodo **Volumi**, selezionare **Visualizza**, quindi selezionare **Aggiungi/Rimuovi colonne**.)

Colonna risultati | Descrizione 
:--------------|:------------ 
Nome | La colonna **Nome** contiene il nome del gruppo di volumi.
Applicazione | La colonna **Applicazioni** mostra il numero di writer del Servizio snapshot del volume attualmente installati e in esecuzione sull'host Windows.
Selezionato | La colonna **Selezionati** mostra il numero di volumi contenuti nel gruppo di volumi. Zero (0) indica che nessuna applicazione è associata ai volumi nel gruppo di volumi.
Importati | La colonna **Importati** mostra il numero di volumi importati. Se impostata su**True**, questa colonna indica che un gruppo di volumi è stato importato dal portale di gestione di Microsoft Azure e non è stato creato in Gestione snapshot StorSimple.
 
>[AZURE.NOTE]I gruppi di volumi di Gestione snapshot StorSimple vengono inoltre visualizzati nella scheda **Criteri di backup** del portale di gestione di Azure.
 
## Creazione di un gruppo di volumi

Utilizzare la procedura seguente per creare un gruppo di volumi.

#### Per creare un gruppo di volumi

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 

2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse su **Gruppi di volumi**, quindi fare clic su **Crea gruppo di volumi**.

    ![Creazione di un gruppo di volumi](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    Viene visualizzata la finestra di dialogo **Crea un gruppo di volumi**.

    ![Finestra di dialogo Crea un gruppo di volumi](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)

3.  Immettere le seguenti informazioni:

    1. Nella casella **Nome**, digitare un nome univoco per il nuovo gruppo di volumi. 

    2. Nella casella **Applicazioni**, selezionare le applicazioni associate ai volumi che verranno aggiunte al gruppo di volumi.

        Nella casella **Applicazioni** vengono elencate solo le applicazioni che utilizzano volumi StorSimple di Azure e che dispongono di writer del Servizio snapshot del volume abilitate per tali volumi. Un writer del Servizio snapshot del volume è abilitato solo se tutti i volumi di cui è a conoscenza sono volumi StorSimple di Azure. Se la casella Applicazioni è vuota, non è stata installata alcuna applicazione che utilizza volumi StorSimple di Azure e che dispone di writer del Servizio snapshot del volume. (Attualmente, Azure StorSimple supporta Microsoft Exchange e SQL Server). Per ulteriori informazioni sui writer del Servizio snapshot del volume, vedere [Integrazione con il servizio Copia Shadow del volume di Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

        Se si seleziona un'applicazione, vengono selezionati automaticamente tutti i volumi associati ad essa. Viceversa, se si selezionano i volumi associati a un'applicazione specifica, l'applicazione viene automaticamente selezionata nella casella **Applicazioni**.

    3. Nella casella **Volumi**, selezionare i volumi StorSimple di Azure da aggiungere al gruppo di volumi.

      - È possibile includere volumi con una o più partizioni. (I volumi con più partizioni possono essere dischi dinamici o dischi di base con più partizioni.) Un volume che contiene più partizioni viene considerato come una singola unità. Di conseguenza, se si aggiunge solo una delle partizioni a un gruppo di volumi, tutte le altre partizioni vengono automaticamente aggiunte a tale gruppo contemporaneamente. Dopo aver aggiunto un volume con più partizioni a un gruppo di volumi, il volume con più partizioni continua a essere considerato come una singola unità.

      - È possibile creare gruppi di volumi vuoti non assegnando alcun volume a essi.

      - Non combinare volumi condivisi cluster (CSV) e volumi non condivisi cluster nello stesso gruppo di volumi. Gestione snapshot StorSimple non supporta una combinazione di volumi condivisi cluster e volumi non condivisi cluster nello stesso snapshot.

4. Fare clic su **OK** per salvare il gruppo di volumi.

## Esecuzione del backup di un gruppo di volumi

Utilizzare la procedura seguente per eseguire il backup di un gruppo di volumi.

#### Per eseguire il backup di un gruppo di volumi

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, espandere il nodo **Gruppi di volumi**, fare clic con il pulsante destro del mouse sul nome di un gruppo di volumi, quindi fare clic su **Esegui backup**.

    ![Esecuzione immediata del backup del gruppo di volumi](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. Nella finestra di dialogo **Esegui backup**, selezionare **Snapshot locale** o **Snapshot cloud**, quindi fare clic su **Crea**.

    ![Finestra di dialogo Esegui backup](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)

4. Per verificare che il backup sia in esecuzione, espandere il nodo **Processi**, quindi fare clic su **In esecuzione**. Il backup dovrebbe essere elencato.

5. Per visualizzare lo snapshot completato, espandere il nodo **Catalogo di backup**, espandere il nome del gruppo di volumi, quindi fare clic su**Snapshot locale** o **Snapshot cloud**. Il backup verrà elencato se è stato completato correttamente.

## Modifica di un gruppo di volumi

Utilizzare la procedura seguente per modificare un gruppo di volumi.

#### Per modificare un gruppo di volumi

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, espandere il nodo **Gruppi di volumi**, fare clic con il pulsante destro del mouse sul nome di un gruppo di volumi, quindi fare clic su **Modifica**.

3. Viene visualizzata la finestra di dialogo **Crea un gruppo di volumi**. È possibile modificare le voci **Nome**, **Applicazioni** e **Volumi**. 

4. Fare clic su **OK** per salvare le modifiche.

## Eliminazione di un gruppo di volumi

Utilizzare la procedura seguente per eliminare un gruppo di volumi.

>[AZURE.WARNING]Ciò elimina anche tutti i backup associati al gruppo di volumi.

#### Per eliminare un gruppo di volumi

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 

2. Nel riquadro **Ambito**, espandere il nodo **Gruppi di volumi**, fare clic con il pulsante destro del mouse sul nome di un gruppo di volumi, quindi fare clic su **Elimina**.

3. Viene visualizzata la finestra di dialogo **Elimina un gruppo di volumi**. Digitare **conferma** nella casella di testo e quindi fare clic su **OK**.

    Il gruppo di volumi eliminato non è più presente nell'elenco del riquadro **Risultati** e tutti i backup associati a tale gruppo vengono eliminati dal catalogo di backup.

## Passaggi successivi

[Utilizzo di Gestione snapshot StorSimple per creare e gestire i criteri di backup](storsimple-snapshot-manager-manage-backup-policies.md)

<!---HONumber=July15_HO5-->