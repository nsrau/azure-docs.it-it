---
title: Pool SQL serverless (anteprima)
description: Informazioni sul pool SQL serverless in Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: e0d47567c3bc0b05c47efafa3bdc8b297a7bdbea
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93306880"
---
# <a name="serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Pool SQL serverless (anteprima) in Azure Synapse Analytics 

Ogni area di lavoro di Azure Synapse Analytics (anteprima) include endpoint del pool SQL serverless (anteprima) che è possibile usare per eseguire query sui dati del data lake.

Il pool SQL serverless è un servizio di query sui dati del data lake. Consente di accedere ai dati tramite le funzionalità seguenti:
 
- Una sintassi T-SQL familiare per l'esecuzione di query sul posto, senza la necessità di copiare i dati o caricarli in un archivio speciale. 
- Connettività integrata tramite l'interfaccia T-SQL, che offre un'ampia gamma di strumenti di business intelligence e query ad hoc, inclusi i driver più diffusi. 

Il pool SQL serverless è un sistema di elaborazione dati distribuito, progettato per dati e funzioni di calcolo su vasta scala. Consente di analizzare i Big Data in pochi secondi o minuti, a seconda del carico di lavoro. Grazie alla tolleranza di errore incorporata per l'esecuzione di query, il sistema garantisce un'affidabilità elevata e alte percentuali di successo anche per query a esecuzione prolungata che coinvolgono set di dati di grandi dimensioni.

Poiché questo pool SQL è serverless, non è necessario configurare un'infrastruttura o gestire cluster. Un endpoint predefinito per questo servizio è disponibile in ogni area di lavoro di Azure Synapse, quindi è possibile iniziare a eseguire query sui dati non appena viene creata l'area di lavoro. 

Non sono previsti addebiti per le risorse riservate. Vengono addebitati solo i costi dei dati elaborati dalle query eseguite e pertanto si tratta effettivamente di un modello con pagamento a consumo.  

Se si usa Apache Spark per Azure Synapse nella pipeline di dati per la preparazione, la pulizia o l'arricchimento dei dati, è possibile [eseguire query su qualsiasi tabella esterna Spark](develop-storage-files-spark-tables.md) creata nel processo, direttamente dal pool SQL serverless. Usare [Collegamento privato](../security/how-to-connect-to-workspace-with-private-links.md) per inserire l'endpoint del pool SQL serverless nella [rete virtuale dell'area di lavoro gestita](../security/synapse-workspace-managed-vnet.md).  

## <a name="serverless-sql-pool-benefits"></a>Vantaggi del pool SQL serverless

Se è necessario esplorare dati nel data lake, acquisirne informazioni dettagliate o ottimizzare l'attuale pipeline di trasformazione dei dati, è possibile trarre vantaggio dall'uso del pool SQL serverless. Il servizio è indicato per gli scenari seguenti:

- Individuazione ed esplorazione di base: è possibile ragionare rapidamente sui dati in vari formati (Parquet, CSV, JSON) nel data lake, in modo da pianificare come estrarne informazioni dettagliate.
- Data warehouse logico: è possibile ottenere un'astrazione relazionale su dati non elaborati o disparati senza spostarli e trasformarli, per averne una visualizzazione sempre aggiornata.
- Trasformazione dei dati: un modo semplice, scalabile e a elevate prestazioni per trasformare i dati nel lake tramite T-SQL, in modo che possano essere inseriti in strumenti di business intelligence e di altro tipo o caricati in un archivio dati relazionale (database di Synapse SQL, Database SQL di Azure e così via).

Diversi ruoli professionali possono trarre vantaggio dal pool SQL serverless:

- Gli ingegneri dei dati possono esplorare il lake, trasformare e preparare i dati usando questo servizio, nonché semplificare le pipeline di trasformazione dei dati. Per altre informazioni, vedere questa [esercitazione](tutorial-data-analyst.md).
- I data scientist possono ragionare rapidamente sul contenuto e sulla struttura dei dati nel lake, grazie a funzionalità come OPENROWSET e l'inferenza automatica dello schema.
- Gli analisti dei dati possono [esplorare i dati e le tabelle esterne Spark](develop-storage-files-spark-tables.md) creati dai data scientist o dagli ingegneri dei dati usando il linguaggio T-SQL con cui hanno familiarità o i loro strumenti preferiti, che possono connettersi al pool SQL serverless.
- I professionisti di business intelligence possono [creare rapidamente report di Power BI sui dati del lake](tutorial-connect-power-bi-desktop.md) e sulle tabelle Spark.

## <a name="how-to-start-using-serverless-sql-pool"></a>Come iniziare a usare il pool SQL serverless

L'endpoint del pool SQL serverless è disponibile all'interno di ogni area di lavoro di Azure Synapse. È possibile creare un'area di lavoro e iniziare immediatamente a eseguire query sui dati usando gli strumenti con cui si ha familiarità.

## <a name="client-tools"></a>Strumenti client

Il pool SQL serverless consente di usare gli attuali strumenti di business intelligence e query ad hoc di SQL per accedere al data lake. Grazie alla familiare sintassi T-SQL, è possibile usare qualsiasi strumento in grado di stabilire connessioni TDS con le offerte SQL per [connettersi ed eseguire query su SQL](connect-overview.md) su richiesta. È possibile connettersi con Azure Data Studio ed eseguire query ad hoc oppure con Power BI per ottenere informazioni dettagliate in pochi minuti.

## <a name="t-sql-support"></a>Supporto di T-SQL

Il pool SQL serverless offre un'area per l'esecuzione di query T-SQL che per alcuni aspetti è stata leggermente migliorata/ampliata per supportare l'esecuzione di query su dati semistrutturati e non strutturati. Inoltre, alcuni aspetti del linguaggio T-SQL non sono supportati a causa della progettazione del pool SQL serverless. Ad esempio, la funzionalità DML non è attualmente supportata.

- Il carico di lavoro può essere organizzato in base a concetti usuali:
- Database: l'endpoint del pool SQL serverless può includere più database.
- Schemi: all'interno di un database sono presenti uno o più gruppi di proprietà di oggetti denominati schemi.
- Viste
- Risorse esterne: origini dati, formati di file e tabelle

La sicurezza può essere applicata tramite:

- Account di accesso e utenti
- Credenziali per controllare l'accesso agli account di archiviazione
- Concessione, negazione e revoca di autorizzazioni per ogni livello di oggetto
- Integrazione di Azure Active Directory

Funzionalità di T-SQL supportate:

- L'intera superficie di attacco [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) è supportata, inclusa la maggioranza di funzioni SQL
- CETAS - CREATE EXTERNAL TABLE AS SELECT
- Istruzioni DDL correlate solo alle viste e alla sicurezza

Il pool SQL serverless non ha uno spazio di archiviazione locale: nei database sono archiviati solo oggetti metadati. Di conseguenza, le funzionalità di T-SQL correlate ai concetti seguenti non sono supportate:

- Tabelle
- Trigger
- Viste materializzate
- Istruzioni DDL diverse da quelle correlate alle viste e alla sicurezza
- Istruzioni DML

### <a name="extensions"></a>Estensioni

Per garantire un'esperienza uniforme per l'esecuzione di query sul posto su dati che risiedono nei file in data lake, il pool SQL serverless estende la funzione [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) con l'aggiunta delle funzionalità seguenti:

[Esecuzione di query su più file o cartelle](query-data-storage.md#query-multiple-files-or-folders)

[Formato di file Parquet](query-data-storage.md#query-parquet-files)

[Opzioni aggiuntive per l'uso di testo delimitato (carattere di terminazione del campo, carattere di terminatore della riga, carattere di escape)](query-data-storage.md#query-csv-files)

[Lettura di un sottoinsieme selezionato di colonne](query-data-storage.md#read-a-chosen-subset-of-columns)

[Inferenza dello schema](query-data-storage.md#schema-inference)

[Funzione filename](query-data-storage.md#filename-function)

[Funzione filepath](query-data-storage.md#filepath-function)

[Uso di tipi complessi e strutture di dati annidate o ripetute](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Sicurezza

Il pool SQL serverless offre meccanismi per proteggere l'accesso ai dati.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori

Il pool SQL serverless consente di gestire a livello centralizzato le identità degli utenti di database e altri servizi Microsoft grazie all'[integrazione con Azure Active Directory](../../azure-sql/database/authentication-aad-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory (Azure AD) supporta [Multi-Factor Authentication](../../azure-sql/database/authentication-mfa-ssms-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA), per una maggiore sicurezza di dati e applicazioni, oltre a un processo Single Sign-On.

#### <a name="authentication"></a>Authentication

Per autenticazione del pool SQL serverless si intende il modo in cui gli utenti dimostrano la loro identità quando si connettono all'endpoint. Sono supportati due tipi di autenticazione:

- **Autenticazione SQL**

  Questo metodo di autenticazione usa nome utente e password.

- **Autenticazione di Azure Active Directory** :

  Questo metodo di autenticazione usa identità gestite da Azure Active Directory. Per gli utenti di Azure AD, è possibile abilitare l'autenticazione a più fattori. [Quando possibile](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), usare l'autenticazione di Active Directory (sicurezza integrata).

#### <a name="authorization"></a>Autorizzazione

Per autorizzazione si intendono le operazioni che l'utente può eseguire in un database del pool SQL serverless, che sono controllate dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente.

Se si usa l'autenticazione SQL, l'utente SQL esiste solo nel pool SQL serverless e le autorizzazioni sono limitate all'ambito degli oggetti del pool SQL serverless. L'accesso a oggetti a protezione diretta in altri servizi, ad esempio Archiviazione di Azure, non può essere concesso direttamente all'utente SQL perché esiste solo nell'ambito del pool SQL serverless. L'utente SQL deve usare uno dei [tipi di autorizzazione supportati](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) per accedere ai file.

Se si usa l'autenticazione di Azure AD, un utente può accedere al pool SQL serverless e ad altri servizi, come Archiviazione di Azure, e può concedere le autorizzazioni all'utente di Azure AD.

### <a name="access-to-storage-accounts"></a>Accesso agli account di archiviazione

Un utente connesso al servizio pool SQL serverless deve essere autorizzato ad accedere ed eseguire query sui file in Archiviazione di Azure. Il pool SQL serverless supporta i tipi di autorizzazione seguenti:

- **Firma di accesso condiviso** : fornisce accesso delegato alle risorse nell'account di archiviazione. Con una firma di accesso condiviso è possibile concedere ai client l'accesso alle risorse nell'account di archiviazione, senza condividere le chiavi dell'account. La firma di accesso condiviso offre un controllo granulare sul tipo di accesso concesso ai client a cui viene assegnata, tra cui intervallo di validità, autorizzazioni concesse, intervallo di indirizzi IP accettabile e protocollo accettabile (HTTPS/HTTP).

- **Identità utente** : nota anche come "pass-through", è un tipo di autorizzazione in cui per concedere l'accesso ai dati viene usata l'identità dell'utente di Azure AD che ha eseguito l'accesso al pool SQL serverless. Prima di accedere ai dati, l'amministratore di Archiviazione di Azure deve concedere le apposite autorizzazioni all'utente di Azure AD. Questo tipo di autorizzazione usa l'utente di Azure AD connesso al pool SQL serverless, pertanto non è supportato per altri tipi di utente SQL.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla connessione all'endpoint e sull'esecuzione di query sui file sono disponibili negli articoli seguenti: 
- [Connettersi all'endpoint](connect-overview.md)
- [Eseguire query sui file](develop-storage-files-overview.md)
