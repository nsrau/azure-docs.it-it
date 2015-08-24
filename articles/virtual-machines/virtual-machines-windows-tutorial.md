<properties
	pageTitle="Creazione di una macchina virtuale che esegue Windows nel portale di anteprima di Azure | Microsoft Azure"
	description="Leggere le informazioni su come creare una risorsa della macchina virtuale di Azure che esegue Windows, utilizzando Azure Marketplace nel portale di anteprima di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/13/2015"
	ms.author="kathydav"/>

# Creare una macchina virtuale con Windows nel portale di anteprima di Azure#

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource manager](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service management](virtual-machines-ps-create-preconfigure-windows-vms.md)

In questa esercitazione viene illustrato come è facile creare una macchina virtuale di Azure in pochi minuti nel portale di anteprima di Azure. Verrà utilizzata un'immagine di Windows Server 2012 R2 Datacenter come esempio per creare la macchina virtuale in Gestione risorse di Azure, ma è solo una delle molte immagini offerte da Azure. Si noti che le opzioni dell’immagine dipendono dalla sottoscrizione. Ad esempio, gli abbonati MSDN possono usufruire di immagini desktop.

È inoltre possibile creare macchine virtuali utilizzando le proprie immagini, con modelli di Gestione risorse o con strumenti di automazione. Per ulteriori informazioni sui diversi metodi, vedere [Diversi modi per creare una macchina virtuale Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Selezione dell'immagine

Andare in Azure Marketplace nel portale di anteprima per individuare l'immagine della macchina virtuale Windows Server desiderata.

1. Accedere al [portale di anteprima](https://portal.azure.com).

2. Nel menu Hub, fare clic su **Nuovo** > **Calcolo** > **Windows Server 2012 R2 Datacenter**.

	![Marketplace](./media/virtual-machines-windows-tutorial/marketplace_new.png)

	>[AZURE.TIP]Per individuare immagini aggiuntive, fare clic su **Marketplace**, quindi eseguire una ricerca o applicare un filtro per gli elementi disponibili.

3. Nella pagina **Windows Server 2012 R2 Datacenter**, selezionare **Utilizzo dello stack di Gestione risorse** per creare la macchina virtuale in Gestione risorse di Azure. (Per la maggior parte dei nuovi carichi di lavoro, è consigliabile lo stack di Gestione risorse. Per considerazioni, vedere [Provider di calcolo, rete e di archiviazione in Gestione risorse di Azure](virtual-machines-azurerm-versus-azuresm.md).) Quindi fare clic su **Crea**.

	![Ricerca in Marketplace](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## Creazione della macchina virtuale

Dopo aver selezionato l'immagine, è possibile utilizzare le impostazioni predefinite di Azure per la maggior parte della configurazione e creare rapidamente la macchina virtuale.

1. Nel pannello **Crea macchina virtuale**, fare clic su **Informazioni di base**. Immettere un **nome** desiderato per la macchina virtuale, il **nome utente** amministrativo e una **password** complessa. Se si dispone di più sottoscrizioni, specificare quella per la nuova macchina virtuale, un **gruppo di risorse** nuovo o esistente e una **posizione** per il datacenter di Azure.

	![Configurazione delle informazioni di base della macchina virtuale](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)

	>[AZURE.NOTE]**Nome utente** indica l'account amministrativo da utilizzare per la gestione del server. Creare una password difficile da scoprire, ma facile da ricordare. **Il nome utente e la password serviranno per accedere alla macchina virtuale**.

2. Fare clic su **Dimensioni** e selezionare delle dimensioni della macchina virtuale appropriate per le proprie esigenze. Ogni dimensione specifica il numero di core di calcolo, la memoria e altre funzionalità, come il supporto per Archiviazione Premium, che influiranno sul prezzo. Azure consiglia determinate dimensioni automaticamente a seconda dell'immagine scelta.

	![Configurazione delle dimensioni della macchina virtuale](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)

	>[AZURE.NOTE]Il servizio di archiviazione Premium è disponibile per le macchine virtuali della serie DS in alcune aree. Il servizio di archiviazione Premium è la migliore opzione di archiviazione per i carichi di lavoro con utilizzo intensivo, come ad esempio un database. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage-preview-portal.md).

3. Fare clic su **Impostazioni** per visualizzare le impostazioni di archiviazione e di rete per la nuova macchina virtuale. Per una prima macchina virtuale è in genere possibile accettare le impostazioni predefinite. Se sono state selezionate delle dimensioni della macchina virtuale che la supportano, è possibile provare Archiviazione Premium selezionando **Premium (SSD)** sotto **Tipo di disco**.

	![Configurazione delle impostazioni della macchina virtuale](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. Fare clic su **Riepilogo** per esaminare le scelte di configurazione. Al termine della revisione o dell'aggiornamento delle impostazioni, fare clic su **Crea**.

	![Configurazione delle impostazioni della macchina virtuale](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. Mentre Azure crea la macchina virtuale, è possibile tenere traccia dello stato di avanzamento in **Notifiche**, nel menu Hub. Dopo che Azure ha creato la macchina virtuale, quest'ultima verrà visualizzata in Schermata iniziale a meno che non sia stata deselezionata l'opzione **Aggiungi a Schermata iniziale** nel pannello **Crea macchina virtuale**.

## Accesso alla macchina virtuale

Dopo aver creato la VM, sarà possibile accedere alla macchina virtuale per gestire le impostazioni e le applicazioni che verranno eseguite al suo interno.

>[AZURE.NOTE]Per informazioni sui requisiti e suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Accedere al [portale di anteprima](https://portal.azure.com), se questa operazione non è già stata eseguita.

2. Fare clic sulla macchina virtuale nella Schermata iniziale. Se è necessario per l'individuazione, fare clic su **Sfoglia tutto** > **Recenti** o **Sfoglia tutto** > **Macchine virtuali**. Selezionare quindi la VM dall'elenco.

3. Nel pannello della macchina virtuale, fare clic su **Connetti**.

	![Accesso alla macchina virtuale](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Fare clic su **Apri** per utilizzare il file RDP (Remote Desktop Protocol) creato automaticamente per la macchina virtuale Windows Server.

5. Fare clic su **Connect**.

6. Digitare il nome utente e la password impostati al momento della creazione della macchina virtuale, quindi fare clic su **OK**.

7. Fare clic su **Yes** per verificare l'identità della macchina virtuale.

	È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

## Passaggi successivi

* Utilizzare Azure PowerShell e l'interfaccia della riga di comando di Azure per [individuare e selezionare le immagini della macchina virtuale](resource-groups-vm-searching.md).
* Automatizzare la distribuzione della macchina virtuale e del carico di lavoro e la gestione utilizzando [Gestione risorse di Azure](virtual-machines-how-to-automate-azure-resource-manager.md) e i [modelli di Gestione risorse di Azure](http://azure.microsoft.com/it-it/documentation/templates/).

<!---HONumber=August15_HO7-->