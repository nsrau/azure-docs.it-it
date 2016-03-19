<properties 
   pageTitle="Gestire il catalogo di backup di StorSimple | Microsoft Azure"
   description="Viene illustrato come utilizzare la pagina del catalogo di backup del servizio StorSimple Manager per elencare, selezionare ed eliminare dei set di backup per un volume."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/30/2015"
   ms.author="v-sharos" />

# Per gestire il catalogo di backup, è possibile usare il servizio StorSimple Manager.

## Panoramica

La pagina **Catalogo di backup** del servizio StorSimple Manager visualizza tutti i set di backup creati quando si eseguono backup manuali o programmati. È possibile utilizzare questa pagina per elencare tutti i backup per un criterio di backup o un volume, selezionare o eliminare i backup o utilizzare un backup per ripristinare o clonare un volume.

In questa esercitazione viene illustrato come elencare, selezionare ed eliminare un set di backup. Per scoprire come ripristinare il dispositivo dal backup, vedere [Ripristinare il dispositivo da un set di backup](storsimple-restore-from-backup-set.md). Per scoprire come clonare un volume, vedere [Clonare un volume StorSimple](storsimple-clone-volume.md).

![Catalogo di backup](./media/storsimple-manage-backup-catalog/HCS_BackupCatalog.png)

La pagina **Catalogo di backup** fornisce una query per limitare la selezione del set di backup. È possibile filtrare i set di backup che vengono recuperati in base ai parametri seguenti:

- **Dispositivo**: il dispositivo in cui è stato creato il set di backup.

- **Criterio di backup o Volume** - il criterio di backup o volume associato a questo set di backup.

- **Da e A** - intervallo di data e ora di creazione del set di backup.

I set di backup filtrati vengono quindi catalogati in base ai seguenti attributi:

- **Nome**: nome del criterio di backup o volume associato al set di backup.

- **Dimensioni**: dimensione effettiva del set di backup.

- **Creato il**: data e ora di creazione dei backup.

- **Tipo**: i set di backup possono essere snapshot in locale o del cloud. Uno snapshot locale è un backup di tutti i dati di volume archiviati localmente sul dispositivo, mentre uno snapshot del cloud si riferisce al backup dei dati di volume che risiedono nel cloud. Gli snapshot in locale forniscono un accesso più rapido, mentre gli snapshot del cloud vengono scelti per la resilienza dei dati.

- **Avviato da**: i backup possono essere avviati automaticamente da una pianificazione o manualmente dall'utente. Per pianificare i backup, è possibile usare un criterio di backup. In alternativa, è possibile usare l'opzione **Esegui backup** per eseguire un backup manuale.

## Elencare i set di backup per un volume
 
Completare la procedura seguente per elencare tutti i backup per un volume.

#### Per elencare i set di backup

1. Nella pagina del servizio StorSimple Manager, fare clic sulla scheda **Catalogo di backup**.

2. Filtrare le selezioni come segue:

    1. Selezionare un dispositivo appropriato.

    2. Nell'elenco a discesa scegliere un volume per visualizzare i backup corrispondenti.

    3. Specificare l'intervallo di tempo.

    4. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) per eseguire la query.
 
    I backup associati al volume selezionato dovrebbero essere visualizzati nell'elenco dei set di backup.

## Selezionare un set di backup

Completare i passaggi seguenti per selezionare un set di backup per un volume o i criteri di backup.

#### Per selezionare un set di backup

1. Nella pagina del servizio StorSimple Manager, fare clic sulla scheda **Catalogo di backup**.

2. Filtrare le selezioni come segue:

    1. Selezionare un dispositivo appropriato.

    2. Nell'elenco a discesa, scegliere il volume o il criterio di backup per il backup che si desidera selezionare.

    3. Specificare l'intervallo di tempo.

    4. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) per eseguire la query.

    I backup associati al volume selezionato o al criterio di backup dovrebbero essere visualizzati nell'elenco dei set di backup.

3. Selezionare ed espandere un set di backup Le opzioni **Ripristina** ed **Elimina** sono visualizzate nella parte inferiore della pagina. È possibile eseguire queste azioni sul set di backup selezionato.

## Eliminare un set di backup

Eliminare un backup quando non si desidera più conservare i dati associati. Eseguire la procedura seguente per eliminare un set di backup.

#### Per eliminare un set di backup

1. Nella pagina del servizio StorSimple Manager fare clic sulla scheda **Catalogo di backup**.

2. Filtrare le selezioni come segue:

    1. Selezionare un dispositivo appropriato.

    2. Nell'elenco a discesa, scegliere il volume o il criterio di backup per il backup che si desidera selezionare.

    3. Specificare l'intervallo di tempo.

    4. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) per eseguire la query.

    I backup associati al volume selezionato o al criterio di backup dovrebbero essere visualizzati nell'elenco dei set di backup.

3. Selezionare ed espandere un set di backup Le opzioni **Ripristina** ed **Elimina** sono visualizzate nella parte inferiore della pagina. Fare clic su **Elimina**.

4. Verrà visualizzata una notifica dell'operazione di eliminazione in corso e del corretto completamento. Al termine dell'eliminazione, aggiornare la query nella pagina. Il set di backup eliminato non verrà più visualizzato nell'elenco dei set di backup.

## Passaggi successivi

- Informazioni su come [usare la pagina Catalogo di backup per ripristinare il dispositivo da un set di backup](storsimple-restore-from-backup-set.md).

- Informazioni su come [utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0107_2016-->