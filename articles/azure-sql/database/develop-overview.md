---
title: Panoramica sullo sviluppo di applicazioni
description: Informazioni sulle librerie di connettività disponibili e procedure consigliate per applicazioni che si connettono al Database SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: b099158261de55c829ab2b89a2f994b35b3e50d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85254039"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Panoramica dello sviluppo di applicazioni: database SQL & SQL Istanza gestita

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Questo articolo illustra le considerazioni di base che uno sviluppatore deve tenere presente durante la scrittura di codice per la connessione al database in Azure. Questo articolo si applica al database SQL di Azure e ad Azure SQL Istanza gestita.

## <a name="language-and-platform"></a>Linguaggio e piattaforma

È possibile usare vari [linguaggi di programmazione e piattaforme](connect-query-content-reference-guide.md) per connettersi ed eseguire query al database SQL di Azure. È possibile trovare [applicazioni di esempio](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) che è possibile usare per connettersi al database.

È possibile sfruttare strumenti open source come [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli) e [Visual Studio Code](https://code.visualstudio.com/). Inoltre, il database SQL di Azure interagisce con gli strumenti Microsoft come [Visual Studio](https://www.visualstudio.com/downloads/) e [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). È anche possibile usare il portale di Azure, PowerShell e le API REST, per aumentare la produttività.

## <a name="authentication"></a>Authentication

L'accesso al database SQL di Azure è protetto con account di accesso e firewall. Il database SQL di Azure supporta gli utenti e gli account di accesso sia SQL Server che [Azure Active Directory](authentication-aad-overview.md) . Gli account di accesso Azure Active Directory sono disponibili solo in SQL Istanza gestita. 

Altre informazioni sulla [gestione dell'accesso al database e degli account di accesso](logins-create-manage.md).

## <a name="connections"></a>Connessioni

Nella logica di connessione client sostituire il timeout predefinito affinché sia pari a 30 secondi. Il valore predefinito di 15 secondi è troppo breve per connessioni che dipendono da Internet.

Se si usa un [pool di connessioni](https://msdn.microsoft.com/library/8xx3tyca.aspx), assicurarsi di chiudere la connessione nel momento in cui il programma non la usa attivamente, né si prepara a riusarla.

Evitare transazioni con esecuzione prolungata, perché qualsiasi errore di infrastruttura o connessione può comportare il rollback della transazione. Se possibile, suddividere la transazione in transazioni più piccole e usare l'[invio in batch per migliorare le prestazioni](../performance-improve-use-batching.md).

## <a name="resiliency"></a>Resilienza

Il database SQL di Azure è un servizio cloud in cui si possono prevedere errori temporanei che si verificano nell'infrastruttura sottostante o nella comunicazione tra le entità cloud. Anche se il database SQL di Azure è resiliente, eventuali errori di infrastruttura transitivi possono avere effetto sulla connettività. Quando si verifica un errore temporaneo durante la connessione al database SQL, il codice deve [ripetere la chiamata](troubleshoot-common-connectivity-issues.md). È consigliabile che la logica di ripetizione dei tentativi usi la logica backoff, in modo da non sovraccaricare il servizio con più client che ritentano simultaneamente. La logica di ripetizione dei tentativi dipende dai [messaggi di errore per programmi client del database SQL](troubleshoot-common-errors-issues.md).

Per altre informazioni su come prepararsi per gli eventi di manutenzione pianificata nel database SQL di Azure, vedere [pianificazione degli eventi di manutenzione di Azure nel database SQL di](planned-maintenance.md)Azure.

## <a name="network-considerations"></a>Considerazioni per la rete

- Nel computer che ospita il programma client, verificare che il firewall consenta le comunicazioni TCP in uscita sulla porta 1433.  Altre informazioni: [Configurazione del firewall di un database SQL di Azure](firewall-configure.md).
- Se il programma client si connette al database SQL mentre il client viene eseguito in una macchina virtuale (VM) di Azure, è necessario aprire determinati intervalli di porte nella macchina virtuale. Altre informazioni: [porte superiori a 1433 per ADO.NET 4,5 e database SQL](adonet-v12-develop-direct-route-ports.md).
- Le connessioni client al database SQL di Azure talvolta ignorano il proxy e interagiscono direttamente con il database. Le porte diverse da 1433 diventano importanti. Per altre informazioni, vedere [Architettura della connettività del database SQL di Azure](connectivity-architecture.md) e [Porte successive alla 1433 per ADO.NET 4.5 e database SQL](adonet-v12-develop-direct-route-ports.md).
- Per la configurazione di rete per un'istanza di SQL Istanza gestita, vedere [configurazione di rete per sql istanza gestita](../managed-instance/how-to-content-reference-guide.md#network-configuration).

## <a name="next-steps"></a>Passaggi successivi

Esplorare tutte le funzionalità del [database SQL](sql-database-paas-overview.md) e di [SQL istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md).

Per iniziare, vedere le guide per il [database SQL di Azure](quickstart-content-reference-guide.md) e le [istanze gestite di SQL di Azure](../managed-instance/quickstart-content-reference-guide.md).
