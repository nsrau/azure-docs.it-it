---
title: Panoramica di Istanza gestita di database SQL di Azure | Microsoft Docs
description: Questo argomento descrive un'istanza gestita di Azure SQL Database e spiega come funziona e in cosa differisce da un database singolo nel database SQL di Azure.
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: bonova
ms.openlocfilehash: f07ce542c176f4038378d54497d7114109ac5bd3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215525"
---
# <a name="what-is-a-managed-instance-preview"></a>Informazioni su Istanza gestita (anteprima)

L’Istanza gestita di database SQL di Azure (anteprima) è una nuova funzionalità del database SQL di Azure che offre quasi il 100% di compatibilità con SQL Server in locale (Enterprise Edition), fornendo un'implementazione della [rete virtuale (VNet)](../virtual-network/virtual-networks-overview.md) nativa che risolve problemi di sicurezza comuni e un [modello aziendale](https://azure.microsoft.com/pricing/details/sql-database/) favorevole per i clienti di SQL Server in locale. Istanza gestita consente ai clienti di SQL Server esistenti di spostare nel cloud le proprie applicazioni locali con modifiche minime nelle applicazioni e nel database. Allo stesso tempo, consente di mantenere tutte le funzionalità PaaS (applicazione automatica di patch e aggiornamenti di versione, backup, disponibilità elevata), che riducono drasticamente il carico di gestione e il TCO.

> [!IMPORTANT]
> Per un elenco delle aree geografiche in cui è attualmente disponibile Istanza gestita, vedere [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/) (Eseguire la migrazione dei database a un servizio completamente gestito con Istanza gestita di database SQL di Azure).
 
Nel diagramma seguente vengono descritte le funzionalità principali di Istanza gestita:

![funzionalità principali](./media/sql-database-managed-instance/key-features.png)

Istanza gestita è concepita come piattaforma preferita per gli scenari seguenti: 

- Clienti locali di SQL Server o IaaS clienti che intendono eseguire la migrazione delle applicazioni in un servizio completamente gestito con una quantità minima di modifiche di progettazione.
- ISV che usano database SQL e vogliono consentire ai propri clienti di eseguire la migrazione al cloud e ottenere un vantaggio competitivo significativo o raggiungere il mercato globale. 

In base alla disponibilità generale, Istanza gestita mira a offrire una compatibilità della superficie di attacco prossima al 100% con l'ultima versione di SQL Server in locale tramite un piano di rilascio a fasi. 

La tabella seguente riassume le differenze principali e gli scenari di utilizzo previsti tra IaaS SQL, il database SQL di Azure e Istanza gestita di database SQL di Azure:

| | Scenario di utilizzo | 
| --- | --- | 
|Istanza gestita di database SQL |Per i clienti che desiderano eseguire la migrazione di un numero maggiore di app da un ambiente locale o IaaS, creato personalmente o fornito da un ISV, con il minor numero di operazioni di migrazione possibile, proporre Istanza gestita. Tramite il [Servizio Migrazione del database (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) completamente automatico di Azure, i clienti possono trasferire il proprio server SQL locale in un'istanza gestita che garantisce la compatibilità con SQL Server in locale e il completo isolamento delle istanze del cliente con il supporto delle reti virtuali native.  Con Software Assurance, è possibile scambiare le licenze esistenti con tariffe scontate per un'istanza gestita di database SQL tramite l'[offerta per uso ibrido di Azure per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Istanza gestita di database SQL è la destinazione di migrazione migliore nel cloud per le istanze di SQL Server che richiedono un livello di sicurezza elevato e una superficie di programmazione avanzata. |
|Database SQL di Azure (singolo o pool) |**Pool elastici**: per i clienti che sviluppano nuove applicazioni SaaS multi-tenant o che intendono trasformare le proprie app locali esistenti in app SaaS multi-tenant, è possibile proporre i pool elastici. I vantaggi di questo modello sono i seguenti: <br><ul><li>Conversione del modello aziendale dalla vendita di licenze alla vendita di abbonamenti a un servizio (per gli ISV)</li></ul><ul><li>Isolamento dei tenant semplice e valido</li></ul><ul><li>Modello di programmazione incentrato sul database semplificato</li></ul><ul><li>Possibilità di scalare orizzontalmente senza raggiungere un limite rigido</li></ul>**Database singoli**: per i clienti che sviluppano nuove app diverse da servizi SaaS multi-tenant, il cui carico di lavoro è stabile e prevedibile, è possibile proporre i database singoli. I vantaggi di questo modello sono i seguenti:<ul><li>Modello di programmazione incentrato sul database semplificato</li></ul>  <ul><li>Prestazioni prevedibili per ogni database</li></ul>|
|Macchina virtuale IaaS SQL|Per i clienti che necessitano di personalizzare il sistema operativo o il server di database, nonché per i clienti con requisiti specifici in termini di esecuzione di app di terze parti insieme a SQL Server (nella stessa macchina virtuale), è possibile proporre macchine virtuali SQL/IaaS come soluzione ottimale|
|||

## <a name="how-to-programmatically-identify-a-managed-instance"></a>Come identificare un'istanza gestita a livello di codice

La tabella seguente mostra diverse proprietà, accessibili tramite Transact SQL, che è possibile usare per rilevare se l'applicazione funziona con Istanza gestita e recuperare proprietà importanti.

|Proprietà|Valore|Comment|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Questo valore è uguale a quello del database SQL.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Questo valore è uguale a quello del database SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Questo valore identifica l'istanza gestita in modo univoco.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS completo dell'istanza nel formato seguente:<instanceName>.<dnsPrefix>. database.Windows.NET, dove <instanceName> è il nome fornito dal cliente, mentre <dnsPrefix> è la parte del nome generata automaticamente che garantisce l'univocità del DNS globale (ad esempio "wcus17662feb9ce98")|Esempio: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>Caratteristiche e funzionalità principali di un'istanza gestita 

> [!IMPORTANT]
> Un'istanza gestita viene eseguita con tutte le funzionalità della versione più recente di SQL Server, incluse le operazioni online, le correzioni automatiche del piano e altri miglioramenti delle prestazioni Enterprise. 

| **Vantaggi di PaaS** | **Continuità aziendale** |
| --- | --- |
|Acquisto e gestione di hardware non necessari <br>Nessun sovraccarico per la gestione dell'infrastruttura sottostante <br>Provisioning rapido e scalabilità del servizio <br>Applicazione automatica di patch e aggiornamento della versione <br>Integrazione con altri servizi dati PaaS |Contratto di servizio relativo al tempo di attività 99,99%  <br>Disponibilità elevata incorporata <br>Dati protetti con backup automatici <br>Periodo di conservazione dei backup configurabile dal cliente (fissato a 7 giorni nell'anteprima pubblica) <br>Backup avviati dall'utente <br>Funzionalità di ripristino temporizzato di un database |
|**Sicurezza e conformità** | **Gestione**|
|Ambiente isolato (integrazione della rete virtuale, servizio a tenant singolo, calcolo e archiviazione dedicati <br>Crittografia dei dati in transito <br>Autenticazione di Azure AD, supporto di Single Sign-On <br>Soddisfa gli standard di conformità del database SQL di Azure <br>Controllo SQL <br>Introduzione al rilevamento delle minacce |API di Azure Resource Manager per l'automazione del provisioning e della scalabilità del servizio <br>Funzionalità del portale di Azure per provisioning e scalabilità del servizio manuali <br>Servizio di migrazione dei dati 

![Single Sign-On](./media/sql-database-managed-instance/sso.png) 

## <a name="vcore-based-purchasing-model-preview"></a>Modello di acquisto basato su vCore (anteprima)

Il modello di acquisto basato su vCore (anteprima) offre flessibilità, controllo e trasparenza, consentendo di convertire con facilità i requisiti dei carichi di lavoro locali per il cloud. Questo modello consente di ridimensionare le risorse di calcolo, memoria e archiviazione in base ai requisiti dei carichi di lavoro. Il modello in base ai vCore dà anche diritto a un risparmio fino al 30% sul [Vantaggio Azure Hybrid Use per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

La memoria centrale virtuale rappresenta la CPU logica offerta con la possibilità di scegliere tra generazioni di hardware diverse.
- Le CPU logiche Generazione 4 si basano su processori Intel E5-2673 v3 (Haswell) a 2,4 GHz.
- Le CPU logiche Generazione 5 si basano su processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz.

La tabella seguente aiuta a capire come selezionare la configurazione ottimale delle risorse di calcolo, memoria, archiviazione e I/O.

||Generazione 4|Generazione 5|
|----|------|-----|
|Hardware|Processori Intel E5-2673 v3 (Haswell) a 2,4 GHz, con unità vCore SSD = 1 PP (core fisico)|Processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz, unità SSD eNVM veloce, vCore = 1 LP (hyperthread)|
|Livelli di prestazioni|8, 16, 24 vCore|8, 16, 24, 32, 40 vCore|
|Memoria|7 GB per vCore|5,5 GB per vCore|
||||

## <a name="managed-instance-service-tier"></a>Livello di servizio di Istanza gestita

Istanza gestita è inizialmente disponibile con un livello di servizio singolo, Utilizzo generico, progettato per le applicazioni con disponibilità tipica e requisiti di latenza di I/O comuni.

L'elenco seguente descrive le caratteristiche principali del livello di servizio Utilizzo generico: 

- Progettazione della maggior parte delle applicazioni aziendali con requisiti di prestazioni e disponibilità elevata tipici 
- Archiviazione Premium di Azure ad alte prestazioni (8 TB) 
- 100 database/istanza 

In questo livello, è possibile selezionare indipendente la capacità di archiviazione e di calcolo. 

Il diagramma seguente illustra i nodi di calcolo attivo e ridondanti in questo livello di servizio.
 
![Livello di servizio Utilizzo generico](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

Di seguito sono indicate le funzionalità principali del livello di servizio Utilizzo generico:

|Funzionalità | DESCRIZIONE|
|---|---|
| Numero di vCore* | 8, 16, 24 (Generazione 4)<br>8, 16, 24, 32, 40 (Generazione 5)|
| Versione/build di SQL Server | SQL Server (versione più recente disponibile) |
| Dimensione minima archiviazione | 32 GB |
| Dimensione massima archiviazione | 8 TB |
| Quantità massima di risorse di archiviazione per database | 8 TB |
| Archiviazione IOPS prevista | 500-7500 IOPS per ogni file di dati (dipende dal file di dati). Vedere [Archiviazione Premium](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Numero di file di dati (RIGHE) per il database | Multipli | 
| Numero di file di log (LOG) per il database | 1 | 
| Backup automatici gestiti | Sì |
| DISPONIBILITÀ ELEVATA | Basata sull'archiviazione remota e [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Monitoraggio predefinito e metriche dell'istanza e del database | Sì |
| Applicazione automatica di patch software | Sì |
| Rete virtuale: distribuzione di Azure Resource Manager | Sì |
| Rete virtuale: modello di distribuzione classica | No  |
| Supporto del portale | Sì|
|||

\* La memoria centrale virtuale rappresenta la CPU logica offerta con la possibilità di scegliere tra generazioni di hardware. Le CPU logiche di quarta generazione sono basate su processori Intel E5-2673 v3 (Haswell) a 2,4 GHz e le CPU logiche di quinta generazione sono basate su processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz. 

## <a name="advanced-security-and-compliance"></a>Sicurezza e conformità avanzate 

### <a name="managed-instance-security-isolation"></a>Isolamento di sicurezza di Istanza gestita 

Istanza gestita offre isolamento di sicurezza aggiuntivo dagli altri tenant nel cloud di Azure. L'isolamento di sicurezza include: 

- L'[implementazione della rete virtuale nativa](sql-database-managed-instance-vnet-configuration.md) e la connettività all'ambiente locale mediante Express Route di Azure o Gateway VPN 
- L'endpoint SQL viene esposto solo tramite un indirizzo IP privato, che consente la connettività sicura da reti private di Azure o ibride
- Tenant singolo con infrastruttura sottostante dedicata (calcolo, archiviazione)

Il diagramma seguente illustra la progettazione dell'isolamento: 

![disponibilità elevata](./media/sql-database-managed-instance/application-deployment-topologies.png)  

### <a name="auditing-for-compliance-and-security"></a>Controllo per conformità e sicurezza 

Il [controllo di Istanza gestita](sql-database-managed-instance-auditing.md) tiene traccia degli eventi del database e li registra in un log di controllo nell'account di archiviazione di Azure. Il controllo consente di agevolare la conformità alle normative, comprendere le attività del database e ottenere informazioni su eventuali discrepanze e anomalie che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza. 

### <a name="data-encryption-in-motion"></a>Crittografia dei dati in movimento 

Istanza gestita protegge i dati fornendo la crittografia per i dati in transito tramite Transport Layer Security.

Oltre alla sicurezza a livello di trasporto, Istanza gestita di database SQL offre protezione per i dati sensibili in movimento, inattivi e durante l'elaborazione di query con [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted è una tecnologia leader del settore che offre un livello di sicurezza dei dati senza uguali, per la protezione da violazioni che implicano il furto di dati critici. Con Always Encrypted, ad esempio, i numeri delle carte di credito sono sempre archiviati in forma crittografata nel database, anche durante l'elaborazione di query, e la decrittografia è consentita nella posizione di utilizzo da parte di personale o applicazioni autorizzati che devono elaborare tali dati. 

### <a name="dynamic-data-masking"></a>Maschera dati dinamica 

La funzione [Maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking) del database SQL limita l'esposizione dei dati sensibili, nascondendoli agli utenti senza privilegi. La maschera dati dinamica impedisce l'accesso non autorizzato ai dati sensibili consentendo di definire la quantità di dati sensibili da rivelare, con un impatto minimo sul livello dell'applicazione. Si tratta di una funzionalità di sicurezza basata su criteri che consente di nascondere i dati sensibili nel set di risultati di una query in campi del database designati, senza alcuna modifica dei dati contenuti nel database. 

### <a name="row-level-security"></a>Sicurezza a livello di riga 

La [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security) consente di controllare l'accesso alle righe in una tabella di database in base alle caratteristiche dell'utente che esegue una query, ad esempio l'appartenenza a un gruppo o il contesto di esecuzione. La sicurezza a livello di riga semplifica la progettazione e la codifica della sicurezza nell'applicazione. Consente di implementare limitazioni per l'accesso alle righe di dati, assicurando ad esempio che i collaboratori possano accedere solo alle righe di dati pertinenti per il proprio reparto o limitando l'accesso ai dati ai soli dati di pertinenti. 

### <a name="threat-detection"></a>Introduzione al rilevamento delle minacce 

Il [rilevamento delle minacce](sql-database-managed-instance-threat-detection.md) è complementare al [controllo di Istanza gestita](sql-database-managed-instance-auditing.md) perché offre un livello aggiuntivo di informazioni sulla sicurezza integrata nel servizio che rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit del database. L'utente viene avvisato di attività sospette, vulnerabilità potenziali e attacchi SQL injection, nonché di modelli anomali di accesso al database. Gli avvisi di Rilevamento minacce sono disponibili nel [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), forniscono i dettagli delle attività sospette e raccomandano azioni per individuare e ridurre la minaccia.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori 

Il database SQL consente di gestire a livello centralizzato le identità degli utenti di database e altri servizi Microsoft grazie all'[integrazione in Azure Active Directory](sql-database-aad-authentication.md). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory supporta l'[autenticazione a più fattori](sql-database-ssms-mfa-authentication-configure.md) (MFA) per una maggiore sicurezza di dati e applicazioni, supportando allo stesso tempo un processo di accesso singolo. 

### <a name="authentication"></a>Authentication 
Per autenticazione del database SQL si intende il modo in cui l'utente dimostra la propria identità durante la connessione al database. Il database SQL supporta due tipi di autenticazione:  

- L'autenticazione SQL, che usa un nome utente e una password.
- L'autenticazione di Azure Active Directory, che usa identità gestite da Azure Active Directory ed è supportata per i domini gestiti e integrati. 

### <a name="authorization"></a>Authorization

Per autorizzazione si intendono le operazioni che l'utente può eseguire in un database SQL di Azure, che sono controllate dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente. Istanza gestita ha le stesse funzionalità di autorizzazione di SQL Server 2017. 

## <a name="database-migration"></a>Migrazione di database 

Il servizio Istanza gestita è destinato a scenari utente con migrazione di massa di database da implementazioni di database locali o IaaS. Istanza gestita supporta diverse opzioni di migrazione di database: 

### <a name="data-migration-service"></a>Servizio di migrazione dei dati

Il Servizio Migrazione del database di Azure è un servizio completamente gestito progettato per abilitare le migrazioni senza interruzioni da più origini di database alle piattaforme di dati di Azure con tempi di inattività minimi. Questo servizio semplifica le attività necessarie per spostare database di SQL Server e di terze parti in Azure. Le opzioni di distribuzione includono il database SQL di Azure, Istanza gestita e SQL Server in una macchina virtuale di Azure nell'anteprima pubblica. Vedere [come eseguire la migrazione del database locale in Istanza gestita tramite DMS](https://aka.ms/migratetoMIusingDMS). 

### <a name="backup-and-restore"></a>Backup e ripristino  

L'approccio di migrazione sfrutta i backup di SQL per l'archiviazione di BLOB di Azure. I backup archiviati in BLOB di archiviazione di Azure possono essere ripristinati direttamente in Istanza gestita. Per ripristinare un database SQL esistente in un'istanza gestita, è possibile:

- Usare [Servizio Migrazione del database](../dms/dms-overview.md). Per un'esercitazione, vedere [Eseguire la migrazione di SQL Server all'istanza gestita di database SQL di Azure](../dms/tutorial-sql-server-to-managed-instance.md) per ripristinare il database da un file di backup.
- Usare il [comando T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql). 
  - Per un'esercitazione su come ripristinare il file di backup di database Wide World Importers - Standard, vedere [Restore a backup file to a Managed Instance](sql-database-managed-instance-restore-from-backup-tutorial.md) (Ripristinare un file di backup in un'istanza gestita). Questa esercitazione illustra come caricare un file di backup in una risorsa di archiviazione BLOB di Azure e proteggerlo usando una chiave di firma di accesso condiviso.
  - Per informazioni sul ripristino dall'URL, vedere [Ripristino nativo da URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

## <a name="sql-features-supported"></a>Funzionalità di SQL supportate 

Istanza gestita mira a offrire una compatibilità della superficie di attacco prossima al 100% con SQL Server in locale disponibile a fasi fino alla disponibilità generale del servizio. Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
 
Istanza gestita supporta la compatibilità con le versioni precedenti per i database di SQL 2008. È supportata la migrazione diretta da server di database di SQL 2005, il livello di compatibilità per i database di SQL 2005 migrati viene aggiornato a SQL 2008. 
 
Il diagramma seguente illustra la compatibilità della superficie di attacco in Istanza gestita:  

![migrazione](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Differenze principali tra SQL Server in locale e Istanza gestita 

Istanza gestita offre vantaggi derivanti dall'essere sempre aggiornati nel cloud, mentre alcune funzionalità di SQL Server in locale possono essere obsolete, essere state ritirate o sostituite con alternative. In alcuni casi specifici gli strumenti devono riconoscere che una determinata funzionalità funziona in modo leggermente diverso o che il servizio non funziona in un ambiente di cui non si ha il controllo completo: 

- La disponibilità elevata è incorporata e preconfigurata. Le funzionalità di disponibilità elevata Always On non sono esposte nello stesso modo delle implementazioni IaaS SQL 
- Backup automatici e ripristino temporizzato. Il cliente può avviare backup `copy-only` che non interferiscono con la catena di backup automatica. 
- Istanza gestita non consente l'uso di percorsi fisici completi, pertanto tutti gli scenari corrispondenti devono essere supportati in modo diverso: RESTORE DB non supporta WITH MOVE, CREATE DB non consente i percorsi fisici, BULK INSERT funziona solo con i BLOB di Azure e così via. 
- Istanza gestita supporta l'[autenticazione di Azure AD](sql-database-aad-authentication.md) come alternativa cloud all'autenticazione di Windows. 
- Istanza gestita gestisce automaticamente filegroup XTP e i file per i database contenenti oggetti di OLTP in memoria
 
### <a name="managed-instance-administration-features"></a>Funzionalità di amministrazione di Istanza gestita  

Istanza gestita consente all'amministratore di sistema di concentrarsi sugli aspetti più importanti per l'azienda. Molte attività dell'amministratore di sistema o di database non sono necessarie o sono semplici. Ad esempio, l'installazione del sistema operativo o di RDBMS e l'applicazione di patch, il ridimensionamento dinamico e la configurazione delle istanze, i backup, la replica di database (inclusi i database di sistema), la configurazione a elevata disponibilità e la configurazione di flussi di dati di monitoraggio dell'integrità e delle prestazioni. 

> [!IMPORTANT]
> Per un elenco delle funzionalità supportate, parzialmente supportate e non supportate, vedere [Funzionalità del Database SQL](sql-database-features.md). Per un elenco delle differenze di T-SQL nelle istanze gestite rispetto a SQL Server, vedere [Differenze di T-SQL in Istanza gestita rispetto a SQL Server](sql-database-managed-instance-transact-sql-information.md)
 
## <a name="next-steps"></a>Passaggi successivi

- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere l'articolo relativo alla [configurazione di una rete virtuale per Istanza gestita](sql-database-managed-instance-vnet-configuration.md).
- Per un'esercitazione sulla creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [Creare un'istanza gestita](sql-database-managed-instance-create-tutorial-portal.md).
- Per un'esercitazione sull'uso di Servizio Migrazione del database di Azure per la migrazione, vedere l'articolo relativo alla [migrazione a Istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).
