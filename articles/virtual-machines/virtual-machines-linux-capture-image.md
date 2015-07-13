<properties
	pageTitle="Acquisire un'immagine di una macchina virtuale che esegue Linux"
	description="Informazioni su come acquisire un'immagine di una macchina virtuale di Azure che esegue Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/11/2015" 
	ms.author="kathydav"/>


# Come acquisire una macchina virtuale Linux da usare come modello##

Questo articolo illustra come acquisire una macchina virtuale di Azure che esegue Linux in modo da usarla come modello per creare altre macchine virtuali. Questo modello include il disco del sistema operativo e gli eventuali dischi dati connessi alla macchina virtuale. Poiché la configurazione di rete non è inclusa, è necessario definirla quando si creano le altre macchine virtuali che usano il modello.

Azure gestisce questo modello come immagine e lo archivia in **Immagini personali**. che è anche la posizione in cui vengono archiviate le immagini caricate. Per altre informazioni sulle immagini, vedere [Informazioni sulle immagini di macchine virtuali in Azure][].

##Prima di iniziare##

Questa procedura presuppone che sia stata creata una macchina virtuale di Azure e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Se non si sono ancora effettuate queste operazioni, vedere le istruzioni seguenti:

- [Come creare una macchina virtuale personalizzata][]
- [Come collegare un disco dati a una macchina virtuale][]

##Acquisizione della macchina virtuale##

1. Connettere la macchina virtuale facendo clic su **Connetti** sulla barra dei comandi. Per informazioni dettagliate, vedere [Come accedere a una macchina virtuale che esegue Linux][].

2. Nella finestra di SSH digitare il comando seguente. Si noti che l'output di `waagent` può variare leggermente, in base alla versione dell'utilità:

	`$ sudo waagent -deprovision+user`

	Questo comando prova a pulire il sistema per renderlo idoneo per un nuovo provisioning. Questa operazione esegue le attività seguenti:

	- Rimuove le chiavi host SSH (se Provisioning.RegenerateSshHostKeyPair è 'y' nel file di configurazione)
	- Cancella la configurazione NameServer in /etc/resolv.conf
	- Rimuove la password `root` dell'utente da /etc/shadow (se Provisioning.DeleteRootPassword è 'y' nel file di configurazione)
	- Rimuove i lease client DHCP memorizzati nella cache
	- Ripristina il nome host su localhost.localdomain
	- Elimina anche l'ultimo account utente (ottenuto da /var/lib/waagent) di cui è stato effettuato il provisioning **e i dati associati**.

	![Deprovisioning della macchina virtuale](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

	>[AZURE.NOTE]Il deprovisioning elimina file e dati nel tentativo di "generalizzare" l'immagine. Eseguire questo comando solo in macchine virtuali che si vogliono acquisire come nuovo modello di immagine.


3. Digitare **y** per continuare.

	![Deprovisioning della macchina virtuale effettuato correttamente](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

	**Si noti** che è possibile aggiungere il parametro `-force` per evitare questo passaggio di conferma.

	>[AZURE.NOTE]Il deprovisioning generalizza solo l'immagine Linux, ma non garantisce che dall'immagine vengano cancellate tutte le informazioni sensibili e che sia adatta per la ridistribuzione a terzi.


4. Digitare **Exit** per chiudere il client SSH.

5. Nel [portale di gestione](http://manage.windowsazure.com) selezionare la macchina virtuale e quindi fare clic su **Arresta**.

6. Dopo l'arresto della macchina virtuale, sulla barra dei comandi fare clic su **Capture** per aprire la finestra di dialogo **Capture the Virtual Machine**.

7.	In **Image Name** digitare un nome per la nuova immagine.

8.	Tutte le immagini di Linux devono essere sottoposte a *deprovisioning* eseguendo il comando `waagent` con l’opzione `-deprovision`. Fare clic su **I have run waagent-deprovision on the virtual machine** per indicare che il sistema operativo è pronto per essere convertito in un'immagine.

9.	Fare clic sul segno di spunta per acquisire l'immagine.

	La nuova immagine è ora disponibile in **Images**. Dopo l'acquisizione dell'immagine, la macchina virtuale viene eliminata.

	![Acquisizione dell'immagine eseguita correttamente](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


##Passaggi successivi##
L'immagine è pronta per essere usata come modello per la creazione di macchine virtuali. Per effettuare questa operazione, creare una macchina virtuale personalizzata usando il metodo **Da raccolta** e selezionare l'immagine creata. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata][].

**Vedere anche:** [Guida dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md)

[Come accedere a una macchina virtuale che esegue Linux]: virtual-machines-linux-how-to-log-on.md
[Informazioni sulle immagini di macchine virtuali in Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[Come creare una macchina virtuale personalizzata]: virtual-machines-create-custom.md
[Come collegare un disco dati a una macchina virtuale]: storage-windows-attach-disk.md

 

<!---HONumber=July15_HO1-->