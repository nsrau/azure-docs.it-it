<properties writer="kathydav" editor="tysonn" manager="timlt" />

Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio.

Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.

> [AZURE.NOTE] Non è possibile scollegare un disco del sistema operativo, a meno che non venga eliminata anche la macchina virtuale.


## Trovare il disco

Se non si conosce il nome del disco o si vuole verificarlo prima di averlo scollegato, attenersi alla procedura seguente.


1. Accedere al [portale di Azure classico](http://manage.windowsazure.com).

2. Fare clic su **Macchine virtuali**, fare clic sul nome della macchina virtuale e quindi fare clic su **Dashboard**.

3. La tabella sotto la sezione **Dischi** elenca il nome e il tipo di tutti i dischi collegati. Ad esempio, in questa schermata è visualizzata una macchina virtuale con un solo disco del sistema operativo e un unico disco dati:

	![Ricerca di un disco dati](./media/howto-detach-disk-windows-linux/FindDataDisks.png)


## Rimuovere il disco

1. Fare clic su **Macchine virtuali**, fare clic sul nome della macchina virtuale con il disco di dati che si vuole scollegare e quindi fare clic su **Dashboard**.

2. Sulla barra dei comandi fare clic su **Disconnetti disco**.

3. Selezionare il disco dati e quindi fare clic sul segno di spunta per scollegarlo.

	![Dettagli relativi allo scollegamento del disco](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

<!---HONumber=AcomDC_0211_2016-->