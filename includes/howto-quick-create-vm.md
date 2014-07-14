<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Come creare rapidamente una macchina virtuale

È possibile utilizzare il metodo **Quick Create** per creare rapidamente una macchina virtuale nel portale di gestione. Per creare la macchina virtuale, è necessario utilizzare una finestra di dialogo per specificare i dettagli di configurazione.

**Nota**: in questo articolo viene creata una macchina virtuale non connessa a una rete virtuale. Se si desidera che la macchina virtuale utilizzi una rete virtuale, utilizzare il metodo **From Gallery** e specificare la rete virtuale durante la creazione della macchina virtuale. Per ulteriori informazioni sulle reti virtuali, vedere[Informazioni generali su Rete virtuale di Azure][1].

1.  Accedere al [portale di gestione di Azure][2].

2.  Sulla barra dei comandi fare clic su **New**.
    
    ![Creare una nuova macchina
    virtuale](./media/howto-quick-create-vm/create.png)

3.  Fare clic su **Virtual Machines** e quindi su **Quick Create**.
    
    ![Creazione rapida di una nuova macchina
    virtuale](./media/howto-quick-create-vm/createquick.png)
    
    Verrà visualizzata la finestra di dialogo **Create a New Virtual Machine**.

4.  Per la nuova macchina virtuale, digitare le informazioni seguenti:
    
    * **DNS Name**: nome utilizzato sia per la macchina virtuale creata sia per il servizio cloud che la contiene.
    * **Image**: immagine della piattaforma utilizzata per creare la macchina virtuale.
    * **User Name**: nome dell'account che si desidera utilizzare per gestire la macchina virtuale.
    * **Account Password**: digitare e confermare una password complessa per l'account.
    * **Location**: area che contiene la macchina virtuale.

5.  Fare clic sul segno di spunta per creare la macchina virtuale.
    
    **Nota:** verrà creato automaticamente un account di archiviazione che conterrà la macchina virtuale.
    
    La nuova macchina virtuale verrà quindi visualizzata nella pagina **Virtual Machines**.
    
    ![Creazione della macchina virtuale
    completata](./media/howto-quick-create-vm/vmsuccesswindows.png)



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com