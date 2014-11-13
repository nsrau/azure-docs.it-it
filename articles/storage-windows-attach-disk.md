<properties urlDisplayName="Attach a disk" pageTitle="Collegare un disco a una macchina virtuale | Azure" metaKeywords="Azure Windows virtual machine, Azure attach disk, Azure initialize disk" description="Informazioni su come collegare un disco dati a una macchina virtuale di Azure e inizializzarlo in modo che sia pronto per l'uso." metaCanonical="" services="virtual-machines,storage" documentationCenter="" title="Come collegare un disco dati a una macchina virtuale" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="7/29/2014" ms.author="kathydav" />

# Come collegare un disco dati a una macchina virtuale Windows

È possibile collegare sia dischi vuoti sia dischi contenenti dati. In entrambi i casi, i dischi sono effettivamente file con estensione vhd che risiedono in un account di archiviazione di Azure. Inoltre, dopo avere collegato il disco, sarà necessario inizializzarlo affinché sia pronto per l'uso.

> [WACOM.NOTE] È consigliabile usare uno o più dischi separati per archiviare i dati di una macchina virtuale. Quando si crea una macchina virtuale di Azure, questa contiene un disco per il sistema operativo mappato all'unità C e un disco temporaneo mappato all'unità D. **Non usare l'unità D per archiviare i dati.** Come si può dedurre dal nome, fornisce solo archiviazione temporanea. Non offre funzionalità di ridondanza o backup perché non risiede nel servizio di archiviazione di Azure.

-   [Procedura: Collegare un disco vuoto][Procedura: Collegare un disco vuoto]
-   [Procedura: Collegare un disco esistente][Procedura: Collegare un disco esistente]
-   [Procedura: Inizializzare un nuovo disco dati in Windows Server][Procedura: Inizializzare un nuovo disco dati in Windows Server]

[WACOM.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

## <span id="initializeinWS"></span></a>Procedura: Inizializzare un nuovo disco dati in Windows Server

1.  Connettersi alla macchina virtuale usando i passaggi descritti in precedenza in [Come accedere a una macchina virtuale che esegue Windows Server][Come accedere a una macchina virtuale che esegue Windows Server].

2.  Dopo aver effettuato l'accesso, avviare **Server Manager**, nel riquadro sinistro espandere **Storage**, quindi fare clic su **Disk Management**.

    ![Avviare Server Manager][Avviare Server Manager]

3.  Fare clic con il pulsante destro del mouse su **Disk 2**, quindi su **Inizializza disco** e infine su **OK**.

    ![Inizializzare il disco][Inizializzare il disco]

4.  Fare clic con il pulsante destro del mouse sull'area di allocazione spazio per il disco 2, scegliere **New Simple Volume**, quindi terminare la procedura guidata con i valori predefiniti.

    ![Inizializzare il volume][Inizializzare il volume]

<!-- -->

    The disk is now online and ready to use with a new drive letter.



    ![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

  [Procedura: Collegare un disco vuoto]: #attachempty
  [Procedura: Collegare un disco esistente]: #attachexisting
  [Procedura: Inizializzare un nuovo disco dati in Windows Server]: #initializeinWS
  [Come accedere a una macchina virtuale che esegue Windows Server]: ../virtual-machines-log-on-windows-server/
  [Avviare Server Manager]: ./media/storage-windows-attach-disk/ServerManager.png
  [Inizializzare il disco]: ./media/storage-windows-attach-disk/InitializeDisk.png
  [Inizializzare il volume]: ./media/storage-windows-attach-disk/InitializeDiskVolume.png
