---
title: Origini dati supportate in Azure Analysis Services | Microsoft Docs
description: Descrive le origini dati supportate per i modelli di dati di Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2018
ms.author: owend
ms.openlocfilehash: e2f7e356b260c0e5af67d28811bd88a63a601312
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Origini dati supportate in Azure Analysis Services

Le origini dati e i connettori visualizzati in Get Data (Recupera dati) o Import Wizard (Importazione guidata) in Visual Studio vengono visualizzati sia per Azure Analysis Services che per SQL Server Analysis Services. Tuttavia, non tutte le origini dati e i connettori visualizzati sono supportati in Azure Analysis Services. I tipi di origini dati a cui è possibile connettersi dipendono da molti fattori, ad esempio livello di compatibilità del modello, connettori di dati disponibili, tipo di autenticazione, provider e supporto di gateway dati locale. 

## <a name="azure-data-sources"></a>Origini dati di Azure

|Origine dati  |In memoria  |DirectQuery  |
|---------|---------|---------|
|database SQL di Azure     |   Sì      |    Sì      |
|Azure SQL Data Warehouse     |   Sì      |   Sì       |
|Archiviazione BLOB di Azure*     |   Sì       |    No       |
|Archiviazione tabelle di Azure*    |   Sì       |    No       |
|Azure Cosmos DB (Beta)*     |  Sì        |  No         |
|Azure Data Lake Store*     |   Sì       |    No       |
|Azure HDInsight (HDFS)*     |     Sì     |   No        |
|Azure HDInsight Spark (Beta)*     |   Sì       |   No        |
|Database di Azure per MySQL (anteprima)*     |   Sì       |   No       |
|Database di Azure per PostgreSQL (anteprima)*     | Sì         |  No        |
||||

\* Solo modelli tabulari 1400.

**Provider**   
I modelli in memoria e DirectQuery che si connettono alle origini dati di Azure usano il provider di dati .NET Framework per SQL Server.

## <a name="on-premises-data-sources"></a>Origini dati locali

La connessione alle origini dati locali richiede un gateway locale. Quando si usa un gateway, verificare che siano installati provider a 64 bit.

### <a name="in-memory-and-directquery"></a>In memoria e DirectQuery

|Origine dati | Provider in memoria | Provider DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, provider Microsoft OLE DB per SQL Server, provider di dati .NET Framework per SQL Server | Provider di dati .NET Framework per SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11.0, provider Microsoft OLE DB per SQL Server, provider di dati .NET Framework per SQL Server | Provider di dati .NET Framework per SQL Server |
| Oracle |Provider Microsoft OLE DB per Oracle, provider di dati Oracle per .NET |Provider di dati Oracle per .NET | |
| Teradata |Provider OLE DB per Teradata, provider di dati Teradata per .NET |Provider di dati Teradata per .NET | |
| | | |

\* Solo modelli tabulari 1400.

### <a name="in-memory-only"></a>Solo in memoria

> [!IMPORTANT]
> La verifica dei provider per le origini dati seguenti è in corso. 

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
|Documento JSON*     |  
|Righe da file binario*     | 
|MySQL Database     | 
|Feed OData*     | 
|Query ODBC     | 
|OLE DB     |  
|Database PostgreSQL*    | 
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

