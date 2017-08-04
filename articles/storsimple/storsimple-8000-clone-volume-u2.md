---
title: Clonare un volume in StorSimple serie 8000 | Microsoft Docs
description: Vengono descritti i diversi tipi di clone e il relativo utilizzo. Viene inoltre illustrato come usare un set di backup per clonare un singolo volume in un dispositivo StorSimple serie 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/26/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 70c85bcb2c26d2ad3d0515d24e028f84495634c0
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Usare il servizio Gestione dispositivi StorSimple nel portale di Azure per clonare un volume

## <a name="overview"></a>Panoramica

Questa esercitazione spiega come usare un set di backup per clonare un singolo volume nel pannello **Catalogo backup**. Viene inoltre spiegata la differenza tra cloni *temporanei* e *permanenti*. Le indicazioni presenti in questa esercitazione sono valide per tutti i dispositivi StorSimple serie 8000 che eseguono l'aggiornamento 3 o una versione successiva.

Il pannello **Catalogo backup** del servizio Gestione dispositivi StorSimple visualizza tutti i set di backup creati quando vengono eseguiti backup manuali o automatici. È quindi possibile selezionare un volume da clonare in un set di backup.

 ![Elenco di set di backup](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Considerazioni per la clonazione di un volume

Per clonare un volume tenere in considerazione le informazioni seguenti.

- Un clone si comporta esattamente come un volume normale. Qualsiasi operazione che è possibile eseguire per un volume è disponibile per il clone.

- Il monitoraggio e il backup predefinito vengono disabilitati automaticamente in un volume clonato. È necessario configurare un volume clonato per tutti i backup.

- Un volume aggiunto in locale viene clonato come volume a livelli. Se è necessario che il volume clonato venga aggiunto in locale, è possibile convertire il clone in un volume aggiunto in locale dopo il completamento dell'operazione di clonazione. Per informazioni sulla conversione di un volume a livelli in volume aggiunto in locale, vedere l'articolo relativo alla [Modificare il tipo di volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Se si tenta di convertire un volume clonato da volume a livelli a volume aggiunto in locale immediatamente dopo la clonazione (quando è ancora un clone temporaneo), la conversione avrà esito negativo con il messaggio di errore seguente:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Questo errore si verifica solo se si esegue la clonazione in un altro dispositivo. È possibile convertire correttamente il volume in volume aggiunto in locale se il clone temporaneo viene prima convertito in un clone permanente. Creare uno snapshot cloud del clone temporaneo per convertirlo in un clone permanente.

## <a name="create-a-clone-of-a-volume"></a>Creare un clone di un volume

È possibile creare un clone nello stesso dispositivo, in un altro dispositivo o persino in un'appliance cloud usando uno snapshot locale o cloud.

La procedura seguente descrive in che modo si crea un clone dal catalogo di backup.  Un metodo alternativo per avviare la clonazione è passare a **Volumi**, selezionare un volume, quindi fare clic con il pulsante destro del mouse per richiamare il menu di scelta rapida e selezionare **Clona**.

Eseguire le operazioni riportate di seguito per creare un clone del volume dal catalogo di backup.

#### <a name="to-clone-a-volume"></a>Per clonare un volume

1. Accedere al servizio Gestione dispositivi StorSimple e fare clic su **Catalogo backup**.

2. Procedura di selezione di un set di backup:
   
   1. Selezionare un dispositivo appropriato.
   2. Nell'elenco a discesa, scegliere il volume o il criterio di backup per il backup che si desidera selezionare.
   3. Specificare l'intervallo di tempo.
   4. Fare clic su **Applica** per eseguire la query.

    I backup associati al volume selezionato o al criterio di backup dovrebbero essere visualizzati nell'elenco dei set di backup.
   
    ![Elenco di set di backup](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Espandere il set di backup per visualizzare i volumi associati. Questi volumi devono essere disconnessi nell'host e nel dispositivo prima di poterli ripristinare. Accedere ai volumi nel pannello **Volumi** del dispositivo, quindi attenersi alla procedura descritta in [Disattivare un volume](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) per disattivarli.
   
   > [!IMPORTANT]
   > Assicurarsi, in primo luogo, di aver disattivato tutti i volumi sull'host, prima di disattivarli sul dispositivo. Se non si eseguono i volumi offline nell'host, si potrebbe verificare un danneggiamento dei dati.
   
4. Tornare alla scheda **Catalogo backup** e selezionare un volume in un set di backup. Fare clic con il pulsante destro del mouse e scegliere **Clona** dal menu di scelta rapida.

   ![Elenco di set di backup](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Nel pannello **Clona** attenersi alla procedura seguente:
   
    1. Identificare un dispositivo di destinazione. Si tratta del percorso in cui verrà creato il clone. È possibile scegliere lo stesso dispositivo o specificare un altro dispositivo.

      > [!NOTE]
      > Assicurarsi che la capacità richiesta per il clone sia inferiore a quella disponibile nel dispositivo di destinazione.
       
    2. Specificare un nome volume univoco per il clone. Il nome deve contenere tra 3 e 127 caratteri.
      
        > [!NOTE]
        > Il campo **Clona volume come** viene impostato su **A livelli** anche se si esegue la clonazione di un volume aggiunto in locale. Non è possibile modificare questa impostazione. Tuttavia, se è necessario che il volume clonato venga aggiunto anche in locale, è possibile convertire il clone in un volume aggiunto in locale dopo averlo creato. Per informazioni sulla conversione di un volume a livelli in volume aggiunto in locale, vedere l'articolo relativo alla [Modificare il tipo di volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
    3. Specificare un record di controllo di accesso (ACR) per il clone in **Host connessi**. È possibile aggiungere un nuovo ACR o scegliere dall'elenco esistente. L'ACR determina quali host possono accedere a questo clone.
      
        ![Elenco di set di backup](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

    4. Fare clic su **Clona** per completare l'operazione.

4. Viene avviato un processo di clonazione e quando il clone viene creato si riceverà una notifica. Fare clic sulla notifica del processo o passare al pannello **Processi** per monitorare il processo di clonazione.

    ![Elenco di set di backup](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Completato il processo di clonazione, passare al dispositivo e fare clic su **Volumi**. Nell'elenco di volumi il clone appena creato appare nello stesso contenitore di volumi che contiene il volume di origine.

    ![Elenco di set di backup](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Un clone creato in questo modo è un clone temporaneo. Per ulteriori informazioni sui tipi di cloni, vedere [Cloni temporanei e cloni permanenti](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Cloni temporanei e cloni permanenti
I cloni temporanei vengono creati solo quando si esegue la clonazione in un altro dispositivo. È possibile clonare un volume specifico da un set di backup in un dispositivo differente gestito da Gestione dispositivi StorSimple. Il clone temporaneo contiene riferimenti ai dati presenti nel volume originale e usa tali dati per la lettura e la scrittura in locale nel dispositivo di destinazione.

Dopo la creazione di uno snapshot cloud di un clone temporaneo, il clone risultante è un clone *permanente* . Durante questo processo viene creata una copia dei dati nel cloud e il tempo di copia è determinato dalla dimensione dei dati e dalle latenze di Azure (si tratta di una copia da Azure ad Azure). Il processo potrebbe richiedere da giorni a settimane. Il clone temporaneo diventa permanente e non contiene alcun riferimento ai dati del volume originale dal quale è stato clonato.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenari per cloni temporanei e cloni permanenti
Nelle sezioni seguenti vengono descritte situazioni di esempio in cui è possibile utilizzare cloni temporanei e permanenti.

### <a name="item-level-recovery-with-a-transient-clone"></a>Ripristino a livello di elemento con un clone temporaneo
È necessario ripristinare un file di presentazione di Microsoft PowerPoint dell’anno precedente. L'amministratore IT identifica il backup specifico da quel momento e filtra il volume. Quindi, l'amministratore clona il volume, individua il file che si sta cercando e lo fornisce all'utente. In questo scenario viene utilizzato un clone temporaneo.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Test nell'ambiente di produzione con un clone permanente
È necessario verificare un bug di test nell'ambiente di produzione. Viene creato un clone del volume nell'ambiente di produzione e quindi creato uno snapshot cloud di questo clone per creare un volume clonato indipendente. In questo scenario viene utilizzato un clone permanente.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [ripristinare un volume StorSimple da un set di backup](storsimple-8000-restore-from-backup-set-u2.md).
* Informazioni su come [usare il servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).


