<properties
	pageTitle="Creare un'immagine personalizzata del lab di sviluppo/test da un file VHD | Microsoft Azure"
	description="Informazioni su come creare un'immagine personalizzata da un file VHD da usare per la creazione di macchine virtuali in un lab di sviluppo/test"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/13/2016"
	ms.author="tarcher"/>

# Creare un'immagine personalizzata del lab di sviluppo/test da un file VHD

## Panoramica

Dopo aver [creato un lab di sviluppo/test](devtest-lab-create-lab.md) è possibile [aggiungere macchine virtuali nel lab](devtest-lab-add-vm-with-artifacts.md). Quando si crea una macchina virtuale, si specifica una *base* che può essere un'*immagine personalizzata* o un'*immagine di Marketplace*. Questo articolo descrive come creare un'immagine personalizzata da un file VHD. Si noti che è necessario accedere a un file VHD valido per eseguire tutti i passaggi di questo articolo.

## Creare un'immagine personalizzata

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Toccare **Sfoglia** e poi **Lab di sviluppo e test** dall'elenco.

1. Nell'elenco dei lab toccare il lab desiderato.

1. Verrà visualizzato il pannello **Impostazioni** del lab selezionato.

1. Nel pannello **Impostazioni** del lab toccare **Immagini personalizzate**.

    ![Opzione Immagini personalizzate](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. Nel pannello **Immagini personalizzate** toccare **+ Immagine personalizzata**.

    ![Aggiungere un'immagine personalizzata](./media/devtest-lab-create-template/add-custom-image.png)

1. Immettere il nome dell'immagine personalizzata. Il nome viene visualizzato nell'elenco delle immagini di base durante la creazione di una nuova macchina virtuale.

1. Immettere la descrizione dell'immagine personalizzata. La descrizione viene visualizzata nell'elenco delle immagini di base durante la creazione di una nuova macchina virtuale.

1. Toccare **File VHD**.

1. Se si ha accesso a un file VHD che non è elencato, aggiungerlo seguendo le istruzioni della sezione [Caricare un file VHD](#upload-a-vhd-file) e tornare qui al termine.

1. Selezionare il file VHD desiderato.

1. Toccare **OK** per chiudere il pannello **File VHD**.

1. Toccare **Configurazione sistema operativo**.

1. Nella scheda **Configurazione sistema operativo** selezionare **Windows** o **Linux**.

1. Se si seleziona **Windows**, usare la casella di controllo per specificare se *Sysprep* è stato eseguito nel computer.

1. Toccare **OK** per chiudere il pannello **Configurazione sistema operativo**.

1. Toccare **OK** per creare l'immagine personalizzata.

1. Passare alla sezione [Passaggi successivi](#next-steps).

##Caricare un file VHD

Per aggiungere una nuova immagine personalizzata, è necessario accedere a un file VHD.

1. Nel pannello **File VHD** toccare l'opzione per il **caricamento di un file VHD tramite PowerShell**.

    ![Caricare un'immagine](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. Il pannello successivo visualizzerà le istruzioni per la modifica e l'esecuzione di uno script di PowerShell che carica un file VHD nella sottoscrizione di Azure. **Nota:** questo processo può richiedere molto tempo a seconda delle dimensioni del file VHD e della velocità della connessione.

##Passaggi successivi

Dopo aver aggiunto un'immagine personalizzata da usare durante la creazione di una macchina virtuale, il passaggio successivo consiste nell'[aggiungere una macchina virtuale nel lab di sviluppo/test](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0316_2016-->