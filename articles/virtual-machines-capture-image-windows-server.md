<properties linkid="manage-windows-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Come acquisire un'immagine di una macchina virtuale in cui viene eseguito Windows Server
========================================================================================

È possibile utilizzare le immagini della raccolta per creare facilmente macchine virtuali oppure acquisire e utilizzare proprie immagini per creare macchine virtuali personalizzate. Un'immagine è un file del disco rigido virtuale (con estensione vhd) che viene utilizzato come modello per la creazione di una macchina virtuale. Un'immagine è un modello poiché a esso non sono associate impostazioni specifiche come per una macchina virtuale configurata, ad esempio quelle relative al nome del computer e all'account utente. Per creare più macchine virtuali con la stessa configurazione, è possibile acquisire l'immagine di una macchina virtuale configurata e utilizzarla come modello.

1.  Connettersi alla macchina virtuale utilizzando i passaggi descritti in precedenza in [Come accedere a una macchina virtuale che esegue Windows Server](http://www.windowsazure.com/it-it/manage/windows/how-to-guides/log-on-a-windows-vm/).

2.  Aprire una finestra del prompt dei comandi come amministratore.

3.  Impostare la directory su `%windir%\system32\sysprep`, quindi eseguire sysprep.exe.

4.  Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema**.

    In **Azione pulizia sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione **Generalizza** sia selezionata. Per ulteriori informazioni sull'utilizzo di Sysprep, vedere [Introduzione all'utilizzo di Sysprep](http://technet.microsoft.com/en-us/library/bb457073.aspx).

5.  In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

6.  Fare clic su **OK**.

7.  Sysprep arresta la macchina virtuale e lo stato della macchina virtuale nel [portale di gestione](http://manage.windowsazure.com) passa a **Stopped**.

8.  Fare clic su **Virtual Machines** e quindi selezionare la macchina virtuale di cui si desidera acquisire l'immagine.

9.  Nella barra dei comandi fare clic su **Capture**.

    Verrà visualizzata la finestra di dialogo **Capture an Image from a Virtual Machine**.

10. In **Image Name** digitare un nome per la nuova immagine.

11. Prima di aggiungere un'immagine di Windows Server al set di immagini personalizzate, è necessario eseguire Sysprep per generalizzare la macchina virtuale, come indicato nei passaggi precedenti. Fare clic su **I have run Sysprep on the virtual machine** per indicare che l'operazione è stata eseguita.

12. Fare clic sul segno di spunta per acquisire l'immagine. Quando si acquisisce un'immagine di una macchina virtuale, quest'ultima viene eliminata.

    La nuova immagine è ora disponibile in **Images**.

    Quando si crea una macchina virtuale utilizzando il metodo **From Gallery**, è possibile utilizzare l'immagine acquisita facendo clic su **My Images** nella pagina **Choose an Image**.


