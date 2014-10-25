<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Machine Learning Sample: CRM task | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Esempio di Azure Machine Learning: Attività CRM

<em>L'esperimento di esempio associato a questo modello in ML Studio è disponibile nella sezione **ESPERIMENTI** nella scheda **ESEMPI**. Il nome dell'esperimento è:</em>

    Sample Experiments - CRM - Development

## Descrizione del problema

Previsione della varianza (cambio di provider), della possibilità di upselling (acquisto di aggiornamenti o componenti aggiuntivi) e della propensità all'acquisto di un nuovo prodotto (appetenza) dei clienti.

## Dati

Set di dati di 50.000 clienti della società telecom francese Orange in Francia. Ogni cliente ha 230 elementi resi anonimi. I dati sono tratti da KDD Cup 2009.

Si tratta di elementi di tipo numerico e stringa. Gli elementi sono molto sparsi.

## Modello

L'unica elaborazione preliminare dei dati è stata eseguita per gestire i valori mancanti, che sono stati sostituiti da "0" negli elementi stringa. Quasi tutti gli elementi numerici hanno valori non negativi. Il valore 1 è stato aggiunto ai valori esistenti degli elementi numerici e il valore 0 è stato usato per la sostituzione dei valori mancanti in alcune voci. In questo modo è possibile distinguere tra i valori 0 originali e i valori 0 che indicano un valore mancante. Questo risultato è stato ottenuto applicando prima di tutto un'operazione matematica (+1) ai soli elementi numerici. In seguito tutti i valori mancanti sono stati sostituiti con 0 (o "0" per le stringhe).

Poiché sono presenti sia elementi numerici che elementi categorici, è stata usata una funzione di classificazione avanzata dell'albero decisionale. Tutti e tre i problemi non sono bilanciati e gli esempi positivi sono una minoranza. In alcuni casi ciò può avere come conseguenza il fatto che la funzionalità di classificazione ignorerà aree in uno spazio di elementi con un numero ridotto di esempi positivi. Per eseguire un test, sono stati usati due modelli per ogni problema: un modello usa i dati non elaborati e l'altro esegue la replica degli esempi positivi, in modo che il nuovo numero di esempi positivi sia quasi uguale al numero di esempi di test negativi. Questo risultato è stato ottenuto tramite un semplice script R che ha suddiviso gli esempi positivi e negativi e quindi ha aggiunto 13 copie dell'insieme positivo all'insieme negativo.

## Risultati

I modelli hanno come output un punteggio correlato alla probabilità per un esito positivo per ogni attività. Poiché è possibile selezionare una soglia limite arbitraria per etichettare un'osservazione come positiva, le prestazioni vengono misurate in base all'area di curvatura (AUC) delle caratteristiche operative del ricevente (ROC). Si noti che per i casi di varianza e upselling la replica degli esempi positivi per creare un insieme più bilanciato ha permesso di migliorare in modo marginale le prestazioni del modello.

<table border="1">
<tr><td>Varianza</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>Nessuna replica</td><td>0.711</td></tr>
<tr><td>Positivi replicati</td><td>0.728</td></tr>
</table>


<table border="0">
<tr><td>Upselling</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>Nessuna replica</td><td>0.853</td></tr>
<tr><td>Positivi replicati</td><td>0.865</td></tr>
</table>


<table border="0">
<tr><td>Appetenza</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>Nessuna replica</td><td>0.805</td></tr>
<tr><td>Positivi replicati</td><td>0.8</td></tr>
</table>

## API

Il modello non è stato reso operativo poiché include elementi anonimi.

