<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Machine Learning Sample: Prediction of bike rentals | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Esempio di Azure Machine Learning: Previsione del numero di noleggi di biciclette

*L'esperimento di esempio associato a questo modello in ML Studio è disponibile nella sezione **ESPERIMENTI** nella scheda **ESEMPI**. Il nome dell'esperimento è:*

    Sample Experiment - Demand Forecasting of Bikes

## Descrizione del problema

Previsione del numero di biciclette che saranno noleggiate ogni ora oggi, in base alla cronologia dei noleggi, alle condizioni meteorologiche misurate con cadenza oraria e all'indicazione del giorno come festività/giorno feriale/fine settimana. Le previsioni sono diverse per ogni ora, ad esempio
 al mattino i noleggi sono numerosi e di notte sono minimi.

## Dati

Set di dati di UCI Bike Rental basato su dati reali della società Capital Bikeshare che gestisce una rete di noleggio di biciclette a Washington DC. Il set di dati comprende una riga per ogni ora di ogni giorno del 2011 e del 2012, per un totale di 17379 righe. Il numero di biciclette noleggiate su base oraria è compreso tra 1 e 977.

## Modello

Abbiamo usato i dati del 2011 come set di apprendimento e quelli del 2012 come set di test. Abbiamo confrontato 4 set di componenti:

1.  funzioni meteo + festività + giorno feriale + fine settimana per la giornata prevista
2.  numero di biciclette noleggiate in ognuna delle 12 ore precedenti
3.  numero di biciclette noleggiate in ognuno dei 12 giorni precedenti alla stessa ora
4.  numero di biciclette noleggiate in ognuna delle 12 settimane precedenti nello stesso giorno e alla stessa ora

I componenti B acquisiscono le richiede di biciclette molto recenti. I componenti C acquisiscono la richiesta di biciclette in una determinata ora. I componenti D acquisiscono la richiesta di biciclette in un particolare orario e giorno della settimana.

Dato che l'etichetta (numero di noleggi) ha un valore reale, è impostata la regressione. Inoltre, dato che il numero di componenti è relativamente piccolo (inferiore a 100) e che non sono sparsi, il confine di decisione è probabilmente non lineare. Sulla base di questo si è deciso di usare l'algoritmo di regressione dell'albero delle decisioni con boosting.

## Risultati

| Componenti | Errore assoluto medio | Radice dell'errore quadratico medio |
|------------|-----------------------|-------------------------------------|
| A          | 89,7                  | 124,9                               |
| A+B        | 51,2                  | 86,7                                |
| A+B+C      | 48,5                  | 83,7                                |
| A+B+C+D    | 48,8                  | 83,2                                |

</table>
I risultati migliori sono mostrati dai componenti A+B+C e A+B+C+D. Sorprendentemente, i componenti D non apportano miglioramenti significativi rispetto a A+B+C.

