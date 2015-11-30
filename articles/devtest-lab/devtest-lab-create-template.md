    <properties
	pageTitle="Create VM templates | Microsoft Azure"
	description="Learn how to create VM templates from VHD images"
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
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Creare modelli di macchina virtuale

## Panoramica

Dopo aver [creato un lab](devtest-lab-create-lab.md), è possibile [aggiungervi macchine virtuali](devtest-lab-add-vm-with-artifacts.md) usando un elenco di modelli di macchina virtuale. In questo articolo verrà illustrato come caricare e configurare un file di immagine del disco rigido virtuale come modello in base al quale creare le macchine virtuali. Per gli utenti poco esperti di immagini del disco rigido virtuale, vedere l'articolo [Creare e caricare un disco rigido virtuale con Windows Server in Azure](https://azure.microsoft.com/it-IT/documentation/articles/virtual-machines-create-upload-vhd-windows-server/) per altre info su come creare un'immagine di questo tipo. Dopo aver creato o ottenuto l'accesso a un'immagine del disco rigido virtuale, fare riferimento a questo articolo, dove è descritta la procedura dettagliata di caricamento e creazione di un modello.

## Creare un modello di macchina virtuale

1. Accedere al [portale di anteprima di Azure](http://portal.azure.com).

1. Toccare **Sfoglia** e quindi **Lab DevTest** nell'elenco.

1. Nell'elenco dei lab toccare il lab desiderato.

1. Nel pannello del lab toccare **Impostazioni**.

    ![Impostazioni del lab](./media/devtest-lab-create-template/lab-blade-settings.png)

1. Nel pannello **Impostazioni** del lab toccare **Modelli**.

    ![Opzione Modelli](./media/devtest-lab-create-template/lab-blade-settings-templates.png)

1. Nel pannello **Modelli** toccare **+ Modello**.

    ![Aggiungere un modello](./media/devtest-lab-create-template/add-template.png)

1. Nel pannello **Aggiungi modello**:

	1. Immettere il nome del modello. Questo nome viene visualizzato nell'elenco di modelli quando viene creata una nuova macchina virtuale.

	1. Immettere la descrizione del modello. Questa descrizione viene visualizzata nell'elenco di modelli quando viene creata una nuova macchina virtuale.

	1. Toccare **Immagine**.

	1. Se l'immagine desiderata non è visualizzata e si desidera aggiungerla, passare alla sezione [Aggiungere una nuova immagine del modello](#add-a-new-template-image) e tornare a questa sezione dopo aver completato la procedura.

	1. Selezionare l'immagine desiderata.

	1. Toccare **OK** per chiudere il pannello **Aggiungi modello**.

1. Toccare **Configurazione sistema operativo**.

1. Nella scheda **Configurazione sistema operativo** selezionare **Windows** o **Linux**.

1. Se si seleziona **Windows**, usare la casella di controllo per specificare se *Sysprep* è stato eseguito nel computer.

1. Immettere un **nome utente** per la macchina.

1. Immettere una **password** per la macchina. **Nota:** la password viene visualizzata come testo non crittografato.

1. Toccare **OK** per chiudere il pannello **Configurazione sistema operativo**.

1. Specificare il **percorso**.

1. Toccare **OK** per creare il modello.

##Aggiungere una nuova immagine del modello

Per aggiungere una nuova immagine del modello, sarà necessario accedere a un file di immagine del disco rigido virtuale.

1. Nel pannello **Aggiungi immagine modello** toccare **Carica immagine mediante PowerShell**.

    ![Caricare un'immagine](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. Il pannello successivo visualizzerà le istruzioni per la modifica e l'esecuzione di uno script di PowerShell che carica un file di immagine del disco rigido virtuale nella sottoscrizione di Azure. **Nota:** questo processo può richiedere molto tempo a seconda delle dimensioni del file di immagine e della velocità della connessione.

##Passaggi successivi

Dopo aver aggiunto un modello di macchina virtuale da usare durante la creazione di una macchina virtuale, il passaggio successivo consiste nell'[aggiungere una macchina virtuale al lab DevTest](devtest-lab-add-vm-with-artifacts).

<!---HONumber=Nov15_HO4-->