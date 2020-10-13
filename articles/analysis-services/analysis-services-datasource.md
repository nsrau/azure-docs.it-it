---
title: Origini dati supportate in Azure Analysis Services | Microsoft Docs
description: Vengono descritti le origini dati e i connettori supportati per i modelli di dati tabulari 1200 e superiori in Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b99ac957c9c5030b484b244223847be4aa53a39d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88749097"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Origini dati supportate in Azure Analysis Services

Le origini dati e i connettori visualizzati per l'opzione Recupera dati o l'Importazione guidata tabella in Visual Studio con i progetti di Analysis Services sono visualizzati sia per Azure Analysis Services che per SQL Server Analysis Services. Tuttavia, non tutte le origini dati e tutti i connettori visualizzati sono supportati in Azure Analysis Services. I tipi di origini dati a cui è possibile connettersi dipendono da molti fattori, ad esempio il livello di compatibilità del modello, i connettori dati disponibili, il tipo di autenticazione e il supporto del gateway dati locale. Le tabelle seguenti descrivono le origini dati supportate per Azure Analysis Services.

## <a name="azure-data-sources"></a>Origini dati di Azure

|Origine dati  |In memoria  |DirectQuery  |Note |
|---------|---------|---------|---------|
|database SQL di Azure      |   Sì      |    Sì      |<sup>[2](#azprovider)</sup>, <sup>[3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   Sì      |   Sì       |<sup>[2](#azprovider)</sup>|
|Archiviazione BLOB di Azure      |   Sì       |    No      | <sup>[1](#tab1400a)</sup> |
|Archiviazione tabelle di Azure     |   Sì       |    No      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Sì        |  No        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Sì       |    No      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Sì       |    No      |<sup>[1](#tab1400a)</sup>, <sup>[5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Sì     |   No       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Sì       |   No       |<sup>[1](#tab1400a)</sup>, <sup>[4](#databricks)</sup>|
||||

**Note:**

<a name="tab1400a">1</a> - Solo modelli tabulari 1400 e superiori.  
<a name="azprovider">2</a> - Quando l'origine dati viene specificata come origine dati del *provider* nei modelli tabulari 1200 e superiori, sia i modelli in memoria che DirectQuery richiedono Microsoft OLE DB Driver per SQL Server MSOLEDBSQL (consigliato), SQL Server Native Client 11.0 o il provider di dati .NET Framework per SQL Server.  
<a name="azsqlmanaged">3</a> -istanza gestita SQL di Azure è supportata. Poiché SQL Istanza gestita viene eseguito all'interno di Azure VNet con un indirizzo IP privato, è necessario abilitare l'endpoint pubblico nell'istanza. Se non è abilitato, è necessario un [gateway dati locale](analysis-services-gateway.md).  
<a name="databricks">4</a> - Azure Databricks con il connettore Spark non è attualmente supportato.  
<a name="gen2">5</a> - Il connettore ADLS Gen2 non è attualmente supportato, tuttavia è possibile usare il connettore di Archiviazione BLOB di Azure con un'origine dati ADLS Gen2.

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
|Righe di file binario      | Sì | No | <sup>[6](#tab1400b)</sup> |
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
|Elenco SharePoint      |   Sì | No | <sup>[6](#tab1400b)</sup>, <sup>[11](#filesSP)</sup> |
|SQL Server |Sì   | Sì  | <sup>[7](#sqlim)</sup>, <sup>[8](#instgw)</sup> |
|SQL Server Data Warehouse |Sì   | Sì  | <sup>[7](#sqlim)</sup>, <sup>[8](#instgw)</sup> |
|Database di Sybase     |  Sì | No |  |
|Teradata | Sì  | Sì  | <sup>[10](#teradata)</sup> |
|File TXT  |Sì | No |  |
|Tabella XML    |  Sì | No | <sup>[6](#tab1400b)</sup> |
| | | |

**Note:**  
<a name="tab1400b">6</a> - Solo modelli tabulari 1400 e superiori.  
<a name="sqlim">7</a> - Quando l'origine dati viene specificata come origine dati del *provider* nei modelli tabulari 1200 e superiori, specificare Microsoft OLE DB Driver per SQL Server MSOLEDBSQL (consigliato), SQL Server Native Client 11.0 o il provider di dati .NET Framework per SQL Server.  
<a name="instgw">8</a> - Se si specifica MSOLEDBSQL come provider di dati, può essere necessario scaricare e installare [Microsoft OLE DB Driver per SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) nello stesso computer del gateway dati locale.  
<a name="oracle">9</a> - Per i modelli tabulari 1200 o come origine dati del *provider* nei modelli tabulari 1400 o superiori, specificare Oracle Data Provider for .NET. Se specificato come origine dati strutturata, assicurarsi di [abilitare il provider gestito Oracle](#enable-oracle-managed-provider).   
<a name="teradata">10</a> - Per i modelli tabulari 1200 o come origine dati del *provider* nei modelli tabulari 1400 o superiori, specificare Teradata Data Provider for .NET.  
<a name="filesSP">11</a> - I file nelle istanze locali di SharePoint non sono supportati.

La connessione alle origini dati locali da un server Azure Analysis Services richiede un [gateway locale](analysis-services-gateway.md). Quando si usa un gateway, sono necessari i provider a 64 bit.

## <a name="understanding-providers"></a>Informazioni sui provider

Quando si creano progetti di modelli tabulari 1400 e superiori in Visual Studio, per impostazione predefinita non viene specificato un provider di dati quando ci si connette a un'origine dati usando l'opzione **Recupera dati**. I modelli tabulari 1400 e superiori usano i connettori [Power Query](/power-query/power-query-what-is-power-query) per gestire connessioni, query di dati e mashup tra l'origine dati e Analysis Services. In questo caso talvolta si parla di connessioni alle origini dati *strutturate* in cui le impostazioni delle proprietà di connessione sono definite automaticamente. È tuttavia possibile abilitare origini dati legacy per un progetto di modello in Visual Studio. Se l'opzione è abilitata, è possibile usare l'**Importazione guidata tabella** per connettersi a determinate origini dati tradizionalmente supportate nei modelli tabulari 1200 e inferiori come le origini dati *legacy* o del *provider*. Quando viene specificata come origine dati del provider, è possibile indicare un provider di dati specifico e altre proprietà di connessione avanzate. Ad esempio, è possibile connettersi a un'istanza di data warehouse SQL Server o anche a un database SQL di Azure come origine dati legacy. È quindi possibile selezionare il provider di dati OLE DB Driver per SQL Server MSOLEDBSQL. In questo caso, la selezione di un provider di dati OLE DB può offrire prestazioni migliori rispetto al connettore Power Query. 

Quando si usa l'Importazione guidata tabella in Visual Studio, le connessioni a qualsiasi origine dati richiedono un provider di dati. Viene selezionato automaticamente un provider di dati predefinito. Se necessario, è possibile modificare tale provider. Il tipo di provider scelto può dipendere dalle prestazioni, dal fatto che il modello usi o meno l'archiviazione in memoria o DirectQuery e dalla piattaforma Analysis Services in cui si distribuisce il modello.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Specificare le origini dati del provider nei progetti di modelli tabulari 1400 e superiori

Per abilitare le origini dati del provider, in Visual Studio fare clic su **Strumenti** > **Opzioni** > **Analysis Services Tabular** > **Importazione dati** e selezionare **Abilita origini dati legacy**.

![Abilita origini dati legacy](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Con le origini dati legacy abilitate, in **Esplora modelli tabulari** fare clic con il pulsante destro del mouse su **Origini dati** > **Importa da origine dati (legacy)** .

![Origini dati legacy in Esplora modelli tabulari](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Analogamente a quanto avviene con i progetti di modelli tabulari 1200, usare l'**Importazione guidata tabella** per connettersi a un'origine dati. Nella pagina di connessione fare clic su **Avanzate**. Specificare il provider di dati e altre impostazioni di connessione in **Impostazione delle proprietà avanzate**.

![Proprietà avanzate delle origini dati legacy](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Rappresentazione
In alcuni casi può essere necessario specificare un account di rappresentazione differente. L'account di rappresentazione può essere specificato in Visual Studio o SQL Server Management Studio (SSMS).

Per le origini dati locali:

* Se si usa l'autenticazione SQL, la rappresentazione deve essere l'account del servizio.
* Se si usa l'autenticazione di Windows, impostare nome utente/password di Windows. Per SQL Server, l'autenticazione di Windows con un account di rappresentazione specifico è supportata solo per i modelli di dati In-memory.

Per le origini dati cloud:

* Se si usa l'autenticazione SQL, la rappresentazione deve essere l'account del servizio.

## <a name="oauth-credentials"></a>Credenziali OAuth

Per i modelli tabulari con livello di compatibilità 1400 e superiore con la modalità in memoria, il database SQL di Azure, la sinapsi di Azure (in precedenza SQL Data Warehouse), Dynamics 365 e l'elenco di SharePoint supportano le credenziali OAuth. Azure Analysis Services gestisce l'aggiornamento del token per le origini dati OAuth per evitare timeout per le operazioni di aggiornamento con esecuzione prolungata. Per generare token validi, impostare le credenziali utilizzando Power Query.

La modalità DirectQuery non è supportata con le credenziali OAuth.

## <a name="enable-oracle-managed-provider"></a>Abilita provider gestito Oracle

In alcuni casi, le query DAX a un'origine dati Oracle possono restituire risultati imprevisti. Questo problema può essere dovuto al provider utilizzato per la connessione all'origine dati.

Come descritto nella sezione [Understanding Providers](#understanding-providers) , i modelli tabulari si connettono alle origini dati come un'origine dati *strutturata* o un'origine dati del *provider* . Per i modelli con un'origine dati Oracle specificata come origine dati del provider, verificare che il provider specificato sia Oracle provider di dati per .NET (Oracle. DataAccess. Client). 

Se l'origine dati Oracle viene specificata come origine dati strutturata, abilitare la proprietà server **MDataEngine\UseManagedOracleProvider** . L'impostazione di questa proprietà garantisce che il modello venga connesso all'origine dati Oracle utilizzando il provider di dati Oracle consigliato per il provider gestito .NET.
 
Per abilitare il provider gestito Oracle:

1. In SQL Server Management Studio connettersi al server.
2. Creare una query XMLA con lo script seguente. Sostituire **ServerName** con il nome completo del server, quindi eseguire la query.

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. Riavviare il server.


## <a name="next-steps"></a>Passaggi successivi

* [Gateway locale](analysis-services-gateway.md)
* [Gestire il server](analysis-services-manage.md)
