<properties urlDisplayName="Capture an image" pageTitle="Acquisire un'immagine di una macchina virtuale che esegue Linux" metaKeywords="Azure Linux vm, Linux vm" description="Informazioni su come acquisire un'immagine di una macchina virtuale di Azure che esegue Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="Come acquisire un'immagine di una macchina virtuale che esegue Linux" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Come acquisire un'immagine di una macchina virtuale che esegue Linux

Questo articolo illustra come acquisire una macchina virtuale Linux in modo da usarla come modello per creare altre macchine virtuali. Questo modello di macchina virtuale include il disco del sistema operativo e gli eventuali dischi dati connessi alla macchina virtuale. Poiché la configurazione di rete non è inclusa, è necessario definirla quando si creano le altre macchine virtuali che usano il modello.

Una volta acquisita, la macchina virtuale risulta disponibile nell'area **Immagini personali** quando si crea una macchina virtuale. Il file dell'immagine viene salvato come VHD nell'account di archiviazione. Per altre informazioni sulle immagini, vedere [Gestione di dischi e immagini][Gestione di dischi e immagini].

## Prima di iniziare

Questa procedura presuppone che sia stata creata una macchina virtuale di Azure e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Se non si sono ancora effettuate queste operazioni, vedere le istruzioni seguenti:

-   [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata]
-   [Come collegare un disco dati a una macchina virtuale][Come collegare un disco dati a una macchina virtuale]

## Acquisizione della macchina virtuale

1.  Connettere la macchina virtuale facendo clic su **Connetti** sulla barra dei comandi. Per informazioni dettagliate, vedere [Come accedere a una macchina virtuale che esegue Linux][Come accedere a una macchina virtuale che esegue Linux].

2.  Nella finestra di SSH digitare il comando seguente, quindi immettere la password per l'account creato per la gestione della macchina virtuale. Si noti che l'output di `waagent` può variare leggermente, in base alla versione dell'utilità:

    `sudo waagent -deprovision`

    ![Deprovisioning della macchina virtuale][Deprovisioning della macchina virtuale]

3.  Digitare **y** per continuare.

    ![Deprovisioning della macchina virtuale effettuato correttamente][Deprovisioning della macchina virtuale effettuato correttamente]

4.  Digitare **Exit** per chiudere il client SSH.

5.  Nel [portale di gestione][portale di gestione] selezionare la macchina virtuale e quindi fare clic su **Arresta**.

6.  Dopo l'arresto della macchina virtuale, sulla barra dei comandi fare clic su **Capture** per aprire la finestra di dialogo **Capture the Virtual Machine**.

7.  In **Image Name** digitare un nome per la nuova immagine.

8.  È necessario *effettuare il deprovisioning* di tutte le immagini Linux eseguendo il comando `waagent` con l'opzione `-deprovision`. Fare clic su **I have run waagent-deprovision on the virtual machine** per indicare che il sistema operativo è pronto per essere convertito in un'immagine.

9.  Fare clic sul segno di spunta per acquisire l'immagine.

    La nuova immagine è ora disponibile in **Images**. Dopo l'acquisizione dell'immagine, la macchina virtuale viene eliminata.

    ![Acquisizione dell'immagine eseguita correttamente][Acquisizione dell'immagine eseguita correttamente]

## Passaggi successivi

L'immagine è pronta per essere usata come modello per la creazione di macchine virtuali. Per effettuare questa operazione, creare una macchina virtuale personalizzata usando il metodo **Da raccolta** e selezionare l'immagine creata. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata].

  [Gestione di dischi e immagini]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [Come creare una macchina virtuale personalizzata]: ../virtual-machines-create-custom/
  [Come collegare un disco dati a una macchina virtuale]: ../storage-windows-attach-disk/
  [Come accedere a una macchina virtuale che esegue Linux]: ../virtual-machines-linux-how-to-log-on
  [Deprovisioning della macchina virtuale]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision.png
  [Deprovisioning della macchina virtuale effettuato correttamente]: ./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png
  [portale di gestione]: http://manage.windowsazure.com
  [Acquisizione dell'immagine eseguita correttamente]: ./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png
