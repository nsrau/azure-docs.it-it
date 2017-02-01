---
title: Panoramica sulla protezione dei dati del database SQL di Azure | Documentazione Microsoft
description: Informazioni sulla protezione dei dati in un database SQL di Azure.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/21/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 67b32b224d86e8d0ed06d8248c3a2c5f5569c5cb


---
# <a name="protecting-data-within-your-sql-database"></a>Protezione dei dati nel database SQL

Il database SQL protegge i dati usando la crittografia.  

## <a name="overview"></a>Panoramica

Il database SQL protegge i dati in movimento con la crittografia [Transport Layer Security](https://support.microsoft.com/en-us/kb/3135244), i dati inattivi con la crittografia [Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242) e i dati in uso con la crittografia [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). Per informazioni sull'uso di queste funzionalità di protezione dei dati nel database SQL, vedere [Data protection and security](sql-database-protect-data.md) (Protezione e sicurezza dei dati).

Per altri modi di crittografare i dati, considerare quanto segue:

* [Crittografia a livello di cella](https://msdn.microsoft.com/library/ms179331.aspx) per crittografare colonne specifiche o anche celle di dati con chiavi di crittografia diverse.
* Se è necessario un modulo di sicurezza Hardware o la gestione centralizzata della gerarchia di chiavi di crittografia, è consigliabile utilizzare l' [insieme di credenziali chiave di Azure con SQL Server in una relativa macchina virtuale](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).


Il database SQL protegge i dati fornendo funzionalità di controllo e di rilevamento delle minacce. 

### <a name="auditing"></a>Controllo
Il servizio di controllo del database SQL tiene traccia delle attività del database e consente di gestire la conformità alle normative, registrando gli eventi del database in un log di controllo nell'account di archiviazione di Azure. Il servizio di controllo consente di conoscere le attività del database in corso e di analizzare ed esaminare l'attività cronologica per identificare potenziali minacce o uso improprio sospetto e violazioni della sicurezza. Per altre informazioni, vedere [Introduzione al controllo del database SQL](sql-database-auditing-get-started.md).  

### <a name="threat-detection"></a>Introduzione al rilevamento delle minacce
Il rilevamento delle minacce è complementare al controllo perché offre un livello aggiuntivo di informazioni sulla sicurezza integrato nel servizio del database SQL di Azure, oltre a riconoscere, profilare e rilevare attività anomale del database. L'utente viene avvisato di attività sospette, vulnerabilità potenziali, attacchi SQL injection e modelli di accesso al database anomali. È possibile rispondere agli avvisi seguendo le informazioni fornite e le istruzioni operative. Per altre informazioni, vedere l' [Introduzione al rilevamento delle minacce nel database SQL](sql-database-threat-detection-get-started.md).  

## <a name="connection-security"></a>Sicurezza delle connessioni
La sicurezza delle connessioni fa riferimento al modo che si limitano e proteggono le connessioni al database mediante regole del firewall e crittografia di connessione.

Le regole del firewall vengono usate dal server e dal database per rifiutare i tentativi di connessione da indirizzi IP che non sono stati esplicitamente inclusi nell'elenco di IP consentiti. Per consentire all'applicazione o all'indirizzo IP pubblico del computer client di connettersi a un nuovo database, è innanzitutto necessario creare una regola firewall di livello server tramite il portale di Azure classico, l'API REST o PowerShell. Come procedura consigliata, si suggerisce di limitare gli intervalli di indirizzi IP consentiti attraverso il firewall del server quanto più possibile. Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/ee621782).

Tutte le connessioni al database SQL di Azure richiedono la crittografia SSL/TLS (SSL/TLS) in qualsiasi caso quando i dati sono "in transito" da e verso il database in qualsiasi momento. Nella stringa di connessione dell'applicazione è necessario specificare parametri per crittografare la connessione e *non* considerare attendibile il certificato del server (ciò viene eseguito automaticamente se si copia la stringa di connessione dal portale di Azure classico). In caso contrario, la connessione non può verificare l'identità del server e sono possibili attacchi "man-in-the-middle". Per il driver ADO.NET, ad esempio, questi parametri della stringa di connessione sono **Encrypt=True** e **TrustServerCertificate=False**. Per ulteriori informazioni, vedere [Crittografia di connessione al database SQL di Azure e convalida dei certificati](https://msdn.microsoft.com/library/azure/ff394108#encryption).

## <a name="authentication"></a>Autenticazione
Per autenticazione si intende il modo in cui viene dimostrata la propria identità durante la connessione al database. Il database SQL supporta due tipi di autenticazione:

* **SQL Authentication**, che utilizza nome utente e password
* **Azure Active Directory Authentication**, che utilizza identità gestite da Azure Active Directory ed è supportata da domini gestiti e integrati.

Durante la creazione del server logico per il database, è stato specificato un account di accesso "amministratore del server" con un nome utente e una password. Utilizzando queste credenziali, è possibile essere autenticati in qualsiasi database di tale server in qualità di proprietario del database o "dbo". Se si desidera utilizzare l'autenticazione di Azure Active Directory, è necessario creare un altro amministratore del server denominato "admin Azure AD," che è autorizzato ad amministrare utenti e gruppi di Azure AD. Questo amministratore può inoltre eseguire tutte le operazioni che un amministratore del server regolare può fare. Vedere [Connessione al Database SQL utilizzando l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md) per una procedura dettagliata di come creare un amministratore di Azure AD per abilitare l'autenticazione di Azure Active Directory.

Si consiglia che per l'applicazione si utilizzi un account diverso per l'autenticazione; SSL/TLSin questo modo è possibile limitare le autorizzazioni concesse all'applicazione e ridurre i rischi di attività dannose nel caso in cui il codice dell'applicazione sia vulnerabile a un attacco SQL injection. L'approccio consigliato consiste nel creare un [utente di database indipendente](https://msdn.microsoft.com/library/ff929188), consentendo così l'autenticazione dell'app direttamente nel database. È possibile creare un utente del database che utilizza l’autenticazione SQL tramite l'esecuzione dell'istruzione T-SQL seguente durante la connessione al database utente con l'account di accesso di amministrazione del server:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Se è stato creato un amministratore di Azure AD, è possibile creare un utente del database che utilizza l’autenticazione Azure Active Directory tramite l'esecuzione dell'istruzione T-SQL seguente durante la connessione al database utente con l'account di amministrazione di Azure AD:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

In ogni caso, nella stringa di connessione dell'applicazione devono essere specificate queste credenziali, piuttosto che l'accesso di amministratore del server, per connettersi al database.

Per ulteriori informazioni su come avviene l'autenticazione con database SQL, vedere [Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md).

## <a name="authorization"></a>Authorization
Per autorizzazione si intende a le operazioni che è possibile effettuare all'interno di un database SQL di Azure e questa funzionalità viene controllata da autorizzazioni e da appartenenze ai ruoli dell'account utente. Come procedura consigliata, è opportuno concedere agli utenti i privilegi minimi necessari. Il database  SQL di Azure ne semplifica la gestione con i ruoli in+ T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

L'account di amministrazione del server a cui ci si sta connettendo è un membro del ruolo db_owner, che è autorizzato a eseguire qualsiasi operazione all'interno del database. Salvare questo account per la distribuzione degli aggiornamenti allo schema e altre operazioni di gestione. Utilizzare l'account "ApplicationUser" con autorizzazioni più limitate per la connessione dall'applicazione al database con i privilegi minimi richiesti dall'applicazione.

Esistono modi per limitare ulteriormente le operazioni possibili con il database SQL di Azure:

* È possibile usare [ruoli del database](https://msdn.microsoft.com/library/ms189121) diversi da db_datareader e db_datawriter per creare account utente dell'applicazione più potenti o account di gestione meno potenti.
* È possibile utilizzare [autorizzazioni](https://msdn.microsoft.com/library/ms191291) che consentono di controllare le operazioni possibili per le singole colonne, tabelle, viste, procedure e altri oggetti nel database.
* È possibile usare la [rappresentazione](https://msdn.microsoft.com/library/vstudio/bb669087) e la [firma del modulo](https://msdn.microsoft.com/library/bb669102) per elevare temporaneamente le autorizzazioni in modo sicuro.
* [Sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131) può essere utilizzato come limite alle cui righe un utente può accedere.
* [Mascheramento dei dati](sql-database-dynamic-data-masking-get-started.md) per limitare l'esposizione dei dati sensibili
* [Stored procedure](https://msdn.microsoft.com/library/ms190782) per limitare le operazioni che possono essere eseguite nel database.

La gestione di database e server logici dal portale di Azure classico o mediante l'API di gestione risorse di Azure viene controllata dalle assegnazioni di ruolo dell'account utente del portale. Per ulteriori informazioni su questo argomento, vedere [Controllo di accesso basato sui ruoli nel portale di Azure](../active-directory/role-based-access-control-configure.md).

## <a name="encryption"></a>Crittografia
I database SQL di Azure consentono di proteggere i dati mediante la crittografia dei dati quando sono in stato "inattivo" o vengono archiviati in file di database e backup, utilizzando la funzionalità [Crittografia dati trasparenti](http://go.microsoft.com/fwlink/?LinkId=526242). Per crittografare il database, connettersi in qualità di proprietario del database ed eseguire:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Per altri modi crittografare dati sensibili, è consigliabile prendere in considerazione quanto segue:

* [Crittografia a livello di cella](https://msdn.microsoft.com/library/ms179331.aspx) per crittografare colonne specifiche o anche celle di dati con chiavi di crittografia diverse.
* Se è necessario un modulo di sicurezza Hardware o la gestione centralizzata della gerarchia di chiavi di crittografia, è consigliabile utilizzare l' [insieme di credenziali chiave di Azure con SQL Server in una relativa macchina virtuale](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Sempre crittografati](https://msdn.microsoft.com/library/mt163865.aspx) (in anteprima) effettua la crittografia trasparente alle applicazioni e consente ai client di crittografare dati riservati all'interno delle applicazioni del client senza condividere le chiavi di crittografia con il Database SQL.

## <a name="auditing"></a>Controllo
Il controllo e il monitoraggio di eventi del database consentono di mantenere la conformità alle normative e di identificare attività sospette. La funzionalità di controllo del database SQL consente di registrare gli eventi nel database in un log di controllo nell'account di archiviazione Azure. La funzionalità di controllo del database SQL si integra inoltre con Microsoft Power BI per facilitare l'esecuzione di analisi e report drill-down. Per ulteriori informazioni, vedere l' [Introduzione al controllo del database SQL](sql-database-auditing-get-started.md).

Il rilevamento delle minacce nel database SQL offre un livello di sicurezza aggiuntivo oltre al controllo. Consente di rispondere alle minacce appena si verificano fornendo avvisi di protezione relativi ad attività anomale. Per altre informazioni, vedere l' [Introduzione al rilevamento delle minacce nel database SQL](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Conformità
Oltre alle caratteristiche e alle funzionalità che consentono all'applicazione di soddisfare i diversi requisiti di conformità di sicurezza, il database SQL di Azure prende parte inoltre a controlli regolari ed ha ottenuto la certificazione per diversi standard di conformità. Per ulteriori informazioni, vedere il [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/), dove è possibile trovare l'elenco più recente di [certificazioni di conformità del database SQL](https://azure.microsoft.com/support/trust-center/services/).


## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di tutte le funzionalità di sicurezza del database SQL, vedere la [panoramica della sicurezza in SQL](sql-database-security-overview.md).
- Per informazioni sull'uso delle funzionalità di controllo dell'accesso nel database SQL, vedere [Control access](sql-database-control-access.md) (Controllare l'accesso).
- Per informazioni sul monitoraggio proattivo, vedere [Introduzione al controllo del database SQL](sql-database-auditing-get-started.md) e [Introduzione al rilevamento delle minacce nel database SQL](sql-database-threat-detection-get-started.md).



<!--HONumber=Dec16_HO4-->


