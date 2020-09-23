---
title: Funzionalità e funzionalità di Azure Arc abilitate per SQL Istanza gestita
description: Funzionalità e funzionalità di Azure Arc abilitate per SQL Istanza gestita
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 810a08d2f72359b385d2a7567b796aa222c6ab14
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940779"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Funzionalità e funzionalità di Azure Arc abilitate per SQL Istanza gestita

Azure Arc Enabled SQL Istanza gestita condividere una codebase comune con la versione stabile più recente di SQL Server. La maggior parte delle funzionalità standard del linguaggio SQL, dell'elaborazione delle query e di gestione del database sono identiche. Le funzionalità comuni tra SQL Server e il database SQL o SQL Istanza gestita sono:

- Funzionalità del linguaggio: [controllo di parole chiave del linguaggio di flusso](/sql/t-sql/language-elements/control-of-flow), [cursori](/sql/t-sql/language-elements/cursors-transact-sql), [tipi di dati](/sql/t-sql/data-types/data-types-transact-sql), [istruzioni DML](/sql/t-sql/queries/queries), [predicati](/sql/t-sql/queries/predicates), [numeri di sequenza](/sql/relational-databases/sequence-numbers/sequence-numbers), [stored procedure](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)e [variabili](/sql/t-sql/language-elements/variables-transact-sql).
- Funzionalità di database- [ottimizzazione automatica (utilizzo forzato del piano)](/sql/relational-databases/automatic-tuning/automatic-tuning), [rilevamento delle modifiche](/sql/relational-databases/track-changes/about-change-tracking-sql-server), regole di confronto del [database](/sql/relational-databases/collations/set-or-change-the-database-collation), [database indipendenti](/sql/relational-databases/databases/contained-databases), [utenti indipendenti](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [compressione dei dati](/sql/relational-databases/data-compression/data-compression), [impostazioni di configurazione del database](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), operazioni sugli [indici online](/sql/relational-databases/indexes/perform-index-operations-online), [partizionamento](/sql/relational-databases/partitions/partitioned-tables-and-indexes)e [tabelle temporali](/sql/relational-databases/tables/temporal-tables) ([vedere la Guida introduttiva](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)).
- Funzionalità di sicurezza: [ruoli applicazione](/sql/relational-databases/security/authentication-access/application-roles), [maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking) ([Introduzione alla maschera dati dinamica del database SQL con la portale di Azure](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security)
- Funzionalità multimodello: [elaborazione di grafici](/sql/relational-databases/graphs/sql-graph-overview), [dati JSON](/sql/relational-databases/json/json-data-sql-server), [OpenXML](/sql/t-sql/functions/openxml-transact-sql), [spaziali](/sql/relational-databases/spatial/spatial-data-sql-server), [OPENJSON](/sql/t-sql/functions/openjson-transact-sql)e [indici XML](/sql/t-sql/statements/create-xml-index-transact-sql).



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Funzionalità di Azure Arc abilitate per SQL Istanza gestita

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> Disponibilità elevata RDBMS  
  
|Funzionalità|Istanza gestita SQL abilitato per Azure Arc|
|-------------|----------------|
|Log shipping|Sì| 
|Compressione backup|Sì|
|Snapshot del database|Sì|
|Istanza del cluster di failover Always On<sup>1</sup>| Non applicabile. Funzionalità simili disponibili |
|Gruppi di disponibilità AlwaysOn<sup>2</sup>|Sono pianificate le funzionalità a disponibilità elevata.|
|Gruppi di disponibilità di base <sup>2</sup>|Sono pianificate le funzionalità a disponibilità elevata.|
|Gruppo di disponibilità di commit della replica minima <sup>2</sup>|Sono pianificate le funzionalità a disponibilità elevata.|
|Gruppo di disponibilità senza cluster|Sì|
|Ripristino di pagine e file online|Sì|
|Indicizzazione online|Sì|
|Ricompilazioni degli indici online ripristinabili|Sì|
|Modifica dello schema online|Sì|
|Recupero rapido|Sì|
|Backup con mirroring|Sì|
|Aggiunta di memoria a caldo e CPU|Sì|
|Backup crittografato|Sì|
|Backup ibrido in Microsoft Azure (backup nell'URL)|Sì|

<sup>1</sup> nello scenario in cui si verifica un errore di Pod, una nuova istanza gestita SQL verrà avviata e ricollegata al volume permanente contenente i dati. [Altre informazioni sui volumi permanenti di Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes)sono disponibili qui.

<sup>2</sup> versioni future forniranno le funzionalità del gruppo di disponibilità 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> Scalabilità e prestazioni RDBMS  

|Funzionalità|Istanza gestita SQL abilitato per Azure Arc|
|-------------|----------------|
|columnstore|   Sì|
|File binari di oggetti di grandi dimensioni in indici columnstore cluster|    Sì|
|Ricompilazione degli indici columnstore non cluster online| Sì|
|OLTP in memoria|    Sì|
|Memoria principale persistente|    Sì|
|Partizionamento di tabelle e indici|  Sì
|Compressione dei dati|  Sì|
|Resource Governor| Sì|
|Parallelismo della tabella partizionata| Sì|
|Allocazione di una matrice di buffer e di memoria in pagine grandi con supporto NUMA|  Sì|
|Governance delle risorse di I/O|    Sì|
|Durabilità posticipata|    Sì|
|Ottimizzazione automatica|  Sì|
|Join adattivi in modalità batch| Sì|
|Feedback delle concessioni di memoria in modalità batch|  Sì|
|Esecuzione interleaved per funzioni con valori di tabella a più istruzioni|  Sì|
|Miglioramenti dell'inserimento bulk   |Sì|

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> Sicurezza RDBMS  
|Funzionalità|Istanza gestita SQL abilitato per Azure Arc|
|-------------|----------------|
|Sicurezza a livello di riga|    Sì|
|Always Encrypted|  Sì|
|Always Encrypted con enclave sicuri| No|
|Maschera dati dinamica|  Sì|
|Controllo di base|    Sì|
|Controllo con granularità fine| Sì|
|Crittografia trasparente del database|   Sì|
|Ruoli definiti dall'utente|    Sì|
|Database indipendenti|   Sì|
|Crittografia per backup|    Sì|

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> Gestione RDBMS  

|Funzionalità|Istanza gestita SQL abilitato per Azure Arc|
|-------------|----------------|
|Connessione amministrativa dedicata|    Sì|
|Supporto per script di PowerShell|  Sì|
|Supporto per le operazioni del componente dell'applicazione livello dati (DAC) - estrazione, distribuzione, aggiornamento, eliminazione| Sì
|Automazione dei criteri (controllo pianificato e modifica)   |Sì|
|Agente di raccolta dati relativi alle prestazioni|    Sì|
|Report di prestazioni standard   |Sì|
|Guide di piano e blocco del piano per le guide di piano| Sì|
|Query diretta di viste indicizzate (tramite hint NOEXPAND)|   Sì|
|Gestione automatica viste indicizzate    |Sì|
|Viste partizionate distribuite| Sì|
|Operazioni indicizzate parallele    |Sì|
|Utilizzo automatico di viste indicizzate da Query Optimizer|  Sì|
|Verifica di coerenza parallela |Sì|


### <a name="programmability"></a><a name="Programmability"></a> Programmability  

|Funzionalità|Istanza gestita SQL abilitato per Azure Arc|
|-------------|----------------|
|JSON|  Sì |       |
|Archivio query    |Sì    |       
|Temporale|  Sì |       
|Supporto XML nativo|    Sì |       
|Indicizzazione XML   |Sì    |       
|Funzionalità MERGE e UPSERT|   Sì |       
|Tipi di dati data e ora    |Sì    |       
|Supporto di internazionalizzazione|  Sì |       
|Ricerca full-text e semantica |    No      |
|Impostazione della lingua nelle query |Sì        |   
|Service Broker (messaggistica)|    Sì     |   
|Transact-SQL - endpoint|    Sì |       
|Grafico| Sì |   
|Machine Learning Services| No  |   
|PolyBase| No   |


### <a name="tools"></a>Strumenti

I Istanza gestita SQL abilitati per Azure Arc supportano diversi strumenti di dati che consentono di gestire i dati.

| **Strumento** | Istanza gestita SQL abilitato per Azure Arc|
| --- | --- | --- |
| Portale di Azure <sup>1</sup> | No |
| Interfaccia della riga di comando di Azure | No |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Sì |
| Azure PowerShell | Sì |
| [File BACPAC (esportazione)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Sì |
| [File BACPAC (importazione)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Sì |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Sì |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Sì |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Sì |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Sì |

<sup>1</sup> il portale di Azure viene usato solo per visualizzare le istanze gestite di SQL Azure Arc abilitate in modalità di sola lettura durante l'anteprima.


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> Funzionalità non supportate & servizi

Le funzionalità e i servizi seguenti non sono disponibili per la Istanza gestita SQL di Azure Arc abilitata. Il supporto di queste funzionalità aumenterà nel corso del tempo.

| Area | Funzionalità o servizio non supportato |
|-----|-----|
| **Motore di database** | Replica di tipo merge |
| &nbsp; | Stretch DB |
| &nbsp; | Query distribuita con connessioni di terze parti |
| &nbsp; | Server collegati a origini dati diverse da SQL Server e prodotti SQL di Azure |
| &nbsp; | Stored procedure estese di sistema (XP_CMDSHELL e così via) |
| &nbsp; | FileTable, FILESTREAM |
| &nbsp; | Assembly CLR con il set di autorizzazioni EXTERNAL_ACCESS o UNSAFE |
| &nbsp; | Estensione pool di buffer |
| **SQL Server Agent** |  Sottosistemi: CmdExec, PowerShell, Agente di lettura coda, SSIS, SSAS, SSRS |
| &nbsp; | Avvisi |
| &nbsp; | Backup gestito |
| **Disponibilità elevata** | Mirroring del database  |
| **Sicurezza** | Extensible Key Management |
| &nbsp; | Autenticazione AD per i server collegati | 
| &nbsp; | Autenticazione AD per i gruppi di disponibilità | 