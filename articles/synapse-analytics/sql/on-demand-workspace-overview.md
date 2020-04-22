---
title: SQL su richiesta (anteprima)
description: Informazioni su Synapse SQL su richiesta in Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8d4de424d5d4d6da1ee80e04b35e63ae29df57c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421305"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL su richiesta (anteprima) in Azure Synapse Analytics 

Ogni area di lavoro di Azure Synapse Analytics (anteprima) include endpoint SQL su richiesta (anteprima) che è possibile usare per eseguire query sui dati del lake.

SQL su richiesta è un servizio di query sui dati del data lake. Consente di accedere ai dati tramite le funzionalità seguenti:
 
- Una sintassi T-SQL familiare per l'esecuzione di query sul posto, senza la necessità di copiare i dati o caricarli in un archivio speciale. 
- Connettività integrata tramite l'interfaccia T-SQL, che offre un'ampia gamma di strumenti di business intelligence e query ad hoc, inclusi i driver più diffusi. 

SQL su richiesta è un sistema di elaborazione dei dati distribuito, progettato per dati e risorse di calcolo su vasta scala. SQL su richiesta consente di analizzare i Big Data in pochi secondi o minuti, a seconda del carico di lavoro. Grazie alla tolleranza di errore incorporata per l'esecuzione di query, il sistema garantisce un'affidabilità elevata e alte percentuali di successo anche per query a esecuzione prolungata che coinvolgono set di dati di grandi dimensioni.

SQL su richiesta è serverless, per cui non è necessario configurare un'infrastruttura o mantenere cluster. Un endpoint predefinito per questo servizio è disponibile in ogni area di lavoro di Azure Synapse, quindi è possibile iniziare a eseguire query sui dati non appena viene creata l'area di lavoro. Non sono previsti addebiti per le risorse riservate. Vengono addebitati solo i costi dei dati analizzati dalle query eseguite, per cui questo si tratta effettivamente di un modello con pagamento a consumo.  

Se si usa Spark nella pipeline di dati per la preparazione, la pulizia o l'arricchimento dei dati, è possibile [eseguire query su qualsiasi tabella Spark](develop-storage-files-spark-tables.md) creata nel processo, direttamente da SQL su richiesta. Usare [Collegamento privato](../security/how-to-connect-to-workspace-with-private-links.md) per introdurre l'endpoint SQL su richiesta nella [rete virtuale dell'area di lavoro gestita](../security/synapse-workspace-managed-vnet.md).  

## <a name="who-is-sql-on-demand-for"></a>A chi è destinato SQL su richiesta

Se è necessario esplorare dati nel lake, acquisirne informazioni o ottimizzare l'attuale pipeline di trasformazione dei dati, è possibile trarre vantaggio dall'uso di SQL su richiesta. Il servizio è indicato per gli scenari seguenti:

- Individuazione ed esplorazione di base: è possibile ragionare rapidamente sui dati in vari formati (Parquet, CSV, JSON) nel data lake, in modo da pianificare come estrarne informazioni dettagliate.
- Data warehouse logico: è possibile ottenere un'astrazione relazionale su dati non elaborati o disparati senza spostarli e trasformarli, per averne una visualizzazione sempre aggiornata.
- Trasformazione dei dati: un modo semplice, scalabile e a elevate prestazioni per trasformare i dati nel lake tramite T-SQL, in modo che possano essere inseriti in strumenti di business intelligence e di altro tipo o caricati in un archivio dati relazionale (database di Synapse SQL, Database SQL di Azure e così via).

Diversi ruoli professionali possono trarre vantaggio da SQL su richiesta:

- Gli ingegneri dei dati possono esplorare il lake, trasformare e preparare i dati usando questo servizio, nonché semplificare le pipeline di trasformazione dei dati. Per altre informazioni, vedere questa [esercitazione](tutorial-data-analyst.md).
- I data scientist possono ragionare rapidamente sul contenuto e sulla struttura dei dati nel lake, grazie a funzionalità come OPENROWSET e l'inferenza automatica dello schema.
- Gli analisti dei dati possono [esplorare i dati e le tabelle Spark](develop-storage-files-spark-tables.md) creati dai data scientist o dagli ingegneri dei dati usando un linguaggio T-SQL familiare o i loro strumenti preferiti, che possono connettersi a SQL su richiesta.
- I professionisti di business intelligence possono [creare rapidamente report di Power BI sui dati del lake](tutorial-connect-power-bi-desktop.md) e sulle tabelle Spark.

## <a name="what-do-i-need-to-do-to-start-using-it"></a>Come si può iniziare a usare il servizio?

L'endpoint SQL su richiesta è disponibile all'interno di ogni area di lavoro di Azure Synapse. È possibile creare un'area di lavoro e iniziare immediatamente a eseguire query sui dati usando gli strumenti con cui si ha familiarità.

## <a name="client-tools"></a>Strumenti client

SQL su richiesta consente di usare gli attuali strumenti di business intelligence e query ad hoc di SQL per accedere al data lake. Grazie alla familiare sintassi T-SQL, è possibile usare qualsiasi strumento in grado di stabilire connessioni TDS con le offerte SQL per [connettersi ed eseguire query su SQL](connect-overview.md) su richiesta. È possibile connettersi con Azure Data Studio ed eseguire query ad hoc oppure con Power BI per ottenere informazioni dettagliate in pochi minuti.

## <a name="is-full-t-sql-supported"></a>T-SQL è completamente supportato?

SQL su richiesta offre una superficie di attacco per l'esecuzione di query T-SQL, per alcuni aspetti leggermente migliorata/ampliata per supportare l'esecuzione di query su dati semistrutturati e non strutturati. Inoltre, alcuni aspetti del linguaggio T-SQL non sono supportati a causa della progettazione di SQL su richiesta. Ad esempio, la funzionalità DML non è attualmente supportata.

- Il carico di lavoro può essere organizzato in base a concetti usuali:
- Database: l'endpoint SQL su richiesta può includere più database.
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

SQL su richiesta non include archiviazione locale: nei database sono archiviati solo oggetti metadati. Di conseguenza, le funzionalità di T-SQL correlate ai concetti seguenti non sono supportate:

- Tabelle
- Trigger
- Viste materializzate
- Istruzioni DDL diverse da quelle correlate alle viste e alla sicurezza
- Istruzioni DML

### <a name="extensions"></a>Estensioni

Per garantire un'esperienza uniforme per l'esecuzione di query sul posto su dati che risiedono nei file in data lake, SQL su richiesta estende la funzione [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) con l'aggiunta delle funzionalità seguenti:

[Esecuzione di query su più file o cartelle](develop-storage-files-overview.md#query-multiple-files-or-folders)

[Formato di file Parquet](develop-storage-files-overview.md#parquet-file-format)

[Opzioni aggiuntive per l'uso di testo delimitato (carattere di terminazione del campo, carattere di terminatore della riga, carattere di escape)](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Lettura di un sottoinsieme selezionato di colonne](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Inferenza dello schema](develop-storage-files-overview.md#schema-inference)

[Funzione filename](develop-storage-files-overview.md#filename-function)

[Funzione filepath](develop-storage-files-overview.md#filepath-function)

[Uso di tipi complessi e strutture di dati annidate o ripetute](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Sicurezza

SQL su richiesta offre meccanismi per proteggere l'accesso ai dati.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrazione in Azure Active Directory e autenticazione a più fattori

SQL su richiesta consente di gestire a livello centralizzato le identità degli utenti di database e altri servizi Microsoft grazie all'[integrazione con Azure Active Directory](../../sql-database/sql-database-Azure AD-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Questa funzionalità semplifica la gestione delle autorizzazioni e ottimizza la sicurezza. Azure Active Directory (Azure AD) supporta [Multi-Factor Authentication](../../sql-database/sql-database-ssms-mfa-authentication-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA), per una maggiore sicurezza di dati e applicazioni, oltre a un processo Single Sign-On.

#### <a name="authentication"></a>Authentication

Per autenticazione di SQL su richiesta si intende il modo in cui gli utenti dimostrano la loro identità quando si connettono all'endpoint. Sono supportati due tipi di autenticazione:

- **Autenticazione SQL**

  Questo metodo di autenticazione usa nome utente e password.

- **Autenticazione di Azure Active Directory**

  Questo metodo di autenticazione usa identità gestite da Azure Active Directory. Per gli utenti di Azure AD, è possibile abilitare l'autenticazione a più fattori. [Quando possibile](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), usare l'autenticazione di Active Directory (sicurezza integrata).

#### <a name="authorization"></a>Autorizzazione

Per autorizzazione si intendono le operazioni che l'utente può eseguire in un database di SQL su richiesta, che sono controllate dalle appartenenze ai ruoli del database e dalle autorizzazioni a livello di oggetto dell'account utente.

Se si usa l'autenticazione SQL, l'utente SQL esiste solo in SQL su richiesta e le autorizzazioni hanno come ambito gli oggetti di SQL su richiesta. L'accesso a oggetti a protezione diretta in altri servizi, ad esempio Archiviazione di Azure, non può essere concesso direttamente all'utente SQL perché esiste solo nell'ambito di SQL su richiesta. L'utente SQL deve usare uno dei [tipi di autorizzazione supportati](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) per accedere ai file.

Se si usa l'autenticazione di Azure AD, l'utente può accedere a SQL su richiesta e ad altri servizi, ad esempio Archiviazione di Azure, e può concedere le autorizzazioni all'utente di Azure AD.

### <a name="access-to-storage-accounts"></a>Accesso agli account di archiviazione

Un utente connesso al servizio SQL su richiesta deve essere autorizzato ad accedere ed eseguire query sui file in Archiviazione di Azure. SQL su richiesta supporta i tipi di autorizzazione seguenti:

- **Firma di accesso condiviso**: fornisce accesso delegato alle risorse nell'account di archiviazione. Con una firma di accesso condiviso è possibile concedere ai client l'accesso alle risorse nell'account di archiviazione, senza condividere le chiavi dell'account. La firma di accesso condiviso offre un controllo granulare sul tipo di accesso concesso ai client a cui viene assegnata, tra cui intervallo di validità, autorizzazioni concesse, intervallo di indirizzi IP accettabile e protocollo accettabile (HTTPS/HTTP).

- **Identità utente**: nota anche come "pass-through", è un tipo di autorizzazione per cui per concedere l'accesso ai dati viene usata l'identità dell'utente di Azure AD che ha eseguito l'accesso a SQL su richiesta. Prima di accedere ai dati, l'amministratore di Archiviazione di Azure deve concedere le apposite autorizzazioni all'utente di Azure AD. Questo tipo di autorizzazione usa l'utente di Azure AD connesso a SQL su richiesta, per cui non è supportato per altri tipi di utente SQL.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla connessione all'endpoint e sull'esecuzione di query sui file sono disponibili negli articoli seguenti: 
- [Connettersi all'endpoint](connect-overview.md)
- [Eseguire query sui file](develop-storage-files-overview.md)
