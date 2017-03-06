Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Quando si scollega un disco, viene rimosso il disco dalla macchina virtuale, ma non viene eliminato dall'account di Archiviazione Azure.

Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.  

> [!NOTE]
> Per scollegare un disco del sistema operativo è prima necessario eliminare la macchina virtuale.
>

## <a name="find-the-disk"></a>Trovare il disco
Se non si conosce il nome del disco o si vuole verificarlo prima di averlo scollegato, attenersi alla procedura seguente.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **Macchine virtuali**, quindi selezionare la VM appropriata.

3. Fare clic su **Dischi** sul bordo sinistro del dashboard della macchina dashboard, in **Impostazioni**.

 Il dashboard della macchina virtuale elenca il nome e il tipo di tutti i dischi collegati. Ad esempio, in questa schermata è visualizzata una macchina virtuale con un solo disco del sistema operativo e un unico disco dati:

    ![Ricerca di un disco dati](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>Rimuovere il disco
1. Nel Portale di Azure fare clic su **Macchine virtuali**, quindi sul nome della macchina virtuale con il disco dati che si vuole scollegare.

2. Fare clic su **Dischi** sul bordo sinistro del dashboard della macchina dashboard, in **Impostazioni**.

3. Fare clic sul disco che si desidera scollegare.

  ![Identificare il disco da scollegare](./media/howto-detach-disk-windows-linux/disklist.png)

4. Sulla barra dei comandi fare clic su **Disconnetti**.

  ![Individuare il comando di disconnessione](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. Nella finestra di conferma, fare clic su **Sì** per scollegare il disco.

  ![Confermare la disconnessione del disco](./media/howto-detach-disk-windows-linux/confirmdetach.png)

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.
