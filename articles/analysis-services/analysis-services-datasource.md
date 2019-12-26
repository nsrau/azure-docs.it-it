---
title: Origini dati supportate in Azure Analysis Services | Microsoft Docs
description: Descrive le origini dati e i connettori supportati per i modelli di dati tabulari 1200 e versioni successive in Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7616bcdaf2a2ae6f80b0c1e964f311ef5409a64f
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707161"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Origini dati supportate in Azure Analysis Services

Le origini dati e i connettori visualizzati in recuperare dati o importazione guidata tabella in Visual Studio con Analysis Services progetti vengono visualizzati sia per Azure Analysis Services che per SQL Server Analysis Services. Tuttavia, non tutte le origini dati e i connettori visualizzati sono supportati in Azure Analysis Services. I tipi di origini dati a cui è possibile connettersi dipendono da molti fattori, ad esempio il livello di compatibilità del modello, i connettori dati disponibili, il tipo di autenticazione e il supporto del gateway dati locale. Nelle tabelle seguenti vengono descritte le origini dati supportate per Azure Analysis Services.

## <a name="azure-data-sources"></a>Origini dati di Azure

|Origine dati  |In memoria  |DirectQuery  |Note |
|---------|---------|---------|---------|
|database SQL di Azure      |   SÌ      |    SÌ      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|SQL Data Warehouse di Azure      |   SÌ      |   SÌ       |<sup>[2](#azprovider)</sup>|
|Archiviazione BLOB di Azure      |   SÌ       |    No      | <sup>[1](#tab1400a)</sup> |
|Archiviazione tabelle di Azure     |   SÌ       |    No      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  SÌ        |  No        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   SÌ       |    No      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   SÌ       |    No      |<sup>[1,5](#tab1400a)</sup> <sup> [ ](#gen2)</sup>|
|HDFS HDInsight di Azure    |     SÌ     |   No       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   SÌ       |   No       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Note:**    
<a name="tab1400a">1</a> - Solo modelli tabulari 1400 e superiori.  
<a name="azprovider">2</a> -quando viene specificato come origine dati del *provider* nei modelli tabulari 1200 e versioni successive, sia i modelli in memoria che DirectQuery richiedono il Driver Microsoft OLE DB per SQL Server MSOLEDBSQL (scelta consigliata), SQL Server Native Client 11,0 o .NET Framework provider di dati per SQL Server.    
<a name="azsqlmanaged">3</a> -istanza gestita di database SQL di Azure è supportato. Poiché l'istanza gestita viene eseguita in Azure VNet con un indirizzo IP privato, è necessario abilitare l'endpoint pubblico nell'istanza. Se questa funzionalità non è abilitata, è necessario un [gateway dati locale](analysis-services-gateway.md) .    
<a name="databricks">4</a> -Azure Databricks uso del connettore Spark non è attualmente supportata.   
<a name="gen2">5</a> -ADLS Gen2 Connector non è attualmente supportato, ma il connettore di archiviazione BLOB di Azure può essere usato con un'origine dati ADLS Gen2.   

## <a name="other-data-sources"></a>Altre origini dati

|Origine dati | In memoria | DirectQuery |Note   |
|  --- | --- | --- | --- |
|Database di Access     |  SÌ | No |  |
|Active Directory     |  SÌ | No | <sup>[6](#tab1400b)</sup>  |
|Servizi di analisi     |  SÌ | No |  |
|Piattaforma di strumenti analitici     |  SÌ | No |  |
|File CSV  |SÌ | No |  |
|Dynamics 365     |  SÌ | No | <sup>[6](#tab1400b)</sup> |
|Cartella di lavoro di Excel     |  SÌ | No |  |
|Scambia      |  SÌ | No | <sup>[6](#tab1400b)</sup> |
|Cartella      |SÌ | No | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |SÌ | No |  |
|Documento JSON      |  SÌ | No | <sup>[6](#tab1400b)</sup> |
|Righe da binario      | SÌ | No | <sup>[6](#tab1400b)</sup> |
|Database MySQL     | SÌ | No |  |
|Feed OData      |  SÌ | No | <sup>[6](#tab1400b)</sup> |
|Query ODBC     | SÌ | No |  |
|OLE DB     |   SÌ | No |  |
|Oracle  | SÌ  |SÌ  | <sup>[9](#oracle)</sup> |
|Database PostgreSQL   | SÌ | No | <sup>[6](#tab1400b)</sup> |
|Oggetti Salesforce|  SÌ | No | <sup>[6](#tab1400b)</sup> |
|Report di Salesforce |SÌ | No | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  SÌ | No |  |
|SAP Business Warehouse    |  SÌ | No | <sup>[6](#tab1400b)</sup> |
|Elenco SharePoint      |   SÌ | No | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |SÌ   | SÌ  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> | 
|SQL Server Data Warehouse |SÌ   | SÌ  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Database di Sybase     |  SÌ | No |  |
|Teradata | SÌ  | SÌ  | <sup>[10](#teradata)</sup> |
|File TXT  |SÌ | No |  |
|Tabella XML    |  SÌ | No | <sup>[6](#tab1400b)</sup> |
| | | |

**Note:**    
<a name="tab1400b">6</a> -solo modelli tabulari 1400 e versioni successive.  
<a name="sqlim">7</a> -se specificato come origine dati del *provider* nei modelli tabulari 1200 e versioni successive, specificare il Driver Microsoft OLE DB per SQL Server MSOLEDBSQL (scelta consigliata), SQL Server Native Client 11,0 o .NET Framework provider di dati per SQL Server.  
<a name="instgw">8</a> -se si specifica MSOLEDBSQL come provider di dati, potrebbe essere necessario scaricare e installare il [driver Microsoft OLE DB per SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) nello stesso computer del gateway dati locale.  
<a name="oracle">9</a> -per i modelli tabulari 1200 o come origine dati del *provider* nei modelli tabulari 1400 + specificare Oracle provider di dati per .NET.  
<a name="teradata">10</a> -per i modelli tabulari 1200 o come origine dati del *provider* nei modelli tabulari 1400 +, specificare Teradata provider di dati per .NET.   
<a name="filesSP">11</a> -i file in SharePoint locale non sono supportati.

Per la connessione a origini dati locali da un server Azure Analysis Services è necessario un [Gateway locale](analysis-services-gateway.md). Quando si usa un gateway, sono necessari i provider a 64 bit. 

## <a name="understanding-providers"></a>Informazioni sui provider

Quando si creano progetti di modelli tabulari 1400 e versioni successive in Visual Studio, per impostazione predefinita non viene specificato un provider di dati quando ci si connette a un'origine dati usando **Get Data**. I modelli tabulari 1400 e versioni successive usano connettori [Power query](/power-query/power-query-what-is-power-query.md) per gestire connessioni, query di dati e mashup tra l'origine dati e la Analysis Services. Queste sono talvolta denominate connessioni alle origini dati *strutturate* in quanto le impostazioni delle proprietà di connessione sono impostate per l'utente. È tuttavia possibile abilitare origini dati legacy. Se abilitata, è possibile utilizzare l' **importazione guidata tabella** per connettersi a determinate origini dati tradizionalmente supportate nei modelli tabulari 1200 e inferiori come origini dati *legacy*o *provider* . Quando viene specificato come origine dati del provider, è possibile specificare un particolare provider di dati e altre proprietà di connessione avanzate. Ad esempio, è possibile connettersi a un SQL Server locale data warehouse o anche a un database SQL di Azure come origine dati legacy. È quindi possibile selezionare il driver OLE DB per SQL Server provider di dati MSOLEDBSQL. In questo caso, la selezione di un provider di dati OLE DB può fornire prestazioni migliori rispetto al connettore Power Query. 

Quando si utilizza l'importazione guidata tabella in Visual Studio, le connessioni a qualsiasi origine dati richiedono un provider di dati. Viene selezionato un provider di dati predefinito. Se necessario, è possibile modificare il provider di dati. Il tipo di provider scelto può dipendere dalle prestazioni, indipendentemente dal fatto che il modello usi l'archiviazione in memoria o DirectQuery e la piattaforma Analysis Services in cui si distribuisce il modello.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Specificare le origini dati del provider nei progetti di modelli tabulari 1400 e versioni successive

Per abilitare le origini dati del provider, in Visual Studio fare clic su **strumenti** > **Opzioni** > Analysis Services **importazione di dati** > **tabulari** , selezionare **Abilita origini dati legacy**.

![Abilita origini dati legacy](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Con le origini dati legacy abilitate, in **Esplora modelli tabulari**fare clic con il pulsante destro del mouse su **origini dati** > **Importa da origine dati (legacy)** .

![Origini dati legacy in Esplora modelli tabulari](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Analogamente a quanto avviene con i progetti di modello tabulare 1200, utilizzare l' **importazione guidata tabella** per connettersi a un'origine dati. Nella pagina Connetti fare clic su **Avanzate**. Specificare il provider di dati e altre impostazioni di connessione in **Imposta proprietà avanzate**.

![Proprietà avanzate origini dati legacy](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Rappresentazione
In alcuni casi può essere necessario specificare un account di rappresentazione differente. L'account di rappresentazione può essere specificato in Visual Studio o SSMS.

Per le origini dati locali:

* Se si usa l'autenticazione SQL, la rappresentazione deve essere l'account del servizio.
* Se si usa l'autenticazione di Windows, impostare nome utente/password di Windows. Per SQL Server, l'autenticazione di Windows con un account di rappresentazione specifico è supportata solo per i modelli di dati In-memory.

Per le origini dati cloud:

* Se si usa l'autenticazione SQL, la rappresentazione deve essere l'account del servizio.

## <a name="oauth-credentials"></a>Credenziali OAuth

Per i modelli tabulari con livello di compatibilità 1400 e superiore, il database SQL di Azure, Azure SQL Data Warehouse, Dynamics 365 e l'elenco SharePoint supportano le credenziali OAuth. Azure Analysis Services gestisce l'aggiornamento del token per le origini dati OAuth per evitare timeout per le operazioni di aggiornamento con esecuzione prolungata. Per generare token validi, impostare le credenziali tramite SSMS.

## <a name="next-steps"></a>Passaggi successivi
[Gateway locale](analysis-services-gateway.md)   
[Gestire il server](analysis-services-manage.md)   

