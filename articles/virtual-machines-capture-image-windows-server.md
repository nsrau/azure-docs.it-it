<properties linkid="manage-windows-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Come acquisire una macchina virtuale Windows da usare come modello

Questo articolo illustra come acquisire una macchina virtuale Windows in modo da usarla come modello per creare altre macchine virtuali. Questo modello di macchina virtuale include il disco del sistema operativo e gli eventuali dischi dati connessi alla macchina virtuale. Poiché la configurazione di rete non è inclusa, è necessario definirla quando si creano le altre macchine virtuali che usano il modello.

Una volta acquisita, la macchina virtuale risulta disponibile nell'area **Immagini personali** quando si crea una macchina virtuale. Il file dell'immagine viene salvato come VHD nell'account di archiviazione. Per altre informazioni sulle immagini, vedere [Gestione di dischi e immagini][Gestione di dischi e immagini].

## Prima di iniziare

Questa procedura presuppone che sia stata creata una macchina virtuale di Azure e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Se non si sono ancora effettuate queste operazioni, vedere le istruzioni seguenti:

-   [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata]
-   [Come collegare un disco dati a una macchina virtuale][Come collegare un disco dati a una macchina virtuale]

## Acquisizione della macchina virtuale

1.  Connettere la macchina virtuale facendo clic su **Connetti** sulla barra dei comandi. Per informazioni dettagliate, vedere [Come accedere a una macchina virtuale che esegue Windows Server][Come accedere a una macchina virtuale che esegue Windows Server].

2.  Aprire una finestra del Prompt dei comandi come amministratore.

3.  Impostare la directory su `%windir%\system32\sysprep`, quindi eseguire sysprep.exe.

4.  Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema**. Eseguire le operazioni seguenti:

    -   In **Azione pulizia sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione **Generalizza** sia selezionata. Per ulteriori informazioni sull'utilizzo di Sysprep, vedere [Introduzione all'utilizzo di SysPrep][Introduzione all'utilizzo di SysPrep].

    -   In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

    -   Fare clic su **OK**.

    ![Eseguire Sysprep.][Eseguire Sysprep.]

5.  Sysprep arresta la macchina virtuale il cui stato nel [portale di gestione][portale di gestione] passa a **Interrotto**.

6.  Fare clic su **Virtual Machines** e quindi selezionare la macchina virtuale di cui si desidera acquisire l'immagine.

7.  Nella barra dei comandi fare clic su **Capture**.

    ![Acquisire la macchina virtuale][Acquisire la macchina virtuale]

    Viene visualizzata la finestra di dialogo **Capture the Virtual Machine**.

8.  In **Image Name** digitare un nome per la nuova immagine.

9.  Prima di aggiungere un'immagine di Windows Server al set di immagini personalizzate, è necessario eseguire Sysprep per generalizzare la macchina virtuale, come indicato nei passaggi precedenti. Fare clic su **I have run Sysprep on the virtual machine** per indicare che l'operazione è stata eseguita.

10. Fare clic sul segno di spunta per acquisire l'immagine. Quando si acquisisce un'immagine di una macchina virtuale generalizzata, quest'ultima viene eliminata.

    La nuova immagine è ora disponibile in **Images**.

    ![Acquisizione dell'immagine eseguita correttamente][Acquisizione dell'immagine eseguita correttamente]

## Passaggi successivi

L'immagine è pronta per essere usata come modello per la creazione di macchine virtuali. Per effettuare questa operazione, creare una macchina virtuale personalizzata usando il metodo **Da raccolta** e selezionare l'immagine creata. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata].

  [Gestione di dischi e immagini]: http://go.microsoft.com/fwlink/p/?LinkId=397536
  [Come creare una macchina virtuale personalizzata]: ../virtual-machines-create-custom/
  [Come collegare un disco dati a una macchina virtuale]: ../storage-windows-attach-disk/
  [Come accedere a una macchina virtuale che esegue Windows Server]: http://www.windowsazure.com/it-it/manage/windows/how-to-guides/log-on-a-windows-vm/
  [Introduzione all'utilizzo di SysPrep]: http://technet.microsoft.com/it-it/library/bb457073.aspx
  [Eseguire Sysprep.]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
  [portale di gestione]: http://manage.windowsazure.com
  [Acquisire la macchina virtuale]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
  [Acquisizione dell'immagine eseguita correttamente]: ./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png
