---
title: Origini dati supportate in Azure Analysis Services | Microsoft Docs
description: Descrive le origini dati e i connettori supportati per i modelli di dati tabulari 1200 e versioni successive in Azure Analysis Services.Describes data sources and connectors supported for tabular 1200 and higher data models in Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461658"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Origini dati supportate in Azure Analysis Services

Le origini dati e i connettori illustrati nell'Importazione guidata dati o Tabella in Visual Studio con i progetti di Analysis Services vengono visualizzati sia per Azure Analysis Services che per SQL Server Analysis Services. Tuttavia, non tutte le origini dati e i connettori visualizzati sono supportati in Azure Analysis Services.However, not all data sources and connectors shown are supported in Azure Analysis Services. I tipi di origini dati a cui è possibile connettersi dipendono da molti fattori, ad esempio il livello di compatibilità del modello, i connettori dati disponibili, il tipo di autenticazione e il supporto del gateway dati locale. Le tabelle seguenti descrivono le origini dati supportate per Azure Analysis Services.The following tables describe supported data sources for Azure Analysis Services.

## <a name="azure-data-sources"></a>Origini dati di Azure

|Origine dati  |In memoria  |DirectQuery  |Note |
|---------|---------|---------|---------|
|database SQL di Azure      |   Sì      |    Sì      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL Data Warehouse)      |   Sì      |   Sì       |<sup>[2](#azprovider)</sup>|
|Archiviazione BLOB di Azure      |   Sì       |    No      | <sup>[1](#tab1400a)</sup> |
|Archiviazione tabelle di Azure     |   Sì       |    No      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Sì        |  No        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Sì       |    No      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Sì       |    No      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Sì     |   No       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Sì       |   No       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Note:**   
<a name="tab1400a">1</a> - Solo modelli tabulari 1400 e superiori.  
<a name="azprovider">2</a> - Se specificato come origine dati *del provider* nei modelli tabulari 1200 e superiori, sia i modelli in memoria che i modelli DirectQuery richiedono Driver Microsoft OLE DB per SQL Server MSOLEDBSQL (consigliato), SQL Server Native Client 11.0 o .NET Framework Data Provider per SQL Server.    
<a name="azsqlmanaged">3</a> - È supportata l'istanza gestita del database SQL di Azure.3 - Azure SQL Database Managed Instance is supported. Poiché l'istanza gestita viene eseguita all'interno della rete virtuale di Azure con un indirizzo IP privato, l'endpoint pubblico deve essere abilitato nell'istanza. Se non è abilitato, è necessario un [gateway dati locale.](analysis-services-gateway.md)    
<a name="databricks">4</a> - Azure Databricks con il connettore Spark non è attualmente supportato.   
<a name="gen2">5</a> - Il connettore ADLS Gen2 non è attualmente supportato, tuttavia, il connettore di Archiviazione BLOB di Azure può essere usato con un'origine dati ADLS Gen2.   

## <a name="other-data-sources"></a>Altre origini dati

|Origine dati | In memoria | DirectQuery |Note   |
|  --- | --- | --- | --- |
|Database di Access     |  Sì | No |  |
|Active Directory     |  Sì | No | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Sì | No |  |
|Sistema della piattaforma di analisi     |  Sì | No |  |
|File CSV  |Sì | No |  |
|Dynamics 365     |  Sì | No | <sup>[6](#tab1400b)</sup> |
|Cartella di lavoro di Excel     |  Sì | No |  |
|Exchange      |  Sì | No | <sup>[6](#tab1400b)</sup> |
|Cartella      |Sì | No | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Sì | No |  |
|Documento JSON      |  Sì | No | <sup>[6](#tab1400b)</sup> |
|Linee da binario      | Sì | No | <sup>[6](#tab1400b)</sup> |
|Database MySQL     | Sì | No |  |
|Feed OData      |  Sì | No | <sup>[6](#tab1400b)</sup> |
|Query ODBC     | Sì | No |  |
|OLE DB     |   Sì | No |  |
|Oracle  | Sì  |Sì  | <sup>[9](#oracle)</sup> |
|Database PostgreSQL   | Sì | No | <sup>[6](#tab1400b)</sup> |
|Oggetti Salesforce|  Sì | No | <sup>[6](#tab1400b)</sup> |
|Report di Salesforce |Sì | No | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Sì | No |  |
|SAP Business Warehouse    |  Sì | No | <sup>[6](#tab1400b)</sup> |
|Elenco SharePoint      |   Sì | No | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Sì   | Sì  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> | 
|SQL Server Data Warehouse |Sì   | Sì  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Database di Sybase     |  Sì | No |  |
|Teradata | Sì  | Sì  | <sup>[10](#teradata)</sup> |
|Txt  |Sì | No |  |
|Tabella XML    |  Sì | No | <sup>[6](#tab1400b)</sup> |
| | | |

**Note:**   
<a name="tab1400b">6</a> - Solo modelli tabulari 1400 e superiori.  
<a name="sqlim">7</a> - Se specificato come origine dati *del provider* nei modelli tabulari 1200 e versioni successive, specificare Driver Microsoft OLE DB per SQL Server MSOLEDBSQL (scelta consigliata), SQL Server Native Client 11.0 o .NET Framework Provider per SQL Server.  
<a name="instgw">8</a> - Se si specifica MSOLEDBSQL come provider di dati, potrebbe essere necessario scaricare e installare il [driver Microsoft OLE DB per SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) nello stesso computer del gateway dati locale.  
<a name="oracle">9</a> - Per i modelli tabulari 1200 o come origine dati *del provider* in modelli tabulari 1400, specificare il provider di dati Oracle per .NET.  
<a name="teradata">10</a> - Per i modelli tabulari 1200 o come origine dati *del provider* in modelli tabulari 1400, specificare il provider di dati Teradata per .NET.   
<a name="filesSP">11</a> - I file in SharePoint locale non sono supportati.

La connessione a origini dati locali da un server di Azure Analysis Services richiede un [gateway locale.](analysis-services-gateway.md) Quando si usa un gateway, sono necessari i provider a 64 bit. 

## <a name="understanding-providers"></a>Informazioni sui provider

Quando si creano progetti modello tabulare 1400 e versioni successive in Visual Studio, per impostazione predefinita non si specifica un provider di dati quando ci si connette a un'origine dati utilizzando **Recupero dati**. I modelli tabulari 1400 e versioni successive usano i connettori [Power Query](/power-query/power-query-what-is-power-query) per gestire connessioni, query di dati e mashup tra l'origine dati e Analysis Services. Questi sono talvolta indicati come connessioni a origini dati *strutturate* in quanto le impostazioni delle proprietà di connessione sono impostate per l'utente. È tuttavia possibile abilitare le origini dati legacy. Se abilitata, è possibile utilizzare **Importazione guidata tabella** per connettersi a determinate origini dati tradizionalmente supportate in modelli tabulari 1200 e inferiori come origini dati *legacy*o *provider.* Se specificato come origine dati del provider, è possibile specificare un provider di dati specifico e altre proprietà di connessione avanzate. Ad esempio, è possibile connettersi a un data warehouse di SQL Server locale o anche a un database SQL di Azure come origine dati legacy. È quindi possibile selezionare il driver OLE DB per il provider di dati MSOLEDBSQL di SQL Server. In questo caso, la selezione di un provider di dati OLE DB può fornire prestazioni migliori rispetto al connettore Power Query. 

Quando si usa l'Importazione guidata tabella in Visual Studio, le connessioni a qualsiasi origine dati richiedono un provider di dati. Viene selezionato un provider di dati predefinito. Se necessario, è possibile modificare il provider di dati. Il tipo di provider scelto può dipendere dalle prestazioni, dal fatto che il modello utilizzi o meno l'archiviazione in memoria o DirectQuery e la piattaforma di Analysis ServicesAnalysis Services in cui distribuisci il modello.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Specificare le origini dati del provider nei progetti tabulari 1400 e versioni successiveSpecify provider data sources in tabular 1400 and higher model projects

Per abilitare le origini dati del provider, in Visual Studio fare clic su **Opzioni opzioni** > Analysis Services Tabular Data Import **(Opzioni)** > **per l'importazione dei** > **dati**tabulari , selezionare Abilita origini dati **legacy**.

![Abilitare le origini dati legacyEnable legacy data sources](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Con le origini dati legacy **abilitate,** in Esplora modelli tabulari fare clic con il pulsante destro del mouse su **Origini** > dati**importa da origine dati (legacy).**

![Legacy data sources in Tabular Model Explorer](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Analogamente ai progetti di modello tabulari 1200, usare **l'Importazione guidata tabella** per connettersi a un'origine dati. Nella pagina di connessione fare clic su **Avanzate**. Specificare il provider di dati e altre impostazioni di connessione in **Imposta proprietà avanzate**.

![Origini dati legacy Proprietà avanzate](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Rappresentazione
In alcuni casi può essere necessario specificare un account di rappresentazione differente. L'account di rappresentazione può essere specificato in Visual Studio o SSMS.

Per le origini dati locali:

* Se si usa l'autenticazione SQL, la rappresentazione deve essere l'account del servizio.
* Se si usa l'autenticazione di Windows, impostare nome utente/password di Windows. Per SQL Server, l'autenticazione di Windows con un account di rappresentazione specifico è supportata solo per i modelli di dati In-memory.

Per le origini dati cloud:

* Se si usa l'autenticazione SQL, la rappresentazione deve essere l'account del servizio.

## <a name="oauth-credentials"></a>Credenziali OAuth

Per i modelli tabulari a livello di compatibilità 1400 e superiore che utilizzano la modalità in memoria, il database SQL di Azure, Azure Synapse Analytics (SQL Data Warehouse), Dynamics 365 e L'elenco SharePoint supportano le credenziali OAuth. Azure Analysis Services gestisce l'aggiornamento dei token per le origini dati OAuth per evitare timeout per le operazioni di aggiornamento a esecuzione prolungata. Per generare token validi, impostare le credenziali utilizzando SSMS.

La modalità Direct Query non è supportata con le credenziali OAuth.

## <a name="next-steps"></a>Passaggi successivi
[Gateway locale](analysis-services-gateway.md)   
[Gestire il server](analysis-services-manage.md)   

