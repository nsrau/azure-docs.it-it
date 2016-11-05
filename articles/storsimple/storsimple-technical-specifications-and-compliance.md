---
title: Specifiche tecniche StorSimple | Microsoft Docs
description: Vengono descritte le specifiche tecniche e le informazioni di conformità agli standard normativi per i componenti hardware di StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli

---
# Specifiche e conformità tecniche per il dispositivo StorSimple
## Overview
I componenti hardware del dispositivo Microsoft Azure StorSimple sono conformi alle specifiche tecniche e agli standard normativi descritti in questo articolo. Le specifiche tecniche descrivono i moduli di alimentazione e raffreddamento (PCM, Power and Cooling Module), le unità disco, la capacità dell'archiviazione e gli chassis. Le informazioni sulla conformità riguardano argomenti come gli standard internazionali, la sicurezza, le emissioni e i cavi.

## Specifiche del modulo di alimentazione e raffreddamento
Il dispositivo StorSimple dispone di due moduli di alimentazione e raffreddamento (PCM) compatibili con SBB a doppia ventola da 100-240 V. Fornisce una configurazione di alimentazione ridondante. Se un PCM ha esito negativo, il dispositivo continua a funzionare normalmente sull’altro PCM fino alla sostituzione del modulo non riuscito.

Lo chassis EBOD utilizza un PCM 580 W, mentre lo chassis principale utilizza un PCM 764 W. Nelle tabelle seguenti sono elencate le specifiche tecniche associate ai PCM.

| Specifiche | PCM 580 W (EBOD) | PCM 764 W (principale) |
| --- | --- | --- |
| Potenza massima in uscita |580 W |764 |
| Frequenza |50/60 Hz |50/60 Hz |
| Selezione intervallo di voltaggio |Intervallo automatico: 90 – 264 V CA, 47/63 Hz |Intervallo automatico: 90 - 264 V CA, 47/63 Hz |
| Afflusso di corrente massimo |20 A |20 A |
| Correzione del fattore di potenza |Voltaggio di ingresso nominale > 95% |Voltaggio di ingresso nominale > 95% |
| Armoniche |Conforme allo standard N61000-3-2 |Conforme allo standard N61000-3-2 |
| Output |Tensione di Standby 5V @ 2.0 A |Tensione di Standby 5V @ 2.7 A |
| +5V @ 42 A |+5V @ 40 A | |
| +12V @ 38 A |+12V @ 38 A | |
| Collegabile "hot" |Sì |Sì |
| LED e commutatori |Commutatore CA ACCESO/SPENTO e quattro indicatori LED di stato |Commutatore CA ACCESO/SPENTO e sei indicatori LED di stato |
| Raffreddamento chassis |Ventole di raffreddamento assiale con controllo di velocità della ventola variabile |Ventole di raffreddamento assiale con controllo di velocità della ventola variabile |

## Statistiche sul consumo energetico
Nella tabella seguente vengono elencati i dati sul consumo energetico tipico (i valori effettivi potrebbero differire da quelli pubblicati) per i vari modelli di dispositivo StorSimple.

| Condizioni | CA 240 V | CA 240 V | CA 240 V | CA 110 V | CA 110 V | CA 110 V |
| --- | --- | --- | --- | --- | --- | --- |
|  Ventole lente, unità inattive |1,45 A |0,31 kW |1057,76 BTU/h |3,19 A |0,34 kW |1160,13 BTU/h |
|  Ventole lente, accesso alle unità |1,54 A |0,33 kW |1126,01 BTU/h |3,27 A |0,36 kW |1228,37 BTU/h |
|  Ventole veloci, unità inattive, due PSU alimentati |2,14 A |0,49 kW |1671,95 BTU/h |4,99 A |0,54 kW |1842,56 BTU/h |
|  Ventole veloci, unità inattive, un PSU alimentato uno inattivo |2,05 A |0,48 kW |1637,83 BTU/h |4,58 A |0,50 kW |1706,07 BTU/h |
|  Ventole veloci, accesso alle unità, due PSU alimentati |2,26 A |0,51 kW |1740,19 BTU/h |4,95 A |0,54 kW |1842,56 BTU/h |
|  Ventole veloci, accesso alle unità, un PSU alimentato uno inattivo |2,14 A |0,49 kW |1671,95 BTU/h |4,81 A |0,53 kW |1808,44 BTU/h |

## Specifiche unità disco
Il dispositivo StorSimple supporta fino a 12 unità disco SAS (Serial Attached SCSI) del fattore forma 3,5 pollici. Le unità effettive possono essere una combinazione di unità SSD (Solid State Drive) o unità disco rigido (HDD), a seconda della configurazione del prodotto. I 12 slot di unità disco si trovano in una configurazione 3 per 4 nella parte anteriore dello chassis. Lo chassis EBOD dispone di spazio di archiviazione aggiuntivo per altre 12 unità disco. Si tratta sempre di HDD.

## Specifiche di archiviazione
I dispositivi StorSimple usano una combinazione di unità disco rigido e unità SSD per i modelli 8100 e 8600. La capacità totale utilizzabile per i modelli 8100 e 8600 è di circa 15 TB e 38 TB, rispettivamente. La tabella seguente illustra i dettagli dell'unità SSD, dell'unità disco rigido e della capacità del cloud nel contesto della capacità della soluzione StorSimple.

| Modello del dispositivo / Capacità | 8100 | 8600 |
| --- | --- | --- |
| Numero di unità disco rigido |8 |19 |
| Numero di unità SSD |4 |5 |
| Capacità della singola unità disco rigido |4 TB |4 TB |
| Capacità della singola unità SSD |400 GB |800 GB |
| Capacità di riserva |4 TB |4 TB |
| Capacità utilizzabile dell'unità disco rigido |14 TB |36 TB |
| Capacità utilizzabile dell'unità SSD |800 GB |2 TB |
| Capacità utilizzabile totale* |~ 15 TB |~ 38 TB |
| Capacità massima della soluzione (incluso il cloud) |200 TB |500 TB |

<sup>* </sup>- *La capacità totale utilizzabile include la capacità disponibile per i dati, i metadati e i buffer.*

## Specifiche su peso e dimensioni dello chassis
Nelle tabelle seguenti vengono elencate varie specifiche dello chassis per dimensioni e peso.

### Dimensioni dello chassis
Nella tabella seguente vengono elencate le dimensioni dello chassis in millimetri e pollici.

| Chassis | Millimetri | Pollici |
| --- | --- | --- |
| Altezza: |87,9 |3,46 |
| Larghezza tra la flangia di montaggio |483 |19,02 |
| Larghezza tra il corpo dello chassis |443 |17,44 |
| Profondità dalla flangia di montaggio anteriore all'estremità del corpo dello chassis |577 |22,72 |
| Profondità dal Pannello operativo all'estremità più distante dello chassis |630,5 |24,82 |
| Profondità dalla flangia di montaggio all'estremità più distante dello chassis |603 |23,74 |

### Peso chassis
A seconda della configurazione, uno chassis principale completamente carico può pesare da 21 a 33 kg e, per essere maneggiato, richiede due persone.

| Chassis | Peso |
| --- | --- |
| Peso massimo (a seconda della configurazione) |Da 30 a 33 kg |
| Vuoto (nessuna unità montata) |Da 21 a 23 kg |

## Specifiche ambientali dello chassis
In questa sezione vengono elencate le specifiche relative all'ambiente dello chassis. In questa categoria sono inclusi temperatura, umidità, altitudine, scosse, vibrazione, orientamento, sicurezza e compatibilità elettromagnetica (EMC).

### Temperatura e umidità
| Chassis | Intervallo di temperatura ambientale | Umidità relativa all'ambiente | Bulbo umido massimo |
| --- | --- | --- | --- |
| Operativo |Da 5°C a 35°C (da 41°F a 95°F) |Dal 20% all'80% senza condensa |28°C (82°F) |
| Non operativo |Da -40° a 70°C (da 40°F a 158°F) |Dal 5% al 100% senza condensa |29°C (84°F) |

### Flusso d'aria, altitudine, scosse, vibrazione, orientamento, sicurezza ed EMC
| Chassis | Specifiche operative |
| --- | --- |
| Flusso d'aria |Il flusso d'aria di sistema va dalla parte anteriore a quella posteriore. Il sistema deve essere utilizzato con un'installazione a bassa pressione e scarico posteriore. La pressione posteriore creata dalle porte del rack e da ostacoli non deve superare i 5 Pascal (colonna d'acqua da 0,5 mm). |
| Altitudine, operativa |Da -30 a 3.045 metri (da -100 a 10.000 piedi) con temperatura operativa massima a potenza ridotta di 5°C oltre i 7.000 piedi. |
| Altitudine, non operativa |Da -305 metri a 12.192 metri (da -1.000 piedi a 40.000 piedi) |
| Scossa, operativo |5 g 10 ms ½ sinusoidali |
| Scossa, non operativo |30 g 10 ms ½ sinusoidali |
| Vibrazione, operativa |0,21g RMS 5-500 Hz casuali |
| Vibrazione, non operativa |1,04 g RMS 2-200 Hz casuali |
| Vibrazione, trasferimento |3 g 2-200 Hz sinusoidali |
| Orientamento e montaggio |Montaggio rack da 19" (2 unità EIA) |
| Guide rack |Profondità dei rack minima conforme allo standard IEC 297 per l'adattabilità pari a 700 mm (31,50 pollici) |
| Sicurezza e approvazioni |CE e UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC |EN55022 (CISPR - A), FCC A |

## Conformità agli standard internazionali
Il dispositivo Microsoft Azure StorSimple è conforme ai seguenti standard internazionali:

* CE - EN 60950 - 1
* CB in rapporto a IEC 60950 - 1
* UL e cUL rispetto a UL 60950 - 1

## Conformità di sicurezza
Il dispositivo Microsoft Azure StorSimple soddisfa le seguenti classificazioni di sicurezza:

* Approvazione del tipo di prodotto di sistema: UL, cUL, CE
* Conformità di sicurezza: UL 60950, IEC 60950, EN 60950

## Conformità EMC
Il dispositivo Microsoft Azure StorSimple soddisfa le seguenti classificazioni EMC.

### Emissioni
Il dispositivo è conforme alle classificazioni EMC per i livelli di emissioni effettuate e irradiate.

* Livelli dei limiti delle emissioni effettuate: CFR 47 Part 15B Class A EN55022 Class A CISPR Class A
* Livelli dei limiti delle emissioni irradiate: CFR 47 Part 15B Class A EN55022 Class A CISPR Class A

### Armoniche e sfarfallio
Il dispositivo è conforme allo standard EN61000-3-2/3.

### Livelli del limite di immunità
Il dispositivo è conforme allo standard EN55024.

## Conformità del cavo di alimentazione CA
La spina e l'assembly completo del cavo di alimentazione devono soddisfare gli standard appropriati per il paese in cui viene utilizzato il dispositivo e devono disporre delle certificazioni di sicurezza ritenute accettabili in tale paese. Nelle tabelle seguenti vengono elencati gli standard per gli Stati Uniti e l'Europa.

### Cavi di alimentazione CA - Stati Uniti (devono essere elencati NRTL)
| Componente | Specifiche |
| --- | --- |
| Tipo di cavo |SV o SVT, 18 AWG minimo, 3 conduttori, lunghezza massima pari a 2,0 metri |
| Spina |Spina NEMA 5-15P con messa a terra classificata 120 V, 10 A oppure IEC 320 C14, 250 V, 10 A |
| Presa elettrica |IEC 320 C-13, 250 V, 10 A |

### Cavi di alimentazione CA - Europa
| Componente | Specifiche |
| --- | --- |
| Tipo di cavo |Armonizzate, H05-VVF-3G1.0 |
| Presa elettrica |IEC 320 C-13, 250 V, 10 A |

## Cavi di rete supportati
Per le interfacce di rete 10 GbE, DATA 2 e DATA 3, fare riferimento all'[elenco dei cavi di rete e dei moduli supportati](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## Passaggi successivi
È ora possibile iniziare la distribuzione di un dispositivo StorSimple nel data center. Per altre informazioni, vedere [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough-u2.md).

<!---HONumber=AcomDC_0817_2016-->