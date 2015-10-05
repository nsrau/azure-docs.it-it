<properties
	pageTitle="Acquisire un'immagine di una macchina virtuale Linux | Microsoft Azure"
	description="Informazioni su come acquisire un'immagine di una macchina virtuale di Azure creata con il modello di distribuzione classica che esegue Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="dkshir"/>


# Come acquisire una macchina virtuale Linux da usare come modello

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrato come acquisire un’immagine con il modello di distribuzione classica.

Questo articolo illustra come acquisire una macchina virtuale di Azure che esegue Linux in modo da usarla come modello per creare altre macchine virtuali. Questo modello include il disco del sistema operativo e i dischi dati collegati alla macchina virtuale. Poiché la configurazione di rete non è inclusa, è necessario definirla quando si creano le altre macchine virtuali che usano il modello.

Azure gestisce questo modello come immagine e lo archivia in **Immagini**. che è anche la posizione in cui vengono archiviate le immagini caricate. Per altre informazioni sulle immagini, vedere [Informazioni sulle immagini di macchine virtuali in Azure][].

## Prima di iniziare

Questa procedura presuppone che sia stata creata una macchina virtuale di Azure tramite il modello di distribuzione classico e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Se non si sono ancora effettuate queste operazioni, vedere le istruzioni seguenti:

- [Come creare una macchina virtuale che esegue Linux][]


## Acquisizione della macchina virtuale

1. Effettuare la connessione alla macchina virtuale utilizzando un client SSH a scelta. Per informazioni dettagliate, vedere [Come accedere a una macchina virtuale che esegue Linux][].

2. Nella finestra di SSH digitare il comando seguente. Si noti che l'output di `waagent` può variare leggermente, in base alla versione dell'utilità:

	`sudo waagent -deprovision`

	Questo comando prova a pulire il sistema per renderlo idoneo per un nuovo provisioning. Questa operazione esegue le attività seguenti:

	- Rimuove le chiavi host SSH (se Provisioning.RegenerateSshHostKeyPair è 'y' nel file di configurazione)
	- Cancella la configurazione NameServer in /etc/resolv.conf
	- Rimuove la password `root` dell'utente da /etc/shadow (se Provisioning.DeleteRootPassword è 'y' nel file di configurazione)
	- Rimuove i lease client DHCP memorizzati nella cache
	- Ripristina il nome host su localhost.localdomain
	- Elimina anche l'ultimo account utente (ottenuto da /var/lib/waagent) di cui è stato effettuato il provisioning **e i dati associati**.

	>[AZURE.NOTE]Il deprovisioning elimina file e dati nel tentativo di "generalizzare" l'immagine. Eseguire questo comando solo in macchine virtuali che si vogliono acquisire come nuovo modello di immagine. Ciò non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili o che l'immagine sia adatta per la ridistribuzione a terze parti.


3. Digitare **y** per continuare. È possibile aggiungere il parametro `-force` per evitare questo passaggio di conferma.

4. Digitare **Exit** per chiudere il client SSH.


	>[AZURE.NOTE]I passaggi successivi presuppongono che l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) sia stata già installata sul computer client. Tutti i passaggi riportati di seguito possono essere eseguiti anche nel [portale di gestione][].

5. Dal computer client, aprire l'interfaccia della riga di comando di Azure ed eseguire l'accesso alla sottoscrizione di Azure. Per informazioni dettagliate, leggere [Connessione a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md).

6. Assicurarsi che sia attiva la modalità Gestione dei servizi:

	`azure config mode asm`

7. Arrestare la macchina virtuale già sottoposta al deprovisioning nei passaggi riportati in precedenza con:

	`azure vm shutdown <your-virtual-machine-name>`

	>[AZURE.NOTE]È possibile individuare tutte le macchine virtuali create nella sottoscrizione utilizzando `azure vm list`

8. Quando la macchina virtuale viene arrestata, acquisire l'immagine con il comando:

	`azure vm capture -t <your-virtual-machine-name> <new-image-name>`

	Digitare il nome dell'immagine al posto di _nuovo-nome-immagine_. Questo comando consente di creare un'immagine generalizzata del sistema operativo. Il sottocomando `-t` consente di eliminare la macchina virtuale originale.

9.	La nuova immagine è ora disponibile nell'elenco delle immagini che possono essere utilizzate per configurare nuove macchine virtuali. È possibile visualizzarla con il comando:

	`azure vm image list`

	Verrà visualizzata nell'elenco **IMMAGINI** del [portale di gestione][].

	![Acquisizione dell'immagine eseguita correttamente](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


## Passaggi successivi
L'immagine è pronta per essere usata come modello per la creazione di macchine virtuali. È possibile utilizzare il comando `azure vm create` dell'interfaccia della riga di comando di Azure e indicare il nome dell'immagine appena creata. Per informazioni dettagliate sul comando, vedere [Utilizzo dell'interfaccia della riga di comando con l’API di gestione del servizio](virtual-machines-command-line-tools.md). In alternativa, è possibile utilizzare il [portale di gestione][] per creare una macchina virtuale personalizzata usando il metodo **Da raccolta** e selezionando l'immagine appena creata. Per ulteriori dettagli, vedere [Come creare una macchina virtuale personalizzata][].

**Vedere anche:** [Guida dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md)

[portale di gestione]: http://manage.windowsazure.com
[Come accedere a una macchina virtuale che esegue Linux]: virtual-machines-linux-how-to-log-on.md
[Informazioni sulle immagini di macchine virtuali in Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[Come creare una macchina virtuale personalizzata]: virtual-machines-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: storage-windows-attach-disk.md
[Come creare una macchina virtuale che esegue Linux]: virtual-machines-linux-tutorial.md

<!---HONumber=Sept15_HO4-->