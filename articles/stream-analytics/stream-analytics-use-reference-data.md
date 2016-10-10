<properties
	pageTitle="Usare i dati di riferimento e le tabelle di ricerca in Analisi di flusso | Microsoft Azure"
	description="Usare i dati di riferimento in una query di Analisi di flusso"
	keywords="tabella di ricerca, dati di riferimento"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>  

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="09/26/2016"
	ms.author="jeffstok"/>  

# Uso dei dati di riferimento o delle tabelle di ricerca in un flusso di input di Analisi di flusso

I dati di riferimento (noti anche come tabella di ricerca) sono un set di dati limitato di natura statica o che cambiano molto lentamente, usati per eseguire una ricerca o la correlazione con il flusso di dati. Per usare i dati di riferimento nel processo di analisi di flusso di Azure si usa in genere un [JOIN dei dati di riferimento](https://msdn.microsoft.com/library/azure/dn949258.aspx) nella query. Analisi di flusso usa l'archiviazione BLOB di Azure come livello di archiviazione per i dati di riferimento, mentre con Data factory di Azure i dati di riferimento possono essere trasformati e/o copiati nell'archivio BLOB di Azure, da usare come dati di riferimento, da [un numero qualsiasi di archivi dati basati sul cloud e locali](../data-factory/data-factory-data-movement-activities.md). I dati di riferimento sono modellati come una sequenza di BLOB (definiti nella configurazione di input) in ordine crescente in base alla data/ora specificata nel nome di BLOB. Supporta **solo** supporta l'aggiunta alla fine della sequenza utilizzando una data/ora **maggiore** rispetto a quella specificata dall'ultimo BLOB nella sequenza.

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

Se i dati di riferimento sono costituiti da un set di dati che cambia lentamente, è possibile abilitare il supporto per l'aggiornamento dei dati di riferimento specificando un modello di percorso nella configurazione di input con i token di sostituzione {date} e {time}. L'analisi di flusso selezionerà le definizioni dei dati di riferimento aggiornate in base a questo modello di percorso. Ad esempio, un modello `sample/{date}/{time}/products.csv` con un formato data **"AAAA-MM-GG"** e un formato ora **"HH:mm"** indica all'analisi di flusso di selezionare il BLOB aggiornato `sample/2015-04-16/17:30/products.csv` alle 17.30 del 16 aprile 2015 nel fuso orario UTC.

> [AZURE.NOTE] Attualmente i processi di analisi di flusso cercano l'aggiornamento del BLOB solo quando la data/ora del computer precede quella codificata nel nome del BLOB. Ad esempio, il processo cercherà `sample/2015-04-16/17:30/products.csv` non appena possibile ma non prima delle 17.30 del 16 aprile 2015 nel fuso orario UTC. Il processo non cercherà *mai* un file con una data/ora codificata precedente all'ultima rilevata.
> 
> Ad esempio, quando il processo trova il BLOB `sample/2015-04-16/17:30/products.csv` ignora tutti i file con una data/ora codificata precedente alle 17.30 del 16 aprile 2015. Quindi, se un BLOB `sample/2015-04-16/17:25/products.csv` viene creato in ritardo nello stesso contenitore, il processo lo ignora.
> 
> Analogamente, se il file `sample/2015-04-16/17:30/products.csv` viene generato solo alle 22.03 del 16 aprile 2015, ma nel contenitore non è presente alcun BLOB con una data/ora precedente, il processo usa questo file a partire dalle 22.03 del 16 aprile 2015 e usa i dati di riferimento precedenti fino a quel momento.
> 
> Un'eccezione a questo si verifica quando il processo deve elaborare nuovamente dei dati indietro nel tempo o quando il processo viene in primo luogo avviato. Nel momento iniziale il processo cerca i BLOB più recenti prodotti prima dell'ora di inizio del processo specificata. Questa operazione viene eseguita per verificare la presenza di un set di dati di riferimento **non vuoto** all'avvio del processo. Se non viene trovato, il processo restituisce il messaggio di diagnostica seguente: `Initializing input without a valid reference data blob for UTC time <start time>`.


È possibile usare [Data factory di Azure](https://azure.microsoft.com/documentation/services/data-factory/) per organizzare l'attività di creazione dei BLOB aggiornati richiesti dall'analisi di flusso per aggiornare le definizioni dei dati di riferimento. Data factory è un servizio di integrazione delle informazioni basato sul cloud che permette di automatizzare lo spostamento e la trasformazione dei dati. Data factory supporta la [connessione a un numero elevato di archivi dati basati su cloud e locali](../data-factory/data-factory-data-movement-activities.md) e il semplice trasferimento dei dati in base a una pianificazione regolare specificata dall'utente. Per altre informazioni e per istruzioni dettagliate su come configurare una pipeline di Data factory per generare dati di riferimento per l'analisi di flusso che vengano aggiornati in base a una pianificazione predefinita, consultare questo [esempio di GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## Suggerimenti sull'aggiornamento dei dati di riferimento ##

1. Sovrascrittura dei BLOB dei dati di riferimento non farà verificare l’analisi di flusso per ricaricare il BLOB e in alcuni casi può comportare il mancato completamento del processo. Il modo consigliato per modificare i dati di riferimento è quello di aggiungere un nuovo BLOB usando lo stesso contenitore e il modello di percorso definito nell'input del processo e usare una data/ora **maggiore** rispetto a quella specificata dall'ultimo BLOB nella sequenza.
2.	I BLOB dei dati di riferimento non vengono ordinati in base all’ora dell’"Ultima modifica"del BLOB, ma solo dall’ora e dalla date specificate nel nome del BLBO usando le sostituzioni della {data} e dell’{ora}.
3.	In alcuni casi un processo deve tornare indietro in orario, pertanto il BLOB dei dati di riferimento non deve essere modificato o eliminato.

## Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

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

<!---HONumber=AcomDC_0928_2016-->