---
title: Le procedure consigliate - Microsoft Azure del database
description: Questo articolo fornisce un set di procedure consigliate per la sicurezza del database di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 9cd02172af5246c60b93a6e4696988268abed506
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258675"
---
# <a name="azure-database-security-best-practices"></a>Procedure consigliate per la sicurezza del database di Azure
Questo articolo descrive le procedure consigliate per la sicurezza del database.

Le procedure consigliate si basano su opinioni concordanti e funzionino con le caratteristiche e le capacità correnti della piattaforma Azure. Le opinioni e le tecnologie cambiano nel tempo e questo articolo viene aggiornato regolarmente per riflettere tali modifiche.

## <a name="secure-databases"></a>Database sicuri
La sicurezza è un tema della massima importanza per la gestione dei database ed è sempre stata una priorità per il [database SQL di Azure](https://docs.microsoft.com/azure/sql-database/). I database possono essere protetti in modo sicuro per soddisfare la maggior parte dei requisiti di legge e di sicurezza, tra i quali HIPAA, ISO 27001/27002 e PCI DSS livello 1. Un elenco aggiornato delle certificazioni di conformità di sicurezza è disponibile nel [sito Centro protezione di Microsoft](https://azure.microsoft.com/support/trust-center/services/). È anche possibile posizionare i database in specifici data center di Azure in base ai requisiti normativi.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Usare le regole del firewall per limitare l'accesso al database
Il database SQL di Microsoft Azure assicura un servizio di database relazionale per Azure e altre applicazioni basate su Internet. Per garantire la sicurezza, il database SQL controlla l'accesso tramite:

- Regole del firewall che limitano la connettività in base all'indirizzo IP.
- Meccanismi di autenticazione che richiedono all'utente di dimostrare la propria identità.
- Meccanismi di autorizzazione che limitano l'accesso degli utenti ad azioni e dati specifici.

I firewall impediscono qualsiasi accesso al server del database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.

La figura seguente illustra dove si imposta un firewall del server nel database SQL:

![Regole del firewall](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Il servizio database SQL di Azure è disponibile solo tramite la porta TCP 1433. Per accedere a un database SQL dal proprio computer, verificare che il firewall del computer client consenta la comunicazione TCP in uscita sulla porta TCP 1433. Bloccare le connessioni in ingresso sulla porta TCP 1433 usando le regole del firewall, se queste connessioni non servono per altre applicazioni.

Nell'ambito del processo di connessione le connessioni da macchine virtuali di Azure vengono reindirizzate a un indirizzo IP e una porta che sono univoci per ogni ruolo di lavoro. Il numero di porta è compreso nell'intervallo che va da 11000 a 11999. Per altre informazioni sulle porte TCP, vedere [Porte successive a 1433 per ADO.NET 4.5](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Per informazioni generali sulle regole del firewall, vedere l'articolo relativo alle [regole del firewall per il database SQL](../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Oltre alle regole IP, il firewall gestisce anche le regole di rete virtuale. Le regole di rete virtuale si basano sugli endpoint servizio di rete virtuale. In alcuni casi le regole di rete virtuale potrebbero essere preferibili alle regole IP. Per altre informazioni, vedere [Endpoint servizio di rete virtuale e regole per il database SQL di Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Abilitare l'autenticazione del database
Il database SQL supporta due tipi di autenticazione: l'autenticazione di SQL Server e l'autenticazione di Azure AD.

### <a name="sql-server-authentication"></a>*Autenticazione di SQL Server*

Ecco alcuni vantaggi:

- Consente al database SQL di supportare ambienti con sistemi operativi misti, in cui non tutti gli utenti sono autenticati da un dominio Windows.
- Consente al database SQL di supportare le applicazioni meno recenti e le applicazioni di partner che richiedono l'autenticazione di SQL Server.
- Consente agli utenti di connettersi da domini sconosciuti o non attendibili. Un'applicazione ad esempio in cui clienti specifici si connettono con account di accesso di SQL Server assegnati per ricevere lo stato dei rispettivi ordini.
- Consente al database SQL di supportare le applicazioni Web in cui gli utenti creano le proprie identità.
- Consente agli sviluppatori di software di distribuire le proprie applicazioni tramite una gerarchia di autorizzazioni complessa, basata su account di accesso di SQL Server noti e preimpostati.

> [!NOTE]
> L'autenticazione di SQL Server non può usare il protocollo di sicurezza Kerberos.

Se si usa l'autenticazione di SQL Server, è necessario:

- Gestire autonomamente le credenziali sicure.
- Proteggere le credenziali nella stringa di connessione.
- (Potenzialmente) proteggere le credenziali trasmesse in rete dal server Web al database. Per altre informazioni, vedere [Procedura: Connect to SQL Server Using SQL Authentication in ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)) (Procedura: Connettersi a SQL Server usando l'autenticazione SQL in ASP.NET 2.0).

### <a name="azure-active-directory-ad-authentication"></a>*Autenticazione di Azure Active Directory (AD)*
L'autenticazione di Azure Active Directory è un meccanismo di connessione al database SQL di Azure e a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tramite l'uso di identità in Azure AD. Con l'autenticazione di Azure AD è possibile gestire centralmente le identità degli utenti del database e di altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti del database da un unico punto e semplifica la gestione delle autorizzazioni.

> [!NOTE]
> È preferibile usare l'autenticazione di Azure AD anziché l'autenticazione di SQL Server.

Ecco alcuni vantaggi:

- Offre un'alternativa all'autenticazione di SQL Server.
- Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
- Consente la rotazione delle password in un'unica posizione.
- I clienti possono gestire le autorizzazioni del database usando gruppi (Azure AD) esterni.
- Può eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
- Usa gli utenti di database indipendente per autenticare le identità a livello di database.
- Supporta l'autenticazione basata su token per le applicazioni che si connettono al database SQL.
- Supporta la federazione dei domini di AD FS o l'autenticazione utente/password nativa per un'istanza locale di Azure Active Directory senza la sincronizzazione del dominio.
- Azure AD supporta le connessioni da SQL Server Management Studio che usano l'autenticazione universale di Active Directory, che include Multi-Factor Authentication. Multi-Factor Authentication include funzionalità avanzate di autenticazione con una serie di opzioni di verifica, tra cui chiamata telefonica, SMS, smart card con PIN o notifica tramite app per dispositivi mobili. Per altre informazioni, vedere [Supporto SSMS per l'autenticazione MFA di Azure AD con il database SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

I passaggi di configurazione includono le procedure seguenti per configurare e usare l'autenticazione di Azure AD.

- Creare e popolare un'istanza di Azure AD.
- Facoltativo: associare o cambiare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure.
- Creare un amministratore di Azure Active Directory per il database SQL di Azure o per [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configurare i computer client.
- Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD.
- Connettersi al database usando le identità di Azure AD.

Per le informazioni dettagliate, vedere [Usare Azure Active Directory per l'autenticazione al database SQL, a Istanza gestita e a SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Proteggere i dati con la crittografia e la protezione a livello di riga
[Transparent Data Encryption del database SQL di Azure](../sql-database/transparent-data-encryption-azure-sql.md) aiuta a proteggere i dati su disco e protegge l'hardware da accessi non autorizzati. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. Transparent Data Encryption esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica denominata "chiave di crittografia del database".

Anche quando l'intera risorsa di archiviazione è crittografata è importante crittografare il database. Si tratta di un'implementazione della strategia di difesa approfondita per la protezione dei dati. Se si usa il database SQL di Azure e si vuole proteggere dati riservati, ad esempio il numero della carta di credito o il codice fiscale, è possibile crittografare i database con la crittografia AES a 256 bit con convalida FIPS 140-2. Questa crittografia soddisfa i requisiti di molti standard di settore, ad esempio HIPAA e PCI.

I file correlati all'[estensione del pool di buffer](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) non vengono crittografati quando si esegue la crittografia di un database tramite Transparent Data Encryption. Per questi file è necessario usare gli strumenti di crittografia a livello di file system come [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) o [Encrypting File System (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)).

Poiché un utente autorizzato, ad esempio un amministratore della sicurezza o un amministratore del database, può accedere ai dati anche se il database è crittografato con TDE, è consigliabile seguire anche le raccomandazioni seguenti:

- Abilitare l'autenticazione di SQL Server a livello di database.
- Usare l'autenticazione di Azure AD con i ruoli [Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md).
- Assicurarsi che gli utenti e le applicazioni usino account separati per l'autenticazione. In questo modo è possibile limitare le autorizzazioni concesse a utenti e applicazioni e ridurre il rischio di attività dannose.
- Implementare la sicurezza a livello di database usando ruoli predefiniti del database, ad esempio db_datareader o db_datawriter. È possibile in alternativa creare ruoli personalizzati per l'applicazione per concedere autorizzazioni esplicite su oggetti di database specifici.

Per altri modi di proteggere i dati, considerare quanto segue:

- [Crittografia a livello di cella](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) per crittografare colonne specifiche o anche celle di dati con chiavi di crittografia diverse.
- Funzionalità [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) che consente ai client di crittografare i dati sensibili contenuti nelle applicazioni client e di non rivelare mai le chiavi di crittografia al motore di database (database SQL o SQL Server). Di conseguenza, Always Encrypted consente di separare i proprietari dei dati (che possono visualizzarli) e le persone incaricate della gestione dei dati (che però non devono poter accedere ai dati).
- [Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) che consente ai clienti di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query. Esempi di caratteristiche sono l'appartenenza a un gruppo e il contesto di esecuzione.

Le organizzazioni che non usano la crittografia a livello di database sono potenzialmente più esposte ad attacchi che compromettono i dati presenti nei database SQL.

Per altre informazioni sulla crittografia TDE del database SQL, leggere l'articolo [Transparent Data Encryption con il database SQL di Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Abilitare il controllo del database
Il controllo di un'istanza del motore di database di SQL Server o di un singolo database comporta il rilevamento e la registrazione degli eventi. Per SQL Server, è possibile creare controlli che contengono specifiche per gli eventi a livello di server e specifiche per gli eventi a livello di database. Gli eventi controllati possono essere scritti nei log eventi o in file di controllo.

Esistono numerosi livelli di controllo per SQL Server, in base ai requisiti legislativi o standard per l'installazione. Il servizio di controllo di SQL Server include gli strumenti e i processi necessari per abilitare, archiviare e visualizzare controlli in vari oggetti del server e del database.

Il [servizio di controllo del database SQL di Azure](../sql-database/sql-database-auditing.md) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell'account di Archiviazione di Azure dell'utente.

Il servizio di controllo aiuta a mantenere la conformità alle normative, a comprendere l'attività del database e a individuare discrepanze e anomalie che potrebbero indicare problemi aziendali o violazioni della sicurezza. Il servizio di controllo aiuta a rispettare gli standard di conformità, ma non garantisce la conformità.

Per altre informazioni sul servizio di controllo del database e su come abilitarlo, vedere [Get started with SQL database auditing](../sql-database/sql-database-auditing.md) (Introduzione al servizio di controllo del database SQL).

## <a name="enable-database-threat-detection"></a>Abilitare il rilevamento delle minacce per il database
La protezione dalle minacce va oltre il rilevamento. La protezione del database dalle minacce include gli aspetti seguenti:

- Individuazione e classificazione dei dati più sensibili per poterli proteggere.
- Implementazione nel database di configurazioni protette che ne consentano la protezione.
- Rilevamento e risposta alle minacce potenziali appena si verificano in modo da poter implementare rapidamente una risposta e una correzione.

**Procedura consigliata**: individuare e classificare i dati sensibili presenti nei database e assegnare etichette a tali dati.   
**Dettagli**: classificare i dati nel database SQL abilitando la funzionalità [Individuazione dati e classificazione](../sql-database/sql-database-data-discovery-and-classification.md) nel database SQL di Azure. È possibile monitorare l'accesso ai dati sensibili nei report dei dashboard di Azure o nei report di download.

**Procedura consigliata**: tenere traccia delle vulnerabilità del database in modo da poter migliorare proattivamente la sicurezza del database.   
**Dettagli**: usare il servizio [Valutazione della vulnerabilità](../sql-database/sql-vulnerability-assessment.md) del database SQL di Azure che esegue la scansione delle potenziali vulnerabilità del database. Il servizio usa una knowledge base di regole che contrassegnano le vulnerabilità della sicurezza ed evidenziano le deviazioni dalle procedure consigliate, ad esempio errori di configurazione, autorizzazioni eccessive e dati sensibili non protetti.

Le regole si basano su procedure consigliate di Microsoft e si concentrano sui problemi di sicurezza che presentano maggiori rischi per il database e i preziosi dati in esso contenuti. Queste regole coprono sia i problemi a livello di database che quelli di sicurezza a livello di server, ad esempio le impostazioni del firewall del server e le autorizzazioni a livello di server. Tali regole rappresentano anche molti dei requisiti richiesti da organismi normativi per soddisfare i relativi standard di conformità.

**Procedura consigliata**: abilitare il rilevamento delle minacce.  
**Dettagli**:  abilitare la funzionalità [Rilevamento delle minacce](../sql-database/sql-database-threat-detection.md) del database SQL di Azure per ottenere avvisi di sicurezza e consigli su come indagare sulle minacce e attenuarle. Vengono visualizzati avvisi su attività di database sospette, potenziali vulnerabilità e attacchi SQL injection, nonché anomalie negli accessi al database e nei modelli di query.

[Advanced Threat Protection](../sql-database/sql-advanced-threat-protection.md) è un pacchetto unificato di funzionalità di sicurezza SQL avanzate. Include i servizi citati in precedenza: Individuazione dati e classificazione, Valutazione della vulnerabilità e Rilevamento delle minacce. Consente di abilitare e gestire queste funzionalità da un'unica posizione.

L'abilitazione di queste funzionalità consente di:

- Soddisfare gli standard e i requisiti di conformità alle normative sulla privacy dei dati.
- Controllare l'accesso ai database e rafforzare la sicurezza.
- Monitorare un ambiente di database dinamico in cui è difficile tenere traccia delle modifiche.
- Rilevare e rispondere a potenziali minacce.

Il rilevamento delle minacce integra inoltre gli avvisi con il Centro sicurezza di Azure per offrire un punto di vista centralizzato dello stato della sicurezza di tutte le risorse di Azure.

## <a name="enable-feature-restrictions"></a>Abilitare le restrizioni di funzionalità

I dati contenuti nei database possono essere esposti a utenti malintenzionati tramite gli attacchi che sfruttano gli errori di database e tempi di esecuzione di query. Database SQL di Azure fornisce una serie di meccanismi di restrizione di funzionalità per proteggere il database. Per altre informazioni, vedere [limitazioni delle caratteristiche del Database SQL](../sql-database/sql-database-feature-restrictions.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](security-best-practices-and-patterns.md).

Le risorse seguenti offrono altre informazioni più generali sulla sicurezza di Azure e sui servizi Microsoft correlati:
* [Blog del team di sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/): per informazioni aggiornate sulla sicurezza in Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): consente di segnalare le vulnerabilità della sicurezza di Microsoft, inclusi i problemi relativi ad Azure, tramite posta elettronica all'indirizzo secure@microsoft.com
