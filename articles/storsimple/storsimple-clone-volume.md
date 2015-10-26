<properties
   pageTitle="Clonare il proprio volume StorSimple | Microsoft Azure"
   description="Vengono descritti i diversi tipi di clone e le relative situazioni di utilizzo. Viene inoltre illustrato come utilizzare un set di backup per clonare un singolo volume."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/12/2015"
   ms.author="alkohli" />

# Utilizzare il servizio StorSimple Manager per clonare un volume

## Panoramica

Nella pagina **Catalogo di backup** del servizio StorSimple Manager vengono visualizzati tutti i set di backup creati quando si eseguono backup manuali o automatizzati. È possibile utilizzare questa pagina per elencare tutti i backup per un criterio di backup o un volume, selezionare o eliminare i backup o utilizzare un backup per ripristinare o clonare un volume.

![Pagina catalogo di backup](./media/storsimple-clone-volume/HCS_BackupCatalog.png)

In questa esercitazione viene descritto come utilizzare un set di backup per clonare un singolo volume. Viene inoltre spiegata la differenza tra cloni *temporanei* e *permanenti*.

## Creare un clone di un volume

È possibile creare un clone sullo stesso dispositivo, su un altro dispositivo o persino su una macchina virtuale utilizzando uno snapshot locale o del cloud.

#### Per clonare un volume

1. Nella pagina del servizio StorSimple Manager, fare clic sulla scheda **Catalogo di Backup** e selezionare un set di backup.

2. Espandere il set di backup per visualizzare i volumi associati. Fare clic e selezionare un volume dal set di backup.

     ![Clonare un volume](./media/storsimple-clone-volume/HCS_Clone.png)

3. Fare clic su **Clona** per iniziare a clonare il volume selezionato.

4. Nella procedura guidata Clona volume, in **Impostazione nome e percorso**:

  1. Identificare un dispositivo di destinazione. Si tratta del percorso in cui verrà creato il clone. È possibile scegliere lo stesso dispositivo o specificare un altro dispositivo. Se si sceglie un volume associato ad altri provider di servizi cloud (non Azure), nell'elenco a discesa per il dispositivo di destinazione verranno visualizzati solo i dispositivi fisici. Non è possibile clonare un volume associato con altri provider di servizi cloud in un dispositivo virtuale.

        >  [AZURE.NOTE] Make sure that the capacity required for the clone is lower than the capacity available on the target device.
  2. Specificare un nome volume univoco per il clone. Il nome deve contenere tra 3 e 127 caratteri.
  3. Fare clic sull'icona di freccia ![icona a forma di freccia](./media/storsimple-clone-volume/HCS_ArrowIcon.png) per passare alla pagina successiva.

5. In **Specificare gli host che possono utilizzare questo volume**:

  1. Specificare un record di controllo di accesso (ACR) per il clone. È possibile aggiungere un nuovo ACR o scegliere dall'elenco esistente.
  2. Fare clic sull’icona del segno di spunta ![icona del segno di spunta](./media/storsimple-clone-volume/HCS_CheckIcon.png)per completare l’operazione.

6. Verrà avviato un processo di clonazione e quando il clone viene creato correttamente si riceverà una notifica. Fare clic su **Visualizza processo** per monitorare il processo di clonazione nella pagina **Processi**.

7. Al termine del processo di clonazione:

  1. Nella pagina **Dispositivi**, fare clic sulla scheda **Contenitori di volumi**.
  2. Selezionare il contenitore del volume associato al volume di origine clonato. Nell'elenco di volumi, verrà visualizzato il clone che è stato appena creato.

>[AZURE.NOTE]Il monitoraggio e il backup predefinito vengono disabilitati automaticamente in un volume clonato.

Un clone creato in questo modo è un clone temporaneo. Per ulteriori informazioni sui tipi di cloni, vedere [Cloni temporanei e cloni permanenti](#transient-vs.-permanent-clones).

Questo clone ora è un volume normale e qualsiasi operazione possibile su un volume sarà disponibile anche per il clone. È necessario configurare questo volume per tutti i backup.

## Cloni temporanei e cloni permanenti

È possibile clonare un volume specifico da un set di backup. Un clone creato in questo modo è un clone *temporaneo*. Il clone temporaneo disporrà di riferimenti al volume originale e utilizzerà tale volume per la lettura durante la scrittura in locale. Ciò potrebbe causare il rallentamento delle prestazioni, specialmente se il volume clonato è di grandi dimensioni.

Dopo l'esecuzione di uno snapshot del cloud di un clone temporaneo, il clone risultante sarà un clone *permanente*. Il clone permanente è indipendente e non contiene alcun riferimento al volume originale dal quale è stato clonato. Per prestazioni più veloci, è consigliabile creare cloni permanenti.

## Scenari per cloni temporanei e cloni permanenti

Nelle sezioni seguenti vengono descritte situazioni di esempio in cui è possibile utilizzare cloni temporanei e permanenti.

### Ripristino a livello di elemento con un clone temporaneo

È necessario ripristinare un file di presentazione di Microsoft PowerPoint dell’anno precedente. L'amministratore IT identifica il backup di tale periodo di tempo specifico e filtra il volume. Quindi, l'amministratore clona il volume, individua il file che si sta cercando e lo fornisce all'utente. In questo scenario viene utilizzato un clone temporaneo.
 
![Video disponibile](./media/storsimple-clone-volume/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come è possibile utilizzare le funzionalità di copia e ripristino di StorSimple per ripristinare file eliminati, fare clic [qui](http://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### Test nell'ambiente di produzione con un clone permanente

È necessario verificare un bug di test nell'ambiente di produzione. Viene creato un clone del volume nell'ambiente di produzione. Per migliorare le prestazioni, è necessario creare uno snapshot del cloud di questo clone. Il volume clonato è ora indipendente e di conseguenza le prestazioni sono più veloci. In questo scenario viene utilizzato un clone permanente.

## Passaggi successivi
- Informazioni su come [ripristinare un volume StorSimple da un set di backup](storsimple-restore-from-backup-set.md).

- Informazioni su come [utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

 

<!---HONumber=Oct15_HO3-->