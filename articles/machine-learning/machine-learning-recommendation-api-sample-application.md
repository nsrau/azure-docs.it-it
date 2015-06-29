<properties 
	pageTitle="Operazioni comuni nell'API Recommendations di Machine Learning | Microsoft Azure" 
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
	ms.date="04/15/2015" 
	ms.author="luiscabrer"/>


# Operazioni comuni nell'API Recommendations di Machine Learning

##Scopo

Questo documento illustra l'utilizzo dell'API Recommendations di Azure Machine Learning, tramite un'[applicazione di esempio](http://1drv.ms/1xeO2F3).

Questa applicazione non è concepita per includere tutte le funzionalità, né per l'uso di tutte le API, ma serve solo per dimostrare alcune delle operazioni comuni da eseguire quando si vuole provare il servizio di raccomandazione di Machine Learning.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Introduzione al servizio di raccomandazione di Machine Learning

Le raccomandazioni tramite il servizio di raccomandazione di Machine Learning vengono abilitate quando si compila un modello di raccomandazione basato sui dati seguenti:

* Repository dell'elemento da raccomandare, noto anche come catalogo.
* Dati che rappresentano l'utilizzo di elementi per ogni utente o sessione. Queste informazioni possono essere ottenute nel tempo tramite l'acquisizione dei dati, non come parte dell'app di esempio.

Dopo aver compilato un modello di raccomandazione, è possibile usarlo per una stima degli elementi a cui un utente può essere interessato, in base a un set di elementi o un singolo elemento selezionato dall'utente.

Per abilitare lo scenario precedente, eseguire le operazioni seguenti nel servizio di raccomandazione di Machine Learning:

* Creare un modello: si tratta di un contenitore logico che include i dati, del catalogo e di utilizzo, e il modello o i modelli di stima. Il contenitore di ogni modello è identificato da un ID univoco allocato al momento della creazione. Questo ID è definito ID modello e viene usato dalla maggior parte delle API. 
* Caricare nel catalogo: quando si crea un contenitore del modello, è possibile associarlo a un catalogo.

**Nota**: le operazioni di creazione di un modello e caricamento in un catalogo vengono in genere eseguite una sola volta per il ciclo di vita del modello.

* Caricare i dati di utilizzo: i dati di utilizzo vengono aggiunti al contenitore del modello.
* Compilare un modello di raccomandazione: quando sono disponibili dati sufficienti, si potrà compilare il modello di raccomandazione. Questa operazione usa algoritmi di Machine Learning avanzati per creare un modello di raccomandazione. A ogni compilazione è associato un ID univoco, che dovrà essere conservato, essendo necessario per la funzionalità di alcune API.
* Monitorare il processo di compilazione: la compilazione di un modello di raccomandazione è un'operazione asincrona e può richiedere diversi minuti o diverse ore, a seconda della quantità di dati \(catalogo e utilizzo\) e dei parametri di compilazione. È quindi necessario monitorare la compilazione. Un modello di raccomandazione viene creato solo se la compilazione associata riesce.
* \(Facoltativo\) Scegliere una compilazione del modello di raccomandazione attivo: questo passaggio è necessario solo se nel contenitore del modello è disponibile più di una compilazione del modello di raccomandazione. Qualsiasi richiesta di ottenere raccomandazioni senza indicare il modello di raccomandazione attivo viene reindirizzata automaticamente dal sistema alla compilazione attiva predefinita. 

**Nota**: un modello di raccomandazione attivo è pronto per la produzione e viene compilato per i carichi di lavoro di produzione. Differisce da un modello di raccomandazione non attivo che rimane in un ambiente di tipo test, definito a volte gestione temporanea.

* Ottenere raccomandazioni: dopo avere ottenuto un modello di raccomandazione, è possibile attivare le raccomandazioni per un singolo elemento o un elenco di elementi selezionati. 

In genere si richiama Get Recommendation per un certo periodo di tempo. Durante questo intervallo di tempo è possibile reindirizzare i dati di utilizzo al sistema di raccomandazione di Machine Learning che li aggiungerà al contenitore del modello specificato. Quando si avranno dati di utilizzo sufficienti, si potrà compilare un nuovo modello di raccomandazione che incorpora i dati di utilizzo aggiuntivi.

##Prerequisiti

* Visual Studio 2013
* Accesso a Internet 

##Soluzione di app di esempio di Azure Machine Learning

La soluzione contiene il codice sorgente, i file dei dati di utilizzo e del catalogo di esempio, oltre alle istruzioni per scaricare i pacchetti necessari per la compilazione.

##API usate

L'applicazione usa la funzionalità di raccomandazione di Machine Learning tramite un subset delle API disponibili. Nell'applicazione vengono illustrate le API seguenti:

* Creare un modello: creare il contenitore logico per i dati e i modelli di raccomandazione. Un modello è identificato da un nome. Non è possibile creare più volte un modello con lo stesso nome.
* Caricare un file del catalogo: vengono caricati i dati del catalogo.
* Caricare il file dei dati di utilizzo: vengono caricati i dati di utilizzo.
* Attivare la compilazione: viene creato un modello di raccomandazione.
* Monitorare l'esecuzione della compilazione: viene monitorato lo stato di compilazione di un modello di raccomandazione.
* Scegliere un modello di compilazione per la raccomandazione: viene indicato quale modello di raccomandazione sarà usato per impostazione predefinita per un determinato contenitore del modello. Questo passaggio è necessario solo se è disponibile più di un modello di raccomandazione e si vuole attivare una compilazione non attiva come modello di raccomandazione attivo.
* Ottenere la raccomandazione: viene recuperato l'elemento raccomandato in base a un singolo elemento o un set di elementi specificati. 

Per una descrizione completa delle API, vedere la documentazione di Microsoft Azure Marketplace.

**Nota**: con il tempo, un modello può avere diverse compilazioni \(non contemporaneamente\). Ogni compilazione viene creata con lo stesso catalogo o un catalogo aggiornato e dati di utilizzo aggiuntivi.

## Inconvenienti comuni

* È necessario fornire il nome utente e la chiave primaria dell'account Microsoft Azure Marketplace per eseguire l'app di esempio.
* L'app di esempio non può essere eseguita consecutivamente. Il flusso dell'applicazione include le operazioni di creazione, caricamento, monitoraggio della compilazione e recupero di raccomandazioni da un modello predefinito. Di conseguenza, l'esecuzione consecutiva non riuscirà se non si cambia il nome del modello tra una chiamata e l'altra.
* La raccomandazione potrebbe essere restituita senza dati. L'app di esempio usa un file del catalogo e dei dati di utilizzo molto piccolo. Alcuni elementi del catalogo non avranno pertanto elementi raccomandati.

## Dichiarazione di non responsabilità
L'app di esempio non è destinata all'esecuzione in un ambiente di produzione. I dati forniti nel catalogo sono molto limitati e non offrono un modello di raccomandazione significativo. Vengono forniti per fini dimostrativi.
 

<!---HONumber=58_postMigration-->