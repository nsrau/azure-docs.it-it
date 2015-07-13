<properties
	pageTitle="Come usare Docker rapidamente con l'immagine di macchina virtuale Ubuntu-Docker"
	description="Descrive e dimostra come usare Docker in Ubuntu Server nel giro di pochi minuti direttamente dalla raccolta immagini di Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure"
	ms.date="05/20/2015"
	ms.author="rasquill"/>

# Come iniziare a usare rapidamente Docker in Azure Marketplace

Il modo più rapido per iniziare a utilizzare [Docker] consiste nell’andare in Azure Marketplace e creare una macchina virtuale utilizzando il modello di immagine **Docker su server Ubuntu** creata da [Canonical] in collaborazione con [MSOpenTech]. Ciò consente di creare una macchina virtuale Ubuntu Server e di installare automaticamente l'[estensione della VM Docker](virtual-machines-docker-vm-extension.md) e il motore Docker **più recente** preinstallato ed eseguito in Azure.

È possibile connettersi immediatamente alla VM usando SSH e iniziare a lavorare con Docker direttamente, senza che sia necessario intraprendere altre azioni.

> [AZURE.NOTE]La VM creata dal modello di Azure Marketplace non ospita l'API remota del Docker per la gestione da parte di un client Docker remoto. Per abilitare il controllo dell'host Docker in questa macchina virtuale in remoto, vedere [Esecuzione di Docker con HTTPS](https://docs.docker.com/articles/https/) o seguire i passaggi descritti in [Using the Docker VM Extension from the Azure Portal](virtual-machines-docker-with-portal.md) o [Using the Docker VM Extension from the Azure CLI](virtual-machines-docker-with-xplat-cli.md). Gli utenti più avanzati possono compilare il [client Windows Docker](https://github.com/ahmetalpbalkan/Docker.DotNet) da Github e provare anche quello (oppure ottenerlo da [nuget](https://www.nuget.org/packages/Docker.DotNet/)).

Contenuto dell'argomento:

- [Accedere al portale]
- [Creare una macchina virtuale con l'immagine Docker di Canonical e MSOpenTech]
- [Connettersi tramite SSH e operare remotamente]

## <a id='logon'>Accedere al portale</a>

Questa è la parte più facile, a meno che non si sia ancora privi di un account Azure. [È altrettanto facile ottenerne uno gratuitamente](http://azure.microsoft.com/pricing/free-trial/).

## <a id='createvm'>Creare una macchina virtuale con l'immagine Docker di Canonical e MSOpenTech</a>

1. Una volta eseguita la connessione, fare clic su **Nuovo** in basso a sinistra per creare una nuova immagine di macchina virtuale. L'immagine desiderata potrebbe essere visualizzata nel banner immediatamente,

> ![Scegliere l'immagine Ubuntu Docker nell'intestazione](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. in caso contrario cercarla nella raccolta immagini:

> ![Individuare l'immagine nella raccolta immagini](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. Fornire il nome utente e la password per l'istanza oppure un file **.pem** per abilitare SSH con l'uso di un certificato. Il grafico in basso mostra l'immissione di una combinazione di nome utente e password. Premere quindi **Crea** nella parte inferiore dello schermo.

> ![Scegliere un’istanza di macchina virtuale](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. Attendere l'avvio dell'esecuzione: ciò non dovrebbe richiedere molto tempo.

> ![Immagine docker in esecuzione nel portale](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## <a id='havingfun'>Connettersi con SSH e operare remotamente</a>

A questo punto è possibile iniziare a sperimentare i comandi. È possibile connettersi immediatamente alla VM usando SSH

> ![Connessione tramite SSH](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

e iniziare a usare i comandi di Docker, ricordando che in questa macchina virtuale di Azure la configurazione predefinita richiede **`sudo`**:

> ![Estrazione di immagini](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Ora è possibile iniziare a usare [Docker].

<!--Anchors-->
[Accedere al portale]: #logon
[Creare una macchina virtuale con l'immagine Docker di Canonical e MSOpenTech]: #createvm
[Connettersi tramite SSH e operare remotamente]: #havingfun
[Next steps]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Docker scratch image]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/
 

<!---HONumber=July15_HO1-->