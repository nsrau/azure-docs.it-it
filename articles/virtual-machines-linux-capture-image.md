<properties pageTitle="Acquisire un'immagine di una macchina virtuale che esegue Linux" description="Informazioni su come acquisire un'immagine di una macchina virtuale di Azure che esegue Linux." services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/25/2014" ms.author="kathydav"/>


# Come acquisire una macchina virtuale Linux da usare come modello##

Questo articolo illustra come acquisire una macchina virtuale di Azure che esegue Linux in modo da usarla come modello per creare altre macchine virtuali. Questo modello include il disco del sistema operativo e gli eventuali dischi dati connessi alla macchina virtuale. Poiché la configurazione di rete non è inclusa, è necessario definirla quando si creano le altre macchine virtuali che usano il modello.

Azure gestisce questo modello come immagine e la archivia in **Immagini personali**, che è anche la posizione in cui vengono archiviate le immagini caricate. Per altre informazioni sulle immagini, vedere [Informazioni sulle macchine virtuali in Azure][].

##Prima di iniziare##

Questa procedura presuppone che sia stata creata una macchina virtuale di Azure e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Se non si sono ancora effettuate queste operazioni, vedere le istruzioni seguenti:

- [Come creare una macchina virtuale personalizzata][]
- [Come collegare un disco dati a una macchina virtuale][]

##Acquisire la macchina virtuale##

1. Connettere la macchina virtuale facendo clic su **Connetti** sulla barra dei comandi. Per informazioni dettagliate, vedere [Come accedere a una macchina virtuale che esegue Linux][].

2. Nella finestra di SSH digitare il comando seguente, quindi immettere la password per l'account creato per la gestione della macchina virtuale.  Si noti che l'output di `waagent` può variare leggermente, in base alla versione dell'utilità:

	`sudo waagent -deprovision`

	![Deprovision the virtual machine](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)


3. Digitare **y** per continuare.

	![Deprovision of virtual machine successful](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4. Digitare **Exit** per chiudere il client SSH.

5. Nel [portale di gestione](http://manage.windowsazure.com) selezionare la macchina virtuale e quindi fare clic su **Arresta**.

6. Dopo l'arresto della macchina virtuale, sulla barra dei comandi fare clic su **Acquisisci** per aprire la finestra di dialogo **Acquisire la macchina virtuale**.

7.	In **Nome immagine** digitare un nome per la nuova immagine.

8.	È necessario effettuare il *deprovisioned* di tutte le immagini Linux eseguendo il comando `waagent` con l'opzione `-deprovision`. Fare clic su **waagent-deprovision eseguito sulla macchina virtuale** per indicare che il sistema operativo è pronto per essere convertito in un'immagine.

9.	Fare clic sul segno di spunta per acquisire l'immagine.

	La nuova immagine è ora disponibile in **Immagini**. Dopo l'acquisizione dell'immagine, la macchina virtuale viene eliminata.

	![Image capture successful](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)

##Passaggi successivi##
L'immagine è pronta per essere usata come modello per la creazione di macchine virtuali. Per effettuare questa operazione, creare una macchina virtuale personalizzata usando il metodo **Da raccolta** e selezionare l'immagine creata. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata] [].
	
[Come accedere a una macchina virtuale che esegue Linux]: ../virtual-machines-linux-how-to-log-on
[Informazioni sulle macchine virtuali in Azure]: http://msdn.microsoft.com/it-it/library/azure/dn790290.aspx
[Come creare una macchina virtuale personalizzata]: ../virtual-machines-create-custom/
[Come collegare un disco dati a una macchina virtuale]: ../storage-windows-attach-disk/





<!--HONumber=42-->
