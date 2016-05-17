<properties
   pageTitle="Creare un'istanza di SQL Data Warehouse nel portale di Azure | Microsoft Azure"
   description="Informazioni su come creare un database SQL Data Warehouse nel portale di Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creare un SQL Data Warehouse

> [AZURE.SELECTOR]
- [Portale di Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Questa esercitazione mostra come usare il portale di Azure per creare un'istanza di SQL Data Warehouse contenente un database di esempio AdventureWorksDW.


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **Nuovo** > **Dati e archiviazione** > **SQL Data Warehouse**.

    ![Creazione](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. Nel pannello **SQL Data Warehouse** specificare le informazioni necessarie, quindi fare clic su Crea.

    ![Creazione del database](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **Server**: è consigliabile selezionare prima di tutto il server. È possibile selezionare un server esistente o [crearne uno nuovo](./sql-data-warehouse-get-started-new-server.md). 

	- **Nome database**: nome che verrà usato per fare riferimento a SQL Data Warehouse. Il nome deve essere univoco per il server.
	
    - **Prestazioni**: si consiglia di iniziare con 400 DWU. È possibile spostare il dispositivo di scorrimento a sinistra o a destra per regolare le prestazioni del data warehouse oppure aumentare o ridurre le prestazioni dopo la creazione. Per altre informazioni sulle DWU, vedere la documentazione sul [ridimensionamento](./sql-data-warehouse-overview-scalability.md) o la [pagina dei prezzi](https://azure.microsoft.com/it-IT/pricing/details/sql-data-warehouse/).

    - **Sottoscrizione**: selezionare la sottoscrizione per la fatturazione di questa istanza di SQL Data Warehouse.

    - **Gruppo di risorse**: i gruppi di risorse sono contenitori progettati per facilitare la gestione di una raccolta di risorse di Azure. Altre informazioni sui [gruppi di risorse](../azure-portal/resource-group-portal.md).

    - **Selezionare l'origine**: fare clic su **Selezionare l'origine** > **Esempio**. Poiché per ora è disponibile un solo database di esempio, quando si seleziona Esempio, Azure popola automaticamente l'opzione **Selezionare l'esempio** con AdventureWorksDW.

4. Fare clic su **Crea** per creare l'istanza di SQL Data Warehouse,

5. che sarà pronta dopo alcuni minuti. Al termine, dovrebbe essere visualizzato di nuovo il [portale di Azure](https://portal.azure.com). SQL Data Warehouse è disponibile nel dashboard, elencato tra i database SQL o nel gruppo di risorse usato per crearlo.

    ![Visualizzazione del portale](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [Creare un server di database SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Passaggi successivi

Dopo aver creato un'istanza di SQL Data Warehouse, è possibile [connettersi](./sql-data-warehouse-get-started-connect.md) e iniziare a eseguire query.

Per una panoramica sul caricamento, vedere [Caricare i dati in SQL Data Warehouse](./sql-data-warehouse-overview-load.md).

Se si sta cercando di eseguire la migrazione di un database esistente in SQL Data Warehouse, vedere i [cenni preliminari sulla migrazione](./sql-data-warehouse-overview-migrate.md) o usare l'[utilità di migrazione](./sql-data-warehouse-migrate-migration-utility.md).

<!---HONumber=AcomDC_0511_2016-->