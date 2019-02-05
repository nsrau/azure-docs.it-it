---
title: Panoramica della sicurezza del database SQL di Azure | Microsoft Docs
description: In questo argomento vengono fornite informazioni sulla sicurezza del database SQL Azure e di SQL Server, incluse le differenze tra il cloud e SQL Server locale.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 01/29/2019
ms.openlocfilehash: 7eb3b115c1d16c2a5c380178d316a60b854e80df
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462019"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Panoramica della funzionalità di sicurezza del database SQL di Azure

Questo articolo illustra le informazioni di base relative alla protezione del livello dati di un'applicazione in cui viene usato il database SQL di Azure. La strategia di sicurezza descritta segue l'approccio a più livelli di difesa avanzata come illustrato nell'immagine seguente e si sposta dall'esterno in:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Sicurezza di rete

Il database SQL di Microsoft Azure assicura un servizio di database relazionale per le applicazioni cloud e aziendali. Per contribuire a proteggere i dati dei clienti, i firewall impediscono l'accesso alla rete per il server di database finché non viene esplicitamente concesso l'accesso basato su indirizzo IP o origine del traffico di rete virtuale di Azure.

### <a name="ip-firewall-rules"></a>Regole del firewall IP

Le regole del firewall IP concedono l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta. Per altre informazioni, vedere [Panoramica sulle regole del firewall per il database SQL e SQL Data Warehouse](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale

Gli [Endpoint di servizio della rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) estendono la connettività della rete virtuale tramite il backbone di Azure e il database SQL di Azure per identificare la subnet della rete virtuale che origina il traffico. Per consentire al traffico di raggiungere il database SQL di Azure, usare i [tag di servizio](../virtual-network/security-overview.md) SQL per consentire il traffico in uscita tramite gruppi di sicurezza di rete.

Le [regole della rete virtuale](sql-database-vnet-service-endpoint-rule-overview.md) consentono al database SQL di Azure di accettare solo le comunicazioni inviate dalle subnet all'interno di una rete virtuale.

> [!NOTE]
> Il controllo dell'accesso con regole del firewall *non* si applica a **Istanza gestita di database SQL di Azure**. Per altre informazioni sulla configurazione di rete necessaria, vedere [connessione a un'istanza gestita](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>gestione degli accessi

> [!IMPORTANT]
> La gestione dei database e dei server di database in Azure è controllata dalle assegnazioni di ruolo dell'account del portale utenti. Per altre informazioni su questo articolo, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

L'autenticazione è il processo atto a dimostrare che l'utente sia effettivamente chi dichiara di essere. Il database SQL di Azure supporta due tipi di autenticazione:

- **Autenticazione SQL**:

    L'autenticazione del database SQL si riferisce all'autenticazione di un utente durante la connessione al [database SQL di Azure](sql-database-technical-overview.md) tramite nome utente e password. Durante la creazione di server di database per il database, è necessario specificare un accesso "Amministratore del server" con un nome utente e password. Tramite queste credenziali, un "amministratore del server" può essere autenticato in qualsiasi database di tale server di database in qualità di proprietario del database. In seguito, l'amministratore del server può creare altri utenti e accessi SQL, che consentono agli utenti di connettersi tramite nome utente e password.

- **Autenticazione di Azure Active Directory**:

    L'autenticazione di Azure Active Directory è un meccanismo di connessione al [database SQL](sql-database-technical-overview.md) di Azure e a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tramite le identità di Azure Active Directory (Azure AD). L'autenticazione di Azure AD consente agli amministratori di gestire centralmente le identità e le autorizzazioni degli utenti del database oltre ad altri servizi Microsoft. Ciò include la riduzione dell'archiviazione di password e abilita i criteri centralizzati di rotazione delle password.

     Occorre creare un amministratore del server denominato **Amministratore di Active Directory Domain Services** per usare l'autenticazione di Azure AD con il database SQL. Per altre informazioni, vedere [Connessione al database SQL tramite l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md). L'autenticazione di Azure AD supporta sia gli account gestiti che quelli federati. Gli account federati supportano gli utenti di Windows e gruppi per un dominio utente federato con Azure AD.

    Altre opzioni di autenticazione di Azure AD disponibili sono le connessioni [Autenticazione universale di Active Directory per SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) che includono [Autenticazione a più fattori](../active-directory/authentication/concept-mfa-howitworks.md) e [ Accesso condizionale](sql-database-conditional-access.md).

> [!IMPORTANT]
> La gestione dei database e dei server in Azure è controllata dalle assegnazioni di ruolo dell'account del portale utenti. Per altre informazioni su questo articolo, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../role-based-access-control/overview.md). Il controllo dell'accesso con regole del firewall *non* si applica a **Istanza gestita di database SQL di Azure**. Vedere l'articolo seguente sulla [connessione a un'istanza gestita](sql-database-managed-instance-connect-app.md) per ulteriori informazioni sulla configurazione di rete necessaria.

L'autorizzazione fa riferimento alle autorizzazioni assegnate a un utente all'interno di un database SQL di Azure e determina le operazioni che l'utente è autorizzato a eseguire. Le autorizzazioni sono controllate mediante l'aggiunta degli account utente ai [ruoli del database](/sql/relational-databases/security/authentication-access/database-level-roles) che definiscono le autorizzazioni a livello di database o che consentono all'utente determinate [autorizzazioni a livello di oggetto](/sql/relational-databases/security/permissions-database-engine). Per altre informazioni, vedere [Accessi e utenti](sql-database-manage-logins.md)

Come procedura consigliata, aggiungere utenti al ruolo con i privilegi minimi necessari per svolgere le proprie funzioni. L'account amministratore del server è un membro del ruolo db_owner, che dispone delle autorizzazioni complete e deve essere concesso agli utenti con cautela. Quando si usano le applicazioni con il database SQL di Azure, usare i [Ruoli applicazione](/sql/relational-databases/security/authentication-access/application-roles) con autorizzazioni limitate. Ciò assicura che l'applicazione che si connette al database disponga dei privilegi minimi necessari per l'applicazione.

### <a name="row-level-security"></a>Sicurezza a livello di riga

La sicurezza a livello di riga consente ai clienti di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione. Per altre informazioni, vedere [Sicurezza a livello di riga](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

  Questo metodo di autenticazione usa nome utente e password. 

Per una panoramica di autorizzazioni nel database SQL di Azure, vedere [Accessi e utenti](sql-database-manage-logins.md#permissions)

## <a name="threat-protection"></a>Protezione dalle minacce

Il database SQL protegge i dati dei clienti fornendo funzionalità di controllo e di rilevamento delle minacce.

### <a name="sql-auditing-in-log-analytics-and-event-hubs"></a>Controllo di SQL in Log Analytics e Hub eventi

Il servizio di controllo del database SQL tiene traccia delle attività del database e consente di garantire la conformità alle normative di sicurezza tramite la registrazione di eventi del database in un log di controllo nell'account di archiviazione di Azure, di proprietà del cliente. Il servizio di controllo consente agli utenti di monitorare le attività del database in corso e di analizzare ed esaminare l'attività cronologica per identificare potenziali minacce o uso improprio sospetto e violazioni della sicurezza. Per altre informazioni, vedere [Introduzione al controllo del database SQL](sql-database-auditing.md).  

### <a name="sql-threat-detection"></a>Rilevamento delle minacce a SQL

Il rilevamento delle minacce migliora il controllo analizzando i log di controllo in cerca di comportamenti anomali e potenzialmente dannosi di accesso o sfruttamento dei database. Gli avvisi vengono creati per le attività sospette o i criteri di accesso anomali, ad esempio attacchi SQL injection, potenziali infiltrazioni di dati e attacchi di forza bruta alle password. Gli avvisi di rilevamento delle minacce vengono visualizzati nel [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), in cui vengono forniti i dettagli delle attività sospette e consigli per altre indagini insieme alle azioni da intraprendere per ridurre la minaccia. La funzionalità Rilevamento delle minacce ha un costo di $15/server/mese È gratuita per i primi 60 giorni. Per altre informazioni, vedere l' [Introduzione al rilevamento delle minacce nel database SQL](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Crittografia e protezione delle informazioni

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security (TLS - crittografia in transito)

Il database SQL protegge i dati del cliente mediante la crittografia dei dati in movimento avvalendosi di [Transport Layer Security](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).

> [!IMPORTANT]
> Database SQL di Azure applica la crittografia (SSL/TLS) in qualsiasi caso per tutte le connessioni, in modo da garantire che i dati vengano crittografati quando sono in transito tra il database e il client. Questo avviene indipendentemente dall'impostazione di **Encrypt** o di **TrustServerCertificate** nella stringa di connessione.
>
> Nella stringa di connessione dell'applicazione assicurarsi di specificare una connessione crittografata e di _non_ considerare attendibile il certificato del server. Per il driver ADO.NET corrisponde a **Encrypt=True** e **TrustServerCertificate=False**. In questo modo si impediscono attacchi di tipo man-in-the-middle, forzando l'applicazione a verificare il server e ad applicare la crittografia. Se si ottiene la stringa di connessione dal portale di Azure, le impostazioni saranno corrette.
>
> Per informazioni su TLS e sulla connettività, vedere [Considerazioni su TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (TDE - crittografia inattiva)

[Transparent Data Encryption (TDE) per il database SQL di Azure](transparent-data-encryption-azure-sql.md) aggiunge un livello di sicurezza per proteggere i dati inattivi da accessi non autorizzati o non in linea per i file non elaborati o i backup. Gli scenari comuni includono il furto in data center o eliminazioni non protette di hardware o supporti, ad esempio unità disco e nastri di backup. TDE crittografa l'intero database usando un algoritmo di crittografia AES, che non richiede agli sviluppatori di applicazioni di apportare modifiche alle applicazioni esistenti.

In Azure, tutti i nuovi database SQL vengono crittografati per impostazione predefinita e la chiave di crittografia del database è protetta da un certificato del server incorporato.  La manutenzione e la rotazione del certificato sono gestite dal servizio e non richiedono alcun intervento da parte dell'utente. I clienti che preferiscono assumere il controllo delle chiavi di crittografia possono gestire le chiavi presenti in [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Gestione delle chiavi con Azure Key Vault

Il supporto [Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) per  [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) consente ai clienti di appropriarsi della gestione e rotazione delle chiavi tramite  [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), il sistema di gestione delle chiavi esterne di Azure basato sul cloud. Se viene revocato l'accesso del database per l'insieme di credenziali delle chiavi, il database non può essere decrittografato e letto in memoria. Azure Key Vault offre una piattaforma di gestione centralizzata delle chiavi, sfrutta i moduli di protezione hardware (HSM) accuratamente monitorati e consente la separazione dei compiti tra la gestione delle chiavi e i dati per contribuire a rispettare i requisiti di conformità alle norme di sicurezza.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (crittografia in uso)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) è una funzionalità progettata per proteggere da accessi i dati sensibili archiviati in colonne specifiche del database (ad esempio, i numeri delle carte di credito o i numeri di identificazione nazionale o dati sulla base della _necessità di conoscere_). Sono inclusi gli amministratori del database o altri utenti con privilegi che sono autorizzati ad accedere al database per eseguire attività di gestione, ma che non hanno esigenza di accedere a dati particolari nelle colonne crittografate. I dati vengono sempre crittografati, ossia i dati crittografati vengono decrittografati solo per l'elaborazione da parte di applicazioni client con accesso alla chiave di crittografia.  La chiave di crittografia non viene mai esposta a SQL e può essere archiviata sia nell'[Archivio certificati di Windows](sql-database-always-encrypted.md) sia in [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="masking"></a>Maschera

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

#### <a name="dynamic-data-masking"></a>Maschera dati dinamica

La funzione Maschera dati dinamica del database SQL limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. La maschera dati dinamica individua automaticamente dati potenzialmente sensibili nel database SQL di Azure e fornisce indicazioni pratiche per il mascheramento di questi campi, con un impatto minimo sul livello dell'applicazione. Questa funzionalità si basa sull'offuscamento dei dati sensibili nel set dei risultati di una query su campi designati del database, mentre i dati del database non subiscono modifiche. Per altre informazioni, vedere [Introduzione alla maschera dati dinamica del database SQL](sql-database-dynamic-data-masking-get-started.md).

#### <a name="static-data-masking"></a>Maschera dati statica

La [Maschera dati statica](/sql/relational-databases/security/static-data-masking) è uno strumento lato client disponibile in [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 18.0 anteprima 5 e versioni successive.  La Maschera dati statica consente agli utenti di creare una copia di un database in cui i dati nelle colonne selezionate sono stati mascherati in modo permanente. Le funzioni di maschera disponibili includono maschera NULL, maschera di valore singolo, maschera selezionata in ordine casuale o raggruppata in ordine casuale e stringa di mascheramento composito. Con la copia mascherata del database, le organizzazioni sono in grado di separare l'ambiente di produzione dagli ambienti di test tramite la condivisione della copia mascherata. I dati sensibili sono sufficientemente protetti e sono state mantenute tutte le altre caratteristiche del database. I database di mascheramento sono consigliabili quando terze parti devono accedere al database.

## <a name="security-management"></a>Gestione della sicurezza

### <a name="sql-vulnerability-assessment"></a>Valutazione della vulnerabilità di SQL

[Valutazione della vulnerabilità di SQL](sql-vulnerability-assessment.md) è un servizio semplice da configurare che consente di individuare, monitorare e risolvere potenziali vulnerabilità del database, con l'obiettivo di migliorare in modo proattivo la sicurezza generale del database. La Valutazione della vulnerabilità (VA) fa parte dell'offerta Sicurezza dei dati avanzata (ADS) di SQL, un pacchetto unificato per le funzionalità di sicurezza avanzate di SQL. È possibile accedere e gestire Valutazione della vulnerabilità tramite il portale centrale di Sicurezza dei dati avanzata di SQL.

### <a name="data-discovery--classification"></a>Individuazione dati e classificazione

Individuazione dati e classificazione (attualmente in anteprima) offre funzionalità avanzate incorporate nel database SQL di Azure per l'individuazione, la classificazione e la protezione dei dati sensibili presenti nel database, nonché per l'aggiunta di etichette a tali dati. L'individuazione e la classificazione dei dati più sensibili (dati commerciali e finanziari, dati relativi all'assistenza sanitaria, informazioni personali e così via) possono svolgere un ruolo fondamentale per il livello di protezione delle informazioni aziendali. Individuazione dati e classificazione può svolgere la funzione di infrastruttura per:

- Vari scenari di sicurezza, ad esempio monitoraggio (controllo) e invio di avvisi sulle anomalie di accesso a dati sensibili.
- Controllare l'accesso ai database che contengono dati molto sensibili e rafforzarne la sicurezza.
- Contribuire a soddisfare gli standard e i requisiti di conformità alle normative sulla privacy dei dati.

Per altre informazioni, vedere [Get started with SQL DB Data Discovery & Classification](sql-database-data-discovery-and-classification.md) (Introduzione a Individuazione dati e classificazione dei database SQL).

### <a name="compliance"></a>Conformità

Oltre alle caratteristiche e alle funzionalità sopra descritte, che consentono all'applicazione di soddisfare vari requisiti di sicurezza, il database SQL di Azure è inoltre sottoposto a controlli regolari ed ha ottenuto la certificazione per diversi standard di conformità. Per ulteriori informazioni, vedere il [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/), dove è possibile trovare l'elenco più recente di [certificazioni di conformità del database SQL](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'uso delle funzionalità di controllo dell'accesso nel database SQL, vedere [Controllo dell'accesso](sql-database-control-access.md).
- Per informazioni sul controllo del database, vedere [SQL Database auditing](sql-database-auditing.md) (Controllo del database SQL).
- Per informazioni sul rilevamento delle minacce, vedere [SQL Database threat detection](sql-database-threat-detection.md) (Rilevamento delle minacce nel database SQL).
