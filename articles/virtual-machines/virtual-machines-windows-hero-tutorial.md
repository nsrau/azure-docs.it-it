<properties
	pageTitle="Creare la prima macchina virtuale Windows | Microsoft Azure"
	description="Informazioni su come creare la prima macchina virtuale Windows nel portale di Azure."
	keywords="Macchina virtuale di Windows,creare una macchina virtuale,computer virtuale,configurazione di una macchina virtuale"
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
	ms.topic="hero-article"
	ms.date="05/05/2016"
	ms.author="cynthn"/>

# Creare la prima macchina virtuale Windows nel portale di Azure

Questa esercitazione illustra come è facile creare una VM Windows in pochi minuti nel portale di Azure.

Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.

Ecco un [video con la procedura dettagliata](https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Create-A-Virtual-Machine-Running-Windows-In-The-Azure-Preview-Portal) di questa esercitazione.


## Scegliere l'immagine della VM dal Marketplace

Come esempio viene usata un'immagine di Windows Server 2012 R2 Datacenter, che è solo una delle molte immagini disponibili in Azure. Le opzioni relative all'immagine dipendono dalla sottoscrizione. Per gli [abbonati MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) sono ad esempio disponibili immagini desktop.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Scegliere **Nuovo** > **Macchine virtuali** > **Windows Server 2012 R2 Datacenter** dal menu Hub.

	![Screenshot che illustra le immagini di VM di Azure disponibili nel portale](./media/virtual-machines-windows-hero-tutorial/marketplace-new.png)


3. Nella pagina **Windows Server 2012 R2 Datacenter** verificare che in **Selezionare un modello di distribuzione** sia selezionata l'opzione **Resource Manager**. Fare clic su **Create**.

	![Screenshot che mostra il modello di distribuzione da selezionare per la VM](./media/virtual-machines-windows-hero-tutorial/deployment-model.png)

## Creare la macchina virtuale Windows

Dopo aver selezionato l'immagine, è possibile usare le impostazioni predefinite di Azure per quasi tutto il processo di configurazione e creare rapidamente la macchina virtuale.

1. Nel pannello **Informazioni di base** immettere un **Nome** per la macchina virtuale. Il nome deve avere una lunghezza compresa tra 1 e 15 caratteri e non può contenere caratteri speciali.

2. Immettere un **Nome utente** e una **Password** complessa, da usare per creare un account locale nella VM. L'account locale viene usato per accedere alla macchina virtuale e gestirla.

	La password deve avere una lunghezza compresa almeno tra 8 e 123 caratteri e includere almeno 3 degli elementi seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale.


3. Selezionare un [Gruppo di risorse](../resource-group-overview.md#resource-groups) esistente o immettere il nome per un nuovo gruppo. Immettere la **Località** per un data center di Azure, ad esempio **Stati Uniti occidentali**.

4. Al termine, fare clic su **OK** per passare alla sezione successiva.

	![Screenshot che illustra le impostazioni nel pannello Informazioni di base per la configurazione di una macchina virtuale di Azure](./media/virtual-machines-windows-hero-tutorial/basics-blade.png)

	
5. Scegliere le [dimensioni](virtual-machines-windows-sizes.md) per la VM e quindi fare clic su **Seleziona** per continuare.

	![Screenshot del pannello Dimensioni che mostra le dimensioni di VM di Azure disponibili per la selezione](./media/virtual-machines-windows-hero-tutorial/size-blade.png)

6. Nel pannello **Impostazioni** è possibile cambiare le opzioni di archiviazione e di rete. Per la prima macchina virtuale è in genere possibile accettare le impostazioni predefinite. Se sono state selezionate dimensioni di macchina virtuale che lo supportano, è possibile provare il servizio Archiviazione Premium selezionando **Premium (SSD)** in **Tipo di disco**. Al termine delle modifiche, fare clic su **OK**.

	![Screenshot del pannello Impostazioni in cui è possibile configurare le funzionalità facoltative per una VM di Azure](./media/virtual-machines-windows-hero-tutorial/settings-blade.png)

7. Fare clic su **Riepilogo** per verificare le modifiche. Al termine, fare clic su **OK**.

	![Screenshot della pagina Riepilogo che mostra le scelte di configurazione effettuate per la VM di Azure](./media/virtual-machines-windows-hero-tutorial/summary-blade.png)

8. Mentre Azure crea la macchina virtuale, è possibile tenere traccia dello stato di avanzamento in **Macchine virtuali** nel menu Hub.


## Connettersi alla macchina virtuale ed effettuare l'accesso

1.	Scegliere **Macchine virtuali** dal menu Hub.

2.	Selezionare la macchina virtuale dall'elenco.

3. Nel pannello della macchina virtuale fare clic su **Connetti**. Verrà creato e scaricato un file Remote Desktop Protocol (file con estensione rdp), che è analogo a un collegamento per la connessione alla macchina virtuale. È consigliabile salvare il file sul desktop per semplificare l'accesso. **Aprire** questo file per connettersi alla macchina virtuale.

	![Screenshot del portale di Azure che illustra come connettersi alla VM.](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Verrà visualizzato un avviso che indica che l'autore del file con estensione rdp è sconosciuto. Si tratta di una situazione normale. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.

	![Screenshot di un avviso relativo a un autore sconosciuto.](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Nella finestra Sicurezza di Windows immettere il nome utente e la password per l'account locale creato quando è stata creata la VM. Il nome utente viene immesso come *nomevm*&#92;*nomeutente*. Fare quindi clic su **OK**.

	![Screenshot relativa all'immissione del nome della VM, del nome utente e della password.](./media/virtual-machines-windows-hero-tutorial/credentials.png)
 	
6.	Verrà visualizzato un avviso che indica che non è possibile verificare il certificato. Si tratta di una situazione normale. Fare clic su **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.

	![Screenshot che visualizza un messaggio sulla verifica dell'identità della VM.](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


In caso di problemi quando si cerca di eseguire la connessione, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure basata su Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

È ora possibile lavorare con la macchina virtuale nello stesso modo in cui lo si farebbe con qualsiasi altro server.

## Arrestare la VM

È consigliabile arrestare la VM, in modo che non vengano effettuati addebiti quando non la si usa. È sufficiente fare clic sul pulsante **Arresta** e quindi su **Sì**.

![Screenshot che mostra il pulsante per l'arresto di una VM.](./media/virtual-machines-windows-hero-tutorial/stop-vm.png)
	
Fare clic sul pulsante **Avvia** per riavviare la VM quando si è pronti per usarla di nuovo.


## Passaggi successivi

* È anche possibile sperimentare con il [collegamento di un disco dati](virtual-machines-windows-attach-disk-portal.md) a una macchina virtuale. I dischi dati forniscono più risorse di archiviazione per la macchina virtuale.

* È anche possibile [creare una VM Windows usando Powershell](virtual-machines-windows-ps-create.md) o [creare una macchina virtuale Linux](virtual-machines-linux-quick-create-cli.md) usando l'interfaccia della riga di comando di Azure.

<!---HONumber=AcomDC_0511_2016-->