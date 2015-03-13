<properties 
	pageTitle="Servizio di ricerca: flusso di lavoro per sviluppatori" 
	description="Servizio di ricerca: flusso di lavoro per sviluppatori" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/16/2015" 
	ms.author="heidist"/>

# Ricerca di Azure: flusso di lavoro per lo sviluppo

[WACOM.INCLUDE [This article uses the Azure Preview portal](../includes/preview-portal-note.md)]

Questo articolo contiene una guida di orientamento e alcune procedure consigliate per la creazione e la manutenzione del servizio di ricerca con i relativi indici. 

Si suppone che sia già stato effettuato il provisioning del servizio. In caso contrario, per altre istruzioni vedere [Introduzione a Ricerca di Azure](../search-get-started/).

+ [Passaggio 1: Creare l'indice](#sub-1)
+ [Passaggio 2: Aggiungere i documenti](#sub-2)
+ [Passaggio 3: Eseguire una query su un indice](#sub-3)
+ [Passaggio 4: Aggiornare o eliminare gli indici e i documenti](#sub-4)
+ [Considerazioni sulla progettazione delle risorse di archiviazione](#sub-5)


<h2 id="sub-1">Passaggio 1: Creare l'indice</h2>

Le query sono mirate a un indice di ricerca contenente dati e attributi di ricerca. Il primo passaggio dopo il provisioning del servizio consiste quindi nel definire lo schema dell'indice in formato JSON e nell'eseguire una richiesta PUT HTTPS per creare l'indice nel servizio. 

Gli indici vengono creati dal codice dell'applicazione. Non sono disponibili strumenti o editor predefiniti per la definizione di un indice in un'interfaccia utente. Per esempi relativi ad alcuni modi per creare l'indice, vedere [Creare la prima soluzione di ricerca con Ricerca di Azure](../search-create-first-solution/), in cui si specifica lo schema nel file Program.cs, e [Introduzione all'assegnazione di punteggi per i profili in Ricerca di Azure](../search-get-started-scoring-profiles), che fornisce un indice in un file di schema JSON autonomo. Per altre informazioni sulla creazione dell'indice, vedere [Creare l'indice (API di Ricerca di Azure)](http://msdn.microsoft.com/library/dn798941.aspx) su MSDN.

<h2 id="sub-2">Passaggio 2: Aggiungere i documenti</h2>

Dopo aver creato l'indice di ricerca, sarà possibile aggiungere documenti all'indice eseguendo la richiesta POST in formato JSON. Ogni documento deve prevedere una chiave univoca e una raccolta di campi contenente dati disponibili e non per la ricerca. I dati del documento vengono rappresentati come un insieme di coppie chiave/valore per ogni campo.

È consigliabile aggiungere i documenti in batch per migliorare la velocità effettiva. È possibile creare batch contenenti fino a 1.000 documenti, supponendo che le dimensioni medie di un documento siano di ricerca 1 o 2 KB.

Per la richiesta POST c'è un codice di stato generale. I codici di stato sono HTTP 200 (Success) o HTTP 207 (Multi-Status) se esiste una combinazione di documenti elaborati correttamente o non elaborati. Oltre al codice di stato per la richiesta POST, la Ricerca di Azure prevede un campo di stato per ogni documento. In un caricamento in batch è necessario ottenere lo stato di ogni documento che indichi se l'inserimento è riuscito o no. Il campo di stato fornisce tali informazioni. Sarà impostato su false se non è stato possibile caricare il documento.

Con un carico pesante non è raro riscontrare alcuni errori di caricamento. Qualora ciò dovesse verificarsi, il codice di stato generale è 207, che indica un esito positivo parziale, mentre i documenti per i quali l'indicizzazione ha avuto esito negativo saranno contrassegnati dalla proprietà "status" impostata su false.

> [WACOM.NOTE] Quando il servizio riceve i documenti, questi vengono messi in coda per l'indicizzazione e potrebbero non essere immediatamente inclusi nei risultati della ricerca. Quando non è presente un carico pesante, i documenti vengono generalmente indicizzati entro pochi secondi.


<h2 id="sub-3">Passaggio 3: Eseguire una query su un indice</h2>

Dopo aver indicizzato i documenti è possibile eseguire le query di ricerca. Si può interrogare un indice per volta, usando OData o una semplice sintassi di query:

+	[Sintassi dell'espressione OData per la Ricerca di Azure](http://msdn.microsoft.com/library/dn798921.aspx)
+	[Semplice sintassi di query nella Ricerca di Azure](http://msdn.microsoft.com/library/dn798920.aspx)

<h2 id="sub-4">Passaggio 4: Aggiornare o eliminare gli indici e i documenti</h2>

È facoltativamente possibile apportare modifiche allo schema dell'indice di ricerca, aggiornare o eliminare i documenti all'interno dell'indice ed eliminare gli indici.

Quando si aggiorna un indice è possibile combinare più azioni (inserimento, unione, eliminazione) nello stesso batch, eliminando la necessità di eseguire più round trip. Attualmente, la Ricerca di Azure non supporta gli aggiornamenti parziali (HTTP PATCH), perciò per aggiornare un indice sarà necessario inviare nuovamente la relativa definizione.

<h2 id="sub-5">Considerazioni sulla progettazione delle risorse di archiviazione</h2>

Ricerca di Azure usa le risorse di archiviazione interne per gli indici e i documenti usati nelle operazioni di ricerca. L'analisi dei testi e degli indici dipende dalla disponibilità di tutti i campi per la ricerca e degli attributi associati.

Non tutti i campi di un documento sono disponibili per la ricerca. Ad esempio, se l'applicazione è un catalogo online per musica o video, è consigliabile archiviare i file binari nel servizio BLOB di Azure o in risorse di archiviazione con altri formati. Gli stessi file binari non sono disponibili per la ricerca, quindi non è necessario salvarli in modo permanente nell'archiviazione della Ricerca di Azure. Benché sia opportuno archiviare file immagine, video e audio in altri servizi o percorsi, è consigliabile includere un campo che associ l'URL al percorso del file. In questo modo sarà possibile restituire i dati esterni come parte dei risultati di ricerca. 

Per altre informazioni sulla creazione di indici o documenti, vedere [API REST del servizio di ricerca di Azure](http://msdn.microsoft.com/library/dn798935.aspx).


<!--Anchors-->
[Passaggio 1: Creare l'indice]: #sub-1
[Passaggio 2: Aggiungere i documenti]: #sub-2
[Passaggio 3: Eseguire una query su un indice]: #sub-3
[Passaggio 4: Aggiornare o eliminare gli indici e i documenti]: #sub-4
[Scelta di un archivio documenti]: #sub-5


<!--Image references-->

<!--Link references-->
[Introduzione a Ricerca di Azure]: ../search-get-started/
[Gestire il servizio di ricerca in Microsoft Azure]: ../search-manage/
[Creare la prima soluzione di ricerca con Ricerca di Azure]: ../search-create-first-solution/


<!--HONumber=46--> 
