<properties
	pageTitle="Collegare un disco dati | Microsoft Azure"
	description="Come collegare un disco dati nuovo o esistente a una macchina virtuale nel portale di Azure tramite il modello di distribuzione di gestione delle risorse."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Come collegare un disco dati nel portale di Azure

[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a id="initializeinWS"></a>Procedura: Inizializzare un nuovo disco dati in Windows Server

1. Connettersi alla macchina virtuale. Per istruzioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server](virtual-machines-windows-log-on.md).

2. Dopo aver eseguito l'accesso alla macchina virtuale, aprire **Server Manager**. Nel riquadro sinistro fare clic su **Servizi file e archiviazione**.

	![Avviare Server Manager](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Espandere il menu e selezionare **Dischi**.

4. La sezione **dischi** contiene un elenco dei dischi. Nella maggior parte dei casi, avrà disco 0, disco 1 e disco 2. Il disco 0 è il disco del sistema operativo, il disco 1 è il disco temporaneo e il disco 2 è il disco dati che è stato appena connesso alla VM. Il nuovo disco dati elencherà la partizione come **sconosciuta**. Fare clic con il pulsante destro del mouse sul disco e scegliere **Inizializza**.

5.	Si riceverà una notifica che tutti i dati verranno cancellati quando viene inizializzato il disco. Fare clic su **Sì** per accettare il messaggio di avviso e inizializzare il disco. Una volta completata l’operazione, la partizione verrà elencata come **GPT**. Fare di nuovo clic con il pulsante destro del mouse sul disco e scegliere **Nuovo volume**.

6.	Completare la procedura guidata usando i valori predefiniti. Al termine della procedura guidata, nella sezione **Volumi** verrà visualizzato il nuovo volume. Il disco sarà ora online e pronto per l'archiviazione di dati.


	![Inizializzazione del volume completata](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] Le dimensioni della VM determinano il numero di dischi che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md).


## Passaggi successivi

Se l'applicazione deve usare l'unità D: per archiviare i dati, è possibile [modificare la lettera di unità del disco temporaneo di Windows].

<!---HONumber=AcomDC_0323_2016-->