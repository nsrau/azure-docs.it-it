

Le immagini vengono utilizzate in Azure per fornire una nuova macchina virtuale con un sistema operativo. Un'immagine potrebbe inoltre essere uno o più dischi dati. Sono disponibili immagini da diverse origini:

-	Azure offre immagini di [Marketplace](https://azure.microsoft.com/gallery/virtual-machines/). Sono disponibili versioni recenti di Windows Server e distribuzioni del sistema operativo Linux. Alcune immagini contengono anche le applicazioni, ad esempio SQL Server. Gli abbonati MSDN vantaggio e uso prepagato MSDN hanno accesso a immagini aggiuntive.
-	Community open source offre immagini tramite [VM Depot](http://vmdepot.msopentech.com/List/Index).
-	È inoltre possibile archiviare e utilizzare le proprie immagini in Azure, dal caricamento di un'immagine o una macchina virtuale di Azure esistente da utilizzare come immagine di acquisizione.

## Sulle immagini di macchina virtuale e immagini del sistema operativo

È possibile utilizzare due tipi di immagini in Azure: *immagine di macchina virtuale* e *immagine del sistema operativo*. Immagine di macchina virtuale include un sistema operativo e tutti i dischi collegati a una macchina virtuale quando viene creata l'immagine. Si tratta del tipo più recente dell'immagine. Prima dell'introduzione immagini di macchina virtuale, un'immagine in Azure potrebbe contenere solo un sistema operativo generalizzato e non altri dischi. Immagine di macchina virtuale che contiene solo un sistema operativo generalizzato è sostanzialmente identico al tipo originale dell'immagine, l'immagine del sistema operativo.

È possibile creare immagini personalizzate, in base a una macchina virtuale in Azure o una macchina virtuale in esecuzione in un' posizione che copiata e caricata. Se si desidera utilizzare un'immagine per creare più di una macchina virtuale, sarà necessario prepararla per l'utilizzo come immagine generalizzandola. Per creare un'immagine di Windows Server, eseguire il comando Sysprep nel server per generalizzarla prima di caricare il file con estensione vhd. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://go.microsoft.com/fwlink/p/?LinkId=392030) e [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Per creare un'immagine Linux, a seconda della distribuzione software, è necessario eseguire un set di comandi che sono specifici per la distribuzione, nonché di eseguire l'agente Linux di Azure.

<!---HONumber=AcomDC_0817_2016-->