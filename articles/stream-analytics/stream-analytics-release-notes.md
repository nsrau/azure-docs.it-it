<properties 
	pageTitle="Note sulla versione di Analisi di flusso | Microsoft Azure" 
	description="Note sulla versione di Analisi dei flussi GA | Azure" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/16/2015" 
	ms.author="jeffstok"/>

#Note sulla versione di Analisi di flusso Microsoft

## Note per la versione 10/09/2015 di Analisi di flusso ##

Questa versione contiene gli aggiornamenti seguenti.

Titolo|Descrizione
---|---
Supporto per i gruppi PowerBI|Per abilitare la condivisione dei dati con altri utenti di Power BI, i processi di analisi di flusso ora possono scrivere ai [gruppi PowerBI](stream-analytics-define-outputs.md#power-bi)all'interno dell’account Power BI.

## Note per la versione 20/08/2015 di Analisi di flusso ##

Questa versione contiene gli aggiornamenti seguenti.

Titolo|Descrizione
---|---
Aggiunta funzione LAST |La funzione [LAST](http://msdn.microsoft.com/library/mt421186.aspx) è ora disponibile nei processi di analisi dei flussi. Consente di recuperare l'evento più recente in un flusso di eventi all'interno di un intervallo di tempo specificato.
Nuove funzioni di matrice|Le funzioni di matrice [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx), [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) sono ora disponibili.
Nuove funzioni di record|Le funzioni di record [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) e [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) sono ora disponibili.

## Note per la versione del 30/07/2015 di Analisi di flusso ##

Questa versione contiene gli aggiornamenti seguenti.

Titolo|Descrizione
---|---
ID organizzazione di Power BI separato dall'ID di Azure|Questa funzionalità abilita l'[output di Power BI](stream-analytics-power-bi-dashboard.md) per i processi ASA in qualsiasi tipo di account di Azure (Live ID o ID organizzazione). Inoltre, è possibile disporre di un ID organizzazione per l'account Azure e utilizzarne uno diverso per l'autorizzazione dell'output di Power BI.
Supporto per l'output di code del bus di servizio|Gli output di [Code del bus di servizio](stream-analytics-connect-data-event-outputs.md#service-bus-queues) sono ora disponibili nei processi di analisi di flusso.
Supporto per l'output di argomenti del bus di servizio|Gli output di [argomenti del bus di servizio](stream-analytics-connect-data-event-outputs.md#service-bus-topics) sono ora disponibili nei processi di analisi di flusso.

## Note per la versione 09/07/2015 di Analisi di flusso ##

Questa versione contiene gli aggiornamenti seguenti.


Titolo|Descrizione
---|---
Partizionamento output BLOB personalizzato|Gli output di archiviazione BLOB includono ora un'opzione per specificare la frequenza con cui i BLOB di output vengono scritti e la struttura e il formato della struttura di cartelle del percorso dati di output. 

## Note per la versione 03/05/2015 di Analisi di flusso ##

Questa versione contiene gli aggiornamenti seguenti.


Titolo|Descrizione
---|---
Aumento del valore massimo per Finestra di tolleranza elementi non in ordine|La dimensione massima per Finestra di tolleranza elementi non in ordine è ora 59:59 (mm:ss)
Formato di output JSON: Separato da righe o matrice|Ora è disponibile un'opzione per l'output in Archiviazione Blob o in Hub di eventi come matrice di oggetti JSON o con gli oggetti JSON separati da una nuova riga. 

## Note per la versione 16/04/2015 di Analisi di flusso ##


Titolo|Descrizione
---|---
Ritardo nella configurazione dell'account di archiviazione di Azure|Quando si crea per la prima volta un processo di Analisi dei flussi in un’area, verrà richiesto di creare un nuovo account di archiviazione, o di specificarne uno esistente, per il monitoraggio dei processi di Analisi dei flussi in tale area. A causa della latenza nella configurazione del monitoraggio, la creazione di un altro processo di Analisi dei flussi nella stessa area entro 30 minuti determinerà la visualizzazione della richiesta di specificare un secondo account di archiviazione anziché la visualizzazione di quello appena configurato nel menu a discesa relativo all'account di archiviazione per il monitoraggio. Per evitare la creazione di account di archiviazione non necessari, dopo aver creato per la prima volta un processo in una determinata area, attendere 30 minuti prima di eseguire il provisioning di altri processi nella stessa area.
Aggiornamento dei processi|Analisi dei flussi non supporta al momento modifiche di definizione o configurazione per processi in corso di esecuzione. Per modificare input, output, query, dimensioni o configurazione di un processo in esecuzione, è necessario innanzitutto arrestare il processo stesso.
Tipi di dati dedotti da origine di input|Se non viene usata un'istruzione CREATE TABLE, il tipo di input è derivato dal formato di input, ad esempio tutti i campi da CSV sono stringhe. I campi devono essere convertiti esplicitamente nel tipo corretto tramite la funzione CAST per evitare errori di tipo non corrispondente.
I campi mancanti sono generati come valori null|Il riferimento a un campo non presente nell’origine di input causerà valori null nell’evento di output.
Le istruzioni WITH devono precedere le istruzioni SELECT|Nella query, le istruzioni SELECT devono seguire le sottoquery definite nelle istruzioni WITH.
Problema relativo a memoria insufficiente|I processi di Analisi dei flussi con un'elevata tolleranza per eventi non ordinati e/o query complesse, con relativa conservazione di grandi quantità di stati, possono causare l'esaurimento della memoria, il che a sua volta causerebbe il riavvio del processo. Le operazioni di avvio e di arresto saranno visibili nei log delle operazioni del processo. Per evitare questo comportamento, ridimensionare la query orizzontalmente occupando più partizioni. In una versione futura questa limitazione verrà risolta riducendo le prestazioni dei processi interessati anziché causandone il riavvio.
Input blob di grandi dimensioni senza timestamp payload possono causare problemi relativi a memoria insufficiente|L’utilizzo di file di grandi dimensioni dall’Archiviazione BLOB può causare l’arresto anomalo dei processi di Analisi dei flussi se un campo timestamp non è specificato con TIMESTAMP BY. Per evitare questo problema, mantenere ogni BLOB entro i 10 MB di dimensione.
Limitazione del volume di eventi del database SQL|Quando si usa un database SQL come destinazione di output, un volume troppo elevato di dati di output può causare il timeout del processo di Analisi dei flussi. Per risolvere il problema, ridurre il volume di output tramite operatori di filtro o aggregati oppure scegliere Archiviazione BLOB di Azure o Hub eventi come destinazione di output.
I dataset PowerBI possono contenere solo una tabella|PowerBI non supporta più di una tabella in un dato dataset.

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](../stream.analytics.get.started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=Sept15_HO3-->