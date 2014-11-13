<properties authors="kathydav" editor="tysonn" manager="donaldg" />

# Come connettere macchine virtuali in un servizio cloud

Quando si crea una macchina virtuale, viene creato automaticamente un servizio cloud per contenerla. È possibile creare più macchine virtuali nello stesso servizio cloud per consentire loro di comunicare, per bilanciare il carico tra le macchine virtuali e per garantire una disponibilità elevata di tali macchine.

Per altre informazioni sul bilanciamento del carico delle macchine virtuali, vedere [Bilanciamento del carico delle macchine virtuali][Bilanciamento del carico delle macchine virtuali]. Per altre informazioni sulla gestione della disponibilità dell'applicazione, vedere [Gestione della disponibilità delle macchine virtuali][Gestione della disponibilità delle macchine virtuali].

È innanzitutto necessario creare una macchina virtuale con un nuovo servizio cloud e sarà quindi possibile connettere macchine virtuali aggiuntive alla prima macchina nell'ambito dello stesso servizio cloud.

1.  Creare una macchina virtuale usando i passaggi descritti in [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata].

2.  Dopo aver creato la prima macchina virtuale personalizzata nella barra dei comandi del [portale di gestione][portale di gestione], fare clic su **New**.

    ![Creare una nuova macchina virtuale][Creare una nuova macchina virtuale]

3.  Fare clic su **Virtual Machine**, quindi su **From Gallery**.

    ![Creazione di una macchina virtuale personalizzata][Creazione di una macchina virtuale personalizzata]

    Verrà visualizzata la finestra di dialogo **Select the virtual machine operating system**.

4.  Nella pagina **Choose an image** scegliere un'immagine, quindi fare clic sulla freccia per continuare.

    Verrà visualizzata la prima pagina **Virtual machine configuration**.

5.  In **Virtual Machine Name** digitare il nome da usare per la macchina virtuale.

6.  In **Size** selezionare le dimensioni da usare per la macchina virtuale. Il valore da selezionare dipende dal numero di core necessari per l'applicazione.

7.  In **New User Name** digitare un nome per l'account amministrativo da usare per la gestione del server.

8.  In **New Password** immettere una password complessa per l'account amministrativo. In **Confirm Password** ridigitare la password.

9.  Per proteggere le macchine virtuali che eseguono il sistema operativo Linux, è possibile selezionare una chiave SSH.

10. In **Cloud Service** selezionare il servizio cloud che conterrà la nuova macchina virtuale.

11. In **Storage Account** selezionare un account di archiviazione per archiviare il file VHD oppure lasciare il campo impostato sull'opzione predefinita per creare l'account di archiviazione automaticamente. Verrà creato automaticamente un solo account di archiviazione. Tutte le altre macchine virtuali create con questa impostazione verranno inserite in questo account di archiviazione. È possibile creare un massimo di 20 account di archiviazione.

12. Per usare un set di disponibilità, selezionare quello creato quando si è creata la prima macchina virtuale.

13. Verificare la configurazione dell'endpoint predefinito e, se necessario, modificarla.

14. Fare clic sul segno di spunta per creare la macchina virtuale connessa.

  [Bilanciamento del carico delle macchine virtuali]: ../../articles/load-balance-virtual-machines/
  [Gestione della disponibilità delle macchine virtuali]: ../../articles/manage-availability-virtual-machines/
  [Come creare una macchina virtuale personalizzata]: ../../articles/virtual-machines-create-custom/
  [portale di gestione]: http://manage.windowsazure.com
  [Creare una nuova macchina virtuale]: ./media/howto-connect-vm-cloud-service/Create.png
  [Creazione di una macchina virtuale personalizzata]: ./media/howto-connect-vm-cloud-service/CreateNew.png
