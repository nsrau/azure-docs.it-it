<properties 
	pageTitle="Note sulla versione di Analisi dei flussi | Azure" 
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
	ms.date="07/09/2015" 
	ms.author="jeffstok"/>

#Note sulla versione di Analisi di flusso Microsoft

## Note per la versione 09/07/2015 di Analisi di flusso ##

Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
</tr>

<tr>
<td>Partizionamento output BLOB personalizzato</td>
<td>Gli output di archiviazione BLOB includono ora un'opzione per specificare la frequenza con cui i BLOB di output vengono scritti e la struttura e il formato della struttura di cartelle del percorso dati di output. </td>
</tr>
</table>

## Note per la versione 03/05/2015 di Analisi di flusso ##

Questa versione contiene gli aggiornamenti seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
</tr>

<tr>
<td>Aumento del valore massimo per Finestra di tolleranza elementi non in ordine</td>
<td>La dimensione massima per Finestra di tolleranza elementi non in ordine è ora 59:59 (mm:ss)</td>
</tr>

<tr>
<td>Formato di output JSON: Separato da righe o matrice</td>
<td>Ora è disponibile un'opzione per l'output in Archiviazione Blob o in Hub di eventi come matrice di oggetti JSON o con gli oggetti JSON separati da una nuova riga. </td>
</tr>
</table>

## Note per la versione 16/04/2015 di Analisi di flusso ##

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
</tr>

<tr>
<td>Ritardo nella configurazione dell'account di archiviazione di Azure</td>
<td>Quando si crea per la prima volta un processo di Analisi dei flussi in un’area, verrà richiesto di creare un nuovo account di archiviazione, o di specificarne uno esistente, per il monitoraggio dei processi di Analisi dei flussi in tale area. A causa della latenza nella configurazione del monitoraggio, la creazione di un altro processo di Analisi dei flussi nella stessa area entro 30 minuti determinerà la visualizzazione della richiesta di specificare un secondo account di archiviazione anziché la visualizzazione di quello appena configurato nel menu a discesa relativo all'account di archiviazione per il monitoraggio. Per evitare la creazione di account di archiviazione non necessari, dopo aver creato per la prima volta un processo in una determinata area, attendere 30 minuti prima di eseguire il provisioning di altri processi nella stessa area.</td>
</tr>

<tr>
<td>Aggiornamento dei processi</td>
<td>Analisi dei flussi non supporta al momento modifiche di definizione o configurazione per processi in corso di esecuzione. Per modificare input, output, query, dimensioni o configurazione di un processo in esecuzione, è necessario innanzitutto arrestare il processo stesso.</td>
</tr>

<tr>
<td>Tipi di dati dedotti da origine di input</td>
<td>Se non viene usata un'istruzione CREATE TABLE, il tipo di input è derivato dal formato di input, ad esempio tutti i campi da CSV sono stringhe. I campi devono essere convertiti esplicitamente nel tipo corretto tramite la funzione CAST per evitare errori di tipo non corrispondente.</td>
</tr>

<tr>
<td>I campi mancanti sono generati come valori null</td>
<td>Il riferimento a un campo non presente nell’origine di input causerà valori null nell’evento di output.</td>
</tr>

<tr>
<td>Le istruzioni WITH devono precedere le istruzioni SELECT</td>
<td>Nella query, le istruzioni SELECT devono seguire le sottoquery definite nelle istruzioni WITH.</td>
</tr>

<tr>
<td>Problema relativo a memoria insufficiente</td>
<td>I processi di Analisi dei flussi con un'elevata tolleranza per eventi non ordinati e/o query complesse, con relativa conservazione di grandi quantità di stati, possono causare l'esaurimento della memoria, il che a sua volta causerebbe il riavvio del processo. Le operazioni di avvio e di arresto saranno visibili nei log delle operazioni del processo. Per evitare questo comportamento, ridimensionare la query orizzontalmente occupando più partizioni. In una versione futura questa limitazione verrà risolta riducendo le prestazioni dei processi interessati anziché causandone il riavvio.</td>
</tr>

<tr>
<td>Input blob di grandi dimensioni senza timestamp payload possono causare problemi relativi a memoria insufficiente</td>
<td>L’utilizzo di file di grandi dimensioni dall’Archiviazione BLOB può causare l’arresto anomalo dei processi di Analisi dei flussi se un campo timestamp non è specificato con TIMESTAMP BY. Per evitare questo problema, mantenere ogni BLOB entro i 10 MB di dimensione.</td>
</tr>

<tr>
<td>Limitazione del volume di eventi del database SQL</td>
<td>Quando si usa un database SQL come destinazione di output, un volume troppo elevato di dati di output può causare il timeout del processo di Analisi dei flussi. Per risolvere il problema, ridurre il volume di output tramite operatori di filtro o aggregati oppure scegliere Archiviazione BLOB di Azure o Hub eventi come destinazione di output.</td>
</tr>

<tr>
<td>I dataset PowerBI possono contenere solo una tabella</td>
<td>PowerBI non supporta più di una tabella in un dato dataset.</td>
</tr>
</table>

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](../stream.analytics.get.started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=July15_HO4-->