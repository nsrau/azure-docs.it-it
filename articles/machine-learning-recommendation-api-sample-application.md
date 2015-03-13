<properties 
	pageTitle="Operazioni comuni nell'API Recommendations di Machine Learning | Azure" 
	description="Recommendations di Azure ML - Applicazione di esempio" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/14/2014" 
	ms.author="jaymathe"/>


# Operazioni comuni nell'API Recommendations di Machine Learning

##Scopo

Questo documento illustra l'utilizzo di alcune API Recommendations di Azure ML, tramite un'applicazione di esempio.

Questa applicazione non è concepita per includere tutte le funzionalità o l'uso di tutte le API, ma solo per dimostrare alcune delle operazioni comuni da eseguire quando si vuole provare il servizio di raccomandazione di Azure ML. 

##Introduzione alla raccomandazione

Le raccomandazioni tramite il servizio di raccomandazione di Azure ML vengono abilitate quando si compila un modello di raccomandazione basato sui dati seguenti:

* Repository dell'elemento da raccomandare, noto anche come catalogo.
* Dati che rappresentano l'utilizzo di elementi per ogni utente/sessione. Queste informazioni possono essere ottenute nel tempo tramite l'acquisizione dei dati, non come parte dell'applicazione di esempio.

Dopo aver compilato un modello di raccomandazione, è possibile usarlo per una stima degli elementi da presentare all'utente, in base a un set di elementi o un singolo elemento selezionato.

Per ottenere lo scenario precedente, si eseguirà l'operazione seguente nel servizio di raccomandazione di Azure ML:

* Creare un modello: si tratta di un contenitore logico che includerà i dati (del catalogo e di utilizzo) e il modello o i modelli di stima. Il contenitore di ogni modello, identificato da un ID univoco allocato al momento della creazione e definito ID modello, viene usato dalla maggior parte delle API. 
* Caricare il catalogo: dopo aver creato un contenitore del modello, è possibile associarlo a un catalogo.

Nota: i passaggi precedenti ("creare un modello" e "caricare il catalogo") vengono in genere eseguiti una sola volta per il ciclo di vita del modello.

* Caricare i dati di utilizzo: per aggiungere i dati di utilizzo al contenitore del modello.
* Compilare il modello di raccomandazione: quando sono disponibili dati sufficienti, si attiverà la compilazione del modello di raccomandazione. Questa operazione userà algoritmi di Machine Learning avanzati per creare un modello di raccomandazione. A ogni compilazione è associato un ID univoco che dovrà essere conservato, essendo necessario per la funzionalità di alcune API.
* Monitorare il processo di compilazione: un modello di raccomandazione è un'operazione asincrona e può richiedere diversi minuti o diverse ore a seconda della quantità di dati (catalogo e utilizzo) e dei parametri della build. Sarà quindi necessario monitorare la compilazione. Un modello di raccomandazione viene compilato solo se la build associata termina in modo corretto.
* (Facoltativo) Scegliere una build del modello di raccomandazione attivo. Questo passaggio è necessario solo se nel contenitore del modello è disponibile più di un modello di raccomandazione. Qualsiasi richiesta di ottenere raccomandazioni senza indicare la build attiva (modello di raccomandazione attivo) verrà reindirizzata automaticamente dal sistema alla build attiva predefinita. 

Nota: una build attiva (modello di raccomandazione) è pronta per l'ambiente di produzione e viene compilata per i carichi di lavoro di produzione, a differenza di un modello di raccomandazione non attivo che rimane in un ambiente di tipo test, definito a volte di gestione temporanea.

* Ottenere raccomandazioni: dopo avere ottenuto un modello di raccomandazione, è possibile attivare raccomandazioni per un singolo elemento o un elenco di elementi selezionati. 

In genere si richiama Get Recommendation per un certo periodo di tempo. Nel frattempo, è possibile reindirizzare i dati di utilizzo al sistema di raccomandazione di Azure ML che li aggiungerà al contenitore del modello specificato. Quando si avranno dati di utilizzo sufficienti, si potrà attivare la compilazione di un nuovo modello di raccomandazione per usare i dati più attuali. 

##Prerequisiti

* Visual studio 2013
* Accesso a Internet 

##Soluzione di applicazione di esempio di Azure ML

La soluzione contiene il codice sorgente, file dei dati di utilizzo e del catalogo di esempio, oltre alle istruzioni per scaricare i pacchetti NuGet necessari per la compilazione.

##API usata

L'applicazione usa solo un piccolo subset della funzionalità di raccomandazione di Azure ML tramite un subset dell'API disponibile. Nell'applicazione vengono illustrate le API seguenti:

* Creare un modello: per creare il contenitore logico per i dati e i modelli di raccomandazione. Un modello è identificato da un nome. L'utente non può creare due volte un modello con lo stesso nome.
* Caricare un file del catalogo: per caricare i dati del catalogo.
* Caricare il file dei dati di utilizzo: per caricare i dati di utilizzo.
* Attivare la compilazione: per creare un modello di raccomandazione.
* Monitorare l'esecuzione della compilazione: per monitorare lo stato di compilazione di un modello di raccomandazione.
* Scegliere un modello di compilazione per la raccomandazione: per indicare il modello di raccomandazione da usare per impostazione predefinita per un determinato contenitore del modello. Questo passaggio è necessario solo se è disponibile più di un modello di raccomandazione e si vuole impostare una build non attiva come build attiva.
* Ottenere la raccomandazione: per recuperare l'elemento raccomandato in base a un singolo elemento o un elenco di elementi specifico. 

Per una descrizione completa dell'API, vedere la documentazione di Microsoft Azure Marketplace. 

Nota: con il tempo, un modello può avere diverse build (non contemporaneamente), ognuna delle quali è stata creata con lo stesso catalogo o un catalogo aggiornato e dati di utilizzo aggiuntivi.

## Inconvenienti comuni

* È necessario fornire il nome utente e la chiave primaria dell'account Microsoft Azure Marketplace come riga di comando per eseguire l'applicazione di esempio.
* Non è possibile eseguire consecutivamente l'app di esempio: il flusso dell'applicazione gestisce le operazioni di creazione, caricamento, monitoraggio della compilazione e recupero di raccomandazioni di un nome di modello predefinito, quindi l'esecuzione consecutiva non riuscirà se non si cambia il nome del modello tra una chiamata e l'altra.
* La raccomandazione potrebbe essere restituita senza dati: l'applicazione di esempio usa un file del catalogo e dei dati di utilizzo molto piccolo, quindi il modello di raccomandazione creato non è significativo. Di conseguenza, per alcuni elementi del catalogo non saranno disponibili elementi raccomandati.

## Dichiarazione di non responsabilità
L'applicazione di esempio non è destinata all'esecuzione in un ambiente di produzione. I dati forniti nel catalogo e i dati di utilizzo sono molto limitati e non forniscono un modello di raccomandazione significativo, ma vengono forniti solo per fini dimostrativi. 

## Note legali
Questo documento viene fornito "così com'è". Le informazioni e le indicazioni riportate nel presente documento, inclusi URL e altri riferimenti a siti Web Internet, sono soggette a modifica senza preavviso. 
Alcuni esempi usati in questo documento vengono forniti a scopo puramente illustrativo e sono fittizi. Nessuna associazione reale o connessione è intenzionale o può essere desunta. 
Il presente documento non fornisce all'utente alcun diritto legale rispetto a qualsiasi proprietà intellettuale in qualsiasi prodotto Microsoft. È possibile copiare e usare il presente documento per scopi interni e di riferimento. 
(c) 2014 Microsoft. Tutti i diritti sono riservati. 


<!--HONumber=46--> 
