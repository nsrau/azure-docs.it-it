<properties 
	pageTitle="Set di dati in Azure Data Factory | Microsoft Azure" 
	description="Comprendere i set di dati di Data Factory di Azure e come crearli." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Set di dati in Azure Data Factory

## Descrizione
Un set di dati è una descrizione logica dei dati. I dati descritti possono variare da semplici byte a dati semistrutturati come i CSV fino alle tabelle relazionali o persino ai modelli. Il meccanismo (indirizzo, protocollo, schema di autenticazione) per accedere ai dati è definito nel servizio collegato e citato nella definizione del set di dati.

## Sintassi

	{
	    "name": "<name of dataset>",
	    "properties":
	    {
	       "structure": [ ],
	       "type": "<type of dataset>",
			"external": <boolean flag to indicate external data>,
	        "typeProperties":
	        {
	        },
	        "availability":
	        {
	
	        },
	       "policy": 
	        {      
	
	        }
	    }
	}

**Dettagli sintassi**

| Proprietà | Descrizione | Obbligatorio | Default |
| -------- | ----------- | -------- | ------- |
| name | Nome del set di dati | Sì | ND |
| structure | <p>Schema del set di dati</p><p>Vedere la sezione[Struttura del Dataset](#Structure)per ulteriori dettagli</p> | No. | ND |
| type | Tipo del set di dati | Sì | ND |
| typeProperties | <p>Proprietà corrispondente al tipo selezionato</p><p>Vedere la sezione [Tipo Dataset](#Type)per ulteriori informazioni sui tipi supportati e le relative proprietà.</p> | Sì | ND |
| external | Flag booleano per specificare se un set di dati in modo è generato in modo esplicito da una pipeline di data factory o meno | No | false | 
| disponibilità | <p>Definisce la finestra di elaborazione o il modello di sezionamento per la produzione di set di dati. </p><p>Vedere l’argomento[Disponibilità Dataset](#Availability)per ulteriori informazioni</p><p>Vedere[Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md)per ulteriori dettagli sul modello di sezionamento del set di dati </p> | Sì | ND
| policy | Definisce i criteri o la condizione che devono soddisfare i sezionamenti di set di dati. <p>Vedere l’argomento[Criteri Dataset](#Policy)per ulteriori informazioni</p> | No | ND |

### Esempio

Di seguito è riportato un esempio di un set di dati che rappresenta una tabella denominata**MyTable**nel**database SQL di Azure**. Le stringhe di connessione del database SQL di Azure sono definite in**AzureSqlLinkedService**a cui fa riferimento questo set di dati. Questo set di dati viene sezionato ogni giorno.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

## <a name="Structure"></a>Struttura del set di dati

La sezione**Struttura**afferma lo schema del set di dati. Esso contiene la raccolta di colonne e il tipo di queste ultime. Ogni colonna contiene le proprietà seguenti:

Proprietà | Descrizione | Obbligatorio | Default  
-------- | ----------- | -------- | -------
name | Nome della colonna. | No | ND 
type | Tipo di dati della colonna. | No | ND 

### Esempio

Nell'esempio seguente, il set di dati ha tre colonne pageviews, slicetimestamp e projectname..

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Type"></a>Tipo di set di dati

Le origini dati supportate e i tipi di set di dati vengono allineati. Vedere gli argomenti relativi al connettore citati nell’articolo[Attività di spostamento dati](data-factory-data-movement-activities.md)per ulteriori informazioni sui tipi e sulla configurazione dei set di dati.

## <a name="Availability"></a>Disponibilità dei set di dati

La sezione Disponibilità in un set di dati definisce la finestra di elaborazione o il modello di sezionamento per la produzione di set di dati. Vedere l'articolo [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) per altre informazioni sul modello di sezionamento e dipendenza di set di dati.

| Proprietà | Descrizione | Obbligatorio | Default |
| -------- | ----------- | -------- | ------- |
| frequency | Specifica l'unità di tempo per la produzione di sezioni di set di dati.<p>**Frequenza supportata**: minuto, ora, giorno, settimana, mese</p> | Sì | ND |
| interval | Consente di specificare un moltiplicatore di frequenza<p>"Frequency x interval" determina la frequenza con cui il sezionamento viene generato.</p><p>Se è necessario che il set di dati venga sezionato su base oraria, impostare **Frequency** su **Hour** e **interval** su **1**.</p><p> **Nota:** se si specifica la frequenza in minuti, è consigliabile impostare interval su non meno di 15</p> | Sì | ND |
| style | Specifica se il sezionamento deve essere generato all'inizio o alla fine dell'intervallo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><p>Se frequency è impostata su Month e style è impostato su EndOfInterval, il sezionamento viene generato l'ultimo giorno del mese. Se style è impostato su StartOfInterval, il sezionamento viene generato il primo giorno del mese.</p><p>Se frequency viene impostata su Day e style è impostato su EndOfInterval, il sezionamento viene generato durante l'ultima ora del giorno.</p>Se frequency è impostata su Hour e style è impostato su EndOfInterval, il sezionamento viene generato alla fine dell'ora. Ad esempio, per una sezione per periodo 1 PM – 2 PM, il sezionamento viene generato alle 2 del pomeriggio.</p> | No | EndOfInterval |
| anchorDateTime | Definisce la posizione assoluta nel tempo usato dall'utilità di pianificazione per calcolare i limiti della sezione del set di dati.<p>**Nota:** se AnchorDateTime ha parti della data che sono più granulari rispetto alla frequenza allora le parti più granulari vengono ignorate. Ad esempio, se **interval** è **hourly** (frequenza: ora e intervallo: 1) e **AnchorDateTime** contiene **minutes and seconds**, allora le parti **minutes and seconds** di AnchorDateTime vengono ignorate.</p>| No | 01/01/0001 |
| offset | Intervallo di tempo mediante il quale l'inizio e la fine di tutti i sezionamenti dei set di dati vengono spostati.<p>**Nota:** se vengono specificati sia l’anchorDateTime sia l’offset, il risultato è il turno combinato.</p> | No | ND |

### esempi di anchorDateTime

**Esempio:** sezionamenti dei set di dati di 23 ore che iniziano il 19-04-2007 alle 08:00:00

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": "23",	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}


### offset di esempio

Sezionamenti giornalieri che iniziano alle 6 AM anziché alla mezzanotte predefinita.

	"availability":
	{
		"frequency": "Day",
		"interval": "1",
		"offset": "06:00:00"
	}

In questo caso, SliceStart viene spostato di 6 ore e sarà 6 AM.

Per un programma di 12 mesi (frequency = month, interval = 12), offset: 60.00:00:00 significa ogni anno l'1 o il 2 marzo (60 giorni dall'inizio dell'anno se style = StartOfInterval), a seconda che l'anno sia bisestile o meno.



## <a name="Policy"></a>Criteri di set di dati

La sezione criteri nei set di dati definisce i criteri o la condizione che devono soddisfare i sezionamenti di set di dati.

### Criteri di convalida

| Nome criterio | Descrizione | Applicato a | Obbligatorio | Default |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Verifica che i dati in un BLOB di Azure soddisfino i requisiti di dimensione minima (in MB). | BLOB Azure | No | ND |
|minimumRows | Verifica che i dati in un database SQL Azure o in una tabella di Azure contengano il numero minimo di righe. | <ul><li>Database SQL di Azure</li><li>Tabella di Azure</li></ul> | No | ND

#### esempi

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### Criteri ExternalData

I set di dati esterni sono quelli che sono non stati prodotti da una pipeline in esecuzione in data factory.. Se il set di dati è contrassegnato come External, è possibile definire i criteri di ExternalData per influenzare il comportamento della disponibilità di sezionamento dei set di dati.

| Nome | Descrizione | Obbligatorio | Default Value |
| ---- | ----------- | -------- | -------------- |
| dataDelay | <p>Tempo di attesa per il controllo sulla disponibilità dei dati esterni per il sezionamento specificato. Ad esempio, se i dati dovrebbero essere disponibili una volta ogni ora, il controllo per visualizzare se i dati esterni sono effettivamente disponibili e se il sezionamento corrispondente sia pronto può essere posticipato da dataDelay.</p><p>Si applica solo al momento; ad esempio, se è l'1:00 PM e questo valore è 10 minutes, la convalida verrà avviata all'1:10 PM.</p><p>Questa impostazione non interessa i passati sezionamenti (sezionamenti con Slice End Time + dataDelay < Now) che verranno elaborati senza alcun ritardo.</p> <p>L'orario maggiore di 23:59 deve essere specificato usando il formato giorno.ore:minuti:secondi. Per specificare 24 ore, ad esempio, non utilizzare 24:00:00; utilizzare invece 1.00:00:00. Se si utilizza 24:00:00, verrà considerato come 24 giorni (24.00:00:00). Per 1 giorno e 4 ore, specificare 1:04:00:00. </p>| No | 0 |
| retryInterval | Il tempo di attesa tra un errore e il successivo tentativo. Si applica al tempo presente; Se il precedente tentativo non è riuscito, dopo di esso si aspetta tale tempo. <p>Se è l'1:00 pm, inizia il primo tentativo. Se la durata per completare il primo controllo di convalida è 1 minuto e l'operazione non è riuscita, il tentativo successivo sarà all’1:00 + 1 min (durata) + 1 min (intervallo tentativi) = 1:02 pm. </p><p>Per i sezionamenti passati, non si verificherà alcun ritardo. Il tentativo verrà eseguito immediatamente.</p> | No | 00:01:00 (1 minute) | 
| retryTimeout | Il timeout per ogni nuovo tentativo.<p>Se è impostato su 10 minuti, la convalida deve essere completata entro 10 minuti. Se sono necessari più di 10 minuti per eseguire la convalida, il tentativo verrà sospeso.</p><p>Se tutti i tentativi per la convalida scadono, il sezionamento verrà contrassegnato come TimedOut.</p> | No | 00:10:00 (10 minutes) |
| maximumRetry | Numero di volte per controllare la disponibilità dei dati esterni. Il valore massimo consentito è 10. | No | 3 | 

#### Ulteriori esempi

Se è necessario eseguire una pipeline su base mensile a una data e ora specifiche (si supponga al 3 di ogni mese alle ore 8:00), è possibile utilizzare il tag **offset** per impostare la data e l’ora di esecuzione.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}

<!---HONumber=AcomDC_0218_2016-->