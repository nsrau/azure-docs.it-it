<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Esempio di Machine Learning: Attività CRM | Azure" description="Un esperimento di Azure Machine Learning di esempio per lo sviluppo di più modelli che consentono di stimare la varianza, l'upselling e la propensione di un cliente ad acquistare un nuovo prodotto." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Esempio di Machine Learning di Azure: attività CRM

>[AZURE.NOTE]
>L'[esperimento di esempio] e i [set di dati di esempio] associati a questo modello sono disponibili in ML Studio. Per informazioni dettagliate, vedere di seguito.
[Esperimento di esempio]: #sample-experiment
[Set di dati di esempio]: #sample-datasets

<!--
- [Problem Description]
- [Data]
- [Model]
- [Results]
- [API]
- [Sample Experiment]
-->
[Descrizione del problema]: #problem-description
[Dati]: #data
[Modello]: #model
[Risultati]: #results
[API]: #api

## Descrizione del problema ##

Previsione della varianza (cambio di provider), della possibilità di upselling (acquisto di aggiornamenti o componenti aggiuntivi) e della propensità all'acquisto di un nuovo prodotto (appetenza) dei clienti. 

## Dati ##

Set di dati di 50.000 clienti della società telecom francese Orange in Francia. Ogni cliente ha 230 elementi resi anonimi. I dati sono tratti da KDD Cup 2009. 

Si tratta di elementi di tipo numerico e stringa. Gli elementi sono molto sparsi.
 
## Modello ##

L'unica elaborazione preliminare dei dati è stata eseguita per gestire i valori mancanti, che sono stati sostituiti da "0" negli elementi stringa. Quasi tutti gli elementi numerici hanno valori non negativi. Il valore 1 è stato aggiunto ai valori esistenti degli elementi numerici e il valore 0 è stato usato per la sostituzione dei valori mancanti in alcune voci. In questo modo è possibile distinguere tra i valori 0 originali e i valori 0 che indicano un valore mancante. Questo risultato è stato ottenuto applicando prima di tutto un'operazione matematica (+1) ai soli elementi numerici. In seguito tutti i valori mancanti sono stati sostituiti con 0 (o "0" per le stringhe). 

Poiché sono presenti sia elementi numerici che elementi categorici, è stata usata una funzione di classificazione avanzata dell'albero delle decisioni con boosting. Tutti e tre i problemi non sono bilanciati e gli esempi positivi sono una minoranza. In alcuni casi ciò può avere come conseguenza il fatto che la funzionalità di classificazione ignorerà aree in uno spazio di elementi con un numero ridotto di esempi positivi. Per eseguire un test, sono stati usati due modelli per ogni problema: un modello usa i dati non elaborati e l'altro esegue la replica degli esempi positivi, in modo che il nuovo numero di esempi positivi sia quasi uguale al numero di esempi di test negativi. Questo risultato è stato ottenuto tramite un semplice script R che ha suddiviso gli esempi positivi e negativi e quindi ha aggiunto 13 copie dell'insieme positivo all'insieme negativo. 

## Risultati ##

I modelli hanno come output un punteggio correlato alla probabilità per un esito positivo per ogni attività. Poiché è possibile selezionare una soglia limite arbitraria per etichettare un'osservazione come positiva, le prestazioni vengono misurate in base all'area di curvatura (AUC) delle caratteristiche operative del ricevente (ROC). Si noti che per i casi di varianza e upselling la replica degli esempi positivi per creare un insieme più bilanciato ha permesso di migliorare in modo marginale le prestazioni del modello.  

<table border="1">
<tr><td>Varianza</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.711</td></tr>
<tr><td>Positivi replicati</td><td>0,728</td></tr>
</table>


<table border="0">
<tr><td>Upselling</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.853</td></tr>
<tr><td>Positivi replicati</td><td>0,865</td></tr>
</table>


<table border="0">
<tr><td>Appetenza</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.805</td></tr>
<tr><td>Positivi replicati</td><td>0,8</td></tr>
</table>

## API ##

Il modello non è stato reso operativo poiché include elementi anonimi.



## Esperimento di esempio

L'esperimento di esempio seguente associato a questo modello è disponibile in ML Studio nella sezione **ESPERIMENTI** della scheda **ESEMPI**.

> **Esperimenti di esempio - CRM - Sviluppo**

## Set di dati di esempio

I set di dati di esempio seguenti usati per questo esperimento sono disponibile in ML Studio nella tavolozza dei moduli in **Set di dati salvati**.

###Set di dati CRM condivisi
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]

###Etichette Appetenza CRM condivise
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]

###Etichette Varianza CRM condivise
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]

###Etichette Upselling CRM condivise
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
