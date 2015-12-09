<properties
	pageTitle="Creare una macchina virtuale con Windows nel portale di Azure | Microsoft Azure"
	description="Informazioni su come creare una VM di Azure o un computer virtuale che esegue Windows usando Azure Marketplace nel portale di Azure"
	keywords="vm azure,creare una macchina virtuale,computer virtuale,configurazione di una macchina virtuale"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/14/2015"
	ms.author="cynthn"/>

# Creare una macchina virtuale con Windows nel portale di Azure#

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [Azure classic portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: Resource Manager deployment](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: Classic deployment](virtual-machines-ps-create-preconfigure-windows-vms.md)

<br>

Questa esercitazione illustra come è facile creare una macchina virtuale di Azure in pochi minuti nel portale di Azure. Verrà usata un'immagine Windows Server 2012 R2 Datacenter come esempio per creare la macchina virtuale, ma è solo una delle molte immagini offerte da Azure. Le opzioni relative all'immagine dipendono dalla sottoscrizione. Ad esempio, gli abbonati MSDN possono usufruire di immagini desktop.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica. Per altre informazioni su Gestione risorse, vedere [Panoramica di Gestione risorse di Microsoft Azure](resource-group-overview.md).

È inoltre possibile creare macchine virtuali usando le proprie immagini, con modelli di Gestione risorse o con strumenti di automazione. Per altre informazioni sui diversi metodi, vedere [Diversi modi per creare una macchina virtuale Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Procedura dettagliata video

Di seguito è riportata una procedura dettagliata di questa esercitazione.

[AZURE.VIDEO create-a-virtual-machine-running-windows-in-the-azure-preview-portal]

## Selezionare l'immagine della macchina virtuale.

1. Accedere al portale di Azure.

2. Nel menu Hub fare clic su **Nuovo** > **Calcolo** > **Windows Server 2012 R2 Datacenter**.

	![Schermata che mostra le immagini di VM di Azure disponibili nel portale di anteprima](./media/virtual-machines-windows-tutorial/marketplace_new.png)

	>[AZURE.TIP]Per individuare immagini aggiuntive, fare clic su **Marketplace**, quindi eseguire una ricerca o applicare un filtro per gli elementi disponibili.

3. Nella pagina **Windows Server 2012 R2 Datacenter**, in **Selezionare un modello di distribuzione** fare clic su **Gestione risorse**. Fare clic su **Create**.

	![Schermata che mostra il modello di distribuzione da selezionare per una VM di Azure](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## Creare una macchina virtuale

Dopo aver selezionato l'immagine, è possibile usare le impostazioni predefinite di Azure per quasi tutto il processo di configurazione e creare rapidamente la macchina virtuale.

1. Nel pannello **Crea macchina virtuale** fare clic su **Informazioni di base**. Immettere il **Nome** desiderato per la macchina virtuale, il **Nome utente** amministrativo e una **Password** complessa. Se si hanno più sottoscrizioni, specificare quella per la nuova macchina virtuale, un **Gruppo di risorse** nuovo o esistente e la **Località** per il data center di Azure.

	![Schermata che mostra le impostazioni di base da configurare per una VM di Azure](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)

	>[AZURE.NOTE]**Nome utente** indica l'account amministrativo da usare per la gestione del server. Creare una password difficile da scoprire, ma facile da ricordare. **Il nome utente e la password serviranno per accedere alla macchina virtuale**.

2. Fare clic su **Dimensioni** e selezionare le dimensioni di macchina virtuale appropriate in base alle proprie esigenze. Ogni dimensione specifica il numero di core di calcolo, la memoria e altre funzionalità, come il supporto per Archiviazione Premium, che influiranno sul prezzo. Azure consiglia determinate dimensioni automaticamente a seconda dell'immagine scelta.

	![Schermata che mostra le dimensioni di VM di Azure disponibili per la selezione](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE]Il servizio di archiviazione Premium è disponibile per le macchine virtuali della serie DS in alcune aree. Il servizio di archiviazione Premium è la migliore opzione di archiviazione per i carichi di lavoro con utilizzo intensivo, come ad esempio un database. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage-preview-portal.md).

3. Fare clic su **Impostazioni** per visualizzare le impostazioni di archiviazione e di rete per la nuova macchina virtuale. Per la prima macchina virtuale è in genere possibile accettare le impostazioni predefinite. Se sono state selezionate dimensioni di macchina virtuale che lo supportano, è possibile provare il servizio Archiviazione Premium selezionando **Premium (SSD)** in **Tipo di disco**.

	![Schermata che mostra le funzionalità facoltative che è possibile configurare per una VM di Azure](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. Fare clic su **Riepilogo** per esaminare le scelte di configurazione. Al termine della verifica o dell'aggiornamento delle impostazioni, fare clic su **Crea**.

	![Schermata che mostra il riepilogo delle scelte effettuate per la configurazione della VM di Azure](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. Mentre Azure crea la macchina virtuale, è possibile tenere traccia dello stato di avanzamento in **Notifiche**, nel menu Hub. Dopo che Azure ha creato la macchina virtuale, quest'ultima verrà visualizzata nella schermata iniziale a meno che non sia stata deselezionata l'opzione **Aggiungi alla Schermata iniziale** nel pannello **Crea macchina virtuale**.

## Accesso alla macchina virtuale

Dopo aver creato la macchina virtuale, sarà possibile accedervi per gestire le impostazioni e le applicazioni che verranno eseguite al suo interno.

>[AZURE.NOTE]Per informazioni sui requisiti e suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Accedere al portale di Azure, se questa operazione non è già stata eseguita.

2. Fare clic sulla macchina virtuale nella Schermata iniziale. Se è necessario per l'individuazione, fare clic su **Esplora tutto** > **Recenti** o **Esplora tutto** > **Macchine virtuali**. Selezionare quindi la macchina virtuale dall'elenco.

3. Nel pannello della macchina virtuale fare clic su **Connetti**.

	![Schermata che mostra dove trovare il pulsante Connetti nel pannello della VM di Azure](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Fare clic su **Open** per usare il file RDP (Remote Desktop Protocol) creato automaticamente per la macchina virtuale Windows Server.

5. Fare clic su **Connect**.

6. Digitare il nome utente e la password impostati al momento della creazione della macchina virtuale, quindi fare clic su **OK**.

7. Fare clic su **Yes** per verificare l'identità della macchina virtuale.

	È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

## Passaggi successivi

* Usare Azure PowerShell e l'interfaccia della riga di comando di Azure per [individuare e selezionare le immagini della macchina virtuale](resource-groups-vm-searching.md).
* Automatizzare la gestione e la distribuzione della macchina virtuale e del carico di lavoro usando [Gestione risorse di Azure](virtual-machines-how-to-automate-azure-resource-manager.md) e i [modelli di Gestione risorse di Azure](http://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_1203_2015-->