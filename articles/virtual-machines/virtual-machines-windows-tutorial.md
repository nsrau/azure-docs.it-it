<properties
	pageTitle="Creare una macchina virtuale con Windows nel portale di anteprima di Azure"
	description="Informazioni su come creare una macchina virtuale di Azure che esegue Windows usando Azure Marketplace nel portale di anteprima di Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>
<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/03/2015"
	ms.author="kathydav"/>

# Creare una macchina virtuale con Windows nel portale di anteprima di Azure#

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)

Questa esercitazione illustra la facilità di creazione di un macchina virtuale di Azure (VM) nel Portale di anteprima di Azure. Si utilizzerà un'immagine di Windows Server come esempio, ma questa è solo una delle molte immagini disponibili in Azure. Si noti che le opzioni dell’immagine dipendono dalla sottoscrizione. Ad esempio, gli abbonati MSDN possono usufruire di immagini desktop.

È inoltre possibile creare VM usando le [proprie immagini](virtual-machines-create-upload-vhd-windows-server.md). Per ulteriori informazioni su questo e altri metodi, vedere [Diversi modi per creare una macchina virtuale Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Creazione della macchina virtuale

Prendendo Windows Server 2012 R2 Datacenter come esempio, è possibile creare una VM di prova in pochi minuti. È possibile usare le impostazioni predefinite di Azure per quasi tutto il processo di configurazione.

1. Accedere al [Portale di anteprima](https://portal.azure.com).

2. Nel menu Hub fare clic su **Nuovo**.

3. Nel pannello **Nuovo**, fare clic su **Calcolo** **>** **Windows Server 2012 R2 Datacenter**.

	![Selezionare un'immagine di VM dal Marketplace.](./media/virtual-machines-windows-tutorial/marketplace_portal.png)

4. Nel pannello **Crea macchina virtuale** inserire il **Nome host** scelto per la macchina virtuale, il **Nome utente** amministrativo e una **Password** complessa. **Nome utente** indica l'account amministrativo da usare per la gestione del server. Creare una password difficile da scoprire, ma facile da ricordare. **Il nome utente e la password serviranno per accedere alla macchina virtuale**. Se si dimentica la password, è possibile ripristinarla tramite [queste istruzioni](virtual-machines-windows-reset-password.md)

	![Configurare il nome host e le credenziali di accesso](./media/virtual-machines-windows-tutorial/create_vm_name_pwd_portal.png)

5. Esaminare le impostazioni predefinite, ad esempio il **Livello di prezzo** e **Configurazione opzionale**. Queste opzioni influiscono sulla dimensione della macchina virtuale, nonché sulle opzioni di rete, ad esempio l'appartenenza al dominio. Ad esempio, per provare il servizio di archiviazione Premium in una macchina virtuale, è necessario selezionare un'area e una dimensione che la supporta. Per la prima macchina virtuale, le impostazioni predefinite sono solitamente corrette.

	>[AZURE.NOTE]Il servizio di archiviazione Premium è disponibile per le macchine virtuali della serie DS in alcune aree. Il servizio di archiviazione Premium è la migliore opzione di archiviazione per i carichi di lavoro con utilizzo intensivo, come ad esempio un database. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage-preview-portal.md).

6. Al termine della revisione o dell'aggiornamento delle impostazioni, fare clic su **Crea**.

7. Mentre Azure crea la macchina virtuale, è possibile tenere traccia dello stato di avanzamento in **Notifiche** nel menu Hub. Dopo che Azure ha creato la VM, questa verrà visualizzata nella Schermata iniziale a meno che non sia stata l’opzione **Aggiungi alla Schermata iniziale** nel pannello **Crea VM**.

## Accesso alla macchina virtuale

Dopo aver creato la VM, sarà possibile accedere alla macchina virtuale per gestire le impostazioni e le applicazioni che verranno eseguite al suo interno.

>[AZURE.NOTE]Per informazioni sui requisiti e suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

1. Accedere al [Portale di anteprima](https://portal.azure.com), se questa operazione non è già stata eseguita.

2. Fare clic sulla macchina virtuale nella Schermata iniziale. Per trovarla, fare clic su **Sfoglia** > **Macchine virtuali**. Selezionare quindi la VM dall'elenco.

3. Nel pannello della VM fare clic su **Connetti**.

	![Accesso alla macchina virtuale](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. Fare clic su **Apri** per usare il file RDP (Remote Desktop Protocol) creato automaticamente per la macchina virtuale.

5. Fare clic su **Connect**.

6. Digitare il nome utente e la password specificati al momento della creazione della macchina virtuale, quindi fare clic su **OK**.

7. Fare clic su **Yes** per verificare l'identità della macchina virtuale.

	È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

## Passaggi successivi

Per altre informazioni sulla configurazione di macchine virtuali Windows in Azure, vedere gli articoli seguenti:

[Connettere le Macchine virtuali con una Rete virtuale o un servizio cloud](cloud-services-connect-virtual-machine.md)

[Connettere un disco dati a una macchina virtuale](storage-windows-attach-disk.md)

[Gestione della disponibilità delle macchine virtuali](../manage-availability-virtual-machines.md)

[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)
 

<!---HONumber=July15_HO2-->