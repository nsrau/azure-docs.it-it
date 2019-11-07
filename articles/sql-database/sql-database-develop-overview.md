---
title: Panoramica dello sviluppo di applicazioni del database SQL
description: Informazioni sulle librerie di connettività disponibili e procedure consigliate per applicazioni che si connettono al Database SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 02/07/2019
ms.openlocfilehash: 58f823aad157657de5445b1010a405af01c70171
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690633"
---
# <a name="sql-database-application-development-overview"></a>Panoramica dello sviluppo di applicazioni del database SQL

Questo articolo esamina le considerazioni di base di cui uno sviluppatore deve tenere conto quando scrive il codice per collegarsi al database SQL di Azure. Questo articolo si applica a tutti i modelli di distribuzione del database SQL di Azure (database singolo, pool elastici, istanza gestita).

> [!TIP]
> Se è necessario configurare il database SQL di Azure, consultare le guide introduttive relative ai [database singoli](sql-database-single-database-quickstart-guide.md) e alle [istanze gestite](sql-database-managed-instance-quickstart-guide.md).
>

## <a name="language-and-platform"></a>Linguaggio e piattaforma

È possibile usare vari [linguaggi di programmazione e piattaforme](sql-database-connect-query.md) per connettersi ed eseguire query al database SQL di Azure. Sono disponibili [applicazioni di esempio](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) per connettersi al database SQL di Azure.

È possibile sfruttare strumenti open source come [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli) e [Visual Studio Code](https://code.visualstudio.com/). Inoltre, il database SQL di Azure interagisce con gli strumenti Microsoft come [Visual Studio](https://www.visualstudio.com/downloads/) e [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). È anche possibile usare il portale di Azure, PowerShell e le API REST, per aumentare la produttività.

## <a name="authentication"></a>Autenticazione

L'accesso al database SQL di Azure è protetto con account di accesso e firewall. Il database SQL di Azure supporta utenti e account di accesso per l'autenticazione di SQL Server e [Azure Active Directory](sql-database-aad-authentication.md). Gli account di accesso di AAD sono disponibili solo nell'istanza gestita. 

Altre informazioni sulla [gestione dell'accesso al database e degli account di accesso](sql-database-manage-logins.md).

## <a name="connections"></a>Connessioni

Nella logica di connessione client sostituire il timeout predefinito affinché sia pari a 30 secondi. Il valore predefinito di 15 secondi è troppo breve per connessioni che dipendono da Internet.

Se si usa un [pool di connessioni](https://msdn.microsoft.com/library/8xx3tyca.aspx), assicurarsi di chiudere la connessione nel momento in cui il programma non la usa attivamente, né si prepara a riusarla.

Evitare transazioni con esecuzione prolungata, perché qualsiasi errore di infrastruttura o connessione può comportare il rollback della transazione. Se possibile, suddividere la transazione in transazioni più piccole e usare l'[invio in batch per migliorare le prestazioni](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Resilienza

Il database SQL di Azure è un servizio cloud in cui si possono prevedere errori temporanei che si verificano nell'infrastruttura sottostante o nella comunicazione tra le entità cloud. Anche se il database SQL di Azure è resiliente, eventuali errori di infrastruttura transitivi possono avere effetto sulla connettività. Quando si verifica un errore temporaneo durante la connessione al database SQL, il codice deve [ripetere la chiamata](sql-database-connectivity-issues.md). Per la ripetizione dei tentativi si consiglia di usare una logica backoff, in modo da non sovraccaricare il database SQL con più client che ripetono i tentativi contemporaneamente. La logica di ripetizione dei tentativi dipende dai [messaggi di errore per programmi client del database SQL](sql-database-develop-error-messages.md).

Per altre informazioni sulle attività preliminari per gli eventi di manutenzione pianificata nel database SQL di Azure, vedere [Pianificazione di eventi di manutenzione di Azure nel database SQL di Azure](sql-database-planned-maintenance.md).

## <a name="network-considerations"></a>Considerazioni sulla rete

- Nel computer che ospita il programma client, verificare che il firewall consenta le comunicazioni TCP in uscita sulla porta 1433.  Altre informazioni: [Configurazione del firewall di un database SQL di Azure](sql-database-configure-firewall-settings.md).
- Se il programma client si connette al database SQL mentre il client viene eseguito in una macchina virtuale (VM) di Azure, è necessario aprire determinati intervalli di porte nella macchina virtuale. Altre informazioni: [Porte superiori a 1433 per ADO.NET 4.5 e database SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- Le connessioni client al database SQL di Azure talvolta ignorano il proxy e interagiscono direttamente con il database. Le porte diverse da 1433 diventano importanti. Per altre informazioni, vedere [Architettura della connettività del database SQL di Azure](sql-database-connectivity-architecture.md) e [Porte successive alla 1433 per ADO.NET 4.5 e database SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
- Per la configurazione di rete per un'istanza gestita, vedere [configurazione di rete per le istanze gestite](sql-database-howto-managed-instance.md#network-configuration).

## <a name="next-steps"></a>Passaggi successivi

Esplorare tutte le [funzionalità del database SQL](sql-database-technical-overview.md).
