<properties
	pageTitle="Introduzione a Linux in Azure - Esercitazione di Azure"
	description="Informazioni sull'uso delle macchine virtuali Linux in Azure."
	services="virtual-machines"
	documentationCenter="python"
	authors="szarkos"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2015"
	ms.author="szark"/>





#Introduzione a Linux in Azure

In questo argomento viene fornita una panoramica relativa ad alcuni aspetti dell'uso di macchine virtuali Linux nel cloud di Azure. Se si usa un'immagine presente nella raccolta, la distribuzione di una macchina virtuale Linux è un processo estremamente semplice.

## Sommario ##

* [Autenticazione: nomi utente, password e chiavi SSH.](#authentication)
* [Utilizzo di sudo per ottenere privilegi utente avanzati](#superuserprivileges)
* [Configurazione del firewall](#firewallconfiguration)
* [Modifica del nome host](#hostnamechanges)
* [Acquisizione di immagini di macchine virtuali](#virtualmachine)
* [Collegamento di dischi](#attachingdisks)

## <a id="authentication"></a>Autenticazione: nomi utente, password e chiavi SSH

Quando si crea una macchina virtuale Linux usando il portale di gestione di Azure viene richiesto di inserire un nome utente, una password o una chiave pubblica SSH. La scelta del nome utente per la distribuzione di una macchina virtuale Linux in Azure è soggetta a un vincolo: i nomi degli account di sistema (UID <100) già presenti nella macchina virtuale, ad esempio l'account 'root', non sono consentiti.


 - Vedere [Creare una macchina virtuale che esegue Linux](virtual-machines-linux-tutorial.md)
 - Vedere [Come usare SSH con Linux in Azure](linux-use-ssh-key.md)


## <a id="superuserprivileges"></a>Uso di `sudo` per ottenere privilegi di utente avanzato

L'account utente specificato durante la distribuzione di istanze di macchine virtuali in Azure è un account con privilegi. Tale account viene configurato dall'agente Linux di Azure con la capacità di elevare i privilegi al ruolo di utente ROOT (account utente con privilegi avanzati) tramite l'utilità `sudo`. Dopo aver eseguito l'accesso usando questo account utente, sarà possibile eseguire comandi come utente ROOT usando la sintassi del comando.

	# sudo <COMMAND>

Facoltativamente, è possibile ottenere una shell di root usando **sudo -s**.

- Vedere [Utilizzo di privilegi root su Linux in Macchine virtuali di Azure](virtual-machines-linux-use-root-privileges.md)


## <a id="firewallconfiguration"></a>Configurazione del firewall

In Azure è disponibile un filtro dei pacchetti in ingresso che limita la connettività alle porte specificate nel portale di gestione. Per impostazione predefinita, l'unica porta consentita è SSH. È possibile aprire l'accesso a porte aggiuntive nella macchina virtuale Linux mediante la configurazione di endpoint nel portale di gestione.

 - Vedere: [Come configurare gli endpoint in una macchina virtuale](virtual-machines-set-up-endpoints.md)

Le immagini Linux disponibili nella Raccolta di Azure non abilitano il firewall *iptables* per impostazione predefinita. Se necessario, è possibile configurare il firewall per implementare funzionalità di filtro.


## <a id="hostnamechanges"></a>Modifica del nome host

Quando si distribuisce un'istanza di un'immagine Linux è necessario specificare un nome host per la macchina virtuale. Quando la macchina virtuale è in esecuzione, il nome host viene pubblicato nei server DNS della piattaforma, in modo che più macchine virtuali connesse tra loro possano eseguire ricerche di indirizzi IP usando i nomi host.

Se, dopo la distribuzione di una macchina virtuale, si vuole modificarne il nome host, usare il comando

	# sudo hostname <newname>

L'agente Linux di Azure include funzionalità per il rilevamento automatico delle modifiche del nome host, la configurazione della macchina virtuale per il salvataggio permanente della modifica e la pubblicazione della modifica nei server DNS della piattaforma.

 - [Guida dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md)

### Immagini Ubuntu
Le immagini Ubuntu usano cloud-init, che offre capacità aggiuntive per il bootstrap di una macchina virtuale.

 - Vedere [How to Inject Custom Data](virtual-machines-how-to-inject-custom-data.md) e [Custom Data and Cloud-Init on Microsoft Azure](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)


## <a id="virtualmachine"></a>Acquisizione di immagini di macchine virtuali

Azure offre la possibilità di acquisire lo stato di una macchina virtuale esistente in un'immagine che potrà essere usata in un secondo momento per distribuire altre istanze di macchine virtuali. L'agente Linux di Azure può essere usato per ripristinare lo stato precedente di alcune personalizzazioni effettuate durante il processo di provisioning. Per acquisire una macchina virtuale come immagine, eseguire la procedura seguente:

1. Eseguire **waagent -deprovision** per annullare la personalizzazione del provisioning. Facoltativamente, eseguire **waagent -deprovision+user** per eliminare l'account utente specificato durante il provisioning e tutti i relativi dati.

2. Arrestare/Spegnere la macchina virtuale.

3. Fare clic su *Acquisisci* nel portale di gestione oppure usare Powershell o gli strumenti CLI per acquisire la macchina virtuale come immagine.

 - Vedere: [Come acquisire una macchina virtuale Linux da usare come modello](virtual-machines-linux-capture-image.md)


## <a id="attachingdisks"></a>Collegamento di dischi

Ogni macchina virtuale ha un *disco risorse* temporaneo locale collegato. Poiché è possibile che i dati su un disco risorse non vengano mantenuti tra un riavvio e l'altro, questo tipo di disco viene spesso usato da applicazioni e processi in esecuzione nella macchina virtuale per l'archiviazione **temporanea** di dati. È anche usato per archiviare file di paging o di scambio per il sistema operativo.

In Linux il disco risorse è in genere gestito dall'agente Linux di Azure e viene montato automaticamente in **/mnt/resource** (o **/mnt** nelle immagini Ubuntu).

	>[AZURE.NOTE] Note that the resource disk is a **temporary** disk, and might be deleted and reformatted when the VM is rebooted.

In Linux il kernel potrebbe assegnare al disco dati il nome `/dev/sdc`. In questo caso gli utenti dovranno suddividere in partizioni, formattare e montare tale risorsa. Questa procedura è illustrata in dettaglio nell'esercitazione [Procedura: Come collegare un disco dati a una macchina virtuale](virtual-machines-linux-how-to-attach-disk.md).

 - Vedere anche: [Configurare RAID software in Linux](virtual-machines-linux-configure-raid.md)

<!---HONumber=58-->