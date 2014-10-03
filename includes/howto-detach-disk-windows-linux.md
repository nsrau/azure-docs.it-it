<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

# Come scollegare un disco dati da una macchina virtuale

# 

-   [Concetti][]
-   [Procedura: Individuare i dischi collegati a una macchina virtuale][]
-   [Procedura: Scollegare un disco dati][]

## <span id="concepts"></span> </a>Concetti

Una macchina virtuale in Azure utilizza diversi tipi di dischi, ad esempio un disco del sistema operativo, un disco temporaneo locale e un disco dati facoltativo. È possibile collegare un disco dati a una macchina virtuale per archiviare i dati dell'applicazione. Il disco dati è un disco rigido virtuale che è possibile creare in locale con il proprio computer o nel cloud con Azure.

È possibile collegare e scollegare dischi dati ogni volta che si desidera nel limite del numero di dischi che è possibile collegare a una macchina virtuale in base alle dimensioni della macchina stessa.

Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Questo processo non comporta l'eliminazione del disco dallo spazio di archiviazione. Se si desidera utilizzare di nuovo i dati presenti nel disco, è possibile ricollegare il disco alla stessa macchina virtuale o collegarlo a una nuova macchina virtuale.

Per altre informazioni sull'utilizzo di dischi dati, vedere [Gestione di dischi e immagini][].

## <span id="finddisks"></span> </a>Procedura: Individuare i dischi collegati a una macchina virtuale

È possibile individuare i dischi collegati a una macchina virtuale utilizzando il dashboard o la pagina Disks di Virtual Machines.

### Utilizzare il dashboard per trovare informazioni sui dischi collegati

1.  Accedere al [portale di gestione di Azure][] se questa operazione non è già stata eseguita.

2.  Fare clic su **Virtual Machines** e quindi selezionare la macchina virtuale appropriata.

3.  Fare clic su **Dashboard**. Nel dashboard per la macchina virtuale è possibile visualizzare il numero di dischi collegati e i nomi dei dischi. Nell'esempio seguente è mostrato un disco dati collegato a una macchina virtuale

    ![Ricerca di un disco dati][]

**Nota:** a tutte le macchine virtuali è collegato almeno un disco. A ogni macchina virtuale è collegato un disco del sistema operativo che non è possibile scollegare se non eliminando la macchina virtuale. Il disco temporaneo locale non è visualizzato nella sezione relativa ai dischi in quanto non è permanente.

### Utilizzare la pagina Disks di Virtual Machines per trovare informazioni sui dischi collegati

1.  Accedere al [portale di gestione di Azure][] se questa operazione non è già stata eseguita.

2.  Fare clic su **Virtual Machines** e quindi su **Disks**. In questa pagina è visualizzato l'elenco di tutti i dischi disponibili per l'utilizzo con le macchine virtuali e i dischi attualmente utilizzati dalle macchine virtuali. Nell'elenco sono presenti sia dischi del sistema operativo che dischi dati. Per distinguere i due tipi di dischi collegati alla macchina virtuale, utilizzare il dashboard.

    **Nota:** quando si collega un nuovo disco dati a una macchina virtuale, è possibile assegnare un nome al file con estensione vhd utilizzato per il disco, ma il nome del disco è assegnato da Azure. Tale nome è composto dal nome del servizio cloud, dal nome della macchina virtuale e da un identificatore numerico.

## <span id="detachdisk"></span> </a>Procedura: Scollegare un disco dati

Dopo avere individuato il nome del disco desiderato, eseguire la procedura riportata di seguito per scollegarlo dalla macchina virtuale.

1.  Accedere al portale di gestione di Azure se questa operazione non è già stata eseguita.

2.  Fare clic su **Virtual Machines**, selezionare la macchina virtuale contenente il disco dati da scollegare e quindi fare clic su **Detach The Disk**.

3.  Selezionare il disco dati e quindi fare clic sul segno di spunta per scollegarlo.

    ![Dettagli relativi allo scollegamento del disco][]

    Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

Sarà ora possibile collegare di nuovo il disco alla stessa macchina virtuale o a una nuova macchina. Per le istruzioni, vedere [Come collegare un disco dati a una macchina virtuale][].

  [Concetti]: #concepts
  [Procedura: Individuare i dischi collegati a una macchina virtuale]: #finddisks
  [Procedura: Scollegare un disco dati]: #detachdisk
  [Gestione di dischi e immagini]: http://go.microsoft.com/fwlink/p/?LinkId=263439
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Ricerca di un disco dati]: ./media/howto-detach-disk-windows-linux/FindDataDisks.png
  [Dettagli relativi allo scollegamento del disco]: ./media/howto-detach-disk-windows-linux/DetachDiskDetails.png
  [Come collegare un disco dati a una macchina virtuale]: /en-us/manage/windows/how-to-guides/attach-a-disk/
