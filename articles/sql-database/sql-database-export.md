<properties
	pageTitle="Creare ed esportare un file BACPAC di un database SQL di Azure"
	description="Creare ed esportare un file BACPAC di un database SQL di Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/23/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Creare ed esportare un file BACPAC di un database SQL di Azure

**Database singolo**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

In questo articolo vengono fornite istruzioni per l'esportazione di un file BACPAC del database SQL Azure con il [portale di Azure](https://portal.azure.com).

Un [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) è un file con estensione .bacpac che contiene lo schema del database e i dati. L'utilizzo primario per un file BACPAC è quello di spostare un database da un server all’altro, a [migrazione di un database locale nel cloud](sql-database-cloud-migrate.md), e di archiviare un database esistente in un formato aperto.

> [AZURE.NOTE] I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell’utente. Per ulteriori informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).


Il file BACPAC viene esportato in un contenitore di BLOB di archiviazione di Azure che è possibile scaricare una volta che l'operazione è stata completata correttamente.

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare clic su **ACCOUNT GRATUITO** nella parte superiore della pagina, quindi tornare all'articolo.
- Database SQL di Azure. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
- Un [account di archiviazione Azure](../storage/storage-create-storage-account.md) con un contenitore di BLOB per archiviare il BACPAC.


## Esportazione del database

Aprire il pannello del Database SQL per il database che si desidera esportare:

> [AZURE.IMPORTANT] Per garantire un file BACPAC coerente dal punto di vista transazionale è necessario innanzitutto [creare una copia del database](sql-database-copy.md) e quindi esportare la copia del database.

1.	Accedere al [portale di Azure](https://portal.azure.com).
2.	Fare clic su **ESPLORA TUTTO**.
3.	Fare clic su **database SQL**.
2.	Fare clic sul database che si desidera esportare come file BACPAC.
3.	Nel pannello del database SQL fare clic su **Esporta** per aprire il pannello **Esporta database**:

    ![pulsante esporta][1]

1.  Fare clic su **Archiviazione** e selezionare il contenitore di BLOB e l’account di archiviazione in cui verrà archiviato il file BACPAC:

    ![esporta database][2]

1.  Immettere l’ **account di accesso dell’amministratore del Server** e la **Password** per il server SQL di Azure che contiene il database che si vuole esportare.
1.  Fare clic su **Crea** per esportare il database.

Facendo clic su **Crea** si crea una richiesta di esportazione del database e la si invia al servizio. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere molto tempo.

## Monitorare lo stato dell’operazione di esportazione

2.	Fare clic su **ESPLORA TUTTO**.
3.	Fare clic su **SQL Server**.
2.	Fare clic sul server contenente il database originale (origine) esportato.
3.	Nel pannello server SQL fare clic su **Cronologia importazione/esportazione**:

    ![cronologia importazione/esportazione][3] ![cronologia importazione/esportazione][4]

## Verificare che il file BACPAC sia nel contenitore di archiviazione

2.	Fare clic su **ESPLORA TUTTO**.
3.	Fare clic su **Account di archiviazione (classica)**.
2.	Selezionare l'account di archiviazione in cui è memorizzato il file BACPAC.
3.	Fare clic su **Contenitori** e selezionare il contenitore in cui è stato esportato il database per i dettagli (da qui è possibile scaricare e salvare il file BACPAC).

    ![dettagli del file con estensione bacpac][5]


## Passaggi successivi

- [Importare un database SQL di Azure](sql-database-import.md)



## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-history.png
[4]: ./media/sql-database-export/export-status.png
[5]: ./media/sql-database-export/bacpac-details.png

<!---HONumber=AcomDC_0224_2016-->