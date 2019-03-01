---
title: Note sulla versione 5 di aggiornare il servizio App in Azure Stack | Microsoft Docs
description: Informazioni sulle novità nell'aggiornamento 5 per il servizio App in Azure Stack, i problemi noti e dove scaricare l'aggiornamento.
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
ms.date: 02/27/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 0a0eb9586e78442947138831dd774298906aaf9c
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993320"
---
# <a name="app-service-on-azure-stack-update-5-release-notes"></a>Servizio App in note sulla versione di Azure Stack update 5

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Queste note sulla versione descrivono i miglioramenti e correzioni in servizio App di Azure in Azure Stack Update 5 e problemi noti. Problemi noti sono suddivisi in problemi correlati direttamente per la distribuzione, il processo di aggiornamento e i problemi con la build (post-installazione).

> [!IMPORTANT]
> Applicare l'aggiornamento 1901 al sistema integrato Azure Stack o distribuire il kit di sviluppo di Azure Stack più recente prima di distribuire 1.5 servizio App di Azure.


## <a name="build-reference"></a>Riferimento alla compilazione

Il servizio App sul numero di build di Azure Stack Update 5 è **80.0.2.15**

### <a name="prerequisites"></a>Prerequisiti

Vedere le [documentazione prima di iniziare a](azure-stack-app-service-before-you-get-started.md) prima di iniziare la distribuzione.

Prima di iniziare l'aggiornamento del servizio App di Azure in Azure Stack a 1.5:

- Verificare che tutti i ruoli sono pronti nell'amministrazione del servizio App di Azure nel portale di amministrazione di Azure Stack

- Eseguire il backup nel servizio App e i database Master:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Eseguire il backup della condivisione file di contenuto di App del Tenant

- Pubblicare i **estensione Script personalizzata** versione **1.9.1** dal Marketplace

### <a name="new-features-and-fixes"></a>Nuove funzionalità e correzioni

Servizio App di Azure in Azure Stack aggiornamento 5 include le correzioni e i miglioramenti seguenti:

- Gli aggiornamenti **Tenant del servizio App, amministratore, i portali di funzioni e gli strumenti di Kudu**. Coerente con la versione di SDK portale di Azure Stack.

- Gli aggiornamenti **strumenti di Kudu** risolvere i problemi con lo stile e le funzionalità per i clienti che operano **disconnesso** Azure Stack. 

- Aggiornamenti al servizio di base per migliorare l'affidabilità e messaggistica abilitazione più semplice diagnosi dei problemi comuni degli errori.

- **Gli aggiornamenti per gli strumenti e Framework di applicazione seguenti**:
  - NodeJS aggiunto 10.14.1
  - Aggiunta di NPM 6.4.1
  - Kudu aggiornata a 79.20129.3767
  
- **Aggiornamenti del sistema operativo sottostante di tutti i ruoli**:
  - [Aggiornamento cumulativo per Windows Server 2016-02 2019 per sistemi basati su x64 (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>Passaggi di post-distribuzione

> [!IMPORTANT]  
> Se è stato specificato l'applicazione relying Party di servizio App con un'istanza SQL di sempre su devi [aggiungere i database appservice_hosting e appservice_metering a un gruppo di disponibilità](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) e sincronizzare i database per evitare eventuali perdite di servizio nel evento di un failover del database.

### <a name="post-update-steps"></a>Passaggi di post-aggiornamento

Per i clienti che desiderano eseguire la migrazione al database indipendente per il servizio App di Azure esistente nelle distribuzioni di Azure Stack, eseguire questi passaggi dopo che il servizio App di Azure in Azure Stack 1.5 update ha completato:

> [!IMPORTANT]
> La procedura di migrazione richiede circa 5-10 minuti.  La procedura comporta la terminazione di sessioni di accesso del database esistente.  Pianificare i tempi di inattività eseguire la migrazione e la convalida di servizio App di Azure sulla migrazione di Azure Stack post.  Se è stato completato questi passaggi dopo l'aggiornamento nel servizio App di Azure in Azure Stack 1.3 questi passaggi non sono necessari.
>
>

1. Aggiungere [database AppService (appservice_hosting e appservice_metering) a un gruppo di disponibilità](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Attivare contenuti il database
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Conversione di un Database a parzialmente indipendente, la conversione comporta tempi di inattività come devono essere terminate tutte le sessioni attive

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

### <a name="known-issues-post-installation"></a>Problemi noti (post-installazione)

- I ruoli di lavoro sono in grado di raggiungere il server di file quando il servizio App viene distribuito in una rete virtuale esistente e il file server disponibile solo nella rete privata, come illustrato nel servizio App di Azure nella documentazione relativa alla distribuzione di Azure Stack.

Se si sceglie di distribuire in una rete virtuale esistente e un indirizzo IP interno per la connessione al file server, è necessario aggiungere una regola di sicurezza in uscita, consentendo il traffico tra la subnet del ruolo di lavoro e il file server SMB. Passare a WorkersNsg nel portale di amministrazione e aggiungere una regola di sicurezza in uscita con le proprietà seguenti:
 * Origine: Qualsiasi
 * Intervallo di porte di origine: *
 * Destinazione: Indirizzi IP
 * Intervallo di indirizzi IP di destinazione: Intervallo di indirizzi IP per il file server
 * Intervallo di porte di destinazione: 445
 * Protocollo: TCP
 * Azione: CONSENTI
 * Priorità: 700
 * Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemi noti per gli amministratori Cloud funziona nel servizio App di Azure in Azure Stack

Vedere la documentazione nel [note sulla versione di Azure Stack 1809](azure-stack-update-1809.md)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del servizio App di Azure, vedere [servizio App di Azure nella panoramica di Azure Stack](azure-stack-app-service-overview.md).
- Per altre informazioni su come preparare la distribuzione del servizio App in Azure Stack, vedere [prima di iniziare con il servizio App in Azure Stack](azure-stack-app-service-before-you-get-started.md).
