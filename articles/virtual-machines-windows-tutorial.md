<properties 
	pageTitle="Creare una macchina virtuale che esegue Windows in Azure" 
	description="Come creare una macchina virtuale (VM) Windows in Azure, eseguire l'accesso e collegare un disco dati" 
	services="virtual-machines" 
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
	ms.date="03/04/2015" 
	ms.author="kathydav"/>



# Creazione di una macchina virtuale che esegue Windows

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/virtual-machines-windows-tutorial/" title="Azure Portal" class="current">Portale di Azure</a><a href="/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Azure Preview Portal">Portale di anteprima di Azure</a></div>

Questa esercitazione mostra come è facile creare una macchina virtuale di Azure usando come esempio un'immagine di Windows Server, una delle numerose immagini disponibili tramite Azure. Tra queste sono disponibili immagini di sistemi operativi Windows, sistemi operativi basati su Linux e di applicazioni preinstallate. Le immagini che è possibile scegliere dipendono dal tipo di sottoscrizione di cui si dispone. Ad esempio, gli abbonati MSDN possono usufruire di immagini desktop.


È inoltre possibile creare macchine virtuali di Windows usando [le proprie immagini come modelli](virtual-machines-create-upload-vhd-windows-server.md). Per altre informazioni sulle macchine virtuali di Azure, vedere [Panoramica delle macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx).

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Come creare la macchina virtuale

Questa sezione illustra come usare l'opzione **Da raccolta** nel portale di gestione per la creazione di una macchina virtuale. Questa opzione offre un maggior numero di scelte di configurazione rispetto all'opzione **Creazione rapida**. Ad esempio, se si vuole aggiungere una macchina virtuale a una rete virtuale, sarà necessario usare l'opzione **Da raccolta**.

> [AZURE.NOTE] È anche possibile provare il [portale di anteprima di Azure](https://portal.azure.com), più avanzato e personalizzabile, per creare una macchina virtuale, automatizzare la distribuzione di modelli di applicazioni su più macchine virtuali, usare le funzionalità di diagnostica e monitoraggio delle macchine virtuali avanzate e altro ancora. Le opzioni di configurazione per le macchine virtuali disponibili nei due portali sono sostanzialmente simili ma non identiche.  

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Come accedere alla macchina virtuale dopo averla creata 

Questa sezione descrive come accedere alla macchina virtuale per gestirne le impostazioni e le applicazioni che verranno eseguite al suo interno.

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## <a id="attachdisk"> </a>Come collegare un disco dati alla nuova macchina virtuale 

Questa sezione descrive come collegare un disco dati vuoto alla macchina virtuale. Per altre informazioni, incluse informazioni su come collegare dischi esistenti, vedere l'esercitazione [Collegamento di un disco dati](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/).

1. Accedere al [portale di gestione](http://manage.windowsazure.com) di Azure.

2. Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale **MyTestVM**.

	![Select MyTestVM](./media/virtual-machines-windows-tutorial/selectvm.png)
	
3. Potrebbe venire visualizzata la pagina Avvio rapido. In questo caso, fare clic su **Dashboard** nella parte superiore.

	![Select Dashboard](./media/virtual-machines-windows-tutorial/dashboard.png)

4. Sulla barra dei comandi fare clic su **Connetti** e quindi su **Connetti disco vuoto**.

	![Select Attach from the command bar](./media/virtual-machines-windows-tutorial/commandbarattach.png)	

5. Le impostazioni **Nome macchina virtuale**, **Posizione di memorizzazione**, **Nome file** e **Preferenze cache dell'host** sono definite automaticamente. È sufficiente immettere la dimensione desiderata per il disco. Ad esempio, digitare **5** nel campo **Dimensione**. Fare clic sul segno di spunta per connettere il disco.


	>[AZURE.NOTE] Tutti i dischi vengono creati da file .vhd in Archiviazione di Azure. **L'opzione Nome file** consente di rinominare il file con estensione .vhd usato dal disco, non il disco stesso. Azure assegna automaticamente un nome al disco. 

	![Specify the size of the empty disk](./media/virtual-machines-windows-tutorial/emptydisksize.png)	
	
	>[AZURE.NOTE] I file .vhd vengono archiviati come BLOB di pagine in Archiviazione di Azure. Esternamente ad Azure, per i dischi rigidi virtuali può essere usato un formato VHD o VHDX. I dischi possono inoltre essere fissi, espansi dinamicamente o dischi differenze. Azure supporta dischi fissi di formato VHD. Per altre informazioni, vedere [Informazioni sui dischi rigidi virtuali in Azure](http://msdn.microsoft.com/library/azure/dn790344.aspx)  

6. Tornare al dashboard per verificare che il disco dati sia stato connesso correttamente alla macchina virtuale. Verrà visualizzato nell'elenco **Dischi** dopo il disco del sistema operativo.

	![Attach empty disk](./media/virtual-machines-windows-tutorial/disklistwithdatadisk.png)

	Quando viene collegato, il disco dati è offline e non inizializzato. Prima di poterlo usare per l'archiviazione dei dati, sarà necessario accedere alla macchina virtuale e inizializzarlo.

7. Per connettersi e accedere alla macchina virtuale, attenersi ai passaggi descritti nella sezione precedente, [Come accedere alla macchina virtuale dopo averla creata] (#logon).

8. Dopo aver eseguito l'accesso alla macchina virtuale, aprire **Server Manager**. Nel riquadro sinistro fare clic su **Servizi file e archiviazione**.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/fileandstorageservices.png)

9. Selezionare **Dischi** nel menu espanso.

	![Expand File and Storage Services in Server Manager](./media/virtual-machines-windows-tutorial/selectdisks.png)	
	
10.	Nella sezione **Dischi** è visualizzato un elenco con tre dischi: disco 0, disco 1 e disco 2. Disco 0 è il disco del sistema operativo, disco 1 è un disco di risorse temporaneo (che non deve essere usato per l'archiviazione dei dati) e disco 2 è il disco dati connesso alla macchina virtuale. Il disco dati ha una capacità di 5 GB, come specificato quando il disco è stato collegato. Fare clic con il pulsante destro del mouse sul disco 2 e  scegliere **Inizializza**.

	![Start initialization](./media/virtual-machines-windows-tutorial/initializedisk.png)

11. Fare clic su **Sì**.

	![Continue initialization](./media/virtual-machines-windows-tutorial/yesinitialize.png)

12. Fare di nuovo clic con il pulsante destro del mouse sul disco 2 e scegliere **Nuovo volume**. 

	![Create the volume](./media/virtual-machines-windows-tutorial/initializediskvolume.png)

13. Completare la procedura guidata usando i valori predefiniti. Al termine della procedura guidata, nella sezione **Volumi** verrà visualizzato il nuovo volume. Il disco sarà ora online e pronto per l'archiviazione di dati. 

	![Create the volume](./media/virtual-machines-windows-tutorial/newvolumecreated.png)
	
## Passaggi successivi 

Per altre informazioni sulla configurazione di macchine virtuali Windows in Azure, vedere:

[Come connettere le macchine virtuali con una rete virtuale o un servizio cloud](cloud-services-connect-virtual-machine.md)

[Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure](virtual-machines-create-upload-vhd-windows-server.md)

[Gestione della disponibilità delle macchine virtuali](manage-availability-virtual-machines.md)

[Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

[VIDEO: Introduzione ai dischi rigidi virtuali](http://azure.microsoft.com/documentation/videos/getting-started-with-azure-virtual-machines)

[VIDEO: Risposte alle domande frequenti sull'esecuzione di Windows in Azure con Mark Russinovich](http://azure.microsoft.com/documentation/videos/mark-russinovich-windows-on-azure)

[VIDEO: Aggiunta di una nuova macchina virtuale a una Web farm creando immagini riutilizzabili](http://azure.microsoft.com/documentation/videos/adding-virtual-machines-web-farm)

[VIDEO: Aggiunta di dischi rigidi virtuali, account di archiviazione e ridimensionamento di macchine virtuali](http://azure.microsoft.com/documentation/videos/adding-drives-scaling-virtual-machines)

[VIDEO: Scott Guthrie parla delle macchine virtuali](http://azure.microsoft.com/documentation/videos/virtual-machines-scottgu)

[VIDEO: Nozioni di base su archiviazione e dischi con le macchine virtuali di Azure](http://azure.microsoft.com/documentation/videos/storage-and-disks-virtual-machines)



[Informazioni sulle macchine virtuali in Azure]: #virtualmachine
[Come creare la macchina virtuale]: #custommachine
[Come accedere alla macchina virtuale dopo averla creata]: #logon
[Come collegare un disco dati alla nuova macchina virtuale]: #attachdisk
[Come configurare la comunicazione con la macchina virtuale]: #endpoints

<!--HONumber=47-->
