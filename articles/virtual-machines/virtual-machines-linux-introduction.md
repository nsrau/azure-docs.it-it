<properties
	pageTitle="Introduzione a Linux in Azure | Microsoft Azure"
	description="Informazioni sull'uso delle macchine virtuali Linux in Azure."
	services="virtual-machines"
	documentationCenter="python"
	authors="szarkos"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="szark"/>

#Introduzione a Linux in Azure

In questo argomento viene fornita una panoramica relativa ad alcuni aspetti dell'uso di macchine virtuali Linux nel cloud di Azure. Se si usa un'immagine presente nella raccolta, la distribuzione di una macchina virtuale Linux è un processo estremamente semplice.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Autenticazione: nomi utente, password e chiavi SSH

Quando si crea una macchina virtuale Linux usando il portale di Azure classico viene richiesto di inserire un nome utente, una password o una chiave pubblica SSH. La scelta del nome utente per la distribuzione di una macchina virtuale Linux in Azure è soggetta a un vincolo: i nomi degli account di sistema (UID <100) già presenti nella macchina virtuale, ad esempio l'account 'root', non sono consentiti.


 - Vedere [Creare una macchina virtuale che esegue Linux](virtual-machines-linux-tutorial.md)
 - Vedere [Come usare SSH con Linux in Azure](virtual-machines-linux-use-ssh-key.md)


## Ottenere privilegi utente avanzato tramite `sudo`

L'account utente specificato durante la distribuzione di istanze di macchine virtuali in Azure è un account con privilegi. Tale account viene configurato dall'agente Linux di Azure con la capacità di elevare i privilegi al ruolo di utente ROOT (account utente con privilegi avanzati) tramite l'utilità `sudo`. Dopo aver eseguito l'accesso usando questo account utente, sarà possibile eseguire comandi come utente ROOT usando la sintassi del comando.

	# sudo <COMMAND>

Facoltativamente, è possibile ottenere una shell di root usando **sudo -s**.

- Vedere [Utilizzo di privilegi root su Linux in Macchine virtuali di Azure](virtual-machines-linux-use-root-privileges.md)


## Configurazione del firewall

In Azure è disponibile un filtro dei pacchetti in ingresso che limita la connettività alle porte specificate nel portale di Azure classico. Per impostazione predefinita, l'unica porta consentita è SSH. È possibile aprire l'accesso a porte aggiuntive nella macchina virtuale Linux mediante la configurazione di endpoint nel portale di Azure classico:

 - Vedere: [Come configurare gli endpoint in una macchina virtuale](virtual-machines-set-up-endpoints.md)

Le immagini Linux disponibili nella Raccolta di Azure non abilitano il firewall *iptables* per impostazione predefinita. Se necessario, è possibile configurare il firewall per implementare funzionalità di filtro.


## Modifica del nome host

Quando si distribuisce un'istanza di un'immagine Linux è necessario specificare un nome host per la macchina virtuale. Quando la macchina virtuale è in esecuzione, il nome host viene pubblicato nei server DNS della piattaforma, in modo che più macchine virtuali connesse tra loro possano eseguire ricerche di indirizzi IP usando i nomi host.

Se, dopo la distribuzione di una macchina virtuale, si vuole modificarne il nome host, usare il comando

	# sudo hostname <newname>

L'agente Linux di Azure include funzionalità per il rilevamento automatico delle modifiche del nome host, la configurazione della macchina virtuale per il salvataggio permanente della modifica e la pubblicazione della modifica nei server DNS della piattaforma.

 - [Guida dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md)

### cloud-init
Le immagini **Ubuntu** e **CoreOS** usano cloud-init in Azure, che offre capacità aggiuntive per il bootstrap di una macchina virtuale.

 - [Come inserire dati personalizzati](virtual-machines-how-to-inject-custom-data.md)
 - [Dati personalizzati e cloud-init in Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [Creare partizioni di scambio di Azure con cloud-init](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Come usare CoreOS in Azure](virtual-machines-linux-coreos-how-to.md)


## Acquisizione di immagini di macchine virtuali

Azure offre la possibilità di acquisire lo stato di una macchina virtuale esistente in un'immagine che potrà essere usata in un secondo momento per distribuire altre istanze di macchine virtuali. L'agente Linux di Azure può essere usato per ripristinare lo stato precedente di alcune personalizzazioni effettuate durante il processo di provisioning. Per acquisire una macchina virtuale come immagine, eseguire la procedura seguente:

1. Eseguire **waagent -deprovision** per annullare la personalizzazione del provisioning. Facoltativamente, eseguire **waagent -deprovision+user** per eliminare l'account utente specificato durante il provisioning e tutti i relativi dati.

2. Arrestare/Spegnere la macchina virtuale.

3. Fare clic su *Acquisisci* nel portale di Azure classico oppure usare Powershell o gli strumenti dell’interfaccia della riga di comando per acquisire la macchina virtuale come immagine.

 - Vedere: [Come acquisire una macchina virtuale Linux da usare come modello](virtual-machines-linux-capture-image.md)


## Collegamento di dischi

Ogni macchina virtuale ha un *disco risorse* temporaneo locale collegato. Poiché è possibile che i dati su un disco risorse non vengano mantenuti tra un riavvio e l'altro, questo tipo di disco viene spesso usato da applicazioni e processi in esecuzione nella macchina virtuale per l'archiviazione **temporanea** di dati. È anche usato per archiviare file di paging o di scambio per il sistema operativo.

In Linux il disco risorse è in genere gestito dall'agente Linux di Azure e viene montato automaticamente in **/mnt/resource** (o **/mnt** nelle immagini Ubuntu).


>[AZURE.NOTE] Si noti che il disco risorse è un disco **temporaneo** e potrebbe essere eliminato e riformattato al riavvio della macchina virtuale.

In Linux il kernel potrebbe assegnare al disco dati il nome `/dev/sdc`. In questo caso gli utenti dovranno suddividere in partizioni, formattare e montare tale risorsa. Questa procedura è illustrata in dettaglio nell'esercitazione [Procedura: Come collegare un disco dati a una macchina virtuale](virtual-machines-linux-how-to-attach-disk.md).

 - **Vedere anche:** [Configurare RAID software in Linux](virtual-machines-linux-configure-raid.md)

<!---HONumber=AcomDC_0204_2016-->