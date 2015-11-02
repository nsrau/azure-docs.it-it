<properties 
	pageTitle="Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse Portale| Microsoft Azure" 
	description="Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse Portale" 
	services="sql-data-warehouse" 
	documentationCenter="" 
	authors="twounder" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-data-warehouse" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/21/2015" 
	ms.author="twounder"/>
 
# Introduzione a Transparent Data Encryption (TDE)
> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

La funzionalità Transparent Data Encryption (TDE) di SQL Data Warehouse di Azure consente di proteggersi da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza dover apportare modifiche all'applicazione.

TDE esegue la crittografa dell'archiviazione di un intero database usando una chiave simmetrica detta "chiave di crittografia del database". Nel database SQL la chiave di crittografia del database è protetta da un certificato server incorporato. Il certificato server incorporato è univoco per ogni server di database SQL. Microsoft ruota automaticamente questi certificati almeno ogni 90 giorni. Per una descrizione generale della funzionalità TDE, vedere [Transparent Data Encryption (TDE)].

##Abilitazione della crittografia

Per abilitare TDE per un SQL Data Warehouse, attenersi alla procedura seguente:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. Nel pannello del database fare clic sul pulsante **Impostazioni**	
3. Selezionare l’opzione ![][1] di **Crittografia dati trasparente** 
4. Selezionare l’impostazione ![][2] su **Attiva** 
5. Selezionare **Salva** ![][3]  

##Disabilitazione della crittografia

Per disabilitare TDE per un SQL Data Warehouse, attenersi alla procedura seguente:

1. Aprire il database nel [portale di Azure](https://portal.azure.com)
2. Nel pannello del database fare clic sul pulsante **Impostazioni**	
3. Selezionare l’opzione ![][1] di **Crittografia dati trasparente** 
4. Selezionare l’impostazione ![][4] su **Disattiva** 
5. Selezionare **Salva** ![][5]  




<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/it-IT/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=Oct15_HO4-->