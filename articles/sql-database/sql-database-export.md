---
title: Archiviare un database SQL di Azure in un file BACPAC tramite il portale di Azure
description: Archiviare un database SQL di Azure in un file BACPAC tramite il portale di Azure
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 08/15/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# Archiviare un database SQL di Azure in un file BACPAC tramite il portale di Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](sql-database-export.md)
> * [PowerShell](sql-database-export-powershell.md)
> 
> 

Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com) per archiviare un database SQL di Azure in un file BACPAC (nell'archiviazione BLOB di Azure).

Per creare un archivio di un database SQL di Azure, è possibile esportare lo schema di database e i dati in un file BACPAC. Un file BACPAC è semplicemente un file zip con estensione bacpac. Un file BACPAC in un secondo momento può essere archiviato nell'archiviazione BLOB di Azure o in un percorso locale e successivamente reimportato nel database SQL di Azure o in un'installazione locale di SQL Server.

***Considerazioni***

* Perché un archivio sia coerente dal punto di vista transazionale, è necessario assicurarsi che non si verifichino attività di scrittura durante l'esportazione o che l'esportazione sia eseguita da una [copia coerente dal punto di vista transazionale](sql-database-copy.md) del database SQL di Azure.
* La dimensione massima di un file BACPAC salvato nell'archivio BLOB di Azure è pari a 200 GB. Per archiviare un file BACPAC più grande in un percorso locale, usare l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Questa utilità è disponibile sia in Visual Studio che in SQL Server. È inoltre possibile [scaricare](https://msdn.microsoft.com/library/mt204009.aspx) la versione più recente di SQL Server Data Tools per ottenere questa utilità.
* L'archiviazione Premium di file BACPAC in Azure non è supportata.
* Se l'operazione di esportazione dura oltre 20 ore, potrebbe essere annullata. Per migliorare le prestazioni durante l'esportazione è possibile:
  * Aumentare temporaneamente il livello di servizio.
  * Interrompere tutte le attività di lettura e scrittura durante l'esportazione.
  * Utilizzare un [indice cluster](https://msdn.microsoft.com/library/ms190457.aspx) con valori non null in tutte le tabelle di grandi dimensioni. Senza indici cluster, l'esportazione potrebbe non riuscire se dovesse durare più di 6 - 12 ore. Questo perché i servizi di esportazione devono completare la scansione della tabella prima di provare a esportarla per intero. Un modo valido di determinare se le tabelle sono ottimizzate per l'esportazione consiste nell'eseguire **DBCC SHOW\_STATISTICS** e verificare che il parametro *RANGE\_HI\_KEY* non sia null e il relativo valore abbia distribuzione valide. Per i dettagli, vedere [DBCC SHOW\_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell’utente. Per ulteriori informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).
> 
> 

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Database SQL di Azure.
* Un [account di Archiviazione Standard di Azure](../storage/storage-create-storage-account.md) con un contenitore BLOB per archiviare il file BACPAC nella risorsa di archiviazione standard.

## Esportazione del database
Aprire il pannello del Database SQL per il database che si desidera esportare:

> [!IMPORTANT]
> Per garantire un file BACPAC coerente dal punto di vista transazionale è necessario innanzitutto [creare una copia del database](sql-database-copy.md) e quindi esportare la copia del database.
> 
> 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Database SQL**.
3. Fare clic sul database per archiviare.
4. Nel pannello del database SQL fare clic su **Esporta** per aprire il pannello **Esporta database**:
   
   ![pulsante esporta][1]
5. Fare clic su **Archiviazione** e selezionare il contenitore di BLOB e l’account di archiviazione in cui verrà archiviato il file BACPAC:
   
   ![esporta database][2]
6. Selezionare il tipo di autenticazione.
7. Immettere le credenziali di autenticazione appropriate per il server SQL di Azure contenente il database da esportare.
8. Fare clic su **OK** per archiviare il database. Facendo clic su **OK** si crea una richiesta di esportazione del database che viene inviata al servizio. La durata dell'esportazione dipende dalla dimensione e dalla complessità del database e dal livello del servizio. Si riceverà una notifica.
   
   ![notifica di esportazione][3]

## Monitorare lo stato dell’operazione di esportazione
1. Fare clic su **SQL Server**.
2. Fare clic sul server contenente il database di origine iniziale appena archiviato.
3. Scorrere verso il basso fino a Operazioni.
4. Nel pannello server SQL fare clic su **Cronologia importazione/esportazione**:
   
   ![cronologia importazione/esportazione][4]

## Verificare che il file BACPAC sia nel contenitore di archiviazione
1. Fare clic su **Account di archiviazione**.
2. Selezionare l'account di archiviazione in cui è memorizzato l'archivio BACPAC.
3. Fare clic su **Contenitori** e selezionare il contenitore in cui è stato esportato il database per i dettagli (da qui è possibile scaricare e salvare il file BACPAC).
   
   ![dettagli del file con estensione bacpac][5]

## Passaggi successivi
* Per informazioni sull'importazione di un file BACPAC in un database SQL di Azure, vedere [Importare un file BACPAC per creare un nuovo database SQL di Azure](sql-database-import.md)
* Per informazioni sull'importazione di un file BACPAC in un database di SQL Server, vedere [Importare un file BACPAC per creare un nuovo database utente](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

<!---HONumber=AcomDC_0817_2016-->