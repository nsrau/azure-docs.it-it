---
title: Connessioni alle origini dati | Microsoft Docs
description: Vengono descritte le connessioni alle origini dati per i modelli di dati di Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/28/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 1b439e2b48cc009e727a49c271318cfd4ead9ef2


---
# <a name="datasource-connections"></a>Connessioni alle origini dati
Durante la connessione a particolari origini dati, i modelli di dati di Azure Analysis Services possono richiedere provider di dati differenti. In alcuni casi, i modelli tabulari che si connettono alle origini dati usando provider nativi quali SQL Server Native Client (SQLNCLI11) possono restituire un errore.

Si supponga, ad esempio, di avere un modello di dati In-memory o DirectQuery che si connette a un'origine dati cloud come un database SQL di Azure. Se si usano provider nativi diversi da SQLOLEDB, è possibile che venga visualizzato un messaggio di errore simile al seguente: **"Il provider 'SQLNCLI11.1' non è registrato"**.

Se invece si dispone di un modello DirectQuery che si connette a origini dati locali, se si usano provider nativi è possibile che venga visualizzato un messaggio di errore simile al seguente: **"Errore durante la creazione del set di righe OLE DB. Sintassi errata vicino a 'LIMIT'"**.

## <a name="data-source-providers"></a>Provider delle origini dati
Durante la connessione a origini dati locali o cloud, per modelli di dati In-memory o DirectQuery sono supportati i provider di origini dati seguenti:

|  | **Origine dati** | **In-memory** | **DirectQuery** |
| --- | --- | --- | --- |
| **Cloud** |Azure SQL Data Warehouse |Provider di dati .NET Framework per SQL Server |Provider di dati .NET Framework per SQL Server |
| Database SQL di Azure |Provider di dati .NET Framework per SQL Server |Provider di dati .NET Framework per SQL Server | |
| **Locale** (tramite gateway) |SQL Server |SQL Server Native Client 11.0 |Provider di dati .NET Framework per SQL Server |
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
Se si dispone di origini dati locali, verificare di installare il [gateway locale](analysis-services-gateway.md). Per altre informazioni sulla gestione del server in SSDT o SSMS, vedere [Manage your server](analysis-services-manage.md) (Gestione del server).




<!--HONumber=Nov16_HO3-->


