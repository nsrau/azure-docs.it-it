---
title: Usare Redgate per caricare i dati in Azure Data Warehouse | Documentazione Microsoft
description: Informazioni su come usare Data Platform Studio di Redgate per scenari di data warehousing.
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: cb0b5489ccfabb0e2a4ee412162a18930073b309


---
# <a name="load-data-with-redgate-data-platform-studio"></a>Caricare dati con Data Platform Studio di Redgate
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

Questa esercitazione mostra come usare [DPS (Data Platform Studio) di Redgate](http://www.red-gate.com/products/azure-development/data-platform-studio/) per spostare dati da un'istanza di SQL Server locale a Azure SQL Data Warehouse. Data Platform Studio applica le correzioni e le ottimizzazioni di compatibilità più appropriate, motivo per cui è il modo più rapido per iniziare a utilizzare SQL Data Warehouse.

> [!NOTE]
> [Redgate](http://www.red-gate.com), partner Microsoft da lunga data, offre diversi strumenti di SQL Server. Questa funzionalità di Data Platform Studio è disponibile gratuitamente per uso sia commerciale che non commerciale.
> 
> 

## <a name="before-you-begin"></a>Prima di iniziare
### <a name="create-or-identify-resources"></a>Creare o identificare le risorse
Prima di iniziare questa esercitazione, è necessario avere a disposizione quanto segue:

* **Database di SQL Server locale**: i dati da importare in SQL Data Warehouse devono provenire da un'istanza di SQL Server locale (versione 2008 R2 o successiva). Data Platform Studio non è in grado di importare direttamente dati da un database SQL di Azure o da file di testo.
* **Account di archiviazione di Azure**: Data Platform Studio colloca temporaneamente i dati in Archiviazione BLOB di Azure prima di caricarli in SQL Data Warehouse. L'account di archiviazione deve usare il modello di distribuzione "Resource Manager" (impostazione predefinita) e non il modello di distribuzione "Classica". Se non si dispone di un account di archiviazione, vedere l'articolo relativo alla creazione di tale account. 
* **SQL Data Warehouse**: in questa esercitazione i dati vengono spostati da un'istanza di SQL Server locale a SQL Data Warehouse, motivo per cui è necessario avere un data warehouse online. Se non si dispone di un data warehouse, vedere l'articolo relativo alla creazione di un'istanza di Azure SQL Data Warehouse.

> [!NOTE]
> Per migliorare le prestazioni, creare l'account di archiviazione e il data warehouse nella stessa area geografica.
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>Passaggio 1: Accedere a Data Platform Studio con il proprio account Azure
Aprire il Web browser e passare al sito Web [Data Platform Studio](https://www.dataplatformstudio.com/). Accedere con lo stesso account Azure usato per creare l'account di archiviazione e il data warehouse. Se il proprio indirizzo di posta elettronica è associato sia a un account aziendale o dell'istituto di istruzione che a un account Microsoft, verificare di usare l'account che ha accesso alle risorse.

> [!NOTE]
> Se si usa Data Platform Studio per la prima volta, viene chiesto di concedere l'autorizzazione applicazione per la gestione delle risorse di Azure.
> 
> 

## <a name="step-2-start-the-import-wizard"></a>Passaggio 2: Avviare l'importazione guidata
Nella schermata principale di DPS selezionare il collegamento Import to Azure SQL Data Warehouse (Importa in Azure SQL Data Warehouse) per avviare l'importazione guidata.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>Passaggio 3: Installare il gateway di Data Platform Studio
Per connettersi al database SQL Server locale è necessario installare il gateway di DPS. Il gateway è un agente client che fornisce l'accesso all'ambiente locale, estrae i dati e li carica nell'account di archiviazione. I dati non passano mai attraverso i server Redgate. Per installare il gateway:

1. Fare clic sul collegamento **Create Gateway** (Crea gateway).
2. Scaricare e installare il gateway usando il programma di installazione fornito

![][2]

> [!NOTE]
> Il gateway può essere installato in qualsiasi computer con accesso al database di SQL Server di origine. Accedere al database di SQL Server usando l'autenticazione di Windows con le credenziali dell'utente corrente.
> 
> 

Dopo l'installazione, lo stato del server viene modificato in Connected (Connesso) ed è possibile selezionare Next (Avanti).

## <a name="step-4-identify-the-source-database"></a>Passaggio 4: Identificare il database di origine
Nella casella di testo *Enter Server Name* (Immettere il nome del server) immettere il nome del server che ospita il database e selezionare **Next** (Avanti). Quindi, dal menu a discesa, selezionare il database da cui si vogliono importare i dati.

![][3]

DPS controlla il database selezionato riguardo alle tabelle da importare. Per impostazione predefinita, DPS importa tutte le tabelle del database. È possibile selezionare o deselezionare le tabelle espandendo il collegamento All Tables (Tutte le tabelle). Per continuare, fare clic sul pulsante Next (Avanti).

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>Passaggio 5: Scegliere un account di archiviazione per la collocazione temporanea dei dati
DPS chiede di specificare una posizione per la collocazione temporanea dei dati. Scegliere un account di archiviazione esistente dalla sottoscrizione e selezionare **Next** (Avanti).

> [!NOTE]
> DPS crea un nuovo contenitore BLOB nell'account di archiviazione scelto e usa una cartella distinta per ogni importazione.
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>Passaggio 6: Selezionare un data warehouse
A questo punto, è possibile selezionare un database di [Azure SQL Data Warehouse](http://aka.ms/sqldw) online in cui importare i dati. Dopo aver selezionato il database, immettere le credenziali per connettersi a quest'ultimo e selezionare **Next** (Avanti).

![][5]

> [!NOTE]
> DPS unisce le tabelle di dati di origine nel data warehouse. DPS visualizza un avviso se il nome della tabella deve sovrascrivere le tabelle esistenti nel data warehouse. Facoltativamente, è possibile eliminare eventuali oggetti esistenti nel data warehouse selezionando Delete all existing objects (Elimina tutti gli oggetti esistenti) prima dell'importazione.
> 
> 

## <a name="step-7-import-the-data"></a>Passaggio 7: Importare i dati
DPS conferma che si vogliono importare i dati. È sufficiente fare clic sul pulsante Start import (Avvia importazione) per avviare l'importazione.

![][6]

DPS mostra una visualizzazione contenente lo stato di avanzamento dell'estrazione e del caricamento dei dati dall'istanza di SQL Server locale nonché lo stato di avanzamento dell'importazione in SQL Data Warehouse.

![][7]

Al termine dell'importazione, DPS visualizza un riepilogo dell'importazione dei dati e un report delle modifiche delle correzioni di compatibilità che sono state eseguite.

![][8]

## <a name="next-steps"></a>Passaggi successivi
Per esplorare i dati in SQL Data Warehouse, consultare gli articoli seguenti:

* [Query Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)] (Eseguire query in Azure SQL Data Warehouse (Visual Studio))
* [Visualizzare i dati con Power BI][Visualize data with Power BI]

Per altre informazioni su Data Platform Studio di Redgate:

* [Visitare la home page di DPS](http://www.dataplatformstudio.com/)
* [Guardare una demo di DPS su Channel9](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Per una panoramica dei modi in cui è possibile eseguire la migrazione e caricare i dati in SQL Data Warehouse, vedere gli articoli seguenti:

* [Eseguire la migrazione della soluzione in SQL Data Warehouse][Migrate your solution to SQL Data Warehouse]
* [Caricare i dati in Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md)

Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse](sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md



<!--HONumber=Feb17_HO3-->


