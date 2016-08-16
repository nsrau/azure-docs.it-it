<properties
   pageTitle="Creare un'istanza di SQL Data Warehouse nel portale di Azure | Microsoft Azure"
   description="Informazioni su come creare un'istanza di SQL Data Warehouse nel portale di Azure"
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
   ms.date="07/23/2016"
   ms.author="barbkess;lodipalm;sonyama"/>  

# Creare un Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Portale di Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

In questa esercitazione si usa il portale di Azure per creare un'istanza di SQL Data Warehouse contenente un database di esempio AdventureWorksDW.


## Prerequisiti

Per iniziare, è necessario:

- **Account Azure**: vedere la [versione di valutazione gratuita][] o [Credito Azure mensile per sottoscrittori di Visual Studio][] per creare un account.
- **Server SQL di Azure**: per altre informazioni, vedere [Creare un server logico del database SQL di Azure][] .

> [AZURE.NOTE] La creazione di un'istanza di SQL Data Warehouse può avere come risultato un nuovo servizio fatturabile. Per alte informazioni, vedere [Prezzi di SQL Data Warehouse][].

## Creare un SQL Data Warehouse

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **+ Nuovo** > **Dati e archiviazione** > **SQL Data Warehouse**.

    ![Create](./media/sql-data-warehouse-get-started-provision/create-sample.gif)  

3. Nel pannello **SQL Data Warehouse** specificare le informazioni necessarie, quindi fare clic su 'Crea'.

    ![Creazione del database](./media/sql-data-warehouse-get-started-provision/create-database.png)  

	- **Server**: è consigliabile selezionare prima di tutto il server.

	- **Nome database**: nome che verrà usato per fare riferimento a SQL Data Warehouse. Il nome deve essere univoco per il server.
	
    - **Prestazioni**: è consigliabile iniziare con 400 [DWU][DWU]. È possibile spostare il dispositivo di scorrimento a sinistra o a destra per regolare le prestazioni del data warehouse oppure aumentare o ridurre le prestazioni dopo la creazione. Per altre informazioni sulle DWU, vedere la documentazione sul [ridimensionamento](./sql-data-warehouse-manage-compute-overview.md) o la [pagina dei prezzi][SQL Data Warehouse pricing].

    - **Sottoscrizione**: selezionare la [sottoscrizione] per la fatturazione di questa istanza di SQL Data Warehouse.

    - **Gruppo di risorse**: i [gruppi di risorse][Resource group] sono contenitori progettati per semplificare la gestione di una raccolta di risorse di Azure. Per altre informazioni, vedere [gruppi di risorse](../resource-group-overview.md).

    - **Selezionare l'origine**: fare clic su **Selezionare l'origine** > **Esempio**. Azure inserisce automaticamente AdventureWorksDW per l'opzione **Selezionare l'esempio selezionare**.

4. Fare clic su **Crea** per creare l'istanza di SQL Data Warehouse.

5. Attendere alcuni minuti. Quando il data warehouse è pronto, verrà visualizzato di nuovo il [portale di Azure](https://portal.azure.com). SQL Data Warehouse è disponibile nel dashboard, elencato tra i database SQL o nel gruppo di risorse usato per crearlo.

    ![Visualizzazione del portale](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)  

[AZURE.INCLUDE [Creare un server di database SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Passaggi successivi

Dopo aver creato un'istanza di SQL Data Warehouse è possibile [connettersi](./sql-data-warehouse-connect-overview.md) e iniziare a eseguire query.

Per una panoramica del caricamento, vedere [Caricare i dati in SQL Data Warehouse](./sql-data-warehouse-overview-load.md).

Se si sta cercando di eseguire la migrazione di un database esistente a SQL Data Warehouse, vedere la [panoramica della migrazione](./sql-data-warehouse-overview-migrate.md) o usare l'[utilità di migrazione](./sql-data-warehouse-migrate-migration-utility.md).

Le regole firewall possono anche essere configurate con Transact-SQL. Per altre informazioni, vedere [sp\_set\_firewall\_rule][] e [sp\_set\_database\_firewall\_rule][].

È anche opportuno vedere le [procedure consigliate][].

<!--Article references-->  
[Creare un server logico del database SQL di Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[procedure consigliate]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[sottoscrizione]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group

<!--MSDN references-->
[sp\_set\_firewall\_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp\_set\_database\_firewall\_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->  
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Prezzi di SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[versione di valutazione gratuita]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Credito Azure mensile per sottoscrittori di Visual Studio]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0810_2016-->