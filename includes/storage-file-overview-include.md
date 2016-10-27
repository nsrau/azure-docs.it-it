## <a name="overview"></a>Panoramica

Archiviazione file di Azure offre condivisioni file nel cloud usando il [protocollo Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)standard. Sono supportati sia SMB 2.1 che SMB 3.0. Con Archiviazione file di Azure si può eseguire la migrazione ad Azure delle applicazioni legacy basate su condivisioni file velocemente e senza costose riscritture. Le applicazioni in esecuzione in macchine virtuali di Azure o in servizi cloud oppure in client locali possono montare una condivisione file nel cloud, esattamente come un'applicazione desktop monta una tipica condivisione SMB. Non ci sono limiti per i componenti delle applicazioni che possono montare e accedere contemporaneamente alla condivisione di archiviazione file.

Poiché una condivisione di archiviazione file è una condivisione file SMB standard, le applicazioni in esecuzione in Azure possono accedere ai dati nella condivisione tramite le API di I/O del file system. Gli sviluppatori possono quindi riutilizzare il codice esistente e le competenze acquisite per eseguire la migrazione delle applicazioni esistenti. I professionisti IT possono usare i cmdlet di PowerShell per creare, montare e gestire condivisioni di archiviazione file nell'ambito delle attività di amministrazione per le applicazioni Azure.

È possibile creare condivisioni file di Azure usando il [portale di Azure](https://portal.azure.com), i cmdlet di PowerShell per Archiviazione di Azure, le librerie client di Archiviazione di Azure o l'API REST di Archiviazione di Azure. Poiché inoltre queste condivisioni file sono condivisioni SMB, è possibile accedervi con le familiari API standard del file system.


<!--HONumber=Oct16_HO2-->


