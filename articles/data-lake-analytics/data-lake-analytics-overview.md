<properties 
   pageTitle="Panoramica di Microsoft Azure Data Lake Analytics | Azure" 
   description="Data Lake Analytics è un servizio di calcolo dei Big Data di Azure che consente di usare i dati per la gestione delle attività aziendali mediante le informazioni dettagliate ricavate dai dati archiviati nel cloud, indipendentemente dalla loro posizione o dimensione. Data Lake Analytics consente di eseguire queste operazioni nel modo più semplice, scalabile ed economico possibile. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>  
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>  

# Panoramica di Microsoft Azure Data Lake Analytics

## Che cos'è Azure Data Lake Analytics?

Azure Data Lake Analytics è un nuovo servizio appositamente sviluppato per semplificare l'analisi dei Big Data. Questo servizio consente di concentrarsi sulla scrittura, esecuzione e gestione dei processi anziché sul funzionamento dell'infrastruttura distribuita. Anziché distribuire, configurare e ottimizzare l'hardware, è possibile scrivere query per trasformare i dati ed estrarre informazioni di interesse. Con il servizio di analisi è possibile gestire processi di qualsiasi dimensione immediatamente, semplicemente definendo il livello e l'ambito necessari. Verrà addebitato un costo solo per il processo durante la sua esecuzione, per la massima convenienza. Il servizio di analisi supporta l'integrazione di Azure Active Directory, che consente di gestire facilmente l'accesso e i ruoli, con il sistema di gestione delle identità locale in uso. È incluso anche U-SQL, un linguaggio che combina i vantaggi di SQL con la potenza espressiva del codice utente. Il runtime distribuito scalabile di U-SQL permette di analizzare in modo efficiente i dati nell'archivio e tra server SQL in Azure, il database SQL di Azure e SQL Data Warehouse di Azure.


## Funzionalità principali

- **Scalabilità dinamica**

    Data Lake Analytics è stato progettato da zero per garantire scalabilità e prestazioni di livello cloud. Il servizio effettua il provisioning dinamico delle risorse e permette di svolgere analisi su terabyte e addirittura exabyte di dati. Al termine del processo, il servizio riduce automaticamente le risorse, permettendo di pagare solo per la potenza di elaborazione effettivamente usata. Se si aumenta o riduce le dimensioni dei dati archiviati o la quantità di calcolo usata, non è necessario riscrivere il codice. In questo modo, è possibile concentrarsi solo sulla logica di business e non sul modo in cui elaborare e archiviare set di dati di grandi dimensioni.

- **Strumenti familiari per sviluppo più rapido, debug e ottimizzazione avanzata**

    Data Lake Analytics è caratterizzato da una profonda integrazione con Visual Studio, che permette di usare strumenti familiari per esecuzione, debug e ottimizzazione del codice. Le visualizzazioni dei processi U-SQL permettono di definire la scalabilità con cui viene eseguito il codice, per poter identificare i colli di bottiglia in termini di prestazioni e ridurre i costi.

- **U-SQL: semplice, familiare, potente ed estendibile**

    Data Lake Analytics include U-SQL, un linguaggio di query che estende la natura dichiarativa, semplice e familiare di SQL con l'efficacia espressiva di C#. Il linguaggio U-SQL si basa sullo stesso runtime distribuito usato per i sistemi di Big Data all'interno di Microsoft. Milioni di sviluppatori SQL e .NET possono ora elaborare e analizzare tutti i propri dati sfruttando le competenze già acquisite.

- **Integrazione uniforme con gli investimenti IT**

    Data Lake Analytics può sfruttare gli investimenti IT esistenti per identità, gestione, sicurezza e data warehousing. Questa caratteristica semplifica la governance dei dati e l'estensione delle attuali applicazioni dati. Data Lake Analytics è integrato in Active Directory per la gestione e le autorizzazioni degli utenti e viene fornito con funzionalità integrate di monitoraggio e controllo.

- **Conveniente ed economico**

    Data Lake Analytics è una soluzione a costi ridotti per l'esecuzione di carichi di lavoro di Big Data. Il pagamento avviene in base a ogni processo quando vengono elaborati i dati. Non sono necessari hardware, licenze o contratti di supporto specifici del servizio. Poiché il sistema è in grado di ridimensionarsi automaticamente all'avvio e al completamento del processo, non viene mai pagato in eccesso rispetto a quanto è necessario.

- **Per tutti i dati di Azure**

    Data Lake Analytics può usare numerose origini dati di Azure: archiviazione BLOB di Azure, database SQL di Azure, ed è ottimizzato in particolar modo per usare l'archivio di Azure Data Lake, in modo da offrire il più elevato livello di prestazioni, velocità effettiva e parallelizzazione dei carichi di lavoro dei Big Data.

## Vedere anche

- Introduzione
    - [Introduzione a Analisi Data Lake tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
    - [Introduzione ad Azure Data Lake Analytics con Azure PowerShell](data-lake-analytics-get-started-powershell.md)
    - [Introduzione ad Azure Data Lake Analytics con Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
    - [Sviluppare script U-SQL con Data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL e sviluppo
    - [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
    - [Usare le funzioni finestra di U-SQL per i processi di Analisi Azure Data Lake](data-lake-analytics-use-window-functions.md)
    - [Sviluppare operatori U-SQL definiti dall'utente per i processi di Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- gestione
    - [Gestire Analisi Data Lake di Azure tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
    - [Gestire Azure Data Lake Analytics tramite Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
    - [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
    - [Accessing Diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Accesso ai log di diagnostica per Azure Data Lake Analytics)

- Esercitazione end-to-end
    - [Usare le esercitazioni interattive di Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
    - [Analizzare i log dei siti Web con Analisi Azure Data Lake](data-lake-analytics-analyze-weblogs.md)

- Feedback
    - [Commentare il backlog della documentazione](data-lake-analytics-documentation-backlog.md)
    - [Inviare una richiesta di funzionalità](http://aka.ms/adlafeedback)
    - [Ottenere informazioni sui forum](http://aka.ms/adlaforums)

<!---HONumber=AcomDC_0810_2016-->