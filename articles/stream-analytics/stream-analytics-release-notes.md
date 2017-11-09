---
title: Note sulla versione di analisi di flusso | Microsoft Docs
description: Note sulla versione di analisi di flusso
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5e59f893-cd2c-43fb-9eca-c146ce637203
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/03/2017
ms.author: samacha
ms.openlocfilehash: 3251cd47bb917912d63330345dbf392e724448ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-release-notes"></a>Note sulla versione di analisi di flusso

## <a name="notes-for-06142017-update-of-stream-analytics-tools-for-visual-studio"></a>Note per la versione 14/06/2017 di Strumenti di Analisi di flusso per Visual Studio
Questo aggiornamento è per gli Strumenti di Visual Studio. Questa versione contiene le nuove funzionalità seguenti:

| Titolo | Descrizione |
| --- | --- |
| Supporto per l'editor dello script Java |È possibile usufruire dell'esperienza dell'editor nativo dello script Java dopo la creazione di funzioni dello script Java.|
| Visualizzare il messaggio di errore di runtime del processo | Se sono presenti errori di runtime durante l'esecuzione del processo, è possibile visualizzarli nella scheda Errori regolando l'intervallo di tempo visualizzato. Per impostazione predefinita mostra i messaggi di errore degli ultimi 30 minuti. |
| Supporto CSV e Avro per l'input di test locale | Oltre a JSON, ora è possibile usare il formato file CSV e Avro per l'input di test locale.|

## <a name="notes-for-05032017-update-of-stream-analytics"></a>Note sulla versione 03/05/2017 dell'aggiornamento di Analisi di flusso
Questo aggiornamento è relativo al rilascio della documentazione sulla risoluzione dei problemi.

Sono stati rilasciati la [Guida per la risoluzione dei problemi](stream-analytics-troubleshooting-guide.md) e altri documenti. Esaminare il contenuto. Commenti e suggerimenti sono apprezzati.

## <a name="notes-for-04242017-update-of-stream-analytics-tools-for-visual-studio"></a>Note per la versione 24/04/2017 di Strumenti di Analisi di flusso per Visual Studio
Questo aggiornamento è per gli Strumenti di Visual Studio. Questa versione contiene le nuove funzionalità seguenti:

| Titolo | Descrizione |
| --- | --- |
| Visualizzare il risultato del test locale in Visual Studio | Per visualizzare il risultato dell'output del test locale, premere INVIO nella finestra della console di output o chiuderla. Il risultato viene visualizzato in una finestra in Visual Studio in formato tabella. |
| Risultato locale dell'output in formato JSON | Quando si esegue un test locale, il risultato dell'output viene generato nei formati file JSON e CSV. |
| Visualizzare dati di input/output di archiviazione della tabella/BLOB | Facendo doppio clic su un input/output di archiviazione BLOB o tabelle nella visualizzazione del processo, è possibile visualizzare in anteprima i dati all'interno di Visual Studio in tutta semplicità. |
| Visualizzare messaggio di errore per input/output | In caso di errori di runtime relativi agli input o output del processo, questi errori vengono visualizzati nel diagramma del processo ed è possibile passarvi sopra il puntatore del mouse per esaminare i dettagli del messaggio di errore.|


## <a name="notes-for-02012017-release-of-stream-analytics"></a>Note per la versione 01/02/2017 di Analisi di flusso
Questa versione contiene l'aggiornamento seguente:

| Titolo | Descrizione |
| --- | --- |
| Introduzione alle funzioni JavaScript definite dall'utente (UDF) |Le [funzioni Java definite dall'utente](stream-analytics-javascript-user-defined-functions.md) sono ora disponibili per una maggiore flessibilità nella creazione di query. |
| Introduzione agli strumenti per Visual Studio e analisi di flusso |Gli [strumenti per Visual Studio](stream-analytics-tools-for-visual-studio.md) sono ora disponibili per il debug e per le utility. |
| Abilitare la registrazione diagnostica |La [registrazione diagnostica](stream-analytics-job-diagnostic-logs.md) è ora disponibile per altre opzioni di risoluzione dei problemi. |
| Introduzione alle funzioni geospaziali |Le [funzioni geospaziali](http://msdn.microsoft.com/library/mt778980(Azure.100).aspx) sono ora disponibili a livello generale. |

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Note per la versione 15/04/2016 di Analisi di flusso
Questa versione contiene l'aggiornamento seguente:

| Titolo | Descrizione |
| --- | --- |
| Disponibilità generale per gli output di Power BI |[Gli output di Power BI](stream-analytics-power-bi-dashboard.md) sono ora disponibili a livello generale. La scadenza a 90 giorni dell'autorizzazione per Power BI è stata rimossa. Per altre informazioni sugli scenari in cui è necessario rinnovare l'autorizzazione, vedere la sezione relativa al [rinnovo dell'autorizzazione](stream-analytics-power-bi-dashboard.md#renew-authorization) in Creare un dashboard di Power BI. |

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Note per la versione 03/03/2016 di Analisi di flusso
Questa versione contiene l'aggiornamento seguente:

| Titolo | Descrizione |
| --- | --- |
| Nuovi elementi del linguaggio di query di Analisi di flusso |Ora SAQL ha [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "Pagina MSDN su GetType"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "Pagina MSDN su TRY_CAST") e [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "Pagina MSDN su REGEXMATCH"). |

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Note per la versione 10/12/2015 di Analisi di flusso
Questa versione contiene l'aggiornamento seguente:

| Titolo | Descrizione |
| --- | --- |
| Aggiornamento della versione dell'API REST |La versione dell'API REST è stata aggiornata a 2015-10-01. I dettagli sono disponibili su MSDN nella sezione di [riferimento dell'API REST di gestione di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx) e [Integrazione di Machine Learning in Analisi di flusso](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md). |
| Integrazione con Azure Machine Learning |Questa versione include il supporto per le funzioni definite dall'utente di Azure Machine Learning. Per altre informazioni, vedere l'[esercitazione](stream-analytics-machine-learning-integration-tutorial.md) e l'[annuncio generale sul blog](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx). |

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Note per la versione 12/11/2015 di Analisi di flusso
Questa versione contiene l'aggiornamento seguente:

| Titolo | Descrizione |
| --- | --- |
| Nuovo comportamento di SELECT |SELECT in Analisi di flusso è stato esteso per consentire * come funzione di accesso alla proprietà di un record annidato. Per altre informazioni, vedere [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Tipi di dati complessi"). |

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Note per la versione 22/10/2015 di Analisi di flusso
Questa versione contiene gli aggiornamenti seguenti:

| Titolo | Descrizione |
| --- | --- |
| Funzionalità del linguaggio di query aggiuntive |Analisi di flusso ha espanso il linguaggio delle query includendo le funzionalità seguenti: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [SIGN](https://msdn.microsoft.com/library/azure/mt605290.aspx), [SQUARE](https://msdn.microsoft.com/library/azure/mt605288.aspx)e [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx). |
| Rimuovere le limitazioni di aggregazione |Questa versione consente di rimuovere il limite di 15 aggregazioni in una query. A questo punto non vi sono limiti al numero di aggregazioni per ogni query. |
| Nuova funzionalità GROUP BY di System.Timestamp |La funzione [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) consente ora sia window_type sia [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx). |
| OFFSET aggiunto per le finestre a cascata e di salto. |Per impostazione predefinita, le finestre [a cascata](https://msdn.microsoft.com/library/azure/dn835055.aspx) e [di salto](https://msdn.microsoft.com/library/azure/dn835041.aspx) sono allineate rispetto al tempo zero (1/1/0001 12:00:00 AM UTC). Il nuovo parametro (facoltativo) 'offsetsize' consente di specificare un offset personalizzato (o allineamento). |

## <a name="notes-for-09292015-release-of-stream-analytics"></a>Note per la versione 29/09/2015 di Analisi di flusso
Questa versione contiene gli aggiornamenti seguenti:

| Titolo | Descrizione |
| --- | --- |
| Anteprima pubblica di Azure IoT Suite |L'analisi di flusso è inclusa nell'anteprima pubblica di Azure IoT Suite. |
| Integrazione del portale di Azure |Oltre alla sua presenza costante nel portale di Azure, Analisi di flusso di Azure è ora integrato nel [portale di Azure](https://azure.microsoft.com/overview/preview-portal/). Si noti che le funzionalità di Analisi di flusso di Azure nel portale di anteprima sono attualmente un sottoinsieme delle funzionalità offerte nel portale di Azure, senza il supporto per il test di query nel browser, la configurazione di output di Power BI e l'esplorazione o la creazione di nuove risorse di input e output nelle sottoscrizioni cui si ha accesso. |
| Supporto per l'output Cosmos DB |I processi dell'analisi di flusso ora possono inviare output a [Cosmos DB](https://azure.microsoft.com/services/documentdb/). |
| Supporto per l'input dell'hub IoT |I processi di analisi di flusso sono ora in grado di acquisire dati dagli hub IoT. |
| TIMESTAMP BY per eventi eterogenei |Quando un unico flusso di dati contiene più tipi di evento con timestamp in campi diversi, è ora possibile usare [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) con espressioni per specificare i campi di timestamp diversi per ogni caso. |

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Note per la versione 10/09/2015 di Analisi di flusso
Questa versione contiene gli aggiornamenti seguenti:

| Titolo | Descrizione |
| --- | --- |
| Supporto per i gruppi PowerBI |Per abilitare la condivisione dei dati con altri utenti di Power BI, i processi di Analisi di flusso ora possono eseguire la scrittura in [gruppi PowerBI](stream-analytics-define-outputs.md#power-bi) all'interno dell'account Power BI. |

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Note per la versione 20/08/2015 di Analisi di flusso
Questa versione contiene gli aggiornamenti seguenti:

| Titolo | Descrizione |
| --- | --- |
| Aggiunta funzione LAST |La funzione [LAST](http://msdn.microsoft.com/library/mt421186.aspx) è ora disponibile nei processi di Analisi di flusso. Consente di recuperare l'evento più recente in un flusso di eventi all'interno di un intervallo di tempo specificato. |
| Nuove funzioni di matrice |Ora sono disponibili le funzioni di matrice [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) e [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx). |
| Nuove funzioni di record |Le funzioni di record [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) e [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) sono ora disponibili. |

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Note per la versione del 30/07/2015 di Analisi di flusso
Questa versione contiene gli aggiornamenti seguenti:

| Titolo | Descrizione |
| --- | --- |
| ID organizzazione di Power BI separato dall'ID di Azure |Questa funzionalità abilita l' [output di Power BI](stream-analytics-power-bi-dashboard.md) per i processi ASA in qualsiasi tipo di account di Azure (Live ID o ID organizzazione). Inoltre, è possibile disporre di un ID organizzazione per l'account Azure e utilizzarne uno diverso per l'autorizzazione dell'output di Power BI. |
| Supporto per l'output di code del bus di servizio |Gli output di [code del bus di servizio](stream-analytics-define-outputs.md#service-bus-queues) sono ora disponibili nei processi di Analisi di flusso. |
| Supporto per l'output di argomenti del bus di servizio |Gli output di [argomenti del bus di servizio](stream-analytics-define-outputs.md#service-bus-topics) sono ora disponibili nei processi di Analisi di flusso. |

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Note per la versione 09/07/2015 di Analisi di flusso
Questa versione contiene gli aggiornamenti seguenti:

| Titolo | Descrizione |
| --- | --- |
| Partizionamento output BLOB personalizzato |Gli output di archiviazione BLOB includono ora un'opzione per specificare la frequenza con cui i BLOB di output vengono scritti e la struttura e il formato della struttura di cartelle del percorso dati di output. |

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Note per la versione 03/05/2015 di Analisi di flusso
Questa versione contiene gli aggiornamenti seguenti:

| Titolo | Descrizione |
| --- | --- |
| Aumento del valore massimo per Finestra di tolleranza elementi non in ordine |La dimensione massima per Finestra di tolleranza elementi non in ordine è ora 59:59 (mm:ss) |
| Formato di output JSON: Separato da righe o matrice |Ora è disponibile un'opzione per l'output in Archiviazione Blob o in Hub di eventi come matrice di oggetti JSON o con gli oggetti JSON separati da una nuova riga. |

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Note per la versione 16/04/2015 di Analisi di flusso
| Titolo | Descrizione |
| --- | --- |
| Ritardo nella configurazione dell'account di archiviazione di Azure |Quando si crea un processo di Analisi di flusso di Azure in un'area per la prima volta, viene chiesto di creare un nuovo account di archiviazione o di specificarne uno esistente per il monitoraggio dei processi di Analisi di flusso di Azure nell'area. A causa della latenza nella configurazione del monitoraggio, la creazione di un altro processo di Analisi di flusso di Azure nella stessa area entro 30 minuti determina la visualizzazione della richiesta di specificare un secondo account di archiviazione anziché mostrare quello appena configurato nel menu a discesa relativo all'account di archiviazione per il monitoraggio. Per evitare la creazione di account di archiviazione non necessari, dopo aver creato per la prima volta un processo in una determinata area, attendere 30 minuti prima di eseguire il provisioning di altri processi nella stessa area. |
| Aggiornamento dei processi |Analisi dei flussi non supporta al momento modifiche di definizione o configurazione per processi in corso di esecuzione. Per modificare input, output, query, dimensioni o configurazione di un processo in esecuzione, è necessario innanzitutto arrestare il processo. |
| Tipi di dati dedotti da origine di input |Se non viene usata un'istruzione CREATE TABLE, il tipo di input è derivato dal formato di input, ad esempio tutti i campi da CSV sono stringhe. I campi devono essere convertiti esplicitamente nel tipo corretto tramite la funzione CAST per evitare errori di tipo non corrispondente. |
| I campi mancanti sono generati come valori null |Se si fa riferimento a un campo che non è presente nell'origine di input, vengono restituiti valori null nell'evento di output. |
| Le istruzioni WITH devono precedere le istruzioni SELECT |Nella query, le istruzioni SELECT devono seguire le sottoquery definite nelle istruzioni WITH. |
| Problema relativo a memoria insufficiente |I processi di Analisi dei flussi con un'elevata tolleranza per eventi non ordinati e/o query complesse, con relativa conservazione di grandi quantità di stati, possono causare l'esaurimento della memoria, il che a sua volta causerebbe il riavvio del processo. Le operazioni di avvio e di arresto saranno visibili nei log delle operazioni del processo. Per evitare questo comportamento, scalare orizzontalmente la query su più partizioni. In una delle prossime versioni questa limitazione verrà risolta riducendo le prestazioni dei processi interessati anziché causandone il riavvio. |
| Input blob di grandi dimensioni senza timestamp payload possono causare problemi relativi a memoria insufficiente |L'utilizzo di file di grandi dimensioni dall'Archiviazione BLOB può causare l'arresto anomalo dei processi di Analisi dei flussi se un campo timestamp non è specificato con TIMESTAMP BY. Per evitare questo problema, mantenere ogni BLOB entro 10 MB di dimensioni. |
| Limitazione del volume di eventi del database SQL |Quando si usa un database SQL come destinazione di output, un volume troppo elevato di dati di output può causare il timeout del processo di Analisi dei flussi. Per risolvere il problema, ridurre il volume di output tramite operatori di filtro o aggregati oppure scegliere Archiviazione BLOB di Azure o Hub eventi come destinazione di output. |
| I dataset PowerBI possono contenere solo una tabella |PowerBI non supporta più di una tabella in un dato dataset. |

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
