<properties
	pageTitle="Creazione di una macchina virtuale di Azure che esegue Linux nel portale di Azure"
	description="Utilizzare il portale di Azure per creare una macchina virtuale di Azure che esegue Linux con i gruppi di risorse di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="rasquill"/>

# Creazione di una macchina virtuale che esegue Linux tramite il portale di anteprima di Azure

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [Azure Preview Portal](virtual-machines-linux-tutorial-portal-rm.md)

La creazione di una macchina virtuale di Azure che esegue Linux è un'operazione semplice. In questa esercitazione viene illustrato come utilizzare il portale di anteprima di Azure per creare rapidamente una macchina virtuale e viene utilizzato il file di chiave pubblica `~/.ssh/id_rsa.pub` per proteggere la connessione **SSH** alla macchina virtuale. È inoltre possibile creare macchine virtuali di Linux utilizzando [le proprie immagini come modelli](virtual-machines-linux-create-upload-vhd.md).

> [AZURE.NOTE]In questa esercitazione viene creata una macchina virtuale di Azure gestita dall'API del gruppo di risorse di Azure. Per informazioni dettagliate, vedere [Panoramica del gruppo di risorse di Azure](resource-group-overview.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Selezione dell'immagine

Andare in Azure Marketplace nel portale di anteprima per individuare l'immagine della macchina virtuale Windows Server desiderata.

1. Accedere al [portale di anteprima](https://portal.azure.com).

2. Dal menu Hub, fare clic su **Nuovo** > **Calcolo** > **Ubuntu Server 14.04 LTS**.

	![Scelta dell’immagine di una macchina virtuale](media/virtual-machines-linux-tutorial-portal-rm/chooseubuntuvm.png)

	> [AZURE.TIP]Per individuare immagini aggiuntive, fare clic su **Marketplace**, quindi eseguire una ricerca o applicare un filtro per gli elementi disponibili.

3. Nella parte inferiore della pagina **Ubuntu Server 14.04 LTS**, selezionare **Usa lo stack di Gestione risorse** per creare la macchina virtuale in Gestione risorse di Azure. Per la maggior parte dei nuovi carichi di lavoro, è consigliabile lo stack di Gestione risorse. Per considerazioni, vedere [Provider di calcolo, rete e archiviazione in Gestione risorse di Azure](virtual-machines-azurerm-versus-azuresm.md).

4. Quindi fare clic su ![Pulsante Crea](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png).

	![Passaggio allo stack di calcolo di Gestione risorse](media/virtual-machines-linux-tutorial-portal-rm/changetoresourcestack.png)

## Creazione della macchina virtuale

Dopo aver selezionato l'immagine, è possibile utilizzare le impostazioni predefinite di Azure per la maggior parte della configurazione e creare rapidamente la macchina virtuale.

1. Nel pannello **Crea macchina virtuale**, fare clic su **Informazioni di base**. Immettere un **nome** per la macchina virtuale e un file di chiave pubblica (nel formato **ssh-rsa**, in questo caso dal file `~/.ssh/id_rsa.pub`). Se si dispone di più sottoscrizioni, specificarne una per la nuova macchina virtuale, nonché un **gruppo di risorse** nuovo o esistente e una **posizione** per il datacenter di Azure.

	![](media/virtual-machines-linux-tutorial-portal-rm/step-1-thebasics.png)

	> [AZURE.NOTE]Inoltre è possibile scegliere l'autenticazione nome utente/password qui e immettere tali informazioni se non si desidera proteggere la sessione **ssh** con uno scambio di chiavi pubbliche e private.

2. Fare clic su **Dimensioni** e selezionare delle dimensioni della macchina virtuale appropriate per le proprie esigenze. Ogni dimensione specifica il numero di core di calcolo, la memoria e altre funzionalità, come il supporto per Archiviazione Premium, che influiranno sul prezzo. Azure consiglia determinate dimensioni automaticamente a seconda dell'immagine scelta. Al termine, fare clic su ![Pulsante Seleziona](media/virtual-machines-linux-tutorial-portal-rm/selectbutton-size.png).

	>[AZURE.NOTE]Il servizio di archiviazione Premium è disponibile per le macchine virtuali della serie DS in alcune aree. Il servizio di archiviazione Premium è la migliore opzione di archiviazione per i carichi di lavoro con utilizzo intensivo, come ad esempio un database. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage-preview-portal.md).

3. Fare clic su **Impostazioni** per visualizzare le impostazioni di archiviazione e di rete per la nuova macchina virtuale. Per una prima macchina virtuale è in genere possibile accettare le impostazioni predefinite. Se sono state selezionate delle dimensioni della macchina virtuale che la supportano, è possibile provare Archiviazione Premium selezionando **Premium (SSD)** sotto **Tipo di disco**. Al termine, fare clic su ![Pulsante OK](media/virtual-machines-linux-tutorial-portal-rm/okbutton.png).

	![](media/virtual-machines-linux-tutorial-portal-rm/step-3-settings.png)

6. Fare clic su **Riepilogo** per esaminare le scelte di configurazione. Al termine della revisione o dell'aggiornamento delle impostazioni, fare clic su ![Pulsante OK](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png) .

	![Riepilogo creazione](media/virtual-machines-linux-tutorial-portal-rm/summarybeforecreation.png)

8. Mentre Azure crea la macchina virtuale, è possibile tenere traccia dello stato di avanzamento in **Notifiche**, nel menu Hub. Dopo che Azure ha creato la VM, quest’ultima verrà visualizzata in Schermata iniziale a meno che non sia stata deselezionata l’opzione **Aggiungi a Schermata iniziale** nel pannello **Crea macchina virtuale**.

	> [AZURE.NOTE]Il riepilogo non contiene un nome DNS pubblico come quando una macchina virtuale viene creata all'interno di un servizio cloud tramite lo stack di calcolo di Gestione servizi.

## Connessione alla macchina virtuale di Linux Azure tramite **ssh**

Ora è possibile eseguire la connessione alla macchina virtuale Ubuntu tramite **ssh** in modalità standard. Tuttavia, sarà necessario individuare l'indirizzo IP assegnato alla macchina virtuale di Azure aprendo il riquadro per la macchina virtuale e le relative risorse. È possibile eseguire questa operazione facendo clic su **Sfoglia**, quindi selezionando **Recenti** e cercando la macchina virtuale creata oppure facendo clic sul riquadro creato appositamente in Schermata iniziale. In entrambi i casi, individuare e copiare il valore di **Indirizzo IP pubblico**, mostrato nel grafico seguente.

![Riepilogo creazione avvenuta correttamente](media/virtual-machines-linux-tutorial-portal-rm/successresultwithip.png)

Ora è possibile aggiungere **ssh** alla macchina virtuale di Azure e proseguire.

	ssh ops@23.96.106.1 -p 22
	The authenticity of host '23.96.106.1 (23.96.106.1)' can't be established.
	ECDSA key fingerprint is bc:ee:50:2b:ca:67:b0:1a:24:2f:8a:cb:42:00:42:55.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '23.96.106.1' (ECDSA) to the list of known hosts.
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-43-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Mon Jul 13 21:36:28 UTC 2015

	  System load: 0.31              Memory usage: 5%   Processes:       208
	  Usage of /:  42.1% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@ubuntuvm:~$


> [AZURE.NOTE]È inoltre possibile configurare un nome di dominio completo (FQDN) per la macchina virtuale nel portale. Ulteriori informazioni sul FQDN [qui](virtual-machines-create-fqdn-on-portal.md).

## Passaggi successivi

Per altre informazioni su Linux in Azure, vedere:

- [Computing Linux e open source in Azure](virtual-machines-linux-opensource.md)

- [Come usare gli strumenti da riga di comando di Azure per Mac e Linux](virtual-machines-command-line-tools.md)

- [Distribuire un'applicazione LAMP utilizzando l'estensione CustomScript di Azure per Linux](virtual-machines-linux-script-lamp.md)

- [Estensione della macchina virtuale Docker per Linux in Azure](virtual-machines-docker-vm-extension.md)

<!---HONumber=Sept15_HO4-->