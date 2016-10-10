<properties
	pageTitle="Creazione di set di scalabilità dall'interfaccia della riga di comando del portale | Microsoft Azure"
	description="Distribuire i set di scalabilità tramite il portale di Azure."
	keywords="set di scalabilità di macchine virtuali" 
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gatneil"
	manager="madhana"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machine"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="gatneil"/>

# Creare un set di scalabilità tramite il portale di Azure

Questa esercitazione mostra quanto sia facile creare un set di scalabilità di macchine virtuali in pochi minuti usando il portale di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## Scegliere l'immagine della VM dal Marketplace

Dal portale è possibile distribuire facilmente un set di scalabilità con CentOS, CoreOS, Debian, Suse Open, Red Hat Enterprise Linux, SUSE Linux Enterprise Server o le immagini di Ubuntu Server.

Innanzitutto, passare al [portale di Azure](https://portal.azure.com) in un browser Web. Fare clic su `New`, cercare `scale set` e quindi selezionare la voce `Virtual machine scale set`:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## Creare la macchina virtuale Windows

È ora possibile usare le impostazioni predefinite e creare rapidamente la macchina virtuale.

* Nel pannello `Basics` immettere un nome per il set di scalabilità. Questo nome diventa la base del nome di dominio completo del bilanciamento del carico rispetto al set di scalabilità, pertanto, assicurarsi che il nome sia univoco in tutte le istanze di Azure.

* Selezionare il tipo di sistema operativo desiderato, immettere il nome utente desiderato e selezionare il tipo di autenticazione preferito. Se è stata scelta una password, questa deve avere una lunghezza di 12 caratteri e deve soddisfare almeno tre dei quattro requisiti di complessità seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale. Sono disponibili altre informazioni sui [requisiti relativi a nome utente e password](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Se si sceglie `SSH public key`, assicurarsi di incollare solo la chiave pubblica, NON quella privata:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Immettere il nome del gruppo di risorse desiderato e il percorso e quindi fare clic su `OK`.

* Nel pannello `Virtual machine scale set service settings` immettere l'etichetta del nome di dominio desiderato (la base del nome di dominio completo per il bilanciamento del carico rispetto al set di scalabilità). Questa etichetta deve essere univoca in tutte le istanze di Azure.

* Scegliere l'immagine del disco del sistema operativo desiderata, il numero di istanze e le dimensioni del computer.

* Abilitare o disabilitare il ridimensionamento automatico e, se abilitato, configurarlo:

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* Nel pannello `Summary`, dopo aver completato la convalida, fare clic su `OK`.

* Infine, nel pannello `Purchase` fare clic su `Purchase` per iniziare la distribuzione del set di scalabilità.

## Connettersi a una macchina virtuale nel set di scalabilità

Dopo aver distribuito il set di scalabilità, passare alla scheda `Inbound NAT Rules` del bilanciamento del carico per il set di scalabilità:

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

È possibile connettersi a ogni macchina virtuale nel set di scalabilità usando le regole NAT. Ad esempio, per un set di scalabilità di Windows, se è disponibile una regola NAT nella porta di ingresso 50000, è possibile connettersi a tale computer tramite RDP in `<load-balancer-ip-address>:50000`. Per un set di scalabilità di Linux, è necessario connettersi usando il comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## Passaggi successivi

Per la documentazione relativa alla modalità di distribuzione dei set di scalabilità dall'interfaccia della riga di comando, vedere [questa pagina](./virtual-machine-scale-sets-cli-quick-create.md).

Per la documentazione relativa alla modalità di distribuzione dei set di scalabilità da PowerShell, vedere [questa pagina](./virtual-machine-scale-sets-windows-create.md).

Per la documentazione relativa alla modalità di distribuzione dei set di scalabilità da Visual Studio, vedere [questa pagina](./virtual-machine-scale-sets-vs-create.md).

Per una documentazione generale, vedere la [pagina di panoramica della documentazione per i set di scalabilità](./virtual-machine-scale-sets-overview.md).

Per informazioni generali, vedere la [pagina di destinazione principale per i set di scalabilità](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

<!---HONumber=AcomDC_0928_2016-->