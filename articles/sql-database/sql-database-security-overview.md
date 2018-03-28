---
title: Panoramica della sicurezza del database SQL di Azure | Microsoft Docs
description: In questo argomento vengono fornite informazioni sulla sicurezza del database SQL Azure e di SQL Server, incluse le differenze tra il cloud e SQL Server locale.
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/12/2018
ms.author: giladm
ms.openlocfilehash: 1dc34f021fa6482c65cce0e922951ae329987c43
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="securing-your-sql-database"></a>Protezione del Database SQL

In questo articolo vengono esaminate le informazioni di base relative alla protezione del livello dati di un'applicazione in cui viene utilizzato il database SQL di Azure. In particolare, questo articolo consente di iniziare a usare le risorse per la protezione dei dati, il controllo dell'accesso e il monitoraggio proattivo. 

Per una panoramica completa delle funzionalità di sicurezza disponibili in tutte le versioni di SQL, vedere il [Centro sicurezza PC per il motore di database di SQL Server e per il database SQL di Azure](https://msdn.microsoft.com/library/bb510589). Ulteriori informazioni sono inoltre disponibili nel [white paper tecnico di protezione e del Database di SQL Azure](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="protect-data"></a>Proteggere i dati

### <a name="encryption"></a>Crittografia
Il database SQL protegge i dati in movimento con la crittografia [Transport Layer Security](https://support.microsoft.com/kb/3135244), i dati inattivi con la crittografia [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) e i dati in uso con la crittografia [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). 

> [!IMPORTANT]
>Tutte le connessioni al database SQL di Azure richiedono la crittografia SSL/TLS (SSL/TLS) in qualsiasi caso quando i dati sono "in transito" da e verso il database in qualsiasi momento. Nella stringa di connessione dell'applicazione è necessario specificare parametri per crittografare la connessione e *non* considerare attendibile il certificato del server (ciò viene eseguito automaticamente se si copia la stringa di connessione dal portale di Azure). In caso contrario, la connessione non verifica l'identità del server e sono possibili attacchi "man-in-the-middle". Per il driver ADO.NET, ad esempio, questi parametri della stringa di connessione sono **Encrypt=True** e **TrustServerCertificate=False**. 

Per altri modi di crittografare i dati, considerare quanto segue:

* [Crittografia a livello di cella](https://msdn.microsoft.com/library/ms179331.aspx) per crittografare colonne specifiche o anche celle di dati con chiavi di crittografia diverse.
* Se è necessario un modulo di sicurezza Hardware o la gestione centralizzata della gerarchia di chiavi di crittografia, è consigliabile utilizzare l' [insieme di credenziali chiave di Azure con SQL Server in una relativa macchina virtuale](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="data-discovery--classification"></a>Individuazione dati e classificazione
Individuazione dati e classificazione (attualmente in anteprima) offre funzionalità avanzate incorporate nel database SQL di Azure per l'individuazione, la classificazione e la protezione dei dati sensibili presenti nel database, nonché per l'aggiunta di etichette a tali dati. L'individuazione e la classificazione dei dati più sensibili (dati commerciali e finanziari, dati relativi all'assistenza sanitaria, informazioni personali e così via) possono svolgere un ruolo fondamentale per il livello di protezione delle informazioni aziendali. Individuazione dati e classificazione può svolgere la funzione di infrastruttura per:

- Vari scenari di sicurezza, ad esempio monitoraggio (controllo) e invio di avvisi sulle anomalie di accesso a dati sensibili.
- Controllare l'accesso ai database che contengono dati riservati e rafforzare la sicurezza di questi.
- Contribuire a soddisfare gli standard e i requisiti di conformità alle normative sulla privacy dei dati.

Per altre informazioni, vedere [Get started with SQL DB Data Discovery & Classification](sql-database-data-discovery-and-classification.md) (Introduzione a Individuazione dati e classificazione dei database SQL). 

## <a name="control-access"></a>Controllare l'accesso
Il database SQL protegge i dati limitando l'accesso al database usando regole del firewall, i meccanismi di autenticazione che richiedono agli utenti di dimostrare la propria identità e l'autorizzazione per i dati tramite le appartenenze basate sui ruoli e le autorizzazioni, oltre che tramite la sicurezza a livello di riga e la maschera dati dinamica. Per informazioni sull'uso delle funzionalità di controllo dell'accesso nel database SQL, vedere [Controllo dell'accesso](sql-database-control-access.md).

> [!IMPORTANT]
> La gestione dei database e dei server logici in Azure è controllata dalle assegnazioni di ruolo dell'account del portale utenti. Per altre informazioni su questo articolo, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-what-is.md).
>

### <a name="firewall-and-firewall-rules"></a>Firewall e regole del firewall
Per proteggere i dati, il firewall impedisce qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati usando le [regole del firewall](sql-database-firewall-configure.md). Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.

### <a name="authentication"></a>Authentication
Per autenticazione del database SQL si intende il modo in cui viene dimostrata la propria identità durante la connessione al database. Il database SQL supporta due tipi di autenticazione:
* **Autenticazione SQL**, che usa nome utente e password. Durante la creazione del server logico per il database, è stato specificato un account di accesso "amministratore del server" con un nome utente e una password. Utilizzando queste credenziali, è possibile essere autenticati in qualsiasi database di tale server in qualità di proprietario del database o "dbo". 
* **Autenticazione di Azure Active Directory**, che usa identità gestite da Azure Active Directory ed è supportata per domini gestiti e integrati. [Quando possibile](https://msdn.microsoft.com/library/ms144284.aspx), usare l'autenticazione di Active Directory (sicurezza integrata). Se si desidera utilizzare l'autenticazione di Azure Active Directory, è necessario creare un altro amministratore del server denominato "admin Azure AD," che è autorizzato ad amministrare utenti e gruppi di Azure AD. Questo amministratore può inoltre eseguire tutte le operazioni che un amministratore del server regolare può fare. Vedere [Connessione al Database SQL utilizzando l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md) per una procedura dettagliata di come creare un amministratore di Azure AD per abilitare l'autenticazione di Azure Active Directory.

### <a name="authorization"></a>Authorization
Per autorizzazione si intendono le operazioni che l'utente può eseguire in un database SQL di Azure, che sono controllate dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente. Come procedura consigliata, è opportuno concedere agli utenti i privilegi minimi necessari. L'account di amministrazione del server a cui ci si sta connettendo è un membro del ruolo db_owner, che è autorizzato a eseguire qualsiasi operazione all'interno del database. Salvare questo account per la distribuzione degli aggiornamenti allo schema e altre operazioni di gestione. Utilizzare l'account "ApplicationUser" con autorizzazioni più limitate per la connessione dall'applicazione al database con i privilegi minimi richiesti dall'applicazione.

### <a name="row-level-security"></a>Sicurezza a livello di riga
La sicurezza a livello di riga consente ai clienti di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione. Per altre informazioni, vedere [Sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131).

### <a name="dynamic-data-masking"></a>Maschera dati dinamica 
La funzione Maschera dati dinamica del database SQL limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. La maschera dati dinamica individua automaticamente dati potenzialmente sensibili nel database SQL di Azure e fornisce indicazioni pratiche per il mascheramento di questi campi, con un impatto minimo sul livello dell'applicazione. Questa funzionalità si basa sull'offuscamento dei dati sensibili nel set dei risultati di una query su campi designati del database, mentre i dati del database non subiscono modifiche. Per altre informazioni sull'uso di questa funzionalità per limitare l'esposizione di dati sensibili, vedere [Introduzione alla Maschera dati dinamica del database SQL](sql-database-dynamic-data-masking-get-started.md).

## <a name="proactive-monitoring"></a>Monitoraggio proattivo
Il database SQL protegge i dati fornendo funzionalità di controllo e di rilevamento delle minacce. 

### <a name="auditing"></a>Controllo
Il servizio di controllo del database SQL tiene traccia delle attività del database e consente di gestire la conformità alle normative, registrando gli eventi del database in un log di controllo nell'account di archiviazione di Azure. Il servizio di controllo consente di conoscere le attività del database in corso e di analizzare ed esaminare l'attività cronologica per identificare potenziali minacce o uso improprio sospetto e violazioni della sicurezza. Per altre informazioni, vedere [Introduzione al controllo del database SQL](sql-database-auditing.md).  

### <a name="threat-detection"></a>Introduzione al rilevamento delle minacce
Il rilevamento delle minacce è complementare al controllo perché offre un livello aggiuntivo di informazioni sulla sicurezza integrata nel servizio database SQL di Azure, che rileva tentativi insoliti e potenzialmente dannosi di accedere ai database o di sfruttarli. L'utente viene avvisato di attività sospette, vulnerabilità potenziali e attacchi SQL injection, nonché di modelli anomali di accesso al database. Gli avvisi di Rilevamento minacce sono disponibili nel [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), forniscono i dettagli delle attività sospette e raccomandano azioni per individuare e ridurre la minaccia. La funzionalità Rilevamento delle minacce ha un costo di $15/server/mese È gratuito per i primi 60 giorni. Per altre informazioni, vedere l' [Introduzione al rilevamento delle minacce nel database SQL](sql-database-threat-detection.md).
 
## <a name="compliance"></a>Conformità
Oltre alle caratteristiche e alle funzionalità sopra descritte, che consentono all'applicazione di soddisfare vari requisiti di sicurezza, il database SQL di Azure è inoltre sottoposto a controlli regolari ed ha ottenuto la certificazione per diversi standard di conformità. Per ulteriori informazioni, vedere il [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/), dove è possibile trovare l'elenco più recente di [certificazioni di conformità del database SQL](https://azure.microsoft.com/support/trust-center/services/).


## <a name="security-management"></a>Gestione della sicurezza

Il database SQL di Azure consente di gestire la sicurezza dei dati fornendo analisi di database e una dashboard di sicurezza centralizzata mediante [Valutazione delle vulnerabilità di SQL](sql-vulnerability-assessment.md).

**Valutazione della vulnerabilità**: [Valutazione della vulnerabilità di SQL](sql-vulnerability-assessment.md), attualmente in anteprima, è uno strumento semplice da configurare integrato nel database SQL di Azure che facilita l'individuazione, il monitoraggio e la risoluzione di potenziali vulnerabilità del database. Lo strumento esegue un'analisi delle vulnerabilità sul database e genera un report che offre visibilità sullo stato di sicurezza, incluse azioni implementabili per risolvere i problemi e migliorare la sicurezza del database. È possibile personalizzare il report di valutazione per l'ambiente, impostando una baseline accettabile per le configurazioni delle autorizzazioni e delle funzioni e le impostazioni del database. Questo può essere utile per:

- Soddisfare i requisiti di conformità che richiedono i report di scansione del database. 

- Soddisfare gli standard di privacy. 

- Monitorare un ambiente dinamico del database in cui è difficile rilevare le modifiche.

Per altre informazioni, vedere [Valutazione della vulnerabilità di SQL](sql-vulnerability-assessment.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'uso delle funzionalità di controllo dell'accesso nel database SQL, vedere [Controllo dell'accesso](sql-database-control-access.md).
- Per informazioni sul controllo del database, vedere [SQL Database auditing](sql-database-auditing.md) (Controllo del database SQL).
- Per informazioni sul rilevamento delle minacce, vedere [SQL Database threat detection](sql-database-threat-detection.md) (Rilevamento delle minacce nel database SQL).
