<properties
	pageTitle="Creare la prima macchina virtuale Windows | Microsoft Azure"
	description="Informazioni su come creare la prima macchina virtuale Windows usando il portale di Azure."
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
	ms.date="09/06/2016"
	ms.author="cynthn"/>

# Creare la prima macchina virtuale Windows nel portale di Azure

Questa esercitazione mostra quanto sia facile creare una macchina virtuale Windows in pochi minuti usando il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## Scegliere l'immagine della VM dal Marketplace

Come esempio viene usata un'immagine di Windows Server 2012 R2 Datacenter, che è solo una delle molte immagini disponibili in Azure. Le opzioni relative all'immagine dipendono dalla sottoscrizione. Per gli [abbonati a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) sono ad esempio disponibili immagini desktop.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Scegliere **Nuovo** > **Macchine virtuali** > **Windows Server 2012 R2 Datacenter** dal menu Hub.

	![Screenshot che illustra le immagini di VM di Azure disponibili nel portale](./media/virtual-machines-windows-hero-tutorial/marketplace-new.png)


3. Nel pannello **Windows Server 2012 R2 Datacenter** verificare che in **Selezionare un modello di distribuzione** sia selezionata l'opzione **Resource Manager**. Fare clic su **Create**.

	![Screenshot che mostra il modello di distribuzione da selezionare per la VM](./media/virtual-machines-windows-hero-tutorial/deployment-model.png)

## Creare la macchina virtuale Windows

Dopo aver selezionato l'immagine, è possibile usare le impostazioni predefinite e creare rapidamente la macchina virtuale.

1. Nel pannello **Informazioni di base** immettere un **Nome** per la macchina virtuale. Il nome deve avere una lunghezza compresa tra 1 e 15 caratteri e non può contenere caratteri speciali.

2. Immettere un **Nome utente** e una **Password** complessa, da usare per creare un account locale nella VM. L'account locale viene usato per accedere alla macchina virtuale e gestirla.

	La password deve avere una lunghezza compresa tra 8 e 123 caratteri e soddisfare almeno tre dei quattro requisiti di complessità seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. Sono disponibili altre informazioni sui [requisiti relativi a nome utente e password](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).


3. Selezionare un [gruppo di risorse](../resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo. Immettere la **Località** per un data center di Azure, ad esempio **Stati Uniti occidentali**.

4. Al termine, fare clic su **OK** per passare alla sezione successiva.

	![Screenshot che illustra le impostazioni nel pannello **Informazioni di base** per la configurazione di una macchina virtuale di Azure](./media/virtual-machines-windows-hero-tutorial/basics-blade.png)

	
5. Scegliere le [dimensioni](virtual-machines-windows-sizes.md) per la macchina virtuale e quindi fare clic su **Seleziona** per continuare.

	![Screenshot del pannello Dimensioni che mostra le dimensioni di VM di Azure disponibili per la selezione](./media/virtual-machines-windows-hero-tutorial/size-blade.png)

6. Nel pannello **Impostazioni** è possibile cambiare le opzioni di archiviazione e di rete. Per questa esercitazione, accettare le impostazioni predefinite. Se sono state selezionate dimensioni di macchina virtuale che lo supportano, è possibile provare il servizio Archiviazione Premium di Azure selezionando **Premium (SSD)** in **Tipo di disco**. Al termine delle modifiche, fare clic su **OK**.

	![Screenshot del pannello Impostazioni in cui è possibile configurare le funzionalità facoltative per una VM di Azure](./media/virtual-machines-windows-hero-tutorial/settings-blade.png)

7. Fare clic su **Riepilogo** per verificare le modifiche. Quando viene visualizzato il messaggio **Convalida superata**, fare clic su **OK**.

	![Screenshot della pagina Riepilogo che mostra le scelte di configurazione effettuate per la VM di Azure](./media/virtual-machines-windows-hero-tutorial/summary-blade.png)

8. Mentre Azure crea la macchina virtuale, è possibile tenere traccia dello stato di avanzamento in **Macchine virtuali** nel menu Hub.


## Connettersi alla macchina virtuale ed effettuare l'accesso

1.	Scegliere **Macchine virtuali** dal menu Hub.

2.	Selezionare la macchina virtuale dall'elenco.

3. Nel pannello della macchina virtuale fare clic su **Connetti**. Verrà creato e scaricato un file Remote Desktop Protocol (file con estensione rdp), che è analogo a un collegamento per la connessione alla macchina virtuale. È consigliabile salvare il file sul desktop per semplificare l'accesso. **Aprire** questo file per connettersi alla macchina virtuale.

	![Screenshot del portale di Azure che illustra come connettersi alla VM](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Verrà visualizzato un avviso che indica che l'autore del file RDP è sconosciuto. Si tratta di una situazione normale. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.

	![Screenshot di un avviso relativo a un autore sconosciuto](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Nella finestra Sicurezza di Windows immettere il nome utente e la password per l'account locale creato quando è stata creata la VM. Il nome utente viene immesso come *nomevm*&#92;*nomeutente*. Fare quindi clic su **OK**.

	![Screenshot relativo all'immissione del nome della VM, del nome utente e della password](./media/virtual-machines-windows-hero-tutorial/credentials.png)
 	
6.	Verrà visualizzato un avviso che indica che non è possibile verificare il certificato. Si tratta di una situazione normale. Fare clic su **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.

	![Screenshot che mostra un messaggio sulla verifica dell'identità della VM](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


In caso di problemi quando si cerca di eseguire la connessione, vedere [Risolvere i problemi di connessioni Desktop remoto a una macchina virtuale di Azure che esegue Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

Ora è possibile usare la macchina virtuale come un qualsiasi altro server.



## Facoltativo: Arrestare la macchina virtuale

È consigliabile arrestare la VM, in modo che non vengano effettuati addebiti quando non la si usa. Fare clic su **Arresta** e quindi su **Sì**.

![Screenshot che mostra il pulsante per l'arresto di una VM](./media/virtual-machines-windows-hero-tutorial/stop-vm.png)
	
Fare clic sul pulsante **Avvia** per riavviare la macchina virtuale quando si è pronti per usarla di nuovo.


## Passaggi successivi

- Per provare la nuova macchina virtuale è possibile [installare IIS](virtual-machines-windows-hero-role.md). Questa esercitazione mostra anche come aprire la porta 80 per il traffico Web in ingresso tramite un gruppo di sicurezza di rete (NSG).

- È anche possibile [creare una macchina virtuale Windows con PowerShell](virtual-machines-windows-ps-create.md) o [creare una macchina virtuale Linux in Azure tramite l'interfaccia della riga di comando](virtual-machines-linux-quick-create-cli.md).

- Per automatizzare le distribuzioni, vedere [Creare una macchina virtuale Windows con un modello di Resource Manager](virtual-machines-windows-ps-template.md).

<!---HONumber=AcomDC_0907_2016-->