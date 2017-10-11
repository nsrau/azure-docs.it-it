---
title: Panoramica della sicurezza del database di Azure | Microsoft Docs
description: "Questo articolo offre una panoramica delle funzionalità di sicurezza del database di Azure."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: TomSh
ms.openlocfilehash: 036ce3dce28e7951bb39754c4351661fae85f06c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="azure-database-security-overview"></a>Panoramica della sicurezza del database di Azure

La sicurezza è un tema della massima importanza per la gestione dei database ed è sempre stata una priorità per il database SQL di Azure. Il database SQL di Azure supporta la sicurezza della connessione con regole del firewall e crittografia delle connessioni. Supporta anche l'autenticazione con nome utente e password e l'autenticazione di Azure Active Directory, che usa identità gestite da Azure Active Directory. Per l'autorizzazione viene usato il controllo degli accessi in base al ruolo.

Il database SQL di Azure supporta la crittografia eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza dover apportare modifiche all'applicazione.

Microsoft offre altri modi per crittografare i dati aziendali:

-   Crittografia a livello di cella per crittografare colonne specifiche o anche celle di dati con chiavi di crittografia diverse.
-   Se è necessario un modulo di sicurezza hardware o la gestione centralizzata della gerarchia di chiavi di crittografia, prendere in considerazione l'uso di Azure Key Vault con SQL Server in una macchina virtuale di Azure.
-   La funzionalità Always Encrypted (attualmente in anteprima) gestisce la crittografia in modo trasparente per le applicazioni e consente ai client di crittografare dati sensibili all'interno delle applicazioni client senza condividere le chiavi di crittografia con il database SQL.

Il servizio di controllo del database SQL di Azure consente alle aziende di registrare gli eventi in un log di controllo in Archiviazione di Azure. La funzionalità di controllo del database SQL si integra inoltre con Microsoft Power BI per facilitare l'esecuzione di analisi e report drill-down.

 I database SQL di Azure possono essere protetti in modo sicuro per soddisfare la maggior parte dei requisiti di legge e di sicurezza, tra i quali HIPAA, ISO 27001/27002 e PCI DSS livello 1. Un elenco aggiornato delle certificazioni di conformità di sicurezza è disponibile nel [sito Centro protezione di Microsoft Azure](http://azure.microsoft.com/support/trust-center/services/).

Questo articolo presenta le informazioni di base relative alla protezione dei database SQL di Microsoft Azure per i dati strutturati, tabulari e relazionali. In particolare, questo articolo consente di iniziare a usare le risorse per la protezione dei dati, il controllo dell'accesso e il monitoraggio proattivo.

Questa panoramica della sicurezza del database di Azure è incentrata sugli argomenti seguenti:

-   Proteggere i dati
-   Controllo di accesso
-   Monitoraggio proattivo
-   Gestione centralizzata della sicurezza
-   Azure Marketplace

## <a name="protect-data"></a>Proteggere i dati

Il database SQL protegge i dati in movimento con [Transport Layer Security](https://support.microsoft.com/kb/3135244) (TLS), i dati inattivi con [Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242) (TDE) e i dati in uso con [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Argomenti trattati in questa sezione:

-   Crittografia di dati in movimento
-   Crittografia di dati inattivi
-   Crittografia di dati in uso (client)

Per altri modi di crittografare i dati, considerare quanto segue:

-   [Crittografia a livello di cella](https://msdn.microsoft.com/library/ms179331.aspx) per crittografare colonne specifiche o anche celle di dati con chiavi di crittografia diverse.
-   Se è necessario un modulo di sicurezza Hardware o la gestione centralizzata della gerarchia di chiavi di crittografia, è consigliabile utilizzare l' [insieme di credenziali chiave di Azure con SQL Server in una relativa macchina virtuale](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="encryption-in-motion"></a>Crittografia di dati in movimento

Un problema comune per tutte le applicazioni client/server è la necessità di tutelare la privacy dei dati durante lo spostamento su reti pubbliche e private. Se i dati trasferiti in rete non sono crittografati, sussiste il rischio di acquisizione e furto da parte di utenti non autorizzati. Quando si gestiscono i servizi di database, è necessario assicurarsi che i dati vengano crittografati tra il client del database e il server, così come tra i server di database che comunicano tra loro e con applicazioni di livello intermedio.

Un problema quando si amministra una rete riguarda la protezione dei dati inviati tra le applicazioni in una rete non attendibile. È possibile usare [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) per autenticare server e client e quindi usare questa tecnologia per crittografare i messaggi tra le entità autenticate.

Nel processo di autenticazione, un client TLS/SSL invia un messaggio a un server TLS/SSL e il server risponde con le informazioni necessarie per autenticare se stesso. Il client e il server eseguono un ulteriore scambio di chiavi di sessione e le comunicazioni di autenticazione terminano. Al termine dell'autenticazione, può iniziare la comunicazione protetta con SSL tra il server e il client usando le chiavi di crittografia simmetrica stabilite durante il processo di autenticazione.

Tutte le connessioni al database SQL di Azure richiedono la crittografia SSL/TLS (SSL/TLS) in qualsiasi caso quando i dati sono "in transito" da e verso il database in qualsiasi momento. SQL Azure usa TLS/SSL per autenticare server e client, quindi usa questa tecnologia per crittografare i messaggi tra le entità autenticate. Nella stringa di connessione dell'applicazione, è necessario specificare parametri per crittografare la connessione e non considerare attendibile il certificato del server (ciò avviene automaticamente se si copia la stringa di connessione dal portale di Azure classico), in caso contrario durante le connessioni non verrà verificata l'identità del server e saranno possibili attacchi "man-in-the-middle". Per il driver ADO.NET, ad esempio, questi parametri della stringa di connessione sono Encrypt=True e TrustServerCertificate=False.

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi
È possibile adottare diverse precauzioni per proteggere il database, ad esempio la progettazione di un sistema sicuro, la crittografia di asset riservati e la creazione di un firewall che protegga i server di database. Tuttavia, nell'ipotesi del furto dei supporti fisici, come unità o nastri di backup, un malintenzionato potrebbe semplicemente ripristinare o collegare il database e accedere ai dati in esso contenuti.

Una soluzione consiste nel crittografare i dati sensibili nel database e proteggere con un certificato le chiavi usate per crittografarli. In questo modo, senza disporre delle chiavi nessuno potrà usare i dati. Tuttavia, questo tipo di protezione deve essere pianificato.

Per risolvere questo problema, SQL Server e SQL Azure supportano [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/securityrecryption/transparent-data-encryption-tde). TDE crittografa i file di dati di SQL Server e del database SQL di Azure, noti come dati di crittografia inattivi.

La funzionalità Transparent Data Encryption (TDE) del database SQL di Azure consente di proteggere il sistema da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza dover apportare modifiche all'applicazione.  

TDE esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica detta "chiave di crittografia del database". Nel database SQL la chiave di crittografia del database è protetta da un certificato del server incorporato. Il certificato server incorporato è univoco per ogni server di database SQL.

Se un database partecipa a una relazione GeoDR, è protetto da una chiave diversa in ogni server. Se due database sono connessi allo stesso server, condividono lo stesso certificato predefinito. Microsoft ruota automaticamente questi certificati almeno ogni 90 giorni. Per una descrizione generale della funzionalità TDE, vedere [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Crittografia di dati in uso (client)

La maggior parte delle violazioni di dati comportano il furto di dati critici, ad esempio numeri di carta di credito o informazioni personali. I database possono essere veri e propri forzieri di informazioni sensibili e contenere dati personali dei clienti, informazioni riservate sulla concorrenza e dati di proprietà intellettuale. I dati persi o rubati, in particolare i dati dei clienti, possono causare danni di immagine, svantaggi competitivi, sanzioni e persino problemi legali.

![Always Encrypted](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) è una funzionalità progettata per proteggere i dati sensibili, ad esempio i numeri di carta di credito o i numeri di identificazione nazionale, come i codici fiscali, archiviati nei database SQL di Azure o nei database di SQL Server. La funzionalità Always Encrypted consente ai client di crittografare i dati sensibili nelle applicazioni client e di non rivelare mai le chiavi di crittografia al motore di database (database SQL o SQL Server).

Always Encrypted consente di separare i proprietari dei dati (che possono visualizzarli) e le persone incaricate della gestione dei dati (che però non devono poter accedere ai dati). Lo scopo è assicurare che gli amministratori di database locali, gli operatori di database cloud o altri utenti con privilegi elevati, ma non autorizzati, non possano accedere ai dati crittografati.

Inoltre, con Always Encrypted la crittografia è trasparente per le applicazioni. Un driver abilitato per Always Encrypted viene installato nel computer client in modo che possa automaticamente crittografare e decrittografare i dati sensibili nell'applicazione client. Il driver crittografa i dati nelle colonne sensibili prima di passarli al motore di database e riscrive automaticamente le query in modo da mantenere la semantica per l'applicazione. Analogamente, il driver decrittografa in modo trasparente i dati, archiviati nelle colonne di database crittografate, contenuti nei risultati delle query.

## <a name="access-control"></a>Controllo di accesso
Per garantire la sicurezza, il database SQL controlla l'accesso con regole del firewall che limitano la connettività in base all'indirizzo IP, meccanismi di autenticazione che richiedono agli utenti di dimostrare la propria identità e meccanismi di autorizzazione che consentono agli utenti di usufruire solo di azioni e dati specifici.

### <a name="database-access"></a>Accesso al database

La protezione dei dati inizia con il controllo dell'accesso ai dati. Il data center che ospita i dati gestisce l'accesso fisico, sebbene sia possibile configurare un firewall per gestire la sicurezza a livello di rete. È anche possibile controllare l'accesso configurando gli account di accesso per l'autenticazione e definendo le autorizzazioni per i ruoli del server e del database.

Argomenti trattati in questa sezione:

-   Firewall e regole del firewall
-   Autenticazione
-   Authorization

#### <a name="firewall-and-firewall-rules"></a>Firewall e regole del firewall

Il database SQL di Microsoft Azure fornisce un servizio di database relazionale per Azure e altre applicazioni basate su Internet. Per proteggere i dati, il firewall impedisce qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta. Per altre informazioni, vedere [Panoramica sulle regole del firewall per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Il servizio [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) è disponibile solo tramite la porta TCP 1433. Per accedere al database SQL dal computer, assicurarsi che il firewall del computer client consenta la comunicazione TCP in uscita sulla porta TCP 1433. Se non sono necessarie per altre applicazioni, bloccare le connessioni in ingresso sulla porta TCP 1433.

#### <a name="authentication"></a>Autenticazione

Per autenticazione del database SQL si intende il modo in cui viene dimostrata la propria identità durante la connessione al database. Il database SQL supporta due tipi di autenticazione:

-   **Autenticazione SQL:** quando viene creata un'istanza logica di SQL, viene creato un singolo account di accesso, denominato account sottoscrittore del database SQL. Tale account, che effettua la connessione con l'[autenticazione di SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nome utente e password), è un amministratore nell'istanza logica del server e in tutti i database utente collegati a tale istanza. Le autorizzazioni dell'account sottoscrittore non possono essere soggette a restrizioni. Può esistere un solo account di questo tipo.
-   **Autenticazione di Azure Active Directory:** l'[autenticazione di Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) è un meccanismo di connessione al database SQL di Microsoft Azure e ad Azure SQL Data Warehouse tramite le identità di Azure Active Directory (Azure AD). Ciò consente di gestire in modo centralizzato le identità degli utenti del database.

![Autenticazione](./media/azure-databse-security-overview/azure-database-fig2.png)

 I vantaggi dell'autenticazione di Azure Active Directory includono:
  - Offre un'alternativa all'autenticazione di SQL Server.
  - Contribuisce anche ad arrestare la proliferazione di identità utente tra i server di database e consente la rotazione delle password in un'unica posizione.
  - È possibile gestire le autorizzazioni del database tramite gruppi (Azure Active Directory) esterni.
  - Può eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.

#### <a name="authorization"></a>Authorization
Per [autorizzazione](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) si intendono le operazioni che l'utente può eseguire in un database SQL di Azure, che sono controllate dalle [appartenenze ai ruoli](https://msdn.microsoft.com/library/ms189121) del database e dalle [autorizzazioni a livello di oggetto](https://msdn.microsoft.com/library/ms191291.aspx) dell'account utente. L'autorizzazione è il processo volto a determinare le risorse a protezione diretta accessibili per un'entità di sicurezza e quali operazioni sono consentite per le risorse.

### <a name="application-access"></a>Accesso all'applicazione

Argomenti trattati in questa sezione:

-   Maschera dati dinamica
-   Sicurezza a livello di riga

#### <a name="dynamic-data-masking"></a>Maschera dati dinamica
Un addetto all'assistenza in un call center può identificare i chiamanti da alcune cifre del codice fiscale o del numero di carta di credito, ma tali elementi di dati non devono essere completamente visibili all'addetto.

È possibile definire una regola di maschera che renda visibili solo le ultime quattro cifre del codice fiscale o del numero di carta di credito nel set di risultati di tutte le query.

![Maschera dati dinamica](./media/azure-databse-security-overview/azure-database-fig3.png)

Oppure, è possibile definire una maschera dati appropriata per la protezione di informazioni personali identificabili (PII), in modo che uno sviluppatore possa eseguire una query negli ambienti di produzione a scopi di risoluzione dei problemi senza violare le normative di conformità.

La funzione [Maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. La maschera dati dinamica è supportata per la versione 12 del database SQL di Azure.

La [maschera dati dinamica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) impedisce l'accesso non autorizzato ai dati sensibili consentendo di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi del database designati, senza alcuna modifica dei dati contenuti nel database.


> [!Note]
> Il mascheramento dei dati dinamici può essere configurato dall'amministratore del database di Azure, dall'amministratore del server o dal responsabile della sicurezza.

#### <a name="row-level-security"></a>Sicurezza a livello di riga
Un altro requisito di sicurezza comune per i database multi-tenant è la [sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131.aspx). Questa funzionalità consente di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione.

![Sicurezza a livello di riga](./media/azure-databse-security-overview/azure-database-fig4.png)

La logica di restrizione dell'accesso si trova nel livello database e non lontano dai dati in un altro livello applicazione. Il sistema di database applica le restrizioni di accesso a ogni tentativo di accesso ai dati da qualsiasi livello. Il sistema di sicurezza è così più affidabile e solido, grazie alla riduzione della superficie di attacco del sistema di sicurezza.

La sicurezza a livello di riga introduce il controllo di accesso basato su predicato. Questa funzionalità offre una valutazione flessibile e centralizzata basata su predicato che può prendere in considerazione i metadati o qualsiasi altro criterio ritenuto appropriato dall'amministratore. Il predicato viene usato come criterio per determinare se l'utente dispone o meno dell'accesso appropriato ai dati in base agli attributi utente. Il controllo di accesso basato su etichetta può essere implementato usando il controllo di accesso basato su predicato.

## <a name="proactive-monitoring"></a>Monitoraggio proattivo
Il database SQL protegge i dati fornendo funzionalità di **controllo** e di **rilevamento delle minacce**.

### <a name="auditing"></a>Controllo
Il servizio di controllo del database SQL di Azure aumenta la possibilità di ottenere informazioni dettagliate sugli eventi e le modifiche apportate all'interno del database, inclusi gli aggiornamenti e le query sui dati.

Il servizio di [controllo del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell'account di Archiviazione di Azure dell'utente. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza. Il controllo abilita e facilita il rispetto degli standard di conformità, ma non garantisce la conformità.

Il controllo del database SQL consente di:

-   **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
-   **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l’attività e la segnalazione di eventi.
-   **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

Esistono due metodi di controllo:

-   **Controllo BLOB**: i log vengono scritti nell'Archiviazione BLOB di Azure. Questo è un metodo di controllo più recente che fornisce prestazioni più elevate, supporta una maggiore granularità del controllo a livello di oggetto ed è più conveniente.
-   **Controllo Tabella**: i log vengono scritti nell'archiviazione tabelle di Azure.

### <a name="threat-detection"></a>Introduzione al rilevamento delle minacce
La funzionalità di [rilevamento delle minacce del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) rileva attività sospette che indicano potenziali minacce alla sicurezza. La funzionalità di rilevamento delle minacce consente di rispondere a eventi sospetti nel database, ad esempio attacchi SQL injection, non appena si verificano. Fornisce avvisi e consente di usare il servizio di controllo del database SQL di Azure per esaminare gli eventi sospetti.

![Rilevamento delle minacce](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Ad esempio, l'attacco SQL injection è uno dei problemi di sicurezza comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni guidate dai dati. Gli autori degli attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, con lo scopo di violare o modificare i dati del database.

I responsabili della sicurezza o altri amministratori designati possono ricevere una notifica immediata sulle attività di database sospette non appena si verificano. Ogni notifica contiene dettagli sulle attività sospette e consigli su come eseguire ulteriori indagini e mitigare la minaccia.        

## <a name="centralized-security-management"></a>Gestione centralizzata della sicurezza

Il [Centro sicurezza di Azure](https://azure.microsoft.com/documentation/services/security-center/) consente di impedire, rilevare e gestire le minacce. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Il [Centro sicurezza](https://docs.microsoft.com/azure/security-center/security-center-sql-database) consente di proteggere i dati nel database SQL fornendo visibilità per la sicurezza di tutti i server e database. Con il Centro sicurezza è possibile:

-   Definire i criteri per la crittografia e il controllo del database SQL.
-   Monitorare la sicurezza delle risorse del database SQL per tutte le sottoscrizioni.
-   Identificare e correggere rapidamente i problemi di sicurezza.
-   Integrare gli avvisi generati dal [rilevamento delle minacce del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
-   Il Centro sicurezza supporta l'accesso in base al ruolo.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace è un marketplace online di applicazioni e servizi che consente a startup e fornitori di software indipendenti (ISV) di offrire le proprie soluzioni a clienti di Azure in tutto il mondo.
Azure Marketplace riunisce gli ecosistemi dei partner di Microsoft Azure in una singola piattaforma unificata per offrire un servizio migliore a clienti e partner. Fare clic su [qui](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) per informazioni sui prodotti di sicurezza del database disponibili in Azure Marketplace.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [protezione del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).
- Altre informazioni sul [Centro sicurezza di Azure e il servizio database SQL di Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-database).
- Per altre informazioni sul rilevamento delle minacce, vedere [Rilevamento delle minacce nel database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).
- Per altre informazioni, vedere [Migliorare le prestazioni del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial). 
