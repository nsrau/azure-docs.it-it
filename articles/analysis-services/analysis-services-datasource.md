---
title: Origini dati supportate in Azure Analysis Services | Microsoft Docs
description: Descrive le origini dati supportate per i modelli di dati di Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 79346f0bf80386fb83f55daccda8790652ff8541
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298628"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Origini dati supportate in Azure Analysis Services

Le origini dati e i connettori visualizzati in Get Data (Recupera dati) o Import Wizard (Importazione guidata) in Visual Studio vengono visualizzati sia per Azure Analysis Services che per SQL Server Analysis Services. Tuttavia, non tutte le origini dati e i connettori visualizzati sono supportati in Azure Analysis Services. I tipi di origini dati a cui è possibile connettersi dipendono da molti fattori, ad esempio livello di compatibilità del modello, connettori di dati disponibili, tipo di autenticazione, provider e supporto di gateway dati locale. 

## <a name="azure-data-sources"></a>Origini dati di Azure

|Origine dati  |In memoria  |DirectQuery  |
|---------|---------|---------|
|Database SQL di Azure<sup>[2](#azsqlmanaged)</sup>     |   Yes      |    Yes      |
|Azure SQL Data Warehouse     |   Yes      |   Yes       |
|Archiviazione BLOB di Azure<sup>[1](#tab1400a)</sup>     |   Yes       |    No      |
|Archiviazione tabelle di Azure<sup>[1](#tab1400a)</sup>    |   Yes       |    No      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Yes        |  No        |
|Azure Data Lake Store (Gen 1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Yes       |    No      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Yes     |   No       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Yes       |   No       |
||||

<a name="tab1400a">1</a> - Solo modelli tabulari 1400 e superiori.   
<a name="azsqlmanaged">2</a> - Istanza gestita di database SQL di Azure è supportata. Poiché l'istanza gestita viene eseguita in Azure VNet con un indirizzo IP privato, è necessario abilitare l'endpoint pubblico nell'istanza. Se questa funzionalità non è abilitata, è necessario un gateway dati locale.    
<a name="databricks">3</a> - Azure Databricks con il connettore Spark non è attualmente supportato.   
<a name="gen2">4</a> - ADLS Gen2 non è attualmente supportato.


**Provider**   
I modelli in memoria e DirectQuery che si connettono alle origini dati di Azure usano il provider di dati .NET Framework per SQL Server.

## <a name="other-data-sources"></a>Altre origini dati

La connessione alle origini dati locali da un server Azure Analysis Services richiede un gateway locale. Quando si usa un gateway, sono necessari i provider a 64 bit.

### <a name="in-memory-and-directquery"></a>In memoria e DirectQuery

|Origine dati | Provider in memoria | Provider DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, provider Microsoft OLE DB per SQL Server, provider di dati .NET Framework per SQL Server | Provider di dati .NET Framework per SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11.0, provider Microsoft OLE DB per SQL Server, provider di dati .NET Framework per SQL Server | Provider di dati .NET Framework per SQL Server |
| Oracle | Provider di OLE DB per Oracle, Oracle provider di dati per .NET |Provider di dati Oracle per .NET |
| Teradata |Provider OLE DB per Teradata, provider di dati Teradata per .NET |Provider di dati Teradata per .NET |
| | | |

### <a name="in-memory-only"></a>Solo in memoria

|Origine dati  |  
|---------|
|Database di Access     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Piattaforma di strumenti analitici     |  
|File CSV  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Cartella di lavoro di Excel     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Cartella<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (Beta) |
|Documento JSON<sup>[1](#tab1400b)</sup>     |  
|Righe da file binario<sup>[1](#tab1400b)</sup>     | 
|MySQL Database     | 
|Feed OData<sup>[1](#tab1400b)</sup>     |  
|Query ODBC     | 
|OLE DB     |   
|Database PostgreSQL<sup>[1](#tab1400b)</sup>    | 
|Oggetti Salesforce<sup>[1](#tab1400b)</sup> |  
|Report Salesforce<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|Elenco SharePoint<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Database di Sybase     |  
|File TXT  |
|Tabella XML<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> - Solo modelli tabulari 1400 e superiori.   
<a name="filesSP">2</a> -i file in SharePoint locale non sono supportati.

## <a name="specifying-a-different-provider"></a>Specificare un provider diverso

Durante la connessione a particolari origini dati, i modelli di dati di Azure Analysis Services possono richiedere provider di dati differenti. In alcuni casi, i modelli tabulari che si connettono alle origini dati usando provider nativi quali SQL Server Native Client (SQLNCLI11) possono restituire un errore. Se si usano provider nativi diversi da SQLOLEDB, è possibile che venga visualizzato un messaggio di errore simile al seguente: **Il provider 'SQLNCLI11.1' non è registrato**. Se invece si dispone di un modello DirectQuery che si connette a origini dati locali e si usano provider nativi, è possibile che venga visualizzato un messaggio di errore simile al seguente: **Errore durante la creazione del set di righe OLE DB. Sintassi errata vicino a 'LIMIT'** .

Durante la migrazione di un modello tabulare SQL Server Analysis Services locale in Azure Analysis Services, può essere necessario modificare il provider.

**Per specificare un provider**

1. In SSDT > **Esplora modelli tabulari** > **Origini dati** fare clic con il pulsante destro del mouse su una connessione a un'origine dati e scegliere **Modifica origine dati**.
2. In **Modifica connessione** fare clic su **Avanzate** per aprire la finestra Proprietà avanzate.
3. Selezionare **Impostazione delle proprietà avanzate** > **Provider** e quindi scegliere il provider appropriato.

## <a name="impersonation"></a>Rappresentazione
In alcuni casi può essere necessario specificare un account di rappresentazione differente. L'account di rappresentazione può essere specificato in Visual Studio (SSDT) o SSMS.

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

