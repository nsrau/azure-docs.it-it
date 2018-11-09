---
title: Panoramica della sicurezza del database di Azure | Microsoft Docs
description: Questo articolo offre una panoramica delle funzionalità di sicurezza del database di Azure.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: a61f3572037b1c62ea5ed4e0ac4496b057e2b96d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249052"
---
# <a name="azure-database-security-overview"></a>Panoramica della sicurezza del database di Azure

La sicurezza è un tema della massima importanza per la gestione dei database ed è sempre stata una priorità per il database SQL di Azure. Il database SQL di Azure supporta la sicurezza della connessione con regole del firewall e crittografia delle connessioni. Il database supporta l'autenticazione con nome utente e password e l'autenticazione di Azure Active Directory (Azure AD), che usa identità gestite da Azure Active Directory. Per l'autorizzazione viene usato il controllo degli accessi in base al ruolo.

Il database SQL di Azure supporta la crittografia eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza dover apportare modifiche all'applicazione.

Microsoft offre altri modi per crittografare i dati aziendali:

-   La crittografia a livello di cella consente di crittografare colonne specifiche o anche celle di dati con chiavi di crittografia diverse.
-   Se si ha la necessità di un modulo di protezione hardware o di una gestione centralizzata della gerarchia di chiavi di crittografia, prendere in considerazione l'uso di Azure Key Vault con SQL Server in una macchina virtuale di Azure.
-   La funzionalità Always Encrypted (attualmente in anteprima) gestisce la crittografia in modo trasparente per le applicazioni. Consente inoltre ai client di crittografare dati sensibili all'interno delle applicazioni client senza condividere le chiavi di crittografia con il database SQL.

Il servizio di controllo del database SQL di Azure consente alle aziende di registrare gli eventi in un log di controllo in Archiviazione di Azure. La funzionalità di controllo del database SQL si integra inoltre con Microsoft Power BI per facilitare l'esecuzione di analisi e report drill-down.

I database SQL di Azure possono essere protetti in modo sicuro per soddisfare la maggior parte dei requisiti di legge e di sicurezza, inclusi HIPAA, ISO 27001/27002 e PCI DSS livello 1. Un elenco aggiornato delle certificazioni di conformità di sicurezza è disponibile nel [sito Centro protezione di Microsoft Azure](http://azure.microsoft.com/support/trust-center/services/).

Questo articolo presenta le informazioni di base relative alla protezione dei database SQL di Microsoft Azure per i dati strutturati, tabulari e relazionali. In particolare, questo articolo consente di iniziare a usare le risorse per la protezione dei dati, il controllo dell'accesso e il monitoraggio proattivo.

## <a name="protection-of-data"></a>Protezione dei dati

Il database SQL consente di proteggere i dati rendendo disponibile la crittografia:

- Per i dati in movimento tramite [Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244).
- Per i dati inattivi tramite [Transparent Data Encryption](http://go.microsoft.com/fwlink/?LinkId=526242).
- Per i dati in uso tramite [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Per altri modi di crittografare i dati, considerare quanto segue:

-   [Crittografia a livello di cella](https://msdn.microsoft.com/library/ms179331.aspx) per crittografare colonne specifiche o anche celle di dati con chiavi di crittografia diverse.
-   [Azure Key Vault con SQL Server in una macchina virtuale di Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), se si ha la necessità di un modulo di protezione hardware o di una gestione centralizzata della gerarchia di chiavi di crittografia.

### <a name="encryption-in-motion"></a>Crittografia di dati in movimento

Un problema comune per tutte le applicazioni client/server è la necessità di tutelare la privacy dei dati durante lo spostamento su reti pubbliche e private. Se i dati trasferiti in rete non sono crittografati, sussiste il rischio di acquisizione e furto da parte di utenti non autorizzati. Quando si usano servizi di database, verificare che tra il client e il server di database i dati vengano crittografati. Verificare che i dati siano crittografati anche tra i server di database che comunicano tra loro e con applicazioni di livello intermedio.

Quando si amministra una rete, un problema riguarda la protezione dei dati inviati da un'applicazione a un'altra in una rete non attendibile. È possibile usare [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) per autenticare server e client e quindi usare questa tecnologia per crittografare i messaggi tra le entità autenticate.

Nel processo di autenticazione un client TLS/SSL invia un messaggio a un server TLS/SSL. Il server risponde con le informazioni necessarie per l'autenticazione. Il client e il server eseguono un ulteriore scambio di chiavi di sessione e le comunicazioni di autenticazione terminano. Al termine dell'autenticazione, può iniziare la comunicazione protetta con SSL tra il server e il client tramite le chiavi di crittografia simmetrica stabilite durante il processo di autenticazione.

Tutte le connessioni al database SQL di Azure richiedono sempre la crittografia (TLS/SSL) quando i dati sono "in transito" da e verso il database. Il database SQL usa TLS/SSL per autenticare server e client, quindi usa questa tecnologia per crittografare i messaggi tra le entità autenticate. 

Nella stringa di connessione dell'applicazione è necessario specificare i parametri per crittografare la connessione e non considerare attendibile il certificato del server. Questa operazione viene eseguita automaticamente se si copia la stringa di connessione dal portale di Azure. In caso contrario, la connessione non verificherà l'identità del server e sarà esposta ad attacchi "man-in-the-middle". Per il driver ADO.NET, ad esempio, questi parametri della stringa di connessione sono `Encrypt=True` e `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

È possibile adottare diverse precauzioni per proteggere il database. Ad esempio, la progettazione di un sistema sicuro, la crittografia di asset riservati e la creazione di un firewall che protegga i server di database. Ma nell'ipotesi del furto dei supporti fisici, come unità o nastri di backup, un malintenzionato potrebbe semplicemente ripristinare o collegare il database e accedere ai dati in esso contenuti.

Una soluzione consiste nel crittografare i dati riservati nel database e proteggere con un certificato le chiavi utilizzate per crittografarli. Con questa soluzione, senza disporre delle chiavi nessuno potrà usare i dati. Tuttavia, questo tipo di protezione deve essere pianificato.

Per risolvere questo problema, SQL Server e il database SQL supportano la funzionalità [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Transparent Data Encryption crittografa i file di dati di SQL Server e del database SQL, noti come dati di crittografia inattivi.

La funzionalità Transparent Data Encryption consente di proteggersi dalla minaccia di attività dannose. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione.  

Transparent Data Encryption esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica denominata "chiave di crittografia del database". Nel database SQL la chiave di crittografia del database è protetta da un certificato del server incorporato. Il certificato server incorporato è univoco per ogni server di database SQL.

Se un database partecipa a una relazione di ripristino di emergenza geografico, è protetto da una chiave diversa in ogni server. Se due database sono connessi allo stesso server, condividono lo stesso certificato predefinito. Microsoft ruota automaticamente questi certificati almeno ogni 90 giorni. 

Per altre informazioni, vedere [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Crittografia di dati in uso (client)

La maggior parte delle violazioni di dati comportano il furto di dati critici, ad esempio numeri di carta di credito o informazioni personali. I database possono essere veri e propri forzieri di informazioni sensibili e contenere dati personali dei clienti (come i numeri di carta di identità), informazioni riservate sulla concorrenza e dati di proprietà intellettuale. I dati persi o rubati, in particolare i dati dei clienti, possono causare danni di immagine, svantaggi competitivi, sanzioni e persino problemi legali.

![Funzionalità Always Encrypted illustrata con un lucchetto e una chiave](./media/azure-databse-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) è una funzionalità progettata per proteggere i dati sensibili archiviati nel database SQL di Azure o nei database di SQL Server. La funzionalità Always Encrypted consente ai client di crittografare i dati sensibili contenuti nelle applicazioni client e di non rivelare mai le chiavi di crittografia al motore di database (database SQL o SQL Server).

Always Encrypted consente di separare i proprietari dei dati (che possono visualizzarli) dalle persone incaricate della loro gestione (che però non devono poter accedere ai dati). La funzionalità aiuta ad assicurarsi che amministratori di database locali, operatori di database cloud o altri utenti con privilegi elevati, ma non autorizzati, non possano accedere ai dati crittografati.

Inoltre, con Always Encrypted la crittografia è trasparente per le applicazioni. Nel computer client viene installato un driver abilitato per Always Encrypted che consente di crittografare e decrittografare automaticamente i dati sensibili nell'applicazione client. Il driver crittografa i dati nelle colonne sensibili prima di passarli al motore di database. Il driver riscrive automaticamente le query in modo da mantenere la semantica per l'applicazione. Analogamente, il driver decrittografa in modo trasparente i dati, archiviati nelle colonne di database crittografate, contenuti nei risultati delle query.

## <a name="access-control"></a>Controllo di accesso

Per garantire la sicurezza, il database SQL controlla l'accesso tramite:

- Regole del firewall che limitano la connettività in base all'indirizzo IP.
- Meccanismi di autenticazione che richiedono all'utente di dimostrare la propria identità.
- Meccanismi di autorizzazione che limitano l'accesso degli utenti ad azioni e dati specifici.

### <a name="database-access"></a>Accesso al database

La protezione dei dati inizia con il controllo dell'accesso ai dati. Il data center che ospita i dati gestisce l'accesso fisico. È possibile configurare un firewall per gestire la sicurezza a livello di rete. È anche possibile controllare l'accesso configurando gli account di accesso per l'autenticazione e definendo le autorizzazioni per i ruoli del server e del database.

#### <a name="firewall-and-firewall-rules"></a>Firewall e regole del firewall

Il [database SQL di Azure](https://azure.microsoft.com/services/sql-database/) offre un servizio di database relazionale per Azure e altre applicazioni basate su Internet. Per proteggere i dati, il firewall impedisce qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta. Per altre informazioni, vedere [Panoramica sulle regole del firewall per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

Il servizio database SQL di Azure è disponibile solo tramite la porta TCP 1433. Per accedere a un database SQL dal proprio computer, verificare che il firewall del computer client consenta la comunicazione TCP in uscita sulla porta TCP 1433. Se le connessioni in ingresso non sono necessarie per altre applicazioni, bloccarle sulla porta TCP 1433.

#### <a name="authentication"></a>Authentication

Per autenticazione si intende il modo in cui viene dimostrata la propria identità durante la connessione al database. Il database SQL supporta due tipi di autenticazione:

-   **Autenticazione di SQL Server**: quando viene creata un'istanza logica di SQL, viene creato un singolo account di accesso, denominato account sottoscrittore del database SQL. Questo account, che si connette usando l'[autenticazione di SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nome utente e password), è un amministratore nell'istanza logica del server e in tutti i database utente collegati a tale istanza. Le autorizzazioni dell'account sottoscrittore non possono essere soggette a restrizioni. Può esistere un solo account di questo tipo.
-   **Autenticazione di Azure Active Directory**: l'[autenticazione di Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) è un meccanismo di connessione al database SQL di Azure e ad Azure SQL Data Warehouse tramite le identità presenti in Azure AD. È possibile usarla per gestire in modo centralizzato le identità degli utenti del database.

![Autenticazione di Azure AD con il database SQL](./media/azure-databse-security-overview/azure-database-fig2.png)

 Tra i vantaggi dell'autenticazione di Azure AD si notino i seguenti:
  - Offre un'alternativa all'autenticazione di SQL Server.
  - Contribuisce ad arrestare la proliferazione delle identità utente nei server di database e consente la rotazione delle password in un'unica posizione.
  - Consente di gestire le autorizzazioni del database usando gruppi (Azure AD) esterni.
  - Può eliminare l'archiviazione delle password tramite l'abilitazione dell'autenticazione integrata di Windows e di altre forme di autenticazione supportate da Azure AD.

#### <a name="authorization"></a>Authorization

Il termine [autorizzazione](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) indica le operazioni che un utente è autorizzato a eseguire all'interno di un database SQL di Azure. L'autorizzazione è controllata dalle [appartenenze ai ruoli](https://msdn.microsoft.com/library/ms189121) del database e dalle [autorizzazioni a livello di oggetto](https://msdn.microsoft.com/library/ms191291.aspx) dell'account utente. L'autorizzazione è il processo volto a determinare le risorse a protezione diretta accessibili per un'entità di sicurezza e quali operazioni sono consentite per le risorse.

### <a name="application-access"></a>Accesso all'applicazione

#### <a name="dynamic-data-masking"></a>Maschera dati dinamica

Un addetto all'assistenza in un call center può identificare i chiamanti tramite alcune cifre del codice fiscale o del numero di carta di credito. Tuttavia, questi elementi di dati non devono essere completamente visibili all'addetto all'assistenza.

È possibile definire una regola di maschera che renda visibili solo le ultime quattro cifre del codice fiscale o del numero di carta di credito nel set di risultati di tutte le query.

![Maschera applicata a un numero inviato da un database SQL a un'app aziendale e viceversa](./media/azure-databse-security-overview/azure-database-fig3.png)

Oppure, è possibile definire una maschera dati appropriata per la protezione di informazioni personali identificabili. Uno sviluppatore potrà quindi eseguire una query negli ambienti di produzione per la risoluzione di problemi senza violare le normative di conformità.

La funzionalità [Maschera dati dinamica del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. La maschera dati dinamica è supportata per la versione 12 del database SQL di Azure.

La [maschera dati dinamica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) impedisce l'accesso non autorizzato ai dati sensibili consentendo di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi del database designati, senza alcuna modifica dei dati contenuti nel database.

> [!Note]
> Il mascheramento dei dati dinamici può essere configurato dall'amministratore del database di Azure, dall'amministratore del server o dal responsabile della sicurezza.

#### <a name="row-level-security"></a>Sicurezza a livello di riga

Un altro requisito di sicurezza comune per i database multi-tenant è la [sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131.aspx). È possibile usare questa funzionalità per controllare l'accesso alle righe di una tabella di database in base alle caratteristiche dell'utente che sta eseguendo la query. Esempi di caratteristiche sono l'appartenenza a un gruppo e il contesto di esecuzione.

![Sicurezza a livello di riga che consente a un utente di accedere alle righe di una tabella tramite un'app client](./media/azure-databse-security-overview/azure-database-fig4.png)

La logica di restrizione dell'accesso si trova nel livello database e non lontano dai dati in un altro livello applicazione. Il sistema di database applica le restrizioni di accesso a ogni tentativo di accesso ai dati da qualsiasi livello. Il sistema di sicurezza è così più affidabile e solido, grazie alla riduzione della superficie di attacco del sistema di sicurezza.

La sicurezza a livello di riga introduce il controllo di accesso basato su predicato. Questa funzionalità offre una valutazione flessibile e centralizzata che può prendere in considerazione i metadati o qualsiasi altro criterio ritenuto appropriato dall'amministratore. Il predicato viene usato come criterio per determinare se l'utente dispone o meno dell'accesso appropriato ai dati in base agli attributi utente. È possibile implementare il controllo di accesso basato su etichetta usando il controllo di accesso basato su predicato.

## <a name="proactive-monitoring"></a>Monitoraggio proattivo

Il database SQL consente di proteggere i dati con funzionalità di *controllo* e *rilevamento delle minacce*.

### <a name="auditing"></a>Controllo

Il [servizio di controllo del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) aumenta la possibilità di ottenere informazioni dettagliate su eventi e modifiche all'interno del database, ad esempio aggiornamenti e query sui dati.

Il servizio di controllo del database SQL tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere l'attività del database e ottenere informazioni dettagliate su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza. Il controllo abilita e facilita il rispetto degli standard di conformità, ma non garantisce la conformità.

È possibile usare il servizio di controllo del database SQL per eseguire le operazioni seguenti:

- **Conservare** un audit trail di eventi selezionati. È possibile definire categorie di azioni di database da controllare.
- **Creare report** sulle attività del database. È possibile usare i report preconfigurati e un dashboard per iniziare rapidamente a usare l'attività e la segnalazione di eventi.
- **Analizzare** i report. È possibile individuare eventi sospetti, attività insolite e tendenze.

Esistono due metodi di controllo:

-   **Controllo BLOB**: i log vengono scritti in Archiviazione BLOB di Azure. È un metodo di controllo più recente. Offre prestazioni più elevate, supporta una maggiore granularità del controllo a livello di oggetto ed è più conveniente.
-   **Controllo tabelle**: i log vengono scritti in Archiviazione tabelle di Azure.

### <a name="threat-detection"></a>Introduzione al rilevamento delle minacce

[Advanced Threat Protection per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-advanced-threat-protection) rileva attività sospette che indicano potenziali minacce alla sicurezza. È possibile usare la funzionalità di rilevamento delle minacce per rispondere a eventi sospetti nel database, ad esempio attacchi SQL injection, non appena si verificano. La funzionalità offre avvisi e consente di usare il servizio di controllo del database SQL di Azure per esaminare gli eventi sospetti.

![Rilevamento delle minacce per il database SQL e un'app Web con un utente malintenzionato esterno e uno interno](./media/azure-databse-security-overview/azure-database-fig5.jpg)

SQL Advanced Threat Protection (ATP) offre una serie di funzionalità di sicurezza SQL avanzate, tra cui Individuazione dati e classificazione, Valutazione della vulnerabilità e Rilevamento minacce. 

- [Individuazione dati e classificazione](../sql-database/sql-database-data-discovery-and-classification.md)
- [Valutazione della vulnerabilità](../sql-database/sql-vulnerability-assessment.md)  
- [Rilevamento delle minacce](../sql-database/sql-database-threat-detection.md)

[Advanced Threat Protection per Database di Azure per PostgreSQL](../postgresql/concepts-data-access-and-security-threat-protection.md) offre un nuovo livello di sicurezza che consente di rilevare e gestire le possibili minacce non appena si manifestano attraverso avvisi di sicurezza sulle attività anomale. Gli utenti ricevono un avviso in caso di attività di database sospette, potenziali vulnerabilità e modelli di query e accesso ai database anomali. Advanced Threat Protection per Database di Azure per PostgreSQL integra gli avvisi con il Centro sicurezza di Azure. I tipi di avvisi includono:

- Accesso da posizione insolita
- Accesso da data center di Azure insolito 
- Accesso da entità di sicurezza sconosciuta 
- Accesso da un'applicazione potenzialmente dannosa 
- Attacco di forza bruta alle credenziali del database di Azure per PostgreSQL 

[Advanced Threat Protection per Database di Azure per MySQL](/mysql/concepts-data-access-and-security-threat-protection.md) offre una protezione simile alla protezione avanzata per PostgreSQL.  

## <a name="centralized-security-management"></a>Gestione centralizzata della sicurezza

Il [Centro sicurezza di Azure](https://azure.microsoft.com/documentation/services/security-center/) consente di impedire, rilevare e gestire le minacce. Integra il monitoraggio della sicurezza e la gestione dei criteri in tutte le sottoscrizioni di Azure. Aiuta a rilevare le minacce che potrebbero altrimenti passare inosservate e opera con un ampio ecosistema di soluzioni per la sicurezza.

Il [Centro sicurezza](https://docs.microsoft.com/azure/security-center/security-center-sql-database) consente di proteggere i dati nel database SQL fornendo visibilità per la sicurezza di tutti i server e database. Con il Centro sicurezza è possibile:

- Definire i criteri per la crittografia e il controllo del database SQL.
- Monitorare la sicurezza delle risorse del database SQL per tutte le sottoscrizioni.
- Identificare e correggere rapidamente i problemi di sicurezza.
- Integrare gli avvisi generati dal [rilevamento delle minacce del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Il Centro sicurezza supporta l'accesso in base al ruolo.

## <a name="sql-information-protection"></a>SQL Information Protection

[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) consente di individuare e classificare automaticamente i dati potenzialmente sensibili, offre un meccanismo di assegnazione delle etichette per contrassegnare in modo permanente i dati sensibili con attributi di classificazione e contiene un dashboard dettagliato che indica lo stato di classificazione del database.  

Inoltre, calcola la riservatezza del set di risultati delle query SQL, in modo che le query che estraggono dati sensibili possano essere controllate in modo esplicito, e i dati protetti. Per altre informazioni su SQL Information Protection, vedere Individuazione e classificazione dei dati nel database SQL di Azure.

È possibile personalizzare i [criteri di SQL Information Protection](/security-center/security-center-info-protection-policy.md) nel Centro sicurezza di Azure.

## <a name="azure-marketplace"></a>Azure Marketplace

Azure Marketplace è un marketplace online di applicazioni e servizi che consente a startup e fornitori di software indipendenti (ISV) di offrire le proprie soluzioni a clienti di Azure in tutto il mondo.
Azure Marketplace riunisce gli ecosistemi dei partner di Microsoft Azure in una piattaforma unificata per offrire un servizio migliore a clienti e partner. È possibile [eseguire una ricerca](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) per visualizzare i prodotti per la sicurezza dei database disponibili in Azure Marketplace.

## <a name="next-steps"></a>Passaggi successivi

- [Proteggere il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Centro sicurezza di Azure e servizio database SQL di Azure](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Rilevamento delle minacce nel database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Migliorare le prestazioni del database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
