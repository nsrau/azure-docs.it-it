<properties title="Azure Machine Learning Sample: Flight delay prediction" pageTitle="Machine Learning Sample: Flight delay prediction | Azure" description="A sample Azure Machine Learning experiment to develop a model that predicts whether a scheduled passenger flight will be delayed by more than 15 minutes." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Esempio di Azure Machine Learning: previsione dei ritardi aerei

<em>L'esperimento di esempio associato a questo modello in ML Studio è disponibile nella sezione **ESPERIMENTI** nella scheda **ESEMPI**. Il nome dell'esperimento è:</em>

    Sample Experiment - Flight Delay Prediction - Development

## Descrizione del problema

Si vuole prevede se, sulla base dei dati cronologici relativi alle prestazioni nel tempo e al meteo, il volo passeggeri programmato arriverà con un ritardo superiore a 15 minuti.

## Dati

**Dati relativi alle prestazioni nel tempo dei voli passeggeri ottenuti dalla raccolta dati TranStats del Dipartimento dei trasporti statunitense:** [][]<http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time></a>

Il set di dati si riferisce al periodo aprile-ottobre 2013. Prima del caricamento in Azure ML Studio:

-   Il set di dati è stato filtrato in modo da coprire i 70 aeroporti più trafficati degli Stati Uniti continentali.
-   Sono state selezionate le colonne seguenti: 'Year', 'Month', 'DayofMonth', 'DayOfWeek','Carrier', 'OriginAirportID','DestAirportID','CRSDepTime','DepDelay','DepDel15', 'CRSArrTime','ArrDelay','ArrDel15','Cancelled'
-   I voli cancellati sono stati etichettati in modo da indicare un ritardo superiore a 15 minuti.
-   I voli deviati sono stati esclusi.

**Le osservazioni meteo sono su base oraria e al suolo e vengono fornite dalla NOAA:** <http://cdo.ncdc.noaa.gov/qclcd_ascii/>

I dati relativi al meteo riguardano le osservazioni effettuate dalle stazioni meteo associate ai singoli aeroporti nel periodo aprile-ottobre 2013. Prima del caricamento in ML Studio:

-   Gli ID delle stazioni meteo sono stati mappati agli ID degli aeroporti corrispondenti.
-   Le stazioni meteo non associate ai 70 aeroporti più trafficati sono state escluse.
-   La colonna Date è stata suddivisa in colonne Year, Month e Day distinte.
-   Sono state selezionate le colonne seguenti: 'AirportID', 'Year','Month','Day', 'Time', 'TimeZone', 'SkyCondition', 'Visibility','WeatherType','DryBulbFarenheit', 'DryBulbCelsius', 'WetBulbFarenheit', 'WetBulbCelsius', 'DewPointFarenheit','DewPointCelsius', 'RelativeHumidity', 'WindSpeed', 'WindDirection', 'ValueForWindCharacter','StationPressure', 'PressureTendency', 'PressureChange', 'SeaLevelPressure', 'RecordType', 'HourlyPrecip', 'Altimeter'

## Modello

**Pre-elaborazione dei dati relativi ai voli**

In primo luogo, le colonne che con buona probabilità non daranno risultati, ovvero DepDelay, DepDel15, ArrDelay e Cancelled sono state escluse dal set di dati.

Le colonne 'DayOfWeek','OriginAirportID' e 'DestAirportID' rappresentano gli attributi di categoria. Dal momento però che sono costituiti da valori interi, vengono inizialmente analizzate come numeriche continue e devono essere convertite in categorie tramite l'editor metadati.

L'obiettivo è di unire tramite join i record relativi ai voli a quelli relativi al meteo orario usando l'ora di decollo pianificata come chiave di join. A tale scopo, il valore della colonna CSRDepTime viene arrotondato per difetto all'ora precedente più vicina.

**Pre-elaborazione dei dati relativi al meteo**

In primo luogo, vengono escluse le colonne con la maggior proporzione di dati mancanti, incluse le seguenti:

-   Tutte le colonne con valori stringa
-   ValueForWindCharacter,WetBulbFarenheit,WetBulbCelsius,PressureTendency,PressureChange,SeaLevelPressure,StationPressure

Alle colonne rimanenti viene quindi applicato lo strumento di pulitura dei valori mancanti.

L'ora dell'osservazione meteo viene arrotondata per eccesso all'ora completa successiva più vicina, in modo che possa essere unita tramite join in modo equo con l'ora di decollo pianificata. Notare che l'ora di decollo pianificata e l'ora dell'osservazione meteo vengono arrotondate in direzioni opposte. In tal modo infatti si garantisce che il modello userà solo le osservazioni meteo che si sono verificate nel passato in relazione all'ora del volo.

**Unione di dataset tramite join**

I record relativi ai voli vengono uniti tramite join ai dati relativi al meteo in corrispondenza dell'origine e della destinazione del volo.

Notare che i dati relativi al meteo sono indicati nell'ora locale, ma che origine e destinazione possono trovarsi in fusi orari diversi. Di conseguenza per tenere in considerazione la differenza di fuso orario il valore delle colonne del fuso orario viene sottratto dall'ora di decollo pianificata e dall'ora di osservazione meteo.

**Preparazione dei campioni per il training e la convalida**

Per creare i campioni per il training e la convalida, i dati vengono divisi nei record da aprile a settembre per il training e in quelli di ottobre per la convalida. Le colonne Year e Month vengono quindi rimosse dal set di dati.

I dati di training vengono quindi quantizzati in base a un binning di uguale altezza che viene applicato anche ai dati di convalida.

**Training e convalida del modello**

Viene quindi eseguito il training del modello di albero delle decisioni con boosting a due classi rispetto al campione di training. Il modello viene ottimizzato per il valore migliore di area sottesa alla curva (AUC) usando uno sweep parametrico casuale per 10 volte. Ai fini del confronto viene ottimizzato nello stesso modo il modello di regressione logistica a due classi.

Ai modelli sottoposti a training viene quindi assegnato un punteggio rispetto al set di convalida e, per analizzare la qualità dei modelli tra loro, viene usato il modulo di valutazione del modello.

**Post-elaborazione**

Gli ID aeroporto vengono uniti tramite join ai nomi e alle località leggibili degli aeroporti in modo da facilitare l'analisi dei risultati.

## Risultati

Il valore AUC del modello di albero delle decisioni con boosting rispetto al set di convalida è pari a 0,697, ovvero un valore leggermente migliore rispetto a quello del modello di regressione logistica il cui AUC è pari a 0,675.

  []: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
