---
title: Individuare, identificare e classificare i dati personali in Microsoft Azure | Microsoft Docs
description: Informazioni sulla ricerca, la classificazione, l'individuazione e l'identificazione di dati
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 011fe26c297ff991af1282c1ff3914031a99f1e4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>Individuare, identificare e classificare i dati personali in Microsoft Azure

Questo articolo contiene indicazioni su come individuare, identificare e classificare i dati personali in diversi strumenti e servizi di Azure, tra cui Azure Data Catalog, Azure Active Directory, il database SQL, Power Query per i cluster Hadoop in HDInsight di Azure, Azure Information Protection, Ricerca di Azure e le query SQL per Azure Cosmos DB.

## <a name="scenario-problem-statement-and-goal"></a>Scenario, presentazione del problema e obiettivo

Una società sportiva statunitense raccoglie una varietà di dati personali e di altro tipo, tra cui quelli relativi a clienti e dipendenti. La società li conserva in più database e li archivia in diverse posizioni nel proprio ambiente Azure. Oltre alla vendita di attrezzature sportive, organizza e gestisce la registrazione di esclusivi eventi sportivi in tutto il mondo, anche nell'UE e, in alcuni casi, i dati raccolti relativi ai clienti includono informazioni mediche.

Poiché la società organizza molti tour ciclistici internazionali ogni anno con personale temporaneo in località di tutto il mondo, le dimensioni di un paio di set di dati sono molto grandi. La società dispone anche di applicazioni compilate da sviluppatori che vengono usate da clienti e dipendenti.

I problemi che la società intende risolvere sono i seguenti:

- I dati personali di clienti e dipendenti devono essere classificati/distinti dagli altri dati aziendali raccolti per garantire un livello appropriato di protezione e accesso.
- L'amministratore dei dati deve individuare facilmente la posizione dei dati personali dei clienti nelle diverse aree dell'ambiente Azure.
- I dati personali di clienti e dipendenti presenti nei documenti condivisi e le comunicazioni di posta elettronica devono essere etichettati in modo da garantirne la sicurezza.
- Gli sviluppatori di app dell'azienda devono trovare un modo per cercare facilmente i dati personali di clienti e dipendenti nelle proprie app Web e per dispositivi mobili.
- Hanno anche necessità di eseguire query sui dati personali nel database di documenti.

### <a name="company-goals"></a>Obiettivi aziendali

- Tutti i dati personali di clienti e dipendenti devono essere contrassegnati/annotati in Azure Data Catalog in modo da consentirne una facile individuazione. Preferibilmente i dati personali di clienti e dipendenti vengono contrassegnati o annotati separatamente.
- È necessario che i dati personali contenuti nei profili utente e le informazioni sull'ufficio di clienti e dipendenti che risiedono in Azure Active Directory siano facilmente individuabili.
- È necessario poter eseguire facilmente query sui dati personali presenti in più database SQL. 
- Alcuni set di dati aziendali di grandi dimensioni vengono gestiti tramite Azure HDInsight e archiviati in Hadoop. Per poter eseguire query sui dati personali, i dati devono essere importati in Excel.
- I dati personali condivisi nei documenti e nelle comunicazioni via e-mail devono essere classificati, etichettati e protetti con Azure Information Protection.
- Gli sviluppatori di app aziendali devono poter individuare i dati personali di clienti e dipendenti nelle app che hanno compilato e questa operazione è possibile con Ricerca di Azure.
- Devono essere in grado di individuare i dati personali nel proprio database di documenti.

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory: individuazione di dati

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. È possibile individuare i profili utente e le informazioni sull'ufficio di clienti e dipendenti contenenti dati personali nell'ambiente [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) usando il [portale di Azure](https://portal.azure.com/).

Questa operazione è particolarmente utile se si vuole trovare o modificare i dati personali di un determinato utente. È possibile anche aggiungere o modificare il profilo utente e le informazioni sull'ufficio. È necessario accedere con un account di amministratore globale per la directory.

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>Come è possibile individuare o visualizzare il profilo utente e le informazioni sull'ufficio?

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.

2. Selezionare **Tutti i servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi selezionare **Invio**.

   ![come individuare le informazioni sul profilo utente e sull'ufficio](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. Nel pannello **Utenti e gruppi** selezionare **Utenti**.

      ![Apertura del pannello Utenti e gruppi](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. Nel pannello **Utenti e gruppi - Utenti** selezionare un utente nell'elenco, quindi scegliere **Profilo** nel pannello dell'utente selezionato per visualizzare le informazioni sul profilo utente che potrebbero contenere dati personali.

      ![Seleziona utente](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. Se si vuole aggiungere o modificare le informazioni sul profilo utente, è possibile eseguire questa operazione e selezionare **Salva** nella barra dei comandi.
6. Nel pannello per l'utente selezionato scegliere **Ufficio** per visualizzare le informazioni sull'ufficio dell'utente che possono contenere dati personali.

     ![visualizzazione delle informazioni sull'ufficio](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. Se è necessario aggiungere o modificare le informazioni sull'ufficio dell'utente, è possibile eseguire questa operazione e selezionare **Salva** nella barra dei comandi.

## <a name="azure-sql-database-data-discovery"></a>Database SQL di Azure: individuazione di dati

Il [database SQL di Microsoft Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) è un database basato su cloud che consente agli sviluppatori di compilare e gestire le applicazioni. I dati personali sono reperibili nel [database SQL di Azure](https://azure.microsoft.com/services/sql-database/?v=16.50) usando le query SQL standard. La funzionalità delle query elastiche del database SQL di Azure (anteprima) consente agli utenti di eseguire query tra database.

Un'esercitazione dettagliata sul [database SQL](../sql-database/sql-database-technical-overview.md) illustra molti aspetti dell'utilizzo di un database SQL, come compilarne uno e eseguire query sui dati. Di seguito è riportato un riepilogo delle informazioni disponibili nell'esercitazione con collegamenti a sezioni specifiche.

### <a name="how-do-i-build-a-sql-database"></a>Come si compila un database SQL?

In tre modi:

- È possibile creare un database SQL di Azure nel [portale di Azure](https://portal.azure.com/). Nel corso dell'esercitazione si userà un set specifico di risorse di calcolo e di archiviazione all'interno di un gruppo di risorse e di un server logico. Si useranno i dati di esempio di una società fittizia, AdventureWorks. Verrà anche creata una regola del firewall a livello di server. Per informazioni, visitare l'esercitazione guidata [Creare un database SQL di Azure nel portale di Azure](../sql-database/sql-database-get-started-portal.md).

  ![Creare un database SQL di Azure](media/how-to-discover-classify-personal-data-azure/create-database.png)
- È possibile creare un database SQL anche nell'interfaccia della riga di comando di [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), uno strumento da riga di comando basato su browser. Lo strumento è disponibile nel portale di Azure da cui può essere eseguito direttamente. In questa esercitazione si avvia lo strumento, si definisce le variabili dello script, si crea un gruppo di risorse e un server logico e si configura una regola firewall del server. Creare quindi un database con dati di esempio. Per informazioni su come creare il database in questo modo, visitare l'esercitazione [Creare un singolo database SQL di Azure usando l'interfaccia della riga di comando di Azure](../sql-database/sql-database-get-started-cli.md).

  ![Esercitazione sull'interfaccia della riga di comando](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
Gli sviluppatori e gli amministratori di Linux usano in genere l'interfaccia della riga di comando di Azure. Alcuni utenti la considerano più semplice e intuitiva della terza opzione: PowerShell.

- Infine è possibile creare un database SQL tramite PowerShell, uno strumento da riga di comando o script usato per creare e gestire Azure e altre risorse. In questa esercitazione si avvia lo strumento, si definisce le variabili dello script, si crea un gruppo di risorse e un server logico e si configura una regola firewall del server. Si creerà quindi un database con dati di esempio.

L'esercitazione richiede il modulo Azure PowerShell 4.0 o versioni successive. Eseguire Get-Module -ListAvailable AzureRM per individuare la versione in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere Installare il modulo Azure PowerShell.

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

Per informazioni su come creare il database in questo modo, visitare l'esercitazione [Creare un singolo database SQL di Azure usando Powershell](../sql-database/sql-database-get-started-powershell.md).

>[!Note]
Gli amministratori Windows tendono a usare PowerShell, ma alcuni preferiscono l'interfaccia della riga di comando di Azure.

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>Come è possibile cercare dati personali nel database SQL nel portale di Azure?**

È possibile usare lo strumento editor di query predefinito all'interno del portale di Azure per cercare i dati personali. Si accederà allo strumento usando l'account di accesso e la password di amministratore del server SQL e quindi si immetterà una query.

  ![ricerca SQL usando il portale](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

Il passaggio 5 dell'esercitazione illustra un esempio di query nel riquadro dell'editor di query, ma non è focalizzato sulle informazioni sensibili o personali; inoltre combina i dati di due tabelle e crea gli alias per la colonna di origine nel set di dati restituito. La schermata seguente illustra la query dal passaggio 5 e il riquadro dei risultati restituito:

  ![Editor di query](media/how-to-discover-classify-personal-data-azure/query-editor.png)

Se il database è stato chiamato MyTable, una query di esempio per le informazioni personali può includere nome, numero di previdenza sociale e numero di ID e sarà simile alla seguente:

"SELECT Name, SSN, ID number FROM MyTable"

Eseguire la query ed esaminare i risultati nel riquadro **Risultati**.

Per altre informazioni su come eseguire una query in un database SQL nel portale di Azure, visitare la sezione [Effettuare una query sul database SQL](../sql-database/sql-database-get-started-portal.md) dell'esercitazione.

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>Come cercare i dati in più database?

La funzione di query elastica SQL (anteprima) consente di eseguire query tra database e più query di database e restituire un singolo risultato. La [panoramica dell'esercitazione](../sql-database/sql-database-elastic-query-overview.md) include una descrizione dettagliata degli scenari e illustra la differenza tra i partizionamenti verticale e orizzontale del database. Il partizionamento orizzontale è chiamato "sharding".

  ![Il partizionamento verticale](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![partizionamento orizzontale](media/how-to-discover-classify-personal-data-azure/horizontal.png)

Per iniziare, visitare la pagina [Panoramica delle query elastiche del database SQL di Azure (anteprima)](../sql-database/sql-database-elastic-query-overview.md).

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query per l'importazione di cluster Azure HDInsight Hadoop: individuazione di dati per set di dati di grandi dimensioni

Hadoop è un servizio di archiviazione ed elaborazione Apache open source per set di dati di grandi dimensioni, che vengono analizzati e archiviati in cluster Hadoop. [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) consente agli utenti di usare i cluster Hadoop in Azure. Power Query è un componente aggiuntivo per Excel che, tra le altre cose, consente agli utenti di individuare i dati da origini diverse.

I dati personali associati ai cluster Hadoop in Azure HDInsight possono essere importati in Excel con Power Query. È possibile usare una query per facilitare l'identificazione dei dati in Excel.

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>Come si usa Excel Power Query per importare i cluster Hadoop di Azure HDInsight in Excel?

Un'esercitazione HDInsight illustra l'intero processo. Illustra i prerequisiti e include un collegamento all'esercitazione [Introduzione ad Azure HDInsight](../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md). Le istruzioni sono relative a Excel 2016, 2013 e 2010; i passaggi sono leggermente diversi per le versioni precedenti di Excel. L'esercitazione illustra come ottenere il componente aggiuntivo Power Query per Excel, se non è disponibile. Si inizierà l'esercitazione in Excel e sarà necessario disporre di un account di archiviazione BLOB di Azure associato al cluster.

  ![Eseguire query in Excel](media/how-to-discover-classify-personal-data-azure/excel.png)

Per informazioni su come eseguire questa operazione, visitare l'esercitazione [Connettere Excel a Hadoop mediante Power Query](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md).

Origine: [Connettere Excel a Hadoop mediante Power Query](../hdinsight/hadoop/apache-hadoop-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Azure Information Protection: classificazione dei dati personali per i documenti e i messaggi di posta elettronica

[Azure Information Protection](https://www.microsoft.com/cloud-platform/azure-information-protection) consente ai clienti Azure di applicare etichette per classificare e proteggere internamente o esternamente i documenti condivisi e le comunicazioni di posta elettronica. Alcuni di questi elementi possono contenere informazioni personali di clienti o dipendenti. Le regole e le condizioni possono essere definite automaticamente o manualmente dagli amministratori o dagli utenti. Ad esempio, se un utente salva un documento che include le informazioni della carta di credito, verrà mostrata una raccomandazione di etichetta configurata dall'amministratore.

### <a name="how-do-i-try-it"></a>Come si esegue una prova?

Per consentire ad Azure Information Protection di provare a verificare se la soluzione può essere adatta alla propria organizzazione, visitare l'[esercitazione di avvio rapido](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial). In meno di 30 minuti l'esercitazione si articola in cinque passaggi base: dall'installazione alla configurazione di criteri, alle fasi di classificazione, assegnazione di etichette e prova di condivisione.

### <a name="how-do-i-deploy-it"></a>Come si esegue la distribuzione?

Per distribuire Azure Information Protection nell'organizzazione, visitare la [Guida di orientamento per la distribuzione delle funzionalità di classificazione, assegnazione di etichette e protezione](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap).

### <a name="is-there-anything-else-i-should-know"></a>Quali altre informazioni è necessario conoscere?

Per informazioni integrative utili per configurare questa funzionalità, visitare il post di blog [Ready, set, protect!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/) (Una soluzione immediata per la protezione)
. Per altre informazioni su Azure Information Protection, vedere i collegamenti Altre informazioni elencati di seguito.

## <a name="azure-search-data-discovery-for-developer-apps"></a>Ricerca di Azure: individuazione di dati per le app di sviluppatori

[Ricerca di Azure](https://azure.microsoft.com/services/search/) è una soluzione di ricerca basata su cloud per gli sviluppatori che offre un'esperienza di ricerca avanzata di dati per le applicazioni. Ricerca di Azure consente di individuare i dati tra gli indici definiti dall'utente con origine da Azure Cosmo DB, dal database SQL di Azure, da Archiviazione BLOB di Azure, Archiviazione tabelle di Azure o dai dati JSON personalizzati di clienti. È anche possibile strutturare query Lucene usando l'API REST di Ricerca di Azure per cercare tipi di dati personali o dati personali di altre persone. Le funzionalità includono la ricerca full-text, una semplice sintassi di query e la sintassi di query Lucene. 

## <a name="how-do-i-use-sql-to-query-data"></a>Come si usa SQL per eseguire query sui dati?

Per iniziare con le nozioni base, visitare l'esercitazione [Azure Cosmos DB: Procedura per l'esecuzione di query con SQL](../cosmos-db/tutorial-query-documentdb.md). Questa esercitazione include un documento di esempio e due query SQL di esempio con relativi risultati.

Per altre informazioni dettagliate sulla creazione di query SQL, visitare [Query SQL per l'API DocumentDB di Azure Cosmos DB](../cosmos-db/sql-api-sql-query.md).

Se non si ha familiarità con Azure Cosmos DB e si vuole imparare a creare un database, aggiungere una raccolta e aggiungere i dati, leggere l'esercitazione di avvio rapido [Azure Cosmos DB: creare un'app Web per le API SQL](../cosmos-db/create-sql-api-dotnet.md). Se si vuole eseguire questa operazione in un linguaggio diverso da .NET, come Java o Python, selezionare solo il linguaggio preferito quando si raggiunge il sito.

## <a name="next-steps"></a>Passaggi successivi

[Database SQL di Azure](https://azure.microsoft.com/services/sql-database/?v=16.50)

[Informazioni sul database SQL](../sql-database/sql-database-technical-overview.md)

[SQL Database Query Editor available in Azure portal] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[Che cos'è Azure Information Protection?](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[Che cos'è Azure Rights Management?](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Azure Information Protection: Ready, set, protect!](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/) (Azure Information Protection: Una soluzione immediata per la protezione)
