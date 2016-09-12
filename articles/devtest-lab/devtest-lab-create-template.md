<properties
	pageTitle="Gestire immagini personalizzate di Azure DevTest Labs per creare macchine virtuali | Microsoft Azure"
	description="Informazioni su come creare un'immagine personalizzata da un file VHD o da una macchina virtuale esistente in Azure DevTest Labs"
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
	ms.date="08/24/2016"
	ms.author="tarcher"/>

# Gestire immagini personalizzate di Azure DevTest Labs per creare macchine virtuali

Dopo aver [creato un lab](devtest-lab-create-lab.md) è possibile [aggiungere macchine virtuali nel lab](devtest-lab-add-vm-with-artifacts.md). Quando si crea una macchina virtuale, si specifica una *base* che può essere un'*immagine personalizzata* o un'*immagine di Marketplace*. In questo articolo si apprenderà come [creare un'immagine personalizzata da un disco rigido virtuale](#create-a-custom-image-from-a-vhd) per creare successivamente una macchina virtuale da tale immagine personalizzata. È anche possibile [creare un'immagine personalizzata da una macchina virtuale](#create-a-custom-image-from-a-vm) per la successiva creazione rapida di macchine virtuali.

## Creare un'immagine personalizzata da un file VHD

Questa sezione descrive come creare un'immagine personalizzata da un file VHD. Si noti che è necessario accedere a un file VHD valido per eseguire tutti i passaggi di questa sezione.


1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Esplora** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Verrà visualizzato il pannello **Impostazioni** del lab selezionato.

1. Nel pannello **Impostazioni** del lab selezionare **Immagini personalizzate**.

    ![Opzione per le immagini personalizzate](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. Nel pannello **Immagini personalizzate** selezionare **+ Immagine personalizzata**.

    ![Aggiungere un'immagine personalizzata](./media/devtest-lab-create-template/add-custom-image.png)

1. Immettere il nome dell'immagine personalizzata. Il nome viene visualizzato nell'elenco delle immagini di base durante la creazione di una nuova macchina virtuale.

1. Immettere la descrizione dell'immagine personalizzata. La descrizione viene visualizzata nell'elenco delle immagini di base durante la creazione di una nuova macchina virtuale.

1. Selezionare **File VHD**.

1. Se si ha accesso a un file VHD che non è elencato, aggiungerlo seguendo le istruzioni della sezione [Caricare un file VHD](#upload-a-vhd-file) e tornare qui al termine.

1. Selezionare il file VHD desiderato.

1. Selezionare **OK** per chiudere il pannello **File VHD**.

1. Selezionare **Configurazione sistema operativo**.

1. Nella scheda **Configurazione sistema operativo** selezionare **Windows** o **Linux**.

1. Se si seleziona **Windows**, usare la casella di controllo per specificare se *Sysprep* è stato eseguito nel computer.

1. Selezionare **OK** per chiudere il pannello **Configurazione sistema operativo**.

1. Selezionare **OK** per creare l'immagine personalizzata.

1. Passare alla sezione [Passaggi successivi](#next-steps).

###Caricare un file VHD

Per aggiungere una nuova immagine personalizzata, è necessario accedere a un file VHD.

1. Nel pannello **File VHD** selezionare l'opzione **Carica un disco rigido virtuale con PowerShell**.

    ![Caricare un'immagine](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. Il pannello successivo visualizzerà le istruzioni per la modifica e l'esecuzione di uno script di PowerShell che carica un file VHD nella sottoscrizione di Azure. **Nota:** questo processo può richiedere molto tempo a seconda delle dimensioni del file VHD e della velocità della connessione.

## Creare un'immagine personalizzata da una macchina virtuale
Se è presente una macchina virtuale già configurata, è possibile creare un'immagine personalizzata da tale macchina virtuale e successivamente usare l'immagine personalizzata per creare altre macchine virtuali identiche. I passaggi seguenti illustrano come creare un'immagine personalizzata da una macchina virtuale:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Esplora** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.

1. Nel pannello **Panoramica** del lab selezionare la macchina virtuale dalla quale creare l'immagine personalizzata.

1. Nel pannello della macchina virtuale selezionare **Crea immagine personalizzata (disco rigido virtuale)**.

	![Voce di menu Crea immagine personalizzata](./media/devtest-lab-create-template/create-custom-image.png)

1. Nel pannello **Immagine personalizzata** immettere un nome e una descrizione per l'immagine personalizzata. Queste informazioni verranno visualizzate nell'elenco delle immagini di base quando si crea una macchina virtuale.

	![Pannello Crea immagine personalizzata](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Indicare se è stato eseguito sysprep nella macchina virtuale. Se non è stato eseguito sysprep nella macchina virtuale, specificare se si vuole che venga eseguito sysprep quando viene creata una macchina virtuale da questa immagine personalizzata.

1. Selezionare **OK** al termine per creare l'immagine personalizzata.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Post di blog correlati

- [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copia di immagini personalizzate tra istanze di Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##Passaggi successivi

Dopo aver aggiunto un'immagine personalizzata da usare durante la creazione di una macchina virtuale, il passaggio successivo consiste nell'[aggiungere una macchina virtuale al lab](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0831_2016-->