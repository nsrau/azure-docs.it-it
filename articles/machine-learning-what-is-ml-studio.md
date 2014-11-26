<properties title="What Is Azure Machine Learning Studio?" pageTitle="What Is Machine Learning Studio? | Azure" description="Overview of Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Cos'è Azure Machine Learning Studio?

Microsoft Azure Machine Learning Studio è un ambiente di sviluppo visivo di collaborazione che consente di compilare, testare e distribuire soluzioni analitiche predittive applicate ai dati. Il servizio e l'ambiente di sviluppo di Machine Learning è basato sul cloud, offre risorse di calcolo e flessibilità di memoria ed elimina i problemi di configurazione e installazione perché l'utente lavora tramite il Web browser.

ML Studio è il punto di incontro di scienza dei dati, analisi predittive, risorse cloud e dati.

## Spazio di lavoro interattivo di ML Studio

Per sviluppare un modello di analisi predittiva, in genere si usano dati provenienti da una o più origini, che vengono trasformati e analizzati tramite varie funzioni di modifica dei dati e statistiche e da cui viene generato un set di risultati. Lo sviluppo di un modello come questo è un processo iterativo: man mano che si modificano le varie funzioni e i relativi parametri, i risultati convergono fino a quando l'utente non è soddisfatto del modello sottoposto a training.

**ML Studio** offre uno spazio di lavoro visivo e interattivo per compilare facilmente, testare ed eseguire l'iterazione di un modello di analisi predittiva. È possibile trascinare la selezione di ***set di dati*** e ***moduli***
 di analisi in un'***area di disegno*** interattiva, collegandoli tra loro per ottenere un ***esperimento***, da ***inviare*** a ML Studio per l'esecuzione. Per eseguire l'iterazione del progetto di modulo, si ***modifica*** l'esperimento, si ***salva*** una copia, se lo si desidera, e lo si invia di nuovo.

Non sono necessarie operazioni di programmazione, è sufficiente collegare visivamente i set di dati e i moduli per costruire un modello di analisi predittiva.

![Panoramica di ML Studio][Panoramica di ML Studio]

## Introduzione a ML Studio

Quando si apre ML Studio per la prima volta, a sinistra sono presenti le schede seguenti:

-   **ESPERIMENTI**: esperimenti creati, eseguiti e salvati come bozze.
-   **SERVIZI WEB**: elenco di esperimenti pubblicati.
-   **MODULI**: elenco di riferimento dei moduli di analisi disponibili in ML Studio.
-   **SET DI DATI**: elenco di riferimento dei set di dati disponibili in ML Studio.
-   **IMPOSTAZIONI**: insieme di impostazioni che è possibile usare per configurare l'account e le risorse.

> [WACOM.NOTE] Gli elenchi dei **moduli** e dei **set di dati** visualizzati in queste schede sono solo di riferimento. Questi elenchi includono informazioni aggiuntive sui set di dati e sui moduli, tra cui la data di creazione e i numeri di versione.
> Quando si costruisce un esperimento, viene visualizzato un elenco di lavoro dei set di dati e moduli disponibili a sinistra dell'area di disegno. È l'elenco dei componenti usati per compilare il modello.

## Componenti di un esperimento

Un esperimento è costituito da set di dati che forniscono i dati a moduli di analisi, che sono collegati tra loro per costruire un modello di analisi predittiva. In particolare, un esperimento valido ha le caratteristiche seguenti:

-   Ha almeno un set di dati e un modulo.
-   I set di dati possono essere collegati solo ai moduli.
-   I moduli possono essere collegati a set di dati o ad altri moduli.
-   Tutte le porte di input per i moduli devono avere un collegamento al flusso di dati.
-   Tutti i parametri necessari per un modulo devono essere impostati.

Per un esempio di creazione di un esperimento semplice, vedere **Creare un semplice esperimento in Azure Machine Learning Studio**.
Per una procedura dettagliata più completa della creazione di una soluzione di analisi predittiva, vedere **Sviluppare una soluzione predittiva con Azure Machine Learning**.

### Set di dati

Un set di dati include dati caricati in ML Studio per poter essere usati nel processo di modellazione. In ML Studio sono inclusi alcuni set di dati di esempio per provare a usare il programma. È possibile caricare altri set di dati in base alle esigenze. Ecco alcuni esempi di set di dati inclusi:

-   **Dati relativi al consumo di carburante per diverse automobili**: valori di consumo del carburante per le automobili identificate per numero di cilindri, potenza e così via
-   **Dati relativi al tumore al seno**: dati relativi alla diagnosi del tumore al seno
-   **Dati relativi agli incendi nei boschi**: dimensioni degli incendi boschivi nel nord-est del Portogallo

Quando si compila un esperimento, l'elenco di lavoro dei set di dati è disponibile a sinistra dell'area di disegno. È anche possibile visualizzare l'elenco dei set di dati caricati selezionando la scheda **SET DI DATI** all'avvio di ML Studio.

### Moduli

Un modulo è un algoritmo che è possibile applicare ai dati. ML Studio include diversi moduli, da funzioni di inserimento dei dati a processi di training, valutazione e convalida. Ecco alcuni esempi di moduli inclusi:

-   **Conversione in formato ARFF**: converte un set di dati serializzato .NET in formato ARFF
-   **Statistiche elementari**: calcola le statistiche elementari come la media, la deviazione standard e così via
-   **Regressione lineare**: crea un modello di regressione lineare online basato su valori descent con sfumatura
-   **Modello di punteggio**: assegna un punteggio a un modello di classificazione sottoposto a training o di regressione

Quando si compila un esperimento, l'elenco di lavoro dei moduli è disponibile a sinistra dell'area di disegno. È anche possibile visualizzare l'elenco dei moduli inclusi selezionando la scheda **MODULI** all'avvio di ML Studio.

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nell'area di disegno, i parametri del modulo sono visualizzati nel riquadro a destra dell'area di disegno. È possibile modificare i parametri in questo riquadro per ottimizzare il modello.

  [Panoramica di ML Studio]: ./media/machine-learning-what-is-ml-studio/context.jpg
