<properties
	pageTitle="Archiviare un database SQL di Azure in un file BACPAC tramite il portale di Azure"
	description="Archiviare un database SQL di Azure in un file BACPAC tramite il portale di Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Archiviare un database SQL di Azure in un file BACPAC tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com) per archiviare un database SQL di Azure in un file BACPAC nell'archivio BLOB di Azure.

Per creare un archivio di un database SQL di Azure, è possibile esportare lo schema di database e i dati in un file BACPAC. Un file BACPAC è semplicemente un file ZIP con estensione BACPAC. In un secondo momento il file BACPAC può essere salvato nell'archivio BLOB di Azure o in un archivio e un percorso locali e successivamente reimportato nel database SQL di Azure o in un'installazione locale di SQL Server.

***Considerazioni***

- Perché un archivio sia coerente dal punto di vista transazionale, è necessario assicurarsi che non si verifichino attività di scrittura durante l'esportazione oppure è necessario eseguire l'esportazione da una [copia coerente dal punto di vista transazionale](sql-database-copy.md) del database SQL di Azure
- La dimensione massima di un file BACPAC salvato nell'archivio BLOB di Azure è pari a 200 GB. Per archiviare un file BACPAC più grande in un percorso locale, usare l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Questa utilità è disponibile sia in Visual Studio che in SQL Server. È inoltre possibile [scaricare](https://msdn.microsoft.com/library/mt204009.aspx) la versione più recente di SQL Server Data Tools per ottenere questa utilità.
- La memorizzazione in Archiviazione Premium di Azure tramite un file BACPAC non è supportata.
- Se l'operazione di esportazione dura oltre 20 ore, potrebbe essere annullata. Per migliorare le prestazioni durante l'esportazione è possibile:
 - Aumentare temporaneamente il livello di servizio. 
 - Interrompere tutte le attività di lettura e scrittura durante l'esportazione.
 - Usare un indice cluster in tutte le tabelle di grandi dimensioni. Senza indici cluster, un'esportazione di durata superiore a 6-12 ore potrebbe avere esito negativo. Questo perché i servizi di esportazione devono completare la scansione della tabella prima di provare a esportarla per intero.

> [AZURE.NOTE] I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell’utente. Per ulteriori informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure.
- Database SQL di Azure. 
- Un [account di Archiviazione Standard di Azure](../storage/storage-create-storage-account.md) con un contenitore BLOB per archiviare il file BACPAC nella risorsa di archiviazione standard.

## Esportazione del database

Aprire il pannello del Database SQL per il database che si desidera esportare:

> [AZURE.IMPORTANT] Per garantire un file BACPAC coerente dal punto di vista transazionale è necessario innanzitutto [creare una copia del database](sql-database-copy.md) e quindi esportare la copia del database.

1.	Accedere al [portale di Azure](https://portal.azure.com).
2.	Fare clic su **Database SQL**.
3.	Fare clic sul database per archiviare.
4.	Nel pannello del database SQL fare clic su **Esporta** per aprire il pannello **Esporta database**:

    ![pulsante esporta][1]

5.  Fare clic su **Archiviazione** e selezionare il contenitore di BLOB e l’account di archiviazione in cui verrà archiviato il file BACPAC:

    ![esporta database][2]

6. Selezionare il tipo di autenticazione.
7.  Immettere le credenziali di autenticazione appropriate per il server SQL di Azure contenente il database da esportare.
8.  Fare clic su **OK** per archiviare il database. Facendo clic su **OK** si crea una richiesta di esportazione del database che viene inviata al servizio. La durata dell'esportazione dipende dalla dimensione e dalla complessità del database e dal livello del servizio. Si riceverà una notifica.

    ![notifica di esportazione][3]

## Monitorare lo stato dell’operazione di esportazione

1.	Fare clic su **SQL Server**.
2.	Fare clic sul server contenente il database di origine iniziale appena archiviato.
3.  Scorrere verso il basso fino a Operazioni.
4.	Nel pannello server SQL fare clic su **Cronologia importazione/esportazione**:

    ![cronologia importazione/esportazione][4]

## Verificare che il file BACPAC sia nel contenitore di archiviazione

1.	Fare clic su **Account di archiviazione**.
2.	Selezionare l'account di archiviazione in cui è memorizzato l'archivio BACPAC.
3.	Fare clic su **Contenitori** e selezionare il contenitore in cui è stato esportato il database per i dettagli (da qui è possibile scaricare e salvare il file BACPAC).

    ![dettagli del file con estensione bacpac][5]

## Passaggi successivi

- [Importare un database SQL di Azure][5]



## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

<!---HONumber=AcomDC_0413_2016-->