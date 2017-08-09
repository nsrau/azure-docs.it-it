---
title: Procedure consigliate per la sicurezza del database di Azure | Microsoft Docs
description: Questo articolo fornisce un set di procedure consigliate per la sicurezza del database di Azure.
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 5bd6fe0dd369b3bbc7ca0d697c964badda557cb8
ms.contentlocale: it-it
ms.lasthandoff: 08/03/2017

---

# <a name="azure-database-security-best-practices"></a>Procedure consigliate per la sicurezza del database di Azure

La sicurezza è un tema della massima importanza per la gestione dei database ed è sempre stata una priorità per il database SQL di Azure. I database possono essere protetti in modo sicuro per soddisfare la maggior parte dei requisiti di legge e di sicurezza, tra i quali HIPAA, ISO 27001/27002 e PCI DSS livello 1. Un elenco aggiornato delle certificazioni di conformità di sicurezza è disponibile nel [sito Centro protezione di Microsoft](http://azure.microsoft.com/support/trust-center/services/). È anche possibile posizionare i database in specifici data center di Azure in base ai requisiti normativi.

In questo articolo verrà illustrato un insieme di procedure consigliate per la sicurezza del database di Azure, derivate dalla nostra esperienza con la sicurezza dei database di Azure e dalle esperienze di altri clienti.

Per ogni procedura consigliata verrà illustrato:

-   Qual è la procedura consigliata
-   Il motivo per cui si vuole abilitare tale procedura consigliata
-   Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
-   Come imparare ad abilitare la procedura consigliata

Il presente articolo sulle procedure consigliate per la sicurezza del database di Azure si basa su un parere condiviso, nonché sulle capacità e sui set di funzionalità della piattaforma di Azure esistenti al momento della scrittura. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

Le procedure consigliate per la sicurezza del database di Azure discusse in questo articolo includono:

-   Usare le regole del firewall per limitare l'accesso al database
-   Abilitare l'autenticazione del database
-   Proteggere i dati con la crittografia
-   Proteggere i dati in transito
-   Abilitare il controllo del database
-   Abilitare il rilevamento delle minacce per il database


## <a name="use-firewall-rules-to-restrict-database-access"></a>Usare le regole del firewall per limitare l'accesso al database

Il database SQL di Microsoft Azure fornisce un servizio di database relazionale per Azure e altre applicazioni basate su Internet. Per garantire la sicurezza dell'accesso, il database SQL controlla l'accesso con regole del firewall che limitano la connettività in base all'indirizzo IP, meccanismi di autenticazione che richiedono agli utenti di dimostrare la propria identità e meccanismi di autorizzazione che consentono agli utenti di usufruire solo di azioni e dati specifici. I firewall impediscono qualsiasi accesso al server del database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.

![Regole del firewall](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

Il servizio Database SQL di Azure è disponibile solo tramite la porta TCP 1433. Per accedere al database SQL dal computer, assicurarsi che il firewall del computer client consenta la comunicazione TCP in uscita sulla porta TCP 1433. Se non sono necessarie per altre applicazioni, bloccare le connessioni in ingresso sulla porta TCP 1433 usando le regole del firewall.

Come parte del processo di connessione, le connessioni da macchine virtuali di Azure vengono reindirizzate a un indirizzo IP diverso e a una porta, univoca per ogni ruolo di lavoro. Il numero di porta è compreso nell'intervallo che va da 11000 a 11999. Per altre informazioni sulle porte TCP, vedere [Porte superiori a 1433 per ADO.NET 4.5 e il database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12).

> [!Note]
> Per informazioni generali sulle regole del firewall, vedere l'articolo relativo alle [regole del firewall per il database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="enable-database-authentication"></a>Abilitare l'autenticazione del database
Il database SQL supporta due tipi di autenticazione: l'autenticazione SQL e l'autenticazione di Azure Active Directory (Azure AD).

L'**autenticazione SQL** è consigliata nei casi seguenti:

-   Consente a SQL Azure di supportare gli ambienti con sistemi operativi misti, in cui non tutti gli utenti sono autenticati da un dominio Windows.
-   Consente a SQL Azure di supportare le applicazioni meno recenti e lr applicazioni di terze parti che richiedono l'autenticazione di SQL Server.
-   Consente agli utenti di connettersi da domini sconosciuti o non attendibili. Ad esempio, un'applicazione in cui clienti specifici si connettono con account di accesso di SQL Server assegnati per ricevere lo stato dei relativi ordini.
-   Consente a SQL Azure di supportare le applicazioni basate sul Web in cui gli utenti creano le proprie identità.
-   Consente agli sviluppatori di software di distribuire le proprie applicazioni tramite una gerarchia di autorizzazioni complessa, basata su account di accesso di SQL Server noti e preimpostati.

> [!Note]
> Tuttavia, l'autenticazione di SQL Server non può usare il protocollo di sicurezza Kerberos.

Se si usa l'**autenticazione di SQL** è necessario:

-   Gestire autonomamente le credenziali sicure.
-   Proteggere le credenziali nella stringa di connessione.
-   (Potenzialmente) proteggere le credenziali trasmesse in rete dal server Web al database. Per altre informazioni, vedere [how to: Connect to SQL Server Using SQL Authentication in ASP.NET 2.0](https://msdn.microsoft.com/library/ms998300.aspx) (Procedura: Connettersi a SQL Server usando l'autenticazione SQL in ASP.NET 2.0).

**L'autenticazione di Azure Active Directory** è un meccanismo di connessione al database SQL di Microsoft Azure e a [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) tramite le identità di Azure Active Directory (Azure AD). Con l'autenticazione di Azure AD è possibile gestire centralmente le identità degli utenti del database e altri servizi Microsoft. La gestione centrale degli ID consente di gestire gli utenti del database da un unico punto e semplifica la gestione delle autorizzazioni. Ecco alcuni vantaggi:

-   Offre un'alternativa all'autenticazione di SQL Server.
-   Contribuisce ad arrestare la proliferazione delle identità utente nei server di database.
-   Consente la rotazione delle password in un'unica posizione.
-   I clienti possono gestire le autorizzazioni del database tramite gruppi (AAD) esterni.
-   Può eliminare l'archiviazione delle password abilitando l'autenticazione integrata di Windows e altre forme di autenticazione supportate da Azure Active Directory.
-   L'autenticazione di Azure SD usa gli utenti di un database indipendente per autenticare le identità a livello di database.
-   Azure AD supporta l'autenticazione basata su token per le applicazioni che si connettono a database SQL.
-   L'autenticazione di Azure AD supporta la federazione dei domini di AD FS o l'autenticazione utente/password nativa per un'istanza locale di Azure Active Directory senza la sincronizzazione del dominio.
-   Azure AD supporta le connessioni da SQL Server Management Studio che utilizzano l'autenticazione universale di Active Directory, che include l'MFA (Multi-Factor Authentication). L'MFA include funzionalità avanzate di autenticazione con una serie di semplici opzioni di verifica, tra cui: chiamata telefonica, SMS, smart card con pin o notifica tramite app per dispositivi mobili. Per altre informazioni [Supporto di SQL Server Management Studio (SSMS) per l'autenticazione MFA di Azure AD con il database SQL e SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication).

I passaggi di configurazione includono le procedure seguenti per configurare e usare l'autenticazione di Azure Active Directory.

-   Creare e popolare un'istanza di Azure AD.
-   Facoltativo: associare o modificare l'istanza di Active Directory attualmente associata alla sottoscrizione di Azure.
-   Creare un amministratore di Azure Active Directory per il server di Azure SQL o per [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configurare i computer client.
-   Creare gli utenti di database indipendente nel database di cui è stato eseguito il mapping alle identità di Azure AD.
-   Connettersi al database usando le identità di Azure AD.

Informazioni dettagliate sono disponibili [qui](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

## <a name="protect-your-data-using-encryption"></a>Proteggere i dati con la crittografia

[La funzionalità Transparent Data Encryption del database SQL di Azure (TDE)](https://msdn.microsoft.com/library/dn948096.aspx) consente di proteggere il sistema da attività dannose eseguendo in tempo reale la crittografia e la decrittografia dei database, dei backup associati e dei file di log delle transazioni inattivi, senza apportare modifiche all'applicazione. TDE esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica detta "chiave di crittografia del database".

Anche quando l'intera risorsa di archiviazione è crittografata, è molto importante crittografare anche il database stesso. Si tratta di un'implementazione della strategia di difesa approfondita per la protezione dati. Se si usa il database SQL di Azure e si vuole proteggere dati riservati, ad esempio il numero della carta di credito o il codice fiscale, è possibile crittografare i database con la crittografia AES a 256 bit con convalida FIPS 140-2, che soddisfa i requisiti di molti standard di settore, quali HIPAA e PCI.

È importante comprendere che i file correlati all'[estensione del pool di buffer](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) non vengono crittografati quando un database è crittografato con la funzionalità TDE. È necessario usare gli strumenti di crittografia a livello di file system come [BitLocker](https://technet.microsoft.com/library/cc732774) o [Encrypting File System (EFS)](https://technet.microsoft.com/library/cc700811.aspx) per i file correlati all'estensione del pool di buffer.

Dato che un utente autorizzato, ad esempio un amministratore della sicurezza o un amministratore di database, può accedere ai dati anche se il database è crittografato con TDE, è consigliabile seguire anche le raccomandazioni seguenti:

-   Abilitare l'autenticazione SQL a livello di database.
-   Usare l'autenticazione di Azure AD con i ruoli di [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is).
-   Gli utenti e le applicazioni dovrebbero usare account separati per l'autenticazione. In questo modo è possibile limitare le autorizzazioni concesse a utenti e applicazioni e ridurre i rischi di attività dannose.
-   Implementare la sicurezza a livello di database usando i ruoli predefiniti del database, ad esempio db_datareader o db_datawriter, oppure creare ruoli personalizzati per l'applicazione per concedere autorizzazioni esplicite su determinati oggetti di database.

Per altri modi di crittografare i dati, considerare quanto segue:

-   [Crittografia a livello di cella](https://msdn.microsoft.com/library/ms179331.aspx) per crittografare colonne specifiche o anche celle di dati con chiavi di crittografia diverse.
-   Crittografia di dati in uso con Always Encrypted: la funzionalità [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) consente ai client di crittografare i dati sensibili nelle applicazioni client e di non rivelare mai le chiavi di crittografia al motore di database (database SQL o SQL Server). Di conseguenza, Always Encrypted consente di separare i proprietari dei dati (che possono visualizzarli) e le persone incaricate della gestione dei dati (che però non devono poter accedere ai dati).
-   Uso della sicurezza a livello di riga: la sicurezza a livello di riga consente ai clienti di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione. Per altre informazioni, vedere [Sicurezza a livello di riga](https://msdn.microsoft.com/library/dn765131).

## <a name="protect-data-in-transit"></a>Proteggere i dati in transito
La protezione dei dati in transito deve essere una parte essenziale della strategia di protezione dati. Visto che i dati transiteranno in modo bidirezionale tra molte posizioni, in generale si raccomanda di usare sempre i protocolli SSL/TLS per lo scambio di dati tra posizioni diverse. In alcuni casi è consigliabile isolare l'intero canale di comunicazione tra l'infrastruttura locale e cloud con una rete privata virtuale (VPN).

Per lo spostamento dei dati tra l'infrastruttura locale e Azure, è opportuno considerare le misure di protezione appropriate, ad esempio HTTPS o VPN.

Per le organizzazioni che devono proteggere l'accesso ad Azure da più workstation locali, usare una [VPN da sito a sito di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create).

Per le organizzazioni che devono proteggere l'accesso ad Azure da singole workstation locali o non locali, valutare l'uso di una [VPN da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create).

Set di dati più grandi possono essere spostati su un collegamento WAN ad alta velocità dedicato, ad esempio [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se si decide di usare ExpressRoute, è possibile anche crittografare i dati a livello di applicazione usando [SSL/TLS](https://support.microsoft.com/kb/257591) o altri protocolli per una maggiore protezione.

Se si interagisce con Archiviazione di Azure tramite il portale di Azure, tutte le transazioni hanno luogo tramite HTTPS. È possibile usare anche l'[API REST di archiviazione](https://msdn.microsoft.com/library/azure/dd179355.aspx) su HTTPS per interagire con [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) e il [database SQL di Azure](https://azure.microsoft.com/services/sql-database/).

Le organizzazioni che non riescono a proteggere i dati in transito sono più vulnerabili agli [attacchi man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [eavesdropping](https://technet.microsoft.com/library/gg195641.aspx) e hijack della sessione. Questi attacchi possono essere il primo passo per ottenere l'accesso ai dati riservati.

Per altre informazioni sull'opzione VPN di Azure, vedere l'articolo [Pianificazione e progettazione per il gateway VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-plan-design).

## <a name="enable-database-auditing"></a>Abilitare il controllo del database
Il controllo di un'istanza del motore di database di SQL Server o di un singolo database comporta il rilevamento e la registrazione degli eventi che si verificano nel motore di database. SQL Server Audit consente di creare controlli del server che possono contenere specifiche di controllo del server per gli eventi a livello di server e specifiche di controllo del database per gli eventi a livello di database. Gli eventi controllati possono essere scritti nei log eventi o in file di controllo.

Esistono numerosi livelli di controllo per SQL Server, in base ai requisiti legislativi o standard per l'installazione. SQL Server Audit fornisce gli strumenti e i processi che sono necessari per abilitare, archiviare e visualizzare controlli in vari oggetti server e database.

Il servizio di [controllo del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) tiene traccia degli eventi che si verificano nel database e li registra in un log di controllo nell'account di Archiviazione di Azure dell'utente.

Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza.

Il controllo abilita e facilita il rispetto degli standard di conformità, ma non garantisce la conformità.

Per altre informazioni sul controllo del database e su come abilitarlo, vedere l'articolo [Abilitare il controllo e il rilevamento delle minacce sui server SQL nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="enable-database-threat-detection"></a>Abilitare il rilevamento delle minacce per il database
Il rilevamento delle minacce di SQL consente ai clienti di rilevare e rispondere alle minacce potenziali non appena si verificano, fornendo avvisi di sicurezza sulle attività anomale. È possibile ricevere un avviso in caso di attività di database sospetta, potenziali vulnerabilità e attacchi SQL injection, nonché in caso di modelli di accesso ai database anomali. Gli avvisi di Rilevamento minacce di SQL forniscono i dettagli delle attività sospette e consigliano azioni per analizzare e ridurre la minaccia.

Ad esempio, l'attacco SQL injection è uno dei problemi di sicurezza comuni delle applicazioni Web su Internet, che viene usato per attaccare le applicazioni guidate dai dati. Gli autori degli attacchi sfruttano le vulnerabilità delle applicazioni per introdurre istruzioni SQL dannose nei campi di immissione dell'applicazione, con lo scopo di violare o modificare i dati del database.

Per informazioni su come configurare le funzionalità di rilevamento delle minacce per il database nel portale di Azure, vedere [Rilevamento delle minacce nel database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Gli avvisi per il rilevamento delle minacce di SQL sono integrati con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/). Fai una prova gratuita di 60 giorni.

Per altre informazioni sul rilevamento delle minacce per il database e su come abilitarlo, vedere l'articolo [Abilitare il controllo e il rilevamento delle minacce sui server SQL nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-servers).

## <a name="conclusion"></a>Conclusione
Il database di Azure è una piattaforma di database affidabile, con una gamma completa di funzionalità di sicurezza che soddisfano molti requisiti normativi e aziendali. È possibile proteggere i dati controllando l'accesso fisico e tramite un'ampia gamma di opzioni per la protezione dei dati a livello di file, di colonna o di riga con Transparent Data Encryption, crittografia a livello di cella o sicurezza a livello di riga. Always Encrypted consente anche operazioni sui dati crittografati, semplificando il processo di aggiornamenti dell'applicazione. A sua volta, l'accesso ai log di controllo dell'attività del database SQL fornisce le informazioni necessarie, consentendo di sapere come e quando viene eseguito l'accesso ai dati.

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulle regole del firewall, vedere [Regole del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).
- Per informazioni su utenti e account di accesso, vedere l'articolo su come [gestire gli account di accesso](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).
- Per un'esercitazione, vedere [Proteggere il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial).

