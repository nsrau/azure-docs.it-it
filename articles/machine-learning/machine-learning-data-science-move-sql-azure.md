<properties 
	pageTitle="Spostare i dati a un database SQL di Azure per Azure Machine Learning | Azure" 
	description="Creare una tabella SQL e caricare dati in tabelle SQL" 
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="jacob.spoelstra" 
	editor=""  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="fashah;bradsev" />

# Spostamento dei dati in un database di SQL Azure per Azure Machine Learning


In questo argomento vengono indicate le opzioni per lo spostamento dei dati da file flat (formati CSV o TSV) o da dati archiviati in un server SQL locale a un database SQL Azure. Queste attività per lo spostamento dei dati nel cloud fanno parte di ADAPT (Advanced Analytics Process and Technology) fornito da Azure Machine Learning.

Per un argomento che descrive le opzioni per lo spostamento dei dati a un server SQL locale per Machine Learning, vedere [Spostamento dei dati in SQL Server in una macchina virtuale di Azure](machine-learning-data-science-move-sql-server-virtual-machine.md).

Nella tabella seguente vengono riepilogate le opzioni per lo spostamento dei dati a un database di SQL Azure. <table>

<tr>
<td><b>ORIGINE</b></td>
<td colspan="2"><b>DESTINAZIONE: database SQL di Azure</b></td>
</tr>

<tr>
  <td><b>File flat (con formato CSV o TSV)</b></td>  

  <td>
    1. <a href="#bulk-insert-sql-query">Inserimento di massa query SQL
  </td>
</tr>

<tr>
  <td><b>Server SQL locale</b></td>

  <td>
    1. <a href="#export-flat-file">Esportazione in un file flat<br>
    2. <a href="#insert-tables-bcp">Migrazione guidata database SQL<br>
    3. <a href="#db-migration">Backup e ripristino database<br>
    4. <a href="#adf">Data factory di Azure
  </td>
</tr>

</table>


## <a name="prereqs"></a>Prerequisiti
Questa procedura descritta di seguito richiede di disporre di:

* Una **sottoscrizione Azure**. Se non si dispone di una sottoscrizione, è possibile iscriversi per provare la [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Un **account di archiviazione di Azure**. In questa esercitazione si userà un account di archiviazione di Azure per archiviare i dati. Se non si dispone di un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione di Azure](storage-create-storage-account.md#create-a-storage-account). Dopo avere creato l'account di archiviazione, sarà necessario ottenere la chiave dell'account usata per accedere alla risorsa di archiviazione. Vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Accesso a un **database SQL di Azure**. Se è necessario configurare un database SQL Azure, l’[Introduzione al database SQL di Microsoft Azure](sql-database-get-started.md) fornisce informazioni su come eseguire il provisioning di una nuova istanza di un database di SQL Azure.
* **Azure PowerShell** installato e configurato localmente. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

**Dati**: i processi di migrazione vengono illustrati utilizzando il [set di dati NYC Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/). Il set di dati NYC Taxi contiene informazioni sui dati delle tratte e delle tariffe ed è disponibile, come indicato nel post, nell'archiviazione del BLOB di Azure [qui](http://www.andresmh.com/nyctaxitrips/). Un esempio e una descrizione di questi file sono inclusi in [Descrizione del set di dati Corse dei taxi di NYC](machine-learning-data-science-process-sql-walkthrough.md#dataset).
 
È possibile adattare le procedure descritte di seguito a un set di dati personalizzati o seguire i passaggi come descritto utilizzando il set di dati NYC Taxi. Per caricare il set di dati NYC Taxi nel database di SQL Server locale, attenersi alla procedura descritta in [Importazione in blocco dei dati nel database SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Queste istruzioni sono per un Server SQL in una macchina virtuale di Azure, ma la procedura per il caricamento nel Server SQL locale è la stessa.

## <a name="file-to-azure-sql-database"></a> Spostamento dei dati da un'origine di file flat a un database SQL Azure

I dati nei file flat (nel formato CSV o TSV) possono essere spostati a un database Azure SQL mediante un inserimento di massa query SQL.

### <a name="bulk-insert-sql-query"></a> Inserimento di massa query SQL

I passaggi per la procedura utilizzando l’inserimento di massa query SQL sono simili a quelli descritti nelle sezioni per lo spostamento dei dati da un'origine di file flat a un Server SQL in una VM di Azure. Per ulteriori informazioni, vedere [Inserimento di massa query SQL](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).

##<a name="sql-on-prem-to-sazure-sql-database"></a> Spostamento dei dati da SQL Server locale a un database SQL Azure

Se i dati di origine vengono archiviati in un Server SQL locale, esistono varie possibilità per lo spostamento di dati in un database SQL Azure:

1. [Esportazione in un file flat](#export-flat-file) 
2. [Migrazione guidata database SQL](#insert-tables-bcp)
3. [Backup e ripristino database](#db-migration)
4. [Data factory di Azure](#adf)

I passaggi per i primi tre sono molto simili a tali sezioni in [Spostamento dei dati in SQL Server in una macchina virtuale di Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) che coprono la medesima procedura. Di seguito vengono forniti collegamenti alle sezioni appropriate in tale argomento.

###<a name="export-flat-file"></a>Esportazione in un file flat

La procedura di questo processo di esportazione in un file flat è simile a quelle in [Esportazione in un file flat](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file).

###<a name="insert-tables-bcp"></a>Migrazione guidata database SQL

I passaggi per utilizzare la migrazione guidata nel database SQL sono simili a quelle in [Migrazione guidata database SQL](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration).

###<a name="db-migration"></a>Backup e ripristino database

I passaggi per l'utilizzo del backup e ripristino del database sono simili a quelli in [Backup e ripristino database](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup).

###<a name="adf"></a>Azure Data Factory

La procedura per lo spostamento dei dati a un database Azure SQL con Azure Data Factory (ADF) viene fornita nell'argomento [Spostare i dati da un Server SQL locale a SQL Server su VM di Azure](machine-learning-data-science-move-sql-azure-adf.md). Questo argomento illustra come spostare i dati da un database di Server SQL locale a un database SQL di Azure tramite l'archiviazione BLOB di Azure utilizzando ADF.

È consigliabile utilizzare ADF quando i dati devono essere migrati continuamente in uno scenario ibrido che accede a risorse locali e cloud e quando i dati sono transazionali o devono essere modificati o avere una logica di business aggiunta durante la migrazione. ADF consente la pianificazione e il monitoraggio dei processi utilizzando semplici script JSON che gestiscono lo spostamento dei dati su base periodica. ADF dispone anche di altre funzionalità quali il supporto di operazioni complesse.

<!---HONumber=August15_HO7-->