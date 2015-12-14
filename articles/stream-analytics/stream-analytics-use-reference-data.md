<properties 
	pageTitle="Usare i dati di riferimento | Microsoft Azure" 
	description="Utilizzare dati di riferimento in un flusso di input di Analisi di flusso" 
	keywords="analisi di big data, servizio cloud, internet delle cose, servizio gestito, elaborazione del flusso, analisi di flusso, dati del flusso"
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
	ms.date="11/23/2015" 
	ms.author="jeffstok"/>

# Utilizzo di dati di riferimento in un flusso di input di Analisi di flusso

Dati di riferimento sono un set di dati limitato di natura statica o che cambiano molto lentamente, usati per eseguire una ricerca o la correlazione con il flusso di dati. Per usare i dati di riferimento nel processo di analisi di flusso di Azure si usa in genere un [JOIN dei dati di riferimento](https://msdn.microsoft.com/library/azure/dn949258.aspx) nella query. Analisi di flusso usa l'archiviazione BLOB di Azure come livello di archiviazione per i dati di riferimento, mentre con Data factory di Azure i dati di riferimento possono essere trasformati e/o copiati nell'archivio BLOB di Azure, da usare come dati di riferimento, da [un numero qualsiasi di archivi dati basati sul cloud e locali](./articles/data-factory-data-movement-activities.md). I dati di riferimento sono modellati come una sequenza di BLOB (definiti nella configurazione di input) in ordine crescente in base alla data/ora specificata nel nome di BLOB. Supporta **solo** supporta l'aggiunta alla fine della sequenza utilizzando una data/ora **maggiore** rispetto a quella specificata dall'ultimo BLOB nella sequenza.

## Configurazione dei dati di riferimento

Per configurare i dati di riferimento, è prima di tutto necessario creare un input che sia di tipo **Dati di riferimento**. La tabella seguente illustra ogni proprietà che è necessario fornire durante la creazione di input di dati di riferimento con la relativa descrizione:

<table>
<tbody>
<tr>
<td>Nome proprietà</td>
<td>Descrizione</td>
</tr>
<tr>
<td>Alias di input</td>
<td>Nome descrittivo che verrà usato nella query di processo per fare riferimento a questo input.</td>
</tr>
<tr>
<td>Account di archiviazione</td>
<td>Nome dell'account di archiviazione in cui si trovano i file BLOB. Se si trova nella stessa sottoscrizione del processo di analisi di flusso, è possibile selezionarlo dall'elenco a discesa.</td>
</tr>
<tr>
<td>Chiave dell'account di archiviazione</td>
<td>Chiave privata associata all'account di archiviazione. Viene compilata automaticamente se l'account di archiviazione si trova nella stessa sottoscrizione del processo di analisi di flusso.</td>
</tr>
<tr>
<td>Contenitore di archiviazione</td>
<td>I contenitori forniscono un raggruppamento logico per gli oggetti BLOB archiviati nel servizio BLOB di Microsoft Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB.</td>
</tr>
<tr>
<td>Modello di percorso</td>
<td>Percorso del file usato per individuare i BLOB nel contenitore specificato. All'interno del percorso è possibile scegliere di specificare una o più istanze delle 2 variabili seguenti:<BR>{date}, {time}<BR>Esempio 1: products/{date}/{time}/product-list.csv<BR>Esempio 2: products/{date}/product-list.csv
</tr>
<tr>
<td>Formato data [facoltativo]</td>
<td>Se è stata usata la variabile {date} nello schema prefisso percorso specificato, è possibile selezionare il formato data in cui sono organizzati i file dall'elenco a discesa dei formati supportati. Esempio: AAAA/MM/GG</td>
</tr>
<tr>
<td>Formato ora [facoltativo]</td>
<td>Se è stata usata la variabile {time} nello schema prefisso percorso specificato, è possibile selezionare il formato ora in cui sono organizzati i file dall'elenco a discesa dei formati supportati. Esempio: HH</td>
</tr>
<tr>
<td>Formato di serializzazione eventi</td>
<td>Per accertarsi che le query funzionino come previsto, l'analisi di flusso deve conoscere il formato di serializzazione usato per i flussi di dati in entrata. Per i dati di riferimento, i formati dati supportati sono CSV e JSON.</td>
</tr>
<tr>
<td>Codifica</td>
<td>Al momento UTF-8 è l'unico formato di codifica supportato</td>
</tr>
</tbody>
</table>

## Generazione di dati di riferimento in una pianificazione

Se i dati di riferimento sono costituiti da un set di dati che cambia lentamente, il supporto per l'aggiornamento dei dati di riferimento viene abilitato specificando un modello di percorso nella configurazione di input usando i token {date} e {time}. L'analisi di flusso selezionerà le definizioni dei dati di riferimento aggiornate in base a questo modello di percorso. Ad esempio, un modello di ````"/sample/{date}/{time}/products.csv"```` con un formato data di "AAAA-MM-GG" e un formato ora di "HH:mm" indica all'analisi di flusso di selezionare il BLOB aggiornato ````"/sample/2015-04-16/17:30/products.csv"```` alle 17:30 del 16 aprile 2015 nel fuso orario UTC.

> [AZURE.NOTE]Attualmente i processi di analisi di flusso cercano l'aggiornamento del BLOB solo quando l'ora del computer coincide con l'ora codifica nel nome del BLOB. Ad esempio, il processo cerca /sample/2015-04-16/17:30/products.csv tra le 17.30 e le 17.30.59.9 del 16 aprile 2015 nel fuso orario UTC. Quando l'orologio del computer segna le 17.31, la ricerca di /sample/2015-04-16/17:30/products.csv si arresta e inizia la ricerca di /sample/2015-04-16/17:31/products.csv. Un'eccezione a questo si verifica quando il processo deve elaborare nuovamente dei dati indietro nel tempo o quando il processo viene in primo luogo avviato. Nel momento iniziale il processo cerca i BLOB più recenti prodotti prima dell'ora di inizio del processo specificata. Questa operazione è necessaria per verificare che esista un set di dati di riferimento non vuoto quando il processo inizia. Se non è possibile trovarne uno, il processo non riuscirà e verrà visualizzato un avviso di diagnostica.

È possibile usare [Data factory di Azure](http://azure.microsoft.com/documentation/services/data-factory/) per organizzare l'attività di creazione dei BLOB aggiornati richiesti dall'analisi di flusso per aggiornare le definizioni dei dati di riferimento. Data factory è un servizio di integrazione delle informazioni basato sul cloud che permette di automatizzare lo spostamento e la trasformazione dei dati. Data factory supporta la [connessione a un numero elevato di archivi dati basati su cloud e locali](./articles/data-factory-data-movement-activities.md) e il semplice trasferimento dei dati in base a una pianificazione regolare specificata dall'utente. Per altre informazioni e per istruzioni dettagliate su come configurare una pipeline di Data factory per generare dati di riferimento per l'analisi di flusso che vengano aggiornati in base a una pianificazione predefinita, consultare questo [esempio di GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## Suggerimenti sull'aggiornamento dei dati di riferimento ##

1. Sovrascrittura dei BLOB dei dati di riferimento non farà verificare l’analisi di flusso per ricaricare il BLOB e in alcuni casi può comportare il mancato completamento del processo. Il modo consigliato per modificare i dati di riferimento è quello di aggiungere un nuovo BLOB usando lo stesso contenitore e il modello di percorso definito nell'input del processo e usare una data/ora **maggiore** rispetto a quella specificata dall'ultimo BLOB nella sequenza.
2.	I BLOB dei dati di riferimento non vengono ordinati in base all’ora dell’"Ultima modifica"del BLOB, ma solo dall’ora e dalla date specificate nel nome del BLBO usando le sostituzioni della {data} e dell’{ora}.
3.	In alcuni casi un processo deve tornare indietro in orario, pertanto il BLOB dei dati di riferimento non deve essere modificato o eliminato.

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi
È stato presentato Analisi di flusso, un servizio gestito per l'analisi di flusso su dati provenienti da Internet delle cose. Per altre informazioni su questo servizio, vedere:

- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_1203_2015-->