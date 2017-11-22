---
title: "Novità di Azure Data Catalog | Microsoft Docs"
description: "Questo articolo fornisce una panoramica delle nuove funzionalità aggiunte ad Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/13/2017
ms.author: maroche
ms.openlocfilehash: 2839c265596d98834fb4071dea1684d5a6669907
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="whats-new-in-azure-data-catalog"></a>Novità di Azure Data Catalog
Gli aggiornamenti ad **Azure Data Catalog** vengono rilasciati a intervalli regolari. Non tutte le nuove versioni includono tuttavia nuove funzionalità destinate all'utente, in quanto alcune sono incentrate sulle funzionalità del servizio back-end. Questa pagina illustra le nuove funzionalità destinate all'utente aggiunte al servizio Azure Data Catalog.

## <a name="whats-new-for-november-2017"></a>Novità di novembre 2017 
A partire da novembre 2017 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Supporto per il collegamento diretto ai termini del glossario aziendale specifico nel portale Data Catalog. Gli utenti possono copiare i collegamenti dal glossario aziendale e incorporarli nei documenti, messaggi di posta elettronica, report o altre posizioni per collegarli direttamente alla definizione dei termini del glossario.
* Supporto per le entità servizio di Azure Active Directory. Gli amministratori di Data Catalog possono autorizzare applicazioni client che usano le entità servizio ad accedere al catalogo e possono concedere a tali applicazioni autorizzazioni specifiche nello stesso modo in cui possono concedere autorizzazioni a utenti e gruppi di sicurezza. Per altre informazioni vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).
* Supporto per l'autenticazione di Azure Active Directory durante la connessione al Database SQL di Azure e alle origini dati di Azure SQL Data Warehouse usando lo strumento di registrazione origine dati Data Catalog. Per altre informazioni vedere [Usare l'autenticazione di Azure Active Directory per l'autenticazione di un database SQL o di SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>Novità di settembre 2017 
Da settembre 2017 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Supporto per l'estrazione di metadati di relazioni di join da origini dati DB2 quando si esegue la registrazione di tabelle correlate con lo strumento di registrazione dell'origine dati.
* Supporto per la registrazione di origini dati MongoDB versione 3.4 usando lo strumento di registrazione dell'origine dati.
* Supporto per l'eliminazione di tutti i metadati per gli oggetti contenuti in una singola operazione durante la rimozione di un database o di un altro contenitore da Data Catalog.
* Supporto per la visualizzazione di fino a 1.000 tag, termini del glossario aziendale o altri facet di ricerca quando si affina una ricerca nel portale di Data Catalog.


## <a name="whats-new-for-august-2017"></a>Novità di agosto 2017 
A partire da agosto 2017 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

*   Nuovo esempio per sviluppatori disponibile per la creazione e la gestione dei metadati di relazione con l'API REST di Data Catalog. L'esempio *Import relationship information into Data Catalog* (Importare informazioni sulle relazioni in Data Catalog) è disponibile nella [pagina degli esempi di codice di Data Catalog](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Supporto per l'estrazione di metadati di relazione join da origini dati Teradata durante la registrazione di tabelle correlate con lo strumento di registrazione dell'origine dati.
* Supporto per gli oggetti funzione con valori di tabella di SQL Server durante la registrazione delle origini dati SQL Server con lo strumento di registrazione dell'origine dati.
* Vari aggiornamenti e miglioramenti per aumentare le prestazioni e l'usabilità del portale di Data Catalog.

## <a name="whats-new-for-july-2017"></a>Novità di luglio 2017 
A partire da luglio 2017 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:
*   Supporto per un controllo più granulare sulle operazioni consentite sui metadati, tra cui:
    - Gli amministratori del catalogo possono limitare la capacità degli utenti di aggiungere tag e i metadati correlati al catalogo, abilitando l'accesso in sola lettura al catalogo.
    - Gli amministratori del catalogo possono limitare la capacità degli utenti di registrare nuove origini dati nel catalogo.
    - Gli amministratori del catalogo possono limitare la capacità degli utenti di diventare proprietari di metadati dell'asset di dati nel catalogo.
    - È possibile concedere autorizzazioni a utenti e gruppi di sicurezza di Azure Active Directory per facilitare la gestione delle autorizzazioni.
* Supporto per le relazioni tra gli asset di dati registrati e rilevamento di asset di dati correlati nel portale di Data Catalog, tra cui:
    - Estrazione dei metadati di relazione da origini dati SQL Server (incluso il database SQL di Azure), Oracle e MySQL durante l'uso dello strumento di registrazione dell'origine dati di Data Catalog.
    - Rilevamento di asset di dati correlati durante la visualizzazione dei metadati degli asset nel portale di Data Catalog.
    - Operazioni per definire, rilevare e gestire le relazioni tra gli asset di dati con l'API REST di Data Catalog.

Per altre informazioni sulla gestione delle autorizzazioni in Data Catalog, vedere [Come proteggere l'accesso al catalogo dati e agli asset di dati](data-catalog-how-to-secure-catalog.md).
Per altre informazioni sulle relazioni in Data Catalog, vedere [Come visualizzare gli asset di dati correlati in Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Novità di giugno 2017 
A partire da giugno 2017 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:
*   Supporto per le origini dati Sybase, Apache Cassandra e MongoDB. Gli utenti ora possono registrare e trovare i database e le tabelle di Cassandra e MongoDB e i database, le tabelle e le visualizzazioni di Sybase.

> [!NOTE]
> Quando si registrano tabelle di MongoDB e Cassandra che includono colonne con tipi di dati complessi, ad esempio record e matrici, queste colonne non verranno incluse nei metadati aggiunti a Data Catalog.

## <a name="whats-new-for-may-2017"></a>Novità di maggio 2017 
A partire da maggio 2017 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:
*   È disponibile un nuovo esempio per gli sviluppatori per l'importazione in blocco dei termini del glossario aziendale. L'esempio di importazione in massa del glossario è disponibile nella [pagina Esempi per sviluppatori di Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Supporto per la modifica delle informazioni di connessione ODBC nel portale di Azure Data Catalog. I proprietari degli asset di dati e gli amministratori di Data Catalog ora possono modificare le informazioni di connessione per le origini dati ODBC registrate senza dover registrare nuovamente le origini dati.
*   Supporto per gli URL selezionabili nelle definizioni e nelle descrizioni del glossario aziendale. Quando vengono inclusi URL nelle proprietà di descrizione e definizione per i termini del glossario aziendale, gli URL verranno visualizzati come collegamenti ipertestuali nel portale di Data Catalog.
*   Supporto per la visualizzazione dei nomi degli esperti oltre che degli indirizzi di posta elettronica. Quando si visualizzano gli esperti nelle proprietà di un asset di dati nel portale di Data Catalog, nella descrizione comando verranno visualizzati nome e cognome dell'esperto da Azure Active Directory.

## <a name="whats-new-for-april-2017"></a>Novità di aprile 2017 
A partire da aprile 2017 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:
*   Supporto per origini dati ODBC. Gli utenti ora possono registrare e trovare i database, le tabelle e le visualizzazioni ODBC usando lo strumento di registrazione dell'origine dati di Data Catalog.

## <a name="whats-new-for-march-2017"></a>Novità di marzo 2017 
A partire da marzo 2017 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:
*   Supporto per l'uso dei gruppi di sicurezza AAD per gli amministratori di Data Catalog.
*   Azure Data Catalog è ora disponibile in una nuova area di Azure. I clienti possono effettuare il provisioning di Azure Data Catalog nell'area Stati Uniti centro-occidentali, oltre a Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale, Australia orientale, Europa settentrionale e Asia sud-orientale. Per altre informazioni, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Novità di febbraio 2017 
A partire da febbraio 2017 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:
*   Supporto per impostazioni avanzate nello strumento di registrazione dell'origine dati di Data Catalog. Gli utenti possono specificare i valori di timeout comando quando registrano origini dati di grandi dimensioni.
*   Supporto per le origini dati FTP e PostgreSQL. Gli utenti ora possono registrare e trovare i file e le directory FTP e i database, le tabelle e le visualizzazioni di PostgreSQL.

## <a name="whats-new-for-january-2017"></a>Novità di gennaio 2017 
A partire da gennaio 2017 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:
*   Azure Data Catalog ora è conforme a [CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification).
*   Integrazione con [Introduzione a Microsoft Power Query per Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Gli utenti di Excel possono condividere e trovare le query usando Azure Data Catalog da Excel. Questa funzionalità è disponibile per gli utenti con licenze Power BI Pro.

## <a name="whats-new-for-december-2016"></a>Novità di dicembre 2016
A partire da dicembre 2016 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:
*   Azure Data Catalog ora è conforme a [HIPAA](https://www.microsoft.com/trustcenter/Compliance/HIPAA) e alle [clausole del modello UE](https://www.microsoft.com/TrustCenter/Compliance/EU-Model-Clauses).
*   Supporto per la modifica delle informazioni di connessione dell'origine dati. I proprietari degli asset di dati e gli amministratori di Data Catalog ora possono modificare le informazioni di connessione per le origini dati registrate senza dover registrare nuovamente le origini dati.
*   Supporto per le origini dati Salesforce.com. Gli utenti ora possono registrare e trovare gli oggetti Salesforce.


## <a name="whats-new-for-november-2016"></a>Novità di novembre 2016
A partire da novembre 2016 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:
*   Azure Data Catalog ora è conforme a [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) e [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018).
*   Supporto per la registrazione manuale delle origini dati ODBC usano il portale di Data Catalog e l'API REST.

## <a name="whats-new-for-september-2016"></a>Novità di settembre 2016
A partire da settembre 2016 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Supporto per origini dati IBM DB2. Gli utenti possono ora registrare e individuare database, tabelle e viste DB2.
* Supporto per le origini dati Azure Cosmos DB. Gli utenti ora possono registrare e trovare i database e le raccolte di Cosmos DB.
* Supporto per la personalizzazione del nome del catalogo nel portale di Data Catalog. Gli amministratori del catalogo possono ora specificare il testo che verrà visualizzato nel titolo del portale, ad esempio il nome dell'organizzazione.

## <a name="whats-new-for-august-2016"></a>Novità di agosto 2016
A partire da agosto 2016 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Miglioramenti nella registrazione di origini dati SQL Server Master Data Services (MDS). Gli utenti possono ora includere anteprime e profili dati durante la registrazione di entità MDS con il tool di registrazione delle origini dati di Data Catalog.
* Supporto di ricerche salvate aziendali definite dall'amministratore. Al momento del salvataggio di una ricerca nel portale di Data Catalog, gli amministratori del catalogo dati possono ora scegliere di salvare le ricerche per uso personale o per tutti gli utenti del catalogo. Le ricerche salvate aziendali vengono condivise con tutti gli utenti del catalogo e possono offrire punti di partenza standardizzati per l'individuazione delle origini dati.
* Aggiornamenti alla visualizzazione delle proprietà nel portale di Data Catalog. Tutte le proprietà degli asset di dati vengono ora visualizzate e gestite in un singolo riquadro ridimensionabile, in modo da garantire maggiore coerenza e individuabilità.

## <a name="whats-new-for-july-2016"></a>Novità di luglio 2016
A partire da luglio 2016 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Supporto per origini dati SQL Server Master Data Services (MDS). Gli utenti possono ora registrare e individuare modelli ed entità MDS.
* Supporto per le stored procedure di SQL Server. Gli utenti possono ora registrare e individuare oggetti stored procedure in origini dati SQL Server.
* Supporto di lingue aggiuntive nel portale e nel tool di registrazione delle origini dati di Azure Data Catalog, per un totale di 18 lingue supportate. L'esperienza utente di Azure Data Catalog è localizzata in base alle preferenze della lingua configurate in Windows o nel Web browser.
* Aggiornamenti e perfezionamento della home page del portale di Data Catalog, che includono miglioramenti delle prestazioni e un'esperienza utente semplificata.

## <a name="whats-new-for-june-2016"></a>Novità di giugno 2016
A partire da giugno 2016 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Supporto del ridimensionamento delle colonne nella visualizzazione elenco quando si individuano asset di dati nel portale di Data Catalog. Gli utenti possono ora ridimensionare singole colonne per leggere più facilmente i metadati più estesi degli asset, come tag e descrizioni.
* Aggiunta di Power Query per Excel al menu "Apri in" nel portale di Data Catalog. Gli utenti possono ora aprire le origini dati supportate in Excel 2016, Excel 2010 ed Excel 2013 con il componente aggiuntivo [Power Query per Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) installato.
* Supporto per origini dati Archiviazione tabelle di Azure. Gli utenti possono ora registrare e individuare oggetti tabella in origini dati Archiviazione tabelle di Azure.

## <a name="whats-new-for-may-2016"></a>Novità di maggio 2016
A partire da maggio 2016 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Un glossario aziendale che consente agli amministratori di catalogo di definire termini aziendali e gerarchie per creare un vocabolario aziendale comune. Gli utenti possono assegnare tag ad asset di dati registrati usando termini di glossario, individuando e comprendendo così, in modo più semplice, il contenuto del catalogo. Per altre informazioni, vedere [Come configurare il glossario aziendale per l'assegnazione di tag regolamentata](data-catalog-how-to-business-glossary.md)  
* Miglioramenti del glossario aziendale di Data Catalog, per consentire agli utenti di aggiornare più termini del glossario con una singola operazione. Gli utenti possono selezionare più termini per modificare i campi seguenti:
  * Termine padre: l'utente può selezionare un nuovo termine padre e tutti i termini selezionati vengono aggiornati diventando figli del termine padre scelto. Se i termini selezionati hanno lo stesso padre, tale padre viene visualizzato nella casella di testo. In caso contrario, il campo Termine padre è vuoto.   
  * Tag e stakeholder: gli utenti possono aggiungere e rimuovere tag e stakeholder per più termini del glossario nello stesso modo in cui si assegnano tag a più asset di dati.

> [!NOTE]
> Il glossario aziendale è disponibile nell'edizione Standard di Azure Data Catalog. L'edizione gratuita non prevede l'assegnazione di tag regolamentata né un glossario aziendale tra le funzionalità.

## <a name="whats-new-for-march-2016"></a>Novità di marzo 2016
A partire da marzo 2016 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Un endpoint API REST consolidato per l'accesso a livello di programmazione alle funzionalità di ricerca e alle funzionalità di gestione degli asset del servizio Azure Data Catalog. L'endpoint API di ricerca e l'endpoint API di catalogo sono stati dichiarati obsoleti e interrotti il 21 marzo 2016. Non sono state apportate modifiche alla semantica dell'API. È stato modificato solo l'endpoint dell'URI. Per altre informazioni, vedere le [informazioni di riferimento sull'API REST di Azure Data Catalog](https://msdn.microsoft.com/library/azure/mt267595.aspx). Per esempi di API, vedere gli [esempi per sviluppatori di Azure Data Catalog](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Novità di febbraio 2016
A partire da febbraio 2016 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Una nuova esperienza di selezione dell'origine dati riprogettata nel tool di registrazione delle origini dati di Azure Data Catalog. Lo strumento di registrazione delle origini dati è stato aggiornato in modo da poter essere trovato e selezionato più facilmente dalle origini dati supportate da Azure Data Catalog.
* Il portale e lo strumento di registrazione delle origini dati di Azure Data Catalog supportano ora altre 10 lingue. Oltre all'inglese, l'esperienza di Azure Data Catalog è ora disponibile in tedesco, spagnolo, francese, italiano, giapponese, coreano, portoghese brasiliano, russo, cinese semplificato e cinese tradizionale. L'esperienza utente di Azure Data Catalog è localizzata in base alle preferenze della lingua configurate in Windows o nel Web browser dell'utente.
* Supporto della replica geografica dei dati di Azure Data Catalog per la continuità aziendale e il ripristino di emergenza. Tutto il contenuto di Azure Data Catalog, compresi i metadati delle origini dati e le annotazioni di crowdsourcing, vengono ora replicati tra due aree di Azure senza costi aggiuntivi per i clienti. Le aree di Azure sono pre-abbinate, ad almeno 500 miglia di distanza, e seguono il mapping come descritto in [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure](../best-practices-availability-paired-regions.md).
* Supporto della modifica della sottoscrizione di Azure usata da Azure Data Catalog. Gli amministratori di Azure Data Catalog possono usare la pagina Impostazioni del portale di Azure Data Catalog per selezionare un'altra sottoscrizione di Azure ai fini della fatturazione.

## <a name="whats-new-for-january-2016"></a>Novità di gennaio 2016
A partire da gennaio 2016 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Supporto per la registrazione manuale di tipi di origini dati aggiuntive. Ora è possibile usare "Crea voce manuale" nel portale di Azure Data Catalog oppure usare l'API REST di Azure Data Catalog per registrare le origini dati seguenti:
  * OData: funzione, set di entità e contenitore di entità
  * HTTP: file, endpoint, report e sito
  * File system: file
  * SharePoint: elenco
  * FTP: file e directory
  * Salesforce.com: oggetto
  * DB2: tabella, vista e database
  * PostgreSQL: tabella, vista e database
* Supporto per "Apri in SQL Server Data Tools" per le origini dati SQL Server, inclusi il database SQL di Azure e Azure SQL Data Warehouse.  
* Supporto per la registrazione e l'individuazione di viste e pacchetti SAP HANA. Ora è possibile registrare le origini dati SAP HANA usando lo strumento di registrazione delle origini dati di Azure Data Catalog, oltre ad annotare e individuare le origini dati SAP HANA registrate tramite il portale di Azure Data Catalog.
* Possibilità di aggiungere e rimuovere asset di dati nel portale di Azure Data Catalog. È possibile scegliere di bloccare gli asset di dati per semplificarne la riscoperta e il riuso.
* Nuova home page riprogettata nel portale di Azure Data Catalog. La nuova home page include approfondimenti sulle attuali attività degli utenti, tra cui gli asset pubblicati di recente, gli asset bloccati e le ricerche salvate, oltre ad approfondimenti sulle attività nel catalogo in generale.
* Supporto per impostazioni utente persistenti nel portale di Azure Data Catalog. Le impostazioni di esperienza utente, tra cui la visualizzazione griglia o riquadro, il numero di risultati per pagina, l'attivazione o la disattivazione di evidenziazione dei risultati, sono persistenti tra le sessioni utente.
* Azure Data Catalog è ora disponibile in due nuove aree di Azure. I clienti possono eseguire il provisioning di Azure Data Catalog in Europa settentrionale e Asia sud-orientale, oltre a Stati Uniti orientali, Stati Uniti occidentali, Europa occidentale e Australia orientale. Per altre informazioni, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Apri in SQL Server Data Tools" richiede l'installazione di Visual Studio 2013 con Update 4 e degli strumenti di SQL Server. Per installare la versione più recente di SQL Server Data Tools, vedere [Scaricare SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>Novità di dicembre 2015
A partire da dicembre 2015 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Supporto per profili dati per le origini dati di SQL Data Warehouse di Azure. Durante la registrazione di tabelle e viste di SQL Data Warehouse di Azure, gli utenti possono scegliere di includere metriche dei profili dati con i metadati estratti dall'origine dati.
* Supporto per la registrazione e l'individuazione degli oggetti e dei database MySQL. Gli utenti possono registrare le origini dati MySQL tramite il tool di registrazione delle origini dati di Azure Data Catalog e possono annotare e individuare le origini dati MySQL registrate usando il portale di Azure Data Catalog.
* Supporto per l'autenticazione SPNEGO e Windows per le origini dati di Teradata. Durante la registrazione di tabelle e viste Teradata, gli utenti possono scegliere di connettersi a Teradata mediante autenticazione SPNEGO e Windows, nonché LDAP e TD2.
* Supporto per le origini dati di Archivio Azure Data Lake. Gli utenti possono ora registrarsi e individuare le origini dati di Archivio Azure Data Lake tramite Azure Data Catalog.
* Supporto per specificare manualmente le impostazioni di proxy di rete nello strumento di registrazione delle origini dati di Azure Data Catalog. Gli utenti possono selezionare "Modificare le impostazioni di proxy" nella pagina di benvenuto dello strumento e possono specificare l'indirizzo proxy e la porta che verranno usati dallo strumento.

## <a name="whats-new-for-november-2015"></a>Novità di novembre 2015
A partire da novembre 2015 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Possibilità di visualizzare e copiare le stringhe di connessione dall'interno del portale di Azure Data Catalog per origini dati SQL Server, incluso database SQL di Azure, e origini dati Oracle. Gli utenti possono fare clic sul collegamento "Visualizza stringhe di connessione" nelle informazioni di connessione per una tabella di SQL Server o Oracle, vista o database, per visualizzare le stringhe di connessione usate per la connessione all'origine dati. Le stringhe di connessione ADO.NET, ODBC, OLEDB e JDBC sono disponibili per le origini dati di SQL Server. Le stringhe di connessione ODBC e OLEDB vengono fornite per le origini dati di Oracle.
* Supporto per includere i profili dati durante la registrazione delle tabelle Teradata e visualizzazioni.
* Supporto per "Apri in Power BI Desktop" per le origini di SQL Server (inclusi i database SQL di Azure e SQL Data Warehouse di Azure), SQL Server Analysis Services. Archiviazione di Azure e HDFS.  
* Supporto per l'autenticazione LDAP per le origini dati di Teradata. Durante la registrazione di tabelle e viste Teradata, gli utenti possono scegliere di connettersi a Teradata mediante autenticazione LDAP e TD2.
* Supporto per "Apri in Excel" per le origini dati di Teradata.
* Supporto per termini di ricerca recenti nel portale di Azure Data Catalog. Durante la ricerca nel portale, gli utenti possono selezionare termini di ricerca usati di recente per accelerare l'esperienza di individuazione.
* Supporto per l’anteprima per le origini dati Teradata. Durante la registrazione delle tabelle e viste Teradata, gli utenti possono scegliere di includere record snapshot con i metadati estratti dall'origine dati.
* Supporto per "Apri in Excel" per le origini dati di SQL Data Warehouse di Azure.
* Supporto per la definizione e la modifica di schemi a livello di colonna per gli asset di dati registrati manualmente. Dopo aver creato manualmente un asset di dati tramite il portale di Azure Data Catalog, gli utenti possono aggiungere definizioni di colonna nelle proprietà dell'asset di dati.
* Supporto per query di possesso durante la ricerca in Azure Data Catalog, per abilitare l'individuazione di asset di dati registrati che possiedono metadati specifici. La sintassi di query di Azure Data Catalog ora include:

| Sintassi delle query | Scopo |
| --- | --- |
| `has:previews` |Trova gli asset di dati che includono un'anteprima |
| `has:documentation` |Trova gli asset di dati per i quali è stata fornita la documentazione |
| `has:tableDataProfiles` |Trova gli asset di dati con informazioni sul profilo dei dati a livello di tabella |
| `has:columnsDataProfiles` |Trova gli asset di dati con informazioni sul profilo dei dati a livello di colonna |


> [!NOTE]
> "Apri in Power BI Desktop" richiede che sia installata una versione corrente dell'applicazione Desktop di Power BI. Se si verificano problemi o errori durante l'uso di questa funzionalità, assicurarsi di aver scaricato la versione più recente di Power BI Desktop da [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Novità di ottobre 2015
A partire da ottobre 2015 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Supporto per la crittografia locale delle anteprime e dei profili dati per le origini dati registrate. Azure Data Catalog crittografa in modo trasparente i record di anteprima e le origini dei profili dati registrati con il servizio, senza che gli amministratori del catalogo debbano eseguire alcuna operazione di gestione.
* Supporto per origini dati Teradata. Gli utenti possono ora registrarsi e individuare tabelle e viste Teradata.
* Supporto per le origini dati Hive locali. Gli utenti possono ora registrarsi e individuare tabelle Hive per Apache Hive di Hadoop in origini dati locali.
* Supporto per ricerche salvate nel portale di Azure Data Catalog. Gli utenti possono salvare termini di ricerca e selezioni di filtri per ripetere facilmente ricerche precedenti e definire viste di contenuti del catalogo particolarmente utili. Possono anche contrassegnare una ricerca salvata come ricerca predefinita. Quando un utente fa clic sull'icona di ricerca a forma di lente di ingrandimento nella home page del portale di Azure Data Catalog o nella pagina introduttiva, viene visualizzata direttamente la ricerca salvata contrassegnata come predefinita.
* Supporto per la documentazione in formato RTF per asset e contenitori di dati registrati nel portale di Azure Data Catalog. Gli utenti ora possono fornire la documentazione per asset di dati, ad esempio tabelle, viste e report, e per i contenitori, ad esempio database e modelli, per gli scenari in cui tag e descrizioni non sono sufficienti.
* Supporto per la registrazione manuale di tipi di origini dati conosciuti. Gli utenti possono immettere manualmente informazioni sulle origini dati tramite il portale di Azure Data Catalog per tutti i tipi di origini dati supportati da Azure Data Catalog.
* Supporto per l'autorizzazione di gruppi di sicurezza di Azure Active Directory. Gli amministratori di Data Catalog possono concedere l'accesso al catalogo a gruppi di sicurezza e account utente, rendendo più semplice la gestione dell'accesso ad Azure Data Catalog.
* Supporto per l'apertura di origini dati Hive in Excel dal portale di Azure Data Catalog.

> [!NOTE]
> Per la versione corrente è supportata solo l'autenticazione Teradata TD2. Nelle versioni future saranno supportati meccanismi di autenticazione aggiuntivi.

> [!NOTE]
> Per usare la funzionalità "Apri in Excel" per le origini dati Hive, è necessario installare il driver ODBC per Hive.

## <a name="whats-new-for-september-2015"></a>Novità di settembre 2015
A partire da settembre 2015 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Supporto per includere i profili dati durante la registrazione delle origini dati Hive.
* Supporto per l'individuazione a livello di programmazione dell'API di Azure Data Catalog, per rendere più semplice l'integrazione delle applicazioni con Azure Data Catalog.
* Una nuova esperienza introduttiva per l'individuazione di origini dati nel portale di Azure Data Catalog. Quando gli utenti accedono alla pagina di individuazione del portale di Azure Data Catalog senza immettere un termine di ricerca, viene visualizzata una panoramica dei contenuti del catalogo, inclusi i tag usati più di frequente, gli esperti, i tipi di origini dati e i tipi di oggetti.
* Supporto per la registrazione e l'individuazione degli oggetti e dei database di Azure SQL Data Warehouse. Per altre informazioni su Azure SQL Data Warehouse, vedere [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Supporto per la registrazione e l'individuazione di modelli di SQL Server Analysis Services e di server SQL Server Reporting Services come contenitori. Quando si registrano oggetti SSAS e SSRS, Azure Data Catalog crea una voce per il modello SSAS e il server SSRS, nonché per i report e altri oggetti. I contenitori possono essere individuati e annotati mediante il portale di Azure Data Catalog. Gli utenti possono inoltre eseguire ricerche e applicare filtri nel contenuto di un modello o di un server oltre a eseguire ricerche e applicare filtri nel contenuto del catalogo.
* Supporto per la registrazione e l'individuazione degli oggetti di SQL Server Analysis Services tramite HTTP/HTTPS. Gli utenti possono ora connettersi ai server SSAS usando un URL (ad esempio https://servername/olap/msmdpump.dll) anziché un nome di server e possono usare l'autenticazione di base e le connessioni anonime oltre all'autenticazione di Windows. Per altre informazioni sulle connessioni HTTP/HTTPS a SSAS, vedere [Configurare l'accesso HTTP ad Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* Supporto per le origini dati Hive in HDInsight. Gli utenti possono ora registrarsi e individuare le tabelle Hive per Apache Hive di Hadoop sulle origini dati HDInsight. Per altre informazioni su Hive in HDInsight, vedere il [centro di documentazione HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md).
* Supporto per la registrazione e l'individuazione dei database Oracle e dei cluster HDFS come contenitori. Quando si registrano tabelle e viste Oracle o HDFS, Azure Data Catalog crea una voce per il database, le tabelle e le viste. Il database può essere individuato e annotato mediante il portale di Azure Data Catalog. Gli utenti possono inoltre cercare e filtrare il contenuto di un database o cluster, oltre a cercare e filtrare il contenuto del catalogo.
* Supporto per la registrazione manuale di tipi di origini dati sconosciute. Gli utenti possono immettere manualmente informazioni sull'origine dati tramite il portale di Azure Data Catalog in modo che sia possibile annotare e individuare le origini dati non esplicitamente supportate dal tool di registrazione delle origini dati.
* Supporto per la registrazione e l'individuazione dei database di SQL Server come contenitori. Quando si registrano tabelle e viste SQL Server, Azure Data Catalog crea una voce per il database, le tabelle e le viste. Il database può essere individuato e annotato mediante il portale di Azure Data Catalog. Gli utenti possono inoltre cercare e filtrare il contenuto di un database oltre a cercare e filtrare il contenuto del catalogo.

> [!NOTE]
> Gli oggetti SSAS e SSRS che sono stati registrati prima della versione del 18 settembre devono essere registrati di nuovo usando lo strumento di registrazione dell'origine dati prima di aggiungere la voce del modello o del server nel catalogo. La nuova registrazione di un'origine dati non influisce sulle annotazioni aggiunte dagli utenti nel portale di Azure Data Catalog.

> [!NOTE]
> Le tabelle, le viste e i file HDFS di Oracle che sono stati registrati prima della versione dell'11 settembre devono essere registrati di nuovo usando lo strumento di registrazione dell'origine dati prima di aggiungere la voce del database o cluster nel catalogo. La nuova registrazione di un'origine dati non influisce sulle annotazioni aggiunte dagli utenti nel portale di Azure Data Catalog.

> [!NOTE]
> Le tabelle e le viste di SQL Server che sono state registrate prima della versione del 4 settembre devono essere registrate di nuovo usando lo strumento di registrazione dell'origine dati prima di aggiungere la voce del database nel catalogo. La nuova registrazione di un'origine dati non influisce sulle annotazioni aggiunte dagli utenti nel portale di Azure Data Catalog.

## <a name="whats-new-for-august-2015"></a>Novità di agosto 2015
A partire da agosto 2015 sono state aggiunte ad Azure Data Catalog le funzionalità seguenti:

* Supporto per l'analisi dei dati di origini dati di SQL Server e Oracle. Quando si registrano le viste e le tabelle di SQL Server e Oracle, gli utenti possono scegliere di includere informazioni sul profilo dati per gli oggetti da registrare. Il profilo dati include le statistiche a livello di oggetto e di colonna.
* Supporto per le origini dati HDFS Hadoop. Gli utenti possono ora registrarsi e individuare directory e file HDFS.
* Supporto per fornire informazioni sulla richiesta di accesso per le origini dati registrate. Per qualsiasi asset di dati registrato, gli utenti possono ora fornire istruzioni per richiedere l'accesso, inclusi URL o collegamenti di posta elettronica, per una facile integrazione con i processi e gli strumenti esistenti.
* Descrizioni comandi per i tag e gli esperti, per semplificare l'individuazione dei metadati forniti dagli utenti per gli asset di dati registrati.
* Nuovo menu e pulsante "User" nella barra di spostamento superiore. Questo menu consente di vedere l'account usato per accedere ad Azure Data Catalog ed eventualmente disconnettersi. Questo menu visualizza anche il nome del catalogo, utile per gli sviluppatori che usano l'API REST di Azure Data Catalog.
* Solo edizione Standard: nell'aggiunta di proprietari ad asset di dati, Azure Data Catalog ora supporta sia gli account utente che i gruppi di sicurezza come proprietari. Per aggiungere un gruppo di sicurezza come proprietario di asset di dati selezionati, è possibile immettere il nome visualizzato o l'indirizzo di posta elettronica UPN del gruppo, se presente.
* Supporto per le origini dati dell'archivio BLOB di Azure. Gli utenti possono ora registrarsi e individuare i BLOB e le directory di archiviazione di Azure.

