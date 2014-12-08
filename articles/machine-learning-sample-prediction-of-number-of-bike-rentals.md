<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Esempio di Machine Learning: Previsione del numero di noleggi di biciclette | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Esempio di Machine Learning di Azure: Previsione del numero di noleggi di biciclette

>[AZURE.NOTE]
>L'[esperimento di esempio] e il [set di dati di esempio] associati a questo modello sono disponibili in ML Studio. Per informazioni dettagliate, vedere di seguito.
[Esperimento di esempio]: #sample-experiment
[Set di dati di esempio]: #sample-dataset


## Descrizione del problema ##
Previsione del numero di biciclette che saranno noleggiate ogni ora oggi, in base alla cronologia dei noleggi, alle condizioni meteorologiche misurate con cadenza oraria e all'indicazione del giorno come festività/giorno feriale/fine settimana. Le previsioni sono diverse per ogni ora, ad esempio
 al mattino i noleggi sono numerosi e di notte sono minimi. 

## Dati ##
Set di dati UCI relativo al noleggio di biciclette basato su dati reali della società Capital Bikeshare che gestisce una rete di noleggio di biciclette a Washington DC. Il set di dati comprende una riga per ogni ora di ogni giorno del 2011 e del 2012, per un totale di 17379 righe. Il numero di biciclette noleggiate su base oraria è compreso tra 1 e 977.

## Modello ##
Abbiamo usato i dati del 2011 come training set e quelli del 2012 come set di test. Abbiamo confrontato 4 set di componenti:

1. componenti meteo + festività + giorno feriale + fine settimana per la giornata prevista
1. numero di biciclette noleggiate in ognuna delle 12 ore precedenti
1. numero di biciclette noleggiate in ognuno dei 12 giorni precedenti alla stessa ora
1. numero di biciclette noleggiate in ognuna delle 12 settimane precedenti nello stesso giorno e alla stessa ora

I componenti B acquisiscono le richiede di biciclette molto recenti. I componenti C acquisiscono la richiesta di biciclette in una determinata ora. I componenti D acquisiscono la richiesta di biciclette in un particolare orario e giorno della settimana.

Dato che l'etichetta (numero di noleggi) ha un valore reale, è impostata la regressione. Inoltre, dato che il numero di componenti è relativamente piccolo (inferiore a 100) e che non sono sparsi, il confine di decisione è probabilmente non lineare. Sulla base di questo si è deciso di usare l'algoritmo di regressione dell'albero delle decisioni con boosting.

## Risultati ##

<table border="1">
<tr><th>Componenti</th><th>Errore assoluto medio</th> <th>Radice dell'errore quadratico medio</th> </tr>
<tr style="background-color: #fff"><td>A</td> <td> 89,7</td> <td>124,9 </td> </tr>
<tr style="background-color: #fff"><td>A+B</td><td>51,2 </td> <td>86,7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C</td><td> 48,5</td> <td> 83,7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C+D</td><td> 48,8 </td> <td>83,2 </td></tr>
</table>

</table>

I risultati migliori sono mostrati dai componenti A+B+C e A+B+C+D. Sorprendentemente, i componenti D non apportano miglioramenti significativi rispetto a A+B+C. 

## Esperimento di esempio

L'esperimento di esempio seguente associato a questo modello è disponibile in ML Studio nella sezione **ESPERIMENTI** della scheda **ESEMPI**.

> **Esperimento di esempio - Previsione della domanda di biciclette**


## Set di dati di esempio

L'esempio di set di dati seguente usato per questo esperimento è disponibile in ML Studio nella tavolozza dei moduli in **Set di dati salvati**.

###Set di dati UCI relativo al noleggio di biciclette
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]


