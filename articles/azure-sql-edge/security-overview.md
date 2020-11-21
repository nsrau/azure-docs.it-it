---
title: Proteggere Azure SQL Edge
description: Informazioni sulla sicurezza in Azure SQL Edge
keywords: SQL Edge, sicurezza
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56d5eb981aa02d9da83973d49e8df79fcd9c7e9c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021015"
---
# <a name="securing-azure-sql-edge"></a>Protezione di Azure SQL Edge

Con l'aumento dell'adozione di Internet delle cose e del calcolo perimetrale in tutti i settori, aumenta il numero di dispositivi e i dati generati da questi dispositivi. L'aumento del volume di dati e il numero di endpoint del dispositivo costituiscono una sfida significativa in termini di sicurezza dei dati e dei dispositivi. 

Azure SQL Edge offre più funzionalità e funzionalità che semplificano la protezione dei dati delle cose all'interno dei database SQL Server. Azure SQL Edge è stato creato usando lo stesso motore SQL che consente di Microsoft SQL Server e Azure SQL, condividendo le stesse funzionalità di sicurezza, che semplificano l'estensione degli stessi criteri e procedure di sicurezza dal cloud al perimetro.

Proprio come Microsoft SQL Server e Azure SQL, la protezione delle distribuzioni di Azure SQL Edge può essere considerata come una serie di passaggi che interessano quattro aree: la piattaforma, l'autenticazione, gli oggetti (inclusi i dati) e le applicazioni che accedono al sistema. 

## <a name="platform-and-system-security"></a>Sicurezza della piattaforma e del sistema

La piattaforma per Azure SQL Edge include l'host Docker fisico, il sistema operativo nell'host e i sistemi di rete che collegano il dispositivo fisico ad applicazioni e client. 

L'implementazione della sicurezza della piattaforma inizia con la conservazione di utenti non autorizzati dalla rete. Di seguito sono riportate alcune delle procedure consigliate:
- Implementazione di regole firewall per garantire criteri di sicurezza dell'organizzazione. 
- Verificare che nel sistema operativo del dispositivo fisico siano stati applicati tutti gli aggiornamenti di sicurezza più recenti. 
- Specifica e limitazione delle porte host che usano per Azure SQL Edge
- Verificare che il controllo di accesso appropriato venga applicato a tutti i volumi di dati che ospitano i dati di Azure SQL Edge. 

Per altre informazioni sui protocolli di rete perimetrale di Azure SQL e sugli endpoint TDS, vedere [protocolli di rete ed endpoint TDS](/previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105)).

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione 

### <a name="authentication"></a>Authentication  
L'autenticazione è il processo atto a dimostrare che l'utente sia effettivamente chi dichiara di essere. Azure SQL Edge attualmente supporta solo il `SQL Authentication` meccanismo.

- *Autenticazione SQL*:

    L'autenticazione SQL si riferisce all'autenticazione di un utente durante la connessione ad Azure SQL Edge mediante nome utente e password. È necessario specificare la password di accesso **sa** di SQL durante la distribuzione di SQL Edge. In seguito, l'amministratore del server può creare altri utenti e accessi SQL, che consentono agli utenti di connettersi tramite nome utente e password.

    Per altre informazioni sulla creazione e la gestione di account di accesso e utenti in SQL Edge, vedere [creare un account di accesso](/sql/relational-databases/security/authentication-access/create-a-login) e [creare un utente del database](/sql/relational-databases/security/authentication-access/create-a-database-user).

### <a name="authorization"></a>Autorizzazione   

L'autorizzazione si riferisce alle autorizzazioni assegnate a un utente all'interno di un database in Azure SQL Edge e determina le operazioni che l'utente è autorizzato a eseguire. Le autorizzazioni vengono controllate aggiungendo account utente ai [ruoli del database](/sql/relational-databases/security/authentication-access/database-level-roles) e assegnando autorizzazioni a livello di database a tali ruoli o concedendo all'utente determinate [autorizzazioni a livello di oggetto](/sql/relational-databases/security/permissions-database-engine). Per ulteriori informazioni, vedere [account di accesso e utenti](../azure-sql/database/logins-create-manage.md).

Come procedura consigliata, creare ruoli personalizzati quando necessario. Aggiungere utenti al ruolo con i privilegi minimi necessari per eseguire la funzione del processo. Non assegnare autorizzazioni direttamente agli utenti. L'account amministratore del server è un membro del ruolo db_owner incorporato, che dispone di autorizzazioni estese e deve essere concesso solo a pochi utenti con compiti amministrativi. Per le applicazioni, usare [Execute As](/sql/t-sql/statements/execute-as-clause-transact-sql) per specificare il contesto di esecuzione del modulo chiamato o usare i [ruoli applicazione](/sql/relational-databases/security/authentication-access/application-roles) con autorizzazioni limitate. Questa procedura garantisce che l'applicazione che si connette al database disponga dei privilegi minimi necessari per l'applicazione. Seguendo queste procedure consigliate si favorisce anche la separazione dei compiti.

## <a name="database-object-security"></a>Sicurezza di oggetti di database

Le entità sono singoli, gruppi e processi a cui viene concesso l'accesso a SQL Edge. Le "entità a protezione diretta" sono il server, il database e gli oggetti contenuti nel database. Ogni dispone di un set di autorizzazioni che possono essere configurate per ridurre la superficie di attacco. Nella tabella seguente sono disponibili informazioni su entità ed entità a protezione diretta.

|Per informazioni su|Vedere|  
|---------------------------|---------|  
|Utenti del server e del database, ruoli e processi|[Entità motore di database](/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|Sicurezza del server e degli oggetti di database|[Entità a protezione diretta](/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>Crittografia e certificati  
 
La crittografia non risolve i problemi relativi al controllo di accesso, ma migliora la sicurezza limitando la perdita di dati anche nel raro caso in cui il controllo degli accessi venga eluso. Se, ad esempio, il computer host del database è configurato scorrettamente e un utente malintenzionato ottiene dati sensibili, ad esempio il numero di una carta di credito, le informazioni sottratte potrebbero essere inutilizzabili se crittografate. La tabella seguente contiene altre informazioni sulla crittografia in Azure SQL Edge.  
  
|Per informazioni su|Vedere|  
|---------------------------|---------|  
|Implementazione di connessioni protette|[Crittografia delle connessioni](/sql/linux/sql-server-linux-encrypted-connections)|  
|Funzioni di crittografia|[Funzioni di crittografia &#40;Transact-SQL&#41;](/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|Crittografia dei dati inattivi|[Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Always Encrypted|[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> Le limitazioni di sicurezza descritte per [SQL Server in Linux](/sql/linux/sql-server-linux-security-overview) si applicano anche a Azure SQL Edge. 


> [!NOTE]
> Azure SQL Edge non include l'utilità MSSQL-conf. Tutte le configurazioni, inclusa la configurazione relativa alla crittografia, devono essere eseguite tramite il [file MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file) o le [variabili di ambiente](configure.md#configure-by-using-environment-variables). 


Analogamente a SQL e Microsoft SQL Server di Azure, Azure SQL Edge fornisce lo stesso meccanismo per creare e usare i certificati per migliorare la sicurezza degli oggetti e delle connessioni. Per ulteriori informazioni, vedere [Create Certificate (Transact-SQL)](/sql/t-sql/statements/create-certificate-transact-sql).


## <a name="application-security"></a>Sicurezza dell'applicazione

### <a name="client-programs"></a>Programmi client

Le procedure consigliate per la sicurezza di Azure SQL Edge includono la scrittura di applicazioni client protette. Per altre informazioni sulla protezione delle applicazioni client a livello di rete, vedere [Configurazione di rete dei client](/sql/database-engine/configure-windows/client-network-configuration).

### <a name="security-catalog-views-and-functions"></a>Viste e funzioni del catalogo di sicurezza  
Le informazioni sulla sicurezza sono esposte in diverse viste e funzioni ottimizzate per le prestazioni e l'utilità. La tabella seguente contiene informazioni sulle funzioni e le viste di sicurezza in Azure SQL Edge.  
  
|Funzioni e viste|Collegamenti|  
|---------------------------|---------|  
|Viste del catalogo di sicurezza, che restituiscono informazioni su autorizzazioni, entità, ruoli e così via a livello di database e a livello di server. Sono presenti viste del catalogo che forniscono informazioni su chiavi di crittografia, certificati e credenziali.|[Viste del catalogo relative alla sicurezza &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|Funzioni di sicurezza che restituiscono informazioni sull'utente corrente, autorizzazioni e schemi.|[Funzioni di sicurezza &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)|  
|Viste a gestione dinamica di sicurezza.|[Funzioni e viste a gestione dinamica relative alla sicurezza &#40;Transact-SQL&#41;](/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>Controllo 

Azure SQL Edge fornisce gli stessi meccanismi di controllo di SQL Server. Per ulteriori informazioni, vedere [SQL Server Audit (motore di database)](/sql/relational-databases/security/auditing/sql-server-audit-database-engine).


## <a name="next-steps"></a>Passaggi successivi

- [Introduzione con funzionalità di sicurezza](/sql/linux/sql-server-linux-security-get-started)
- [Esecuzione di Azure SQL Edge come utente non root](configure.md#run-azure-sql-edge-as-non-root-user)
- [Centro sicurezza di Azure per le cose](../defender-for-iot/overview.md)