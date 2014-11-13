<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Come creare rapidamente una macchina virtuale

È possibile usare il metodo **Quick Create** per creare rapidamente una macchina virtuale nel portale di gestione. Per creare la macchina virtuale, è necessario usare una finestra di dialogo per specificare i dettagli di configurazione.

**Nota**: in questo articolo viene creata una macchina virtuale non connessa a una rete virtuale. Se si desidera che la macchina virtuale utilizzi una rete virtuale, usare il metodo **From Gallery** e specificare la rete virtuale durante la creazione della macchina virtuale. Per altre informazioni sulle reti virtuali, vedere [Informazioni generali su Rete virtuale di Azure][Informazioni generali su Rete virtuale di Azure].

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Sulla barra dei comandi fare clic su **New**.

    ![Creare una nuova macchina virtuale][Creare una nuova macchina virtuale]

3.  Fare clic su **Virtual Machines** e quindi su **Quick Create**.

    ![Creazione rapida di una nuova macchina virtuale][Creazione rapida di una nuova macchina virtuale]

    Verrà visualizzata la finestra di dialogo **Create a New Virtual Machine**.

4.  Per la nuova macchina virtuale, digitare le informazioni seguenti:

    -   **DNS Name**: nome usato sia per la macchina virtuale creata sia per il servizio cloud che la contiene.
    -   **Image**: immagine della piattaforma usata per creare la macchina virtuale.
    -   **User Name**: nome dell'account che si desidera usare per gestire la macchina virtuale.
    -   **Account Password**: digitare e confermare una password complessa per l'account.
    -   **Location**: area che contiene la macchina virtuale.

5.  Fare clic sul segno di spunta per creare la macchina virtuale.

    **Nota:** verrà creato automaticamente un account di archiviazione che conterrà la macchina virtuale.

    La nuova macchina virtuale verrà quindi visualizzata nella pagina **Virtual Machines**.

    ![Creazione della macchina virtuale completata][Creazione della macchina virtuale completata]

  [Informazioni generali su Rete virtuale di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Creare una nuova macchina virtuale]: ./media/howto-quick-create-vm/create.png
  [Creazione rapida di una nuova macchina virtuale]: ./media/howto-quick-create-vm/createquick.png
  [Creazione della macchina virtuale completata]: ./media/howto-quick-create-vm/vmsuccesswindows.png
