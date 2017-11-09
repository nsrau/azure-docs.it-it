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
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 5ba7ef5aa2cccad7cda3cb39459a5a5722516524
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Origini dati supportate in Azure Analysis Services
I server Azure Analysis Services supportano la connessione alle origini dati nel cloud e locali nell'organizzazione. Altre origine dati supportate vengono continuamente aggiunte. Ricontrollare spesso. 

Sono attualmente supportate le origini dati seguenti:

| Cloud  |
|---|
| Archiviazione BLOB di Azure*  |
| Database SQL di Azure  |
| Azure Data Warehouse |


| Locale  |   |   |   |
|---|---|---|---|
| Database di Access  | Cartella* | Oracle Database  | Database Teradata |
| Active Directory*  | Documento JSON*  | Database PostgreSQL*  |Tabella XML* |
| Analysis Services  | Righe da file binario*  | SAP HANA*  |
| Piattaforma di strumenti analitici  | MySQL Database  | SAP Business Warehouse*  | |
| Dynamics CRM*  | Feed OData*  | SharePoint*  |
| Cartella di lavoro di Excel  | Query ODBC  | Database SQL  |
| Exchange*  | OLE DB  | Database di Sybase  |

\* Solo modelli tabulari 1400. 

> [!IMPORTANT]
> Per connettersi alle origini dati locali, in un computer dell'ambiente deve essere installato un [gateway dati locale](analysis-services-gateway.md).

## <a name="data-providers"></a>Provider di dati

Durante la connessione a particolari origini dati, i modelli di dati di Azure Analysis Services possono richiedere provider di dati differenti. In alcuni casi, i modelli tabulari che si connettono alle origini dati usando provider nativi quali SQL Server Native Client (SQLNCLI11) possono restituire un errore.

Per modelli di dati che si connettono a un'origine dati cloud, ad esempio il database SQL di Azure, se si usano provider nativi diversi da SQLOLEDB, è possibile che venga visualizzato un messaggio di errore simile al seguente: **"Il provider 'SQLNCLI11.1' non è registrato"**. Se invece si dispone di un modello DirectQuery che si connette a origini dati locali, se si usano provider nativi è possibile che venga visualizzato un messaggio di errore simile al seguente: **"Errore durante la creazione del set di righe OLE DB. Sintassi errata vicino a 'LIMIT'"**.

I provider di origine dati seguenti sono supportati per i modelli di dati DirectQuery o in memoria nella connessione a origini dati nel cloud o in locale:

### <a name="cloud"></a>Cloud
| **Origine dati** | **In-memory** | **DirectQuery** |
|  --- | --- | --- |
| Azure SQL Data Warehouse |Provider di dati .NET Framework per SQL Server |Provider di dati .NET Framework per SQL Server |
| Database SQL di Azure |Provider di dati .NET Framework per SQL Server |Provider di dati .NET Framework per SQL Server | |

### <a name="on-premises-via-gateway"></a>Locale (tramite gateway)
|**Origine dati** | **In-memory** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |Provider di dati .NET Framework per SQL Server |
| SQL Server |Provider Microsoft OLE DB per SQL Server |Provider di dati .NET Framework per SQL Server | |
| SQL Server |Provider di dati .NET Framework per SQL Server |Provider di dati .NET Framework per SQL Server | |
| Oracle |Provider Microsoft OLE DB per Oracle |Provider di dati Oracle per .NET | |
| Oracle |Provider di dati Oracle per .NET |Provider di dati Oracle per .NET | |
| Teradata |Provider OLE DB per Teradata |Provider di dati Teradata per .NET | |
| Teradata |Provider di dati Teradata per .NET |Provider di dati Teradata per .NET | |
| Piattaforma di strumenti analitici |Provider di dati .NET Framework per SQL Server |Provider di dati .NET Framework per SQL Server | |

> [!NOTE]
> Quando si usa il gateway locale, verificare che siano installati provider a 64 bit.
> 
> 

Durante la migrazione di un modello tabulare SQL Server Analysis Services locale in Azure Analysis Services, può essere necessario modificare il provider.

**Per specificare un provider dell'origine dati**

1. In SSDT > **Esplora modelli tabulari** > **Origini dati** fare clic con il pulsante destro del mouse su una connessione a un'origine dati e scegliere **Modifica origine dati**.
2. In **Modifica connessione** fare clic su **Avanzate** per aprire la finestra Proprietà avanzate.
3. Selezionare **Impostazione delle proprietà avanzate** > **Provider** e quindi scegliere il provider appropriato.

## <a name="impersonation"></a>Rappresentazione
In alcuni casi può essere necessario specificare un account di rappresentazione differente. L'account di rappresentazione può essere specificato in SSDT o SSMS.

Per le origini dati locali:

* Se si usa l'autenticazione SQL, la rappresentazione deve essere l'account del servizio.
* Se si usa l'autenticazione di Windows, impostare nome utente/password di Windows. Per SQL Server, l'autenticazione di Windows con un account di rappresentazione specifico è supportata solo per i modelli di dati In-memory.

Per le origini dati cloud:

* Se si usa l'autenticazione SQL, la rappresentazione deve essere l'account del servizio.

## <a name="next-steps"></a>Passaggi successivi
Se si dispone di origini dati locali, verificare di installare il [gateway locale](analysis-services-gateway.md).   
Per altre informazioni sulla gestione del server in SSDT o SSMS, vedere [Manage your server](analysis-services-manage.md) (Gestione del server).

