
Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio. Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.

> [AZURE.NOTE]Una macchina virtuale in Azure usa diversi tipi di dischi, ad esempio un disco del sistema operativo, un disco temporaneo locale e un disco dati facoltativo. Per archiviare dati per una macchina virtuale sono consigliati i dischi dati. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../../virtual-machines-disks-vhds.md). Non è possibile scollegare un disco del sistema operativo, a meno che non venga eliminata anche la macchina virtuale.

## Trovare il disco##

Se non si conosce il nome del disco o si vuole verificarlo prima di averlo scollegato, attenersi alla procedura seguente.

> [AZURE.NOTE]Azure assegna automaticamente un nome al disco quando viene collegato. Tale nome è composto dal nome del servizio cloud, dal nome della macchina virtuale e da un numero.

1. Accedere al [portale di Azure](http://manage.windowsazure.com), se questa operazione non è già stata eseguita.

2. Fare clic su **Macchine virtuali**, fare clic sul nome della macchina virtuale e quindi fare clic su **Dashboard**.

3. La tabella sotto la sezione **Dischi** elenca il nome e il tipo di tutti i dischi collegati. Ad esempio, in questa schermata è visualizzata una macchina virtuale con un solo disco del sistema operativo e un unico disco dati:

	![Ricerca di un disco dati](./media/howto-detach-disk-windows-linux/FindDataDisks.png)


## Rimuovere il disco##

Dopo aver individuato il nome del disco sarà possibile scollegarlo:

1. Fare clic su **Macchine virtuali**, fare clic sul nome della macchina virtuale con il disco di dati che si vuole scollegare e quindi fare clic su **Dashboard**.
2. Sulla barra dei comandi fare clic su **Disconnetti disco**.

3. Selezionare il disco dati e quindi fare clic sul segno di spunta per scollegarlo.

	![Dettagli relativi allo scollegamento del disco](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

<!---HONumber=July15_HO4-->