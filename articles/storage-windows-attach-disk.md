<properties 
	pageTitle="Collegare un disco a una macchina virtuale | Azure" 
	description="Informazioni su come collegare un disco dati a una macchina virtuale di Azure e inizializzarlo in modo che sia pronto per l'uso." 
	services="virtual-machines, storage" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2015" 
	ms.author="kathydav"/>

#Come collegare un disco dati a una macchina virtuale Windows

È possibile collegare sia dischi vuoti sia dischi contenenti dati. In entrambi i casi, i dischi sono effettivamente file con estensione vhd che risiedono in un account di archiviazione di Azure. Inoltre, dopo avere collegato il disco, sarà necessario inizializzarlo affinché sia pronto per l'uso. 

> [AZURE.NOTE] È consigliabile usare uno o più dischi separati per archiviare i dati di una macchina virtuale. Quando si crea una macchina virtuale di Azure, questa contiene un disco per il sistema operativo mappato all'unità C e un disco temporaneo mappato all'unità D. **Non usare l'unità D per archiviare i dati.** Come si può dedurre dal nome, fornisce solo archiviazione temporanea. Non offre funzionalità di ridondanza o backup perché non risiede nel servizio di archiviazione di Azure.

- [Procedura: Collegare un disco vuoto](#attachempty)
- [Procedura: Collegare un disco esistente](#attachexisting)
- [Procedura: Inizializzare un nuovo disco dati in Windows Server](#initializeinWS)


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

##<a id="initializeinWS"></a>Procedura: Inizializzare un nuovo disco dati in Windows Server

1. Connettersi alla macchina virtuale. Per informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server][logon].

2. Dopo aver effettuato l'accesso, avviare **Server Manager**, nel riquadro sinistro espandere **Storage**, quindi fare clic su **Disk Management**.

	![Open Server Manager](./media/storage-windows-attach-disk/ServerManager.png)

3. Fare clic con il pulsante destro del mouse su **Disk 2**, quindi su **Initialize Disk** e infine su **OK**.

	![Initialize the disk](./media/storage-windows-attach-disk/InitializeDisk.png)

4. Fare clic con il pulsante destro del mouse sull'area di allocazione spazio per il disco 2, scegliere **New Simple Volume**, quindi terminare la procedura guidata con i valori predefiniti.

	![Initialize the volume](./media/storage-windows-attach-disk/InitializeDiskVolume.png)

[logon]: ../virtual-machines-log-on-windows-server/

	Il disco sarà ora online e pronto per l'uso con una nuova lettera di unità.

	![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

> [AZURE.NOTE] Il numero massimo di dischi che è possibile collegare a una macchina virtuale varia in base alle dimensioni della macchina virtuale stessa. Ad esempio, è possibile collegare solo 4 dischi allo Standard A2, ma è possibile collegare 32 dischi allo Standard D14 e 64 dischi allo Standard G5. I dettagli sul numero di dischi che è possibile collegare in base alle dimensioni della macchina virtuale sono disponibili [a questo indirizzo](https://msdn.microsoft.com/it-it/library/azure/dn197896.aspx).



<!--HONumber=42-->
