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
	ms.date="02/02/2015" 
	ms.author="rasquill"/>

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Come iniziare a usare rapidamente Docker in Azure Marketplace 

Il modo più rapido per iniziare a usare [Docker] è andare in Azure Marketplace e creare una VM che usi il modello di immagine **Docker on Ubuntu Server** creato da [Canonical] in collaborazione con [MSOpenTech]. Ciò consente di creare una VM Ubuntu Server e di installare automaticamente l'[estensione della VM Docker](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/) e il motore Docker **più recente** preinstallato ed eseguito in Azure.  

È possibile connettersi immediatamente alla VM usando SSH e iniziare a lavorare con Docker direttamente, senza che sia necessario intraprendere altre azioni. 

> [AZURE.NOTE]La VM creata dal modello di Azure Marketplace non ospita l'API remota del Docker per la gestione da parte di un client Docker remoto. Per abilitare il controllo dell'host Docker nella VM da remoto, vedere [Esecuzione di Docker con HTTPS](https://docs.docker.com/articles/https/) o seguire i passaggi negli articoli relativi all'[uso dell'estensione della VM Docker dal portale di Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/) o all'[uso dell'estensione della VM Docker dall'interfaccia della riga di comando azure-cli](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/). Gli utenti più avanzati possono compilare [Windows Docker Client](https://github.com/ahmetalpbalkan/Docker.DotNet) da Github e provare anche quello (oppure ottenerlo da [nuget](https://www.nuget.org/packages/Docker.DotNet/)). 

Contenuto dell'argomento:

- [Accedere al portale]
- [Creare una VM con l'immagine Docker di Canonical e MSOpenTech]
- [Connettersi con SSH e operare remotamente]

## <a id='logon'>Accedere al portale</a>

Questa è la parte più facile, a meno che non si sia ancora privi di un account Azure. [Ma è altrettanto facile ottenerne uno gratuitamente](http://azure.microsoft.com/pricing/free-trial/).

## <a id='createvm'>Creare una VM con l'immagine Docker di Canonical e MSOpenTech</a>

1. Una volta eseguita la connessione, fare clic su **Nuovo** in basso a sinistra per creare una nuova immagine di macchina virtuale. L'immagine desiderata potrebbe essere visualizzata nel banner immediatamente,

> ![Choose the Docker Ubuntu image in the banner](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. in caso contrario cercarla nella raccolta immagini: 

> ![Locate the image in the Image Gallery](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. fornire il nome utente e la password per l'istanza oppure un file **.pem** per abilitare SSH con l'uso di un certificato. Il grafico in basso mostra l'immissione di una combinazione di nome utente e password. Premere quindi **Crea** nella parte inferiore dello schermo.

> ![Configure the vm instance](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. Attendere l'avvio dell'esecuzione: ciò non dovrebbe richiedere molto tempo.

> ![Docker image running in portal](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## <a id='havingfun'>Connettersi con SSH e operare remotamente</a>

A questo punto è possibile iniziare a sperimentare i comandi. È possibile connettersi immediatamente alla VM usando SSH

> ![Connecting with SSH](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

e iniziare a usare i comandi di Docker, ricordando che in questa macchina virtuale di Azure la configurazione di base richiede il prefisso **`sudo`**:

> ![Pulling images](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Ora è possibile iniziare a usare [Docker]. 

<!--Anchors-->
[Accedere al portale]: #logon
[Creare una VM con l'immagine Docker di Canonical e MSOpenTech]: #createvm
[Connettersi con SSH e operare remotamente]: #havingfun
[Passaggi successivi]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Immagine scratch di Docker]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/


<!--HONumber=45--> 
