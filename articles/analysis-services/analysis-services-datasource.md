---
title: Origini dati supportate in Azure Analysis Services | Microsoft Docs
description: Descrive le origini dati supportate per i modelli di dati di Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7d8bde6beeb4bd0c6234d7c5aa63be9e150953f5
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427247"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Origini dati supportate in Azure Analysis Services

Le origini dati e i connettori visualizzati in Get Data (Recupera dati) o Import Wizard (Importazione guidata) in Visual Studio vengono visualizzati sia per Azure Analysis Services che per SQL Server Analysis Services. Tuttavia, non tutte le origini dati e i connettori visualizzati sono supportati in Azure Analysis Services. I tipi di origini dati a cui è possibile connettersi dipendono da molti fattori, ad esempio livello di compatibilità del modello, connettori di dati disponibili, tipo di autenticazione, provider e supporto di gateway dati locale. 

## <a name="azure-data-sources"></a>Origini dati di Azure

|Origine dati  |In memoria  |DirectQuery  |
|---------|---------|---------|
|database SQL di Azure     |   Yes      |    Yes      |
|Azure SQL Data Warehouse     |   Yes      |   Yes       |
|Archiviazione BLOB di Azure*     |   Yes       |    No       |
|Archiviazione tabelle di Azure*    |   Yes       |    No       |
|Azure Cosmos DB*     |  Yes        |  No         |
|Azure Data Lake Store*     |   Yes       |    No       |
|Azure HDInsight (HDFS)*     |     Yes     |   No        |
|Azure HDInsight Spark*     |   Yes       |   No        |
||||

\* Solo modelli tabulari 1400.

**Provider**   
I modelli in memoria e DirectQuery che si connettono alle origini dati di Azure usano il provider di dati .NET Framework per SQL Server.

## <a name="on-premises-data-sources"></a>Origini dati locali

La connessione alle origini dati locali da un server Azure Analysis Services richiede un gateway locale. Quando si usa un gateway, sono necessari i provider a 64 bit.

### <a name="in-memory-and-directquery"></a>In memoria e DirectQuery

|Origine dati | Provider in memoria | Provider DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, provider Microsoft OLE DB per SQL Server, provider di dati .NET Framework per SQL Server | Provider di dati .NET Framework per SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11.0, provider Microsoft OLE DB per SQL Server, provider di dati .NET Framework per SQL Server | Provider di dati .NET Framework per SQL Server |
| Oracle |Provider Microsoft OLE DB per Oracle, provider di dati Oracle per .NET |Provider di dati Oracle per .NET | |
| Teradata |Provider OLE DB per Teradata, provider di dati Teradata per .NET |Provider di dati Teradata per .NET | |
| | | |

### <a name="in-memory-only"></a>Solo in memoria

|Origine dati  |  
|---------|---------|
|Database di Access     |  
|Active Directory*     |  
|Analysis Services     |  
|Piattaforma di strumenti analitici     |  
|Dynamics CRM*     |  
|Cartella di lavoro di Excel     |  
|Exchange*     |  
|Cartella*     |
|IBM Informix* (Beta) |
|Documento JSON*     |  
|Righe da file binario*     | 
|MySQL Database     | 
|Feed OData*     |  
|Query ODBC     | 
|OLE DB     |   
|Database PostgreSQL*    | 
|Oggetti Salesforce* |  
|Report Salesforce* |
|SAP HANA*    |  
|SAP Business Warehouse*    |  
|SharePoint*     |   
|Database di Sybase     |  
|Tabella XML*    |  
|||
 
\* Solo modelli tabulari 1400.

## <a name="specifying-a-different-provider"></a>Specificare un provider diverso

Durante la connessione a particolari origini dati, i modelli di dati di Azure Analysis Services possono richiedere provider di dati differenti. In alcuni casi, i modelli tabulari che si connettono alle origini dati usando provider nativi quali SQL Server Native Client (SQLNCLI11) possono restituire un errore. Se si usano provider nativi diversi da SQLOLEDB, è possibile che venga visualizzato un messaggio di errore simile al seguente: **Il provider 'SQLNCLI11.1' non è registrato**. Se invece si dispone di un modello DirectQuery che si connette a origini dati locali e si usano provider nativi è possibile che venga visualizzato un messaggio di errore simile al seguente: **Errore durante la creazione del set di righe OLE DB. Sintassi errata vicino a 'LIMIT'**.

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

## <a name="next-steps"></a>Passaggi successivi
[Gateway locale](analysis-services-gateway.md)   
[Gestire il server](analysis-services-manage.md)   

