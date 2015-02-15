<properties writer="kathydav" editor="tysonn" manager="timlt" />



#Come scollegare un disco dati da una macchina virtuale 

- [Passaggio 1: Trovare il disco](#finddisks)
- [Passaggio 2: Scollegare un disco dati](#detachdisk)

Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio. Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.  

> [AZURE.NOTE] Una macchina virtuale in Azure usa diversi tipi di dischi, ad esempio un disco del sistema operativo, un disco temporaneo locale e un disco dati facoltativo. Per archiviare dati per una macchina virtuale sono consigliati i dischi dati. Per informazioni sui dischi, vedere l'argomento relativo alla gestione di [dischi e immagini] []. Per le istruzioni, vedere [Come collegare un disco dati a una macchina virtuale] [attachdisk].

## <a id="finddisks"> </a>Passaggio 1: Trovare il disco##


Se non si conosce il nome del disco o non si vuole verificarlo prima di rimuoverlo, attenersi alla procedura seguente. 

> [AZURE.NOTE] Azure assegna automaticamente un nome al disco quando viene collegato. Tale nome è composto dal nome del servizio cloud, dal nome della macchina virtuale e da un numero.

1. Se necessario, accedere al [portale di gestione] di Azure.(http://manage.windowsazure.com). 

2. Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale appropriata. Viene aperto il dashboard della macchina virtuale.

3. La tabella sotto la sezione **Dischi** elenca il nome e il tipo di tutti i dischi collegati. Ad esempio, in questa schermata è visualizzata una macchina virtuale con un solo disco del sistema operativo e un unico disco dati:
		
	![Ricerca di un disco dati](./media/howto-detach-disk-windows-linux/FindDataDisks.png)	


## <a id="detachdisk"> </a>Passaggio 2: Rimuovere il disco##

Dopo aver individuato il nome del disco sarà possibile rimuoverlo.

1. Fare clic su **Macchine virtuali** e selezionare la macchina virtuale contenente il disco dati da scollegare.
2. Sulla barra dei comandi fare clic su **Disconnetti disco**.

2. Selezionare il disco dati e quindi fare clic sul segno di spunta per scollegarlo.


	![Dettagli relativi allo scollegamento del disco](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.



[attachdisk]:/it-it/manage/windows/how-to-guides/attach-a-disk/

[Informazioni su dischi e immagini]:http://go.microsoft.com/fwlink/p/?LinkId=263439

<!--HONumber=42-->
