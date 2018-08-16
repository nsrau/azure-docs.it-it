---
title: Note sulla versione 3 di aggiornare il servizio App in Azure Stack | Microsoft Docs
description: Informazioni sulle novità nell'aggiornamento 3 per il servizio App in Azure Stack, i problemi noti e dove scaricare l'aggiornamento.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 7cf41a10f13e2edeb4ab1944c0d38cc7064c02bb
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161489"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>Servizio App in note sulla versione update 3 di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Queste note sulla versione descrivono i miglioramenti e correzioni in servizio App di Azure in Azure Stack Update 3 e i problemi noti. Problemi noti sono suddivisi in problemi correlati direttamente per la distribuzione, il processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]
> Applicare l'aggiornamento 1807 al sistema integrato Azure Stack o distribuire il kit di sviluppo di Azure Stack più recente prima della distribuzione del servizio App di Azure 1.3.
>
>

## <a name="build-reference"></a>Riferimento alla compilazione

Il servizio App sul numero di build di Azure Stack Update 3 è **74.0.13698.31**

### <a name="prerequisites"></a>Prerequisiti

Vedere le [documentazione prima di iniziare a](azure-stack-app-service-before-you-get-started.md) prima di iniziare la distribuzione.

Prima di iniziare l'aggiornamento del servizio App di Azure in Azure Stack per 1.3, assicurarsi che tutti i ruoli sono pronti nell'amministrazione del servizio App di Azure nel portale di amministrazione di Azure Stack

![Stato ruolo del servizio App](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

Servizio App di Azure in Azure Stack Update 3 include le correzioni e i miglioramenti seguenti:

- Supporto per l'uso di SQL Server Always On per i database di Azure App Service Resource Provider.

- Aggiunto nuovo parametro di ambiente per lo script helper crea AADIdentityApp per assistere targeting diverse aree di AAD.

- Gli aggiornamenti **Tenant del servizio App, amministratore, i portali di funzioni e gli strumenti di Kudu**. Coerente con la versione di SDK portale di Azure Stack.

- Aggiornamenti al servizio di base per migliorare l'affidabilità e messaggistica abilitazione più semplice diagnosi dei problemi comuni degli errori.

- **Gli aggiornamenti per gli strumenti e Framework di applicazione seguenti**:
  - Aggiunta di ASP.Net Core 2.1.2
  - NodeJS aggiunto 10.0.0
  - Aggiunta di Zulu OpenJDK 8.30.0.1
  - Aggiunta di Tomcat 8.5.31 e 9.0.8
  - Versioni PHP aggiunto:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Aggiunta di Wincache 2.0.0.8
  - Git aggiornato per Windows a v 2.17.1.2
  - Kudu aggiornata a 74.10611.3437

### <a name="post-update-steps-optional"></a>Registra i passaggi di aggiornamento (facoltativo)

Per i clienti che desiderano eseguire la migrazione al database indipendente per il servizio App di Azure esistente nelle distribuzioni di Azure Stack, eseguire questi passaggi dopo che il servizio App di Azure nell'aggiornamento di Azure Stack 1.3 è stata completata:

> [!IMPORTANT]
> Questa procedura richiede circa 5-10 minuti.  Questa procedura richiede la terminazione di sessioni di accesso del database esistente.  Pianificare i tempi di inattività eseguire la migrazione e la convalida di servizio App di Azure sulla migrazione di Azure Stack post
>
>

1. Aggiungere [database a un gruppo di disponibilità](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Attivare contenuti il database
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Conversione di un Database a parzialmente indipendente.  Questo passaggio comporterà tempi di inattività come devono essere terminate tutte le sessioni attive

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

Convalida

1. Controllare se SQL Server ha indipendenza abilitata

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Controllare il comportamento indipendente esistente
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemi noti per gli amministratori Cloud funziona nel servizio App di Azure in Azure Stack

Vedere la documentazione nel [note sulla versione di Azure Stack 1807](azure-stack-update-1807.md)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del servizio App di Azure, vedere [servizio App di Azure nella panoramica di Azure Stack](azure-stack-app-service-overview.md).
- Per altre informazioni su come preparare la distribuzione del servizio App in Azure Stack, vedere [prima di iniziare con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md).
