<properties linkid="manage-linux-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Linux" metaKeywords="Azure Linux vm, Linux vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Linux. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Come acquisire un'immagine di una macchina virtuale che esegue Linux
====================================================================

È possibile utilizzare le immagini della raccolta per creare facilmente macchine virtuali oppure acquisire e utilizzare proprie immagini per creare macchine virtuali personalizzate. Un'immagine è un file del disco rigido virtuale (con estensione vhd) che viene utilizzato come modello per la creazione di una macchina virtuale. Un'immagine è un modello perché non dispone di impostazioni specifiche come nel caso di una macchina virtuale configurata, ad esempio il nome host e le impostazioni dell'account utente. Per creare più macchine virtuali con la stessa configurazione, è possibile acquisire l'immagine di una macchina virtuale configurata e utilizzarla come modello.

1.  Connettersi alla macchina virtuale utilizzando i passaggi descritti in precedenza in [Come accedere a una macchina virtuale che esegue Linux](../virtual-machines-linux-how-to-log-on).

2.  Nella finestra di SSH digitare il comando seguente, quindi immettere la password per l'account creato per la gestione della macchina virtuale. Si noti che l'output di `waagent` può variare leggermente, in base alla versione dell'utilità:

    `sudo waagent -deprovision`

    ![Deprovisioning della macchina virtuale](./media/virtual-machines-linux-capture-image/LinuxDeprovision.png)

3.  Digitare **y** per continuare.

    ![Deprovisioning della macchina virtuale effettuato correttamente](./media/virtual-machines-linux-capture-image/LinuxDeprovision2.png)

4.  Digitare **Exit** per chiudere il client SSH.

5.  Nel [portale di gestione](http://manage.windowsazure.com) selezionare la macchina virtuale e quindi fare clic su **Shutdown**.

    ![Arresto della macchina virtuale](./media/virtual-machines-linux-capture-image/ShutdownVM.png)

6.  Fare clic su **Yes** per confermare che la fatturazione continuerà ad essere effettuata anche se la macchina virtuale viene arrestata.

7.  Dopo l'arresto della macchina virtuale, sulla barra dei comandi fare clic su **Capture**.

    ![Acquisizione di un'immagine della macchina virtuale](./media/virtual-machines-linux-capture-image/CaptureVM.png)

    Verrà visualizzata la finestra di dialogo **Capture Virtual Machine**.

    ![Immettere i dettagli dell'acquisizione](./media/virtual-machines-linux-capture-image/CaptureLinux.png)

8.  In **Image Name** immettere il nome per la nuova immagine.

9.  È necessario *effettuare il deprovisioning* di tutte le immagini Linux eseguendo il comando `waagent` con l'opzione `-deprovision`. Fare clic su **I have run the de-provision command on the Virtual Machine** per indicare che il sistema operativo è pronto per essere convertito in un'immagine.

10. Fare clic sul segno di spunta per acquisire l'immagine.

    La nuova immagine è ora disponibile in **Images**. Dopo l'acquisizione dell'immagine, la macchina virtuale viene eliminata.

    ![Acquisizione dell'immagine eseguita correttamente](./media/virtual-machines-linux-capture-image/CaptureSuccess.png)

    Quando si crea una macchina virtuale utilizzando il metodo **From Gallery**, è possibile utilizzare l'immagine acquisita facendo clic su **My Images** nella pagina **Select the virtual machine operating system**.


