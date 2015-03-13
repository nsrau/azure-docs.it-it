<properties 
	pageTitle="Creare una macchina virtuale con Windows nel portale di anteprima di Azure" 
	description="Informazioni su come creare una macchina virtuale di Azure che esegue Windows usando Azure Marketplace nel portale di anteprima di Azure" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="danlep,kathydav,rasquill"/>

<!---Workflow can be confusing...have to select the correct size with **Browse all pricing tiers** then click Select on that pane and the **Recommended pricing tiers** pane to apply. But even after that, it didn't seem to pick the Premium storage account...showed up as 'standard GRS' when I inspected what type of storage account would be used for the VM. 
-->

<!-- Preview portal screenshots getting a little out of date. Please refresh at next update.
-->

# Creare una macchina virtuale nel portale di anteprima di Azure#

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal">Portale di Azure</a><a href="/it-it/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal" class="current">Portale di anteprima di Azure</a></div>

Questa esercitazione mostra come è facile creare una macchina virtuale di Azure che esegue Windows usando come esempio un'immagine di Windows Server da Azure Marketplace nel portale di anteprima di Azure. In Azure Marketplace è disponibile un'ampia gamma di immagini, ad esempio immagini di sistemi operativi Windows, di sistemi operativi basati su Linux e di applicazioni. 

> [AZURE.NOTE] Per completare questa esercitazione, non è necessario essere esperti di macchine virtuali di Azure. È invece richiesto un account Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni, vedere [Creare un account Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/). 

In questa esercitazione si apprenderà:

- [Come creare la macchina virtuale](#createvirtualmachine)
- [Come accedere alla macchina virtuale dopo averla creata](#logon)

Per altre informazioni, vedere [Macchine virtuali](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Come creare la macchina virtuale##

Questa sezione mostra come usare il portale di anteprima per creare una macchina virtuale usando Windows Server 2012 R2 Datacenter come esempio. È possibile usare le impostazioni predefinite per quasi tutto il processo di configurazione e creare la macchina virtuale in pochi minuti.

> [AZURE.NOTE] Le immagini disponibili dipendono dalla sottoscrizione dell'utente. Questa esercitazione usa un'immagine di Windows Server, ma un abbonamento MSDN può offrire immagini aggiuntive, incluse le immagini desktop. 
 

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com). Se non si dispone ancora di una sottoscrizione, usare la [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

2. Nel menu Hub fare clic su **Nuovo**.

	![Select New from the Command Bar](./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png)

3. Nel pannello **Nuovo** fare clic su **Tutto**, quindi su **Macchine virtuali**, **Windows Server 2012 R2 Datacenter** e infine su **Crea**.

	![Select a VM image from the Gallery](./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png)
	
4. Nel pannello **Crea macchina virtuale** inserire il **Nome host** scelto per la macchina virtuale, il **Nome utente** amministrativo e una **Password** complessa.

	>[AZURE.NOTE] **Nome utente** indica l'account amministrativo da usare per la gestione del server. Creare una password univoca per questo account e annotarla per evitare di dimenticarla. **Il nome utente e la password serviranno per accedere alla macchina virtuale**.
	

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_name_pwd_preview_portal.png)
	
	
5. Esaminare le impostazioni predefinite, ad esempio **Piano tariffario**, **Configurazione facoltativa** e **Percorso**. Queste opzioni influiscono sulla dimensione della macchina virtuale, nonché sulle opzioni di rete, ad esempio l'appartenenza al dominio. Ad esempio, per provare il servizio di archiviazione Premium in una macchina virtuale, è necessario selezionare un'area e una dimensione che la supporta. 

	>[AZURE.NOTE] Il servizio di archiviazione Premium è in disponibile in anteprima per le macchine virtuali della serie DS in alcune aree. Per informazioni dettagliate, vedere [Archiviazione Premium: archiviazione dalle prestazioni elevate per carichi di lavoro di macchine virtuali di Azure](http://azure.microsoft.com/documentation/articles/storage-premium-storage-preview-portal/).

	![Configure host name and log on credentials](./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png)
	
6. Al termine della revisione o dell'aggiornamento delle impostazioni, fare clic su **Crea**.	

7. Mentre Azure crea la macchina virtuale, è possibile tenere traccia dello stato di avanzamento **Notifiche** nel menu Hub. Una volta creata, la macchina virtuale verrà visualizzata nella Schermata iniziale.

	![VM appears on the Startboard](./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png)

## <a id="logon"> </a>Come accedere alla macchina virtuale dopo averla creata ##

Questa sezione descrive come accedere alla macchina virtuale per gestirne le impostazioni e le applicazioni che verranno eseguite al suo interno.

>[AZURE.NOTE] Per informazioni sui requisiti e suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com) se questa operazione non è già stata eseguita.

2. Fare clic sulla macchina virtuale nella Schermata iniziale. Per trovarla, fare clic su **Sfoglia**, quindi su **Macchine virtuali**. 

	![Browse to find the VM](./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png)

3. Selezionare quindi la macchina virtuale dall'elenco. 

	![Select your VM name from the list](./media/virtual-machines-windows-tutorial-azure-preview/vm_select_preview_portal.png)

4. Nel pannello della macchina virtuale fare clic su **Connetti** in alto.

	![Log on to the virtual machine](./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png)

5. Fare clic su **Apri** per usare il file RDP (Remote Desktop Protocol) creato automaticamente per la macchina virtuale.
	
6. Fare clic su **Connetti** per procedere con la connessione.

	![Continue with connecting](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

7. Digitare il nome utente e la password dell'account amministrativo della macchina virtuale, quindi fare clic su **OK**.
	
8. Fare clic su **Sì** per verificare l'identità della macchina virtuale.

	![Verify the identity of the machine](./media/virtual-machines-log-on-windows-server/connectverify.png)

	È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

##Passaggi successivi 

Per altre informazioni sulla configurazione di macchine virtuali Windows in Azure, vedere gli articoli seguenti:

[How to Connect Virtual Machines in a Cloud Service](http://azure.microsoft.com/documentation/articles/cloud-services-connect-virtual-machine/)

[Come creare e caricare un disco rigido virtuale che contiene il sistema operativo Windows Server](http://azure.microsoft.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Connettere un disco dati a una macchina virtuale](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)

[Gestire la disponibilità delle macchine virtuali](http://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/)

[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[Come creare la macchina virtuale]: #custommachine
[Come accedere alla macchina virtuale dopo averla creata]: #logon


<!--HONumber=42-->
