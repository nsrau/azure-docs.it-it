---
title: Panoramica della sicurezza nel database SQL
description: In questo argomento vengono fornite informazioni sulla sicurezza database SQL Azure e SQL Server, incluse le differenze tra il cloud e SQL Server locale per quanto riguarda l'autenticazione, l'autorizzazione, la sicurezza della connessione, la crittografia e conformità.
services: sql-database
documentationcenter: ''
author: tmullaney
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 06/09/2016
ms.author: thmullan;jackr

---
# Protezione del Database SQL
## Panoramica
In questo articolo vengono esaminate le informazioni di base relative alla protezione del livello dati di un'applicazione in cui viene utilizzato il database SQL di Azure. In particolare questo articolo fornirà le risorse per la limitazione degli accessi, per la protezione dei dati e per monitorare le attività in un database creato nell'[Esercitazione di introduzione al database SQL](sql-database-get-started.md). Per una panoramica completa delle funzionalità di sicurezza disponibili in tutte le versioni di SQL, vedere il [Centro sicurezza PC per il motore di database di SQL Server e per il database SQL di Azure](https://msdn.microsoft.com/library/bb510589). Ulteriori informazioni sono inoltre disponibili nel [white paper tecnico di protezione e del Database di SQL Azure ](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## Sicurezza delle connessioni
La sicurezza delle connessioni fa riferimento al modo che si limitano e proteggono le connessioni al database mediante regole del firewall e crittografia di connessione.

Le regole del firewall vengono usate dal server e dal database per rifiutare i tentativi di connessione da indirizzi IP che non sono stati esplicitamente inclusi nell'elenco di IP consentiti. Per consentire all'applicazione o all'indirizzo IP pubblico del computer client di connettersi a un nuovo database, è innanzitutto necessario creare una regola firewall di livello server tramite il portale di Azure classico, l'API REST o PowerShell. Come procedura consigliata, si suggerisce di limitare gli intervalli di indirizzi IP consentiti attraverso il firewall del server quanto più possibile. Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/ee621782).

Tutte le connessioni al database SQL di Azure richiedono la crittografia SSL/TLS (SSL/TLS) in qualsiasi caso quando i dati sono "in transito" da e verso il database in qualsiasi momento. Nella stringa di connessione dell'applicazione, è necessario specificare parametri per crittografare la connessione e *non* considerare attendibile il certificato del server (ciò viene eseguito automaticamente se si copia la stringa di connessione dal portale di Azure classico), in caso contrario durante le connessioni non verrà verificata l'identità del server e saranno possibili attacchi "man-in-the-middle". Per il driver ADO.NET, ad esempio, questi parametri della stringa di connessione sono **Encrypt = True** e **TrustServerCertificate = False**. Per ulteriori informazioni, vedere [Crittografia di connessione al database SQL di Azure e convalida dei certificati](https://msdn.microsoft.com/library/azure/ff394108#encryption).

## Autenticazione
Per autenticazione si intende il modo in cui viene dimostrata la propria identità durante la connessione al database. Il database SQL supporta due tipi di autenticazione:

* **SQL Authentication**, che utilizza nome utente e password
* **Azure Active Directory Authentication**, che utilizza identità gestite da Azure Active Directory ed è supportata da domini gestiti e integrati.

Durante la creazione del server logico per il database, è stato specificato un account di accesso "amministratore del server" con un nome utente e una password. Utilizzando queste credenziali, è possibile essere autenticati in qualsiasi database di tale server in qualità di proprietario del database o "dbo". Se si desidera utilizzare l'autenticazione di Azure Active Directory, è necessario creare un altro amministratore del server denominato "admin Azure AD," che è autorizzato ad amministrare utenti e gruppi di Azure AD. Questo amministratore può inoltre eseguire tutte le operazioni che un amministratore del server regolare può fare. Vedere [Connessione al Database SQL utilizzando l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md) per una procedura dettagliata di come creare un amministratore di Azure AD per abilitare l'autenticazione di Azure Active Directory.

Si consiglia che per l'applicazione si utilizzi un account diverso per l'autenticazione; SSL/TLSin questo modo è possibile limitare le autorizzazioni concesse all'applicazione e ridurre i rischi di attività dannose nel caso in cui il codice dell'applicazione sia vulnerabile a un attacco SQL injection. L'approccio consigliato consiste nel creare un [utente del database indipendente](https://msdn.microsoft.com/library/ff929188), che consenta all'applicazione di essere autenticata direttamente con un singolo database. È possibile creare un utente del database che utilizza l’autenticazione SQL tramite l'esecuzione dell'istruzione T-SQL seguente durante la connessione al database utente con l'account di accesso di amministrazione del server:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Se è stato creato un amministratore di Azure AD, è possibile creare un utente del database che utilizza l’autenticazione Azure Active Directory tramite l'esecuzione dell'istruzione T-SQL seguente durante la connessione al database utente con l'account di amministrazione di Azure AD:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

In ogni caso, nella stringa di connessione dell'applicazione devono essere specificate queste credenziali, piuttosto che l'accesso di amministratore del server, per connettersi al database.

Per ulteriori informazioni su come avviene l'autenticazione con database SQL, vedere [Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md).

## Authorization
Per autorizzazione si intende a le operazioni che è possibile effettuare all'interno di un database SQL di Azure e questa funzionalità viene controllata da autorizzazioni e da appartenenze ai ruoli dell'account utente. Come procedura consigliata, è opportuno concedere agli utenti i privilegi minimi necessari. Il database SQL di Azure ne semplifica la gestione con i ruoli in+ T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

L'account di amministrazione del server a cui ci si sta connettendo è un membro del ruolo db\_owner, che è autorizzato a eseguire qualsiasi operazione all'interno del database. Salvare questo account per la distribuzione degli aggiornamenti allo schema e altre operazioni di gestione. Utilizzare l'account "ApplicationUser" con autorizzazioni più limitate per la connessione dall'applicazione al database con i privilegi minimi richiesti dall'applicazione.

Esistono modi per limitare ulteriormente le operazioni possibili con il database SQL di Azure:

* È possibile utilizzare [ruoli del database](https://msdn.microsoft.com/library/ms189121) diversi da db\_datareader e db\_datawriter per creare account utente dell'applicazione più potenti o account di gestione meno potenti.
* È possibile utilizzare [autorizzazioni](https://msdn.microsoft.com/library/ms191291) che consentono di controllare le operazioni possibili per le singole colonne, tabelle, viste, procedure e altri oggetti nel database.
* È possibile utilizzare la [rappresentazione](https://msdn.microsoft.com/library/vstudio/bb669087) e la [firma del modulo](https://msdn.microsoft.com/library/bb669102) in modo sicuro per elevare temporaneamente le autorizzazioni in modo permanente.
* [Sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131) può essere utilizzato come limite alle cui righe un utente può accedere.
* È possibile utilizzare il [Mascheramento dei dati](sql-database-dynamic-data-masking-get-started.md) per limitare l'esposizione dei dati sensibili
* È possibile utilizzare le [Stored procedure](https://msdn.microsoft.com/library/ms190782) per limitare le operazioni che possono essere eseguite nel database.

La gestione di database e server logici dal portale di Azure classico o mediante l'API di gestione risorse di Azure viene controllata dalle assegnazioni di ruolo dell'account utente del portale. Per ulteriori informazioni su questo argomento, vedere [Controllo di accesso basato sui ruoli nel portale di Azure](../active-directory/role-based-access-control-configure.md).

## Crittografia
I database SQL di Azure consentono di proteggere i dati mediante la crittografia dei dati quando sono in stato "inattivo" o vengono archiviati in file di database e backup, utilizzando la funzionalità [Crittografia dati trasparenti](http://go.microsoft.com/fwlink/?LinkId=526242). Per crittografare il database, connettersi in qualità di proprietario del database ed eseguire:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Per altri modi crittografare dati sensibili, è consigliabile prendere in considerazione quanto segue:

* [Crittografia a livello di cella](https://msdn.microsoft.com/library/ms179331.aspx) per crittografare colonne specifiche o anche celle di dati con chiavi di crittografia diverse.
* Se è necessario un modulo di sicurezza Hardware o la gestione centralizzata della gerarchia di chiavi di crittografia, è consigliabile utilizzare l'[insieme di credenziali chiave di Azure con SQL Server in una relativa macchina virtuale](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Sempre crittografati](https://msdn.microsoft.com/library/mt163865.aspx) (in anteprima) effettua la crittografia trasparente alle applicazioni e consente ai client di crittografare dati riservati all'interno delle applicazioni del client senza condividere le chiavi di crittografia con il Database SQL.

## Controllo
Il controllo e il monitoraggio di eventi del database consentono di mantenere la conformità alle normative e di identificare attività sospette. La funzionalità di controllo del database SQL consente di registrare gli eventi nel database in un log di controllo nell'account di archiviazione Azure. La funzionalità di controllo del database SQL si integra inoltre con Microsoft Power BI per facilitare l'esecuzione di analisi e report drill-down. Per ulteriori informazioni, vedere l'[Introduzione al controllo del database SQL](sql-database-auditing-get-started.md).

Il rilevamento delle minacce nel database SQL offre un livello di sicurezza aggiuntivo oltre al controllo. Consente di rispondere alle minacce appena si verificano fornendo avvisi di protezione relativi ad attività anomale. Per altre informazioni, vedere l'[Introduzione al rilevamento delle minacce nel database SQL](sql-database-threat-detection-get-started.md).

## Conformità
Oltre alle caratteristiche e alle funzionalità che consentono all'applicazione di soddisfare i diversi requisiti di conformità di sicurezza, il database SQL di Azure prende parte inoltre a controlli regolari ed ha ottenuto la certificazione per diversi standard di conformità. Per ulteriori informazioni, vedere il [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/), dove è possibile trovare l'elenco più recente di [Certificazioni di conformità del database SQL](https://azure.microsoft.com/support/trust-center/services/).

<!---HONumber=AcomDC_0615_2016-->