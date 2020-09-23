---
title: Glossario di Advisor per le metriche
titleSuffix: Azure Cognitive Services
description: Idee e concetti chiave per il servizio metrica Advisor
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 25e5cb6e34624f3272e83e1e7c1cd847b865ae8e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938795"
---
# <a name="metrics-advisor-glossary-of-common-vocabulary-and-concepts"></a>Glossario del vocabolario e dei concetti comuni di Advisor per le metriche

Questo documento illustra i termini tecnici usati in metriche Advisor. Usare questo articolo per apprendere i concetti e gli oggetti comuni che possono verificarsi quando si usa il servizio.

## <a name="data-feed"></a>Feed di dati

> [!NOTE]
> Più metriche possono condividere la stessa origine dati e persino lo stesso feed di dati.

Un feed di dati è quello che le metriche vengono inserite dall'origine dati, ad esempio Cosmos DB o SQL Server. Un feed di dati contiene righe di:
* timestamp
* zero o più dimensioni
* una o più misure. 

## <a name="metric"></a>Metrica

Una metrica è una misura quantificabile utilizzata per monitorare e valutare lo stato di un processo di business specifico. Può essere una combinazione di più valori della serie temporale divisi in dimensioni. Una metrica di *integrità Web* , ad esempio, può contenere dimensioni per il *numero di utenti* e il *mercato en-US*.

## <a name="dimension"></a>Dimensione

Una dimensione è uno o più valori categorici. La combinazione di questi valori identifica una particolare serie temporale univariata, ad esempio: paese, lingua, tenant e così via.

## <a name="multi-dimensional-metric"></a>Metrica multidimensionale

Che cos'è una metrica MULTIDIMENSIONE? Si useranno due esempi. 

**Ricavi di un'azienda**

Si supponga di disporre di dati per i ricavi dell'azienda. I dati della serie temporale potrebbero avere un aspetto simile al seguente:

| Timestamp | Category | Mercato | Ricavi |
| ----------|----------|--------|----- |
| 2020-6-1 | Food | US | 1000 |
| 2020-6-1 | Abbigliamento | US | 2000 |
| 2020-6-2 | Food | Regno Unito | 800 | 
| ...      | ...  |... | ... |

In questo esempio la *categoria* e il *mercato* sono dimensioni. *Ricavi* è l'indicatore di prestazioni chiave (KPI) che può essere suddiviso in categorie e/o mercati diversi e può anche essere aggregato. Ad esempio, i ricavi del *cibo* per tutti i mercati.
 
**Conteggi degli errori per un'applicazione complessa**

Si supponga di avere dati relativi al numero di errori registrati in un'applicazione. I dati della serie temporale potrebbero avere un aspetto simile al seguente:

| Timestamp | Componente dell'applicazione | Region | Totale errori |
| ----------|----------|--------|----- |
| 2020-6-1 | Database Employee | EUROPA OCCIDENTALE | 9000 |
| 2020-6-1 | Coda messaggi | STATI UNITI ORIENTALI | 1000 |
| 2020-6-2 | Coda messaggi | STATI UNITI ORIENTALI | 8000| 
| ...      | ...           | ...     |  ...|

In questo esempio, il *componente dell'applicazione* e l' *area* sono dimensioni. Il *numero di errori* è l'indicatore KPI che può essere sezionato in categorie e/o mercati diversi e può anche essere aggregato. Ad esempio, il numero di errori della *coda di messaggi* in tutte le aree.

## <a name="measure"></a>Measure

Una misura è un termine fondamentale o specifico dell'unità e un valore quantificabile della metrica.

## <a name="time-series"></a>Serie temporale

Una serie temporale è una serie di punti dati indicizzati (o elencati o grafici) in ordine cronologico. In genere, una serie temporale è una sequenza eseguita in momenti successivi, equidistanti e equidistanti. Si tratta di una sequenza di dati a tempo discreto.

In metrica Advisor, i valori di una metrica in una combinazione di dimensioni specifica vengono chiamati una serie.

## <a name="granularity"></a>Granularità

La granularità indica il modo in cui i punti dati frequenti verranno generati nell'origine dati. Ad esempio, ogni giorno, ogni ora.

## <a name="start-time"></a>Ora di inizio

L'ora di inizio è l'ora in cui si vuole che l'origine dati sia iniziata da metrica Advisor per l'inserimento dei dati dall'origine dati. L'origine dati deve contenere dati all'ora di inizio specificata.

## <a name="confidence-boundaries"></a>Limiti di confidenza

> [!NOTE]
> I limiti di confidenza non sono l'unica misura utilizzata per individuare le anomalie. È possibile che i punti dati all'esterno di questo limite siano contrassegnati come normali dal modello di rilevamento. 

In metrica Advisor i limiti di confidenza rappresentano la riservatezza dell'algoritmo usato e vengono usati per filtrare le anomalie eccessivamente sensibili. Nel portale Web, i limiti di confidenza vengono visualizzati come una banda blu trasparente. Tutti i punti all'interno della banda vengono considerati come punti normali.

Metrica Advisor fornisce strumenti per regolare la riservatezza degli algoritmi utilizzati. Per altre informazioni, vedere [procedura: configurare le metriche e ottimizzare la configurazione del rilevamento](how-tos/configure-metrics.md) .

![Limiti di confidenza](media/confidence-bounds.png)


## <a name="hook"></a>Gancio

Metrica Advisor consente di creare e sottoscrivere gli avvisi in tempo reale. Questi avvisi vengono inviati tramite Internet, [usando un hook](how-tos/alerts.md).

## <a name="anomaly-incident"></a>Evento imprevisto anomalie

Dopo che una configurazione di rilevamento viene applicata alle metriche, gli eventi imprevisti vengono generati ogni volta che una serie al suo interno presenta un'anomalia. Nei set di dati di grandi dimensioni questa operazione può risultare eccessiva, quindi la metrica Advisor raggruppa serie di anomalie all'interno di una metrica in un evento imprevisto. Il servizio valuterà anche la gravità e fornirà strumenti per [la diagnosi dell'evento imprevisto](how-tos/diagnose-incident.md).

### <a name="incident-tree"></a>Albero eventi imprevisti

In metrica Advisor è possibile applicare il rilevamento delle anomalie sulle metriche, quindi la metrica Advisor monitora automaticamente tutte le serie temporali di tutte le combinazioni di dimensioni. Ogni volta che viene rilevata un'anomalia, la metrica Advisor aggrega le anomalie in eventi imprevisti.
Dopo che si è verificato un evento imprevisto, Metrics Advisor fornirà un albero degli eventi imprevisti con una gerarchia di anomalie che contribuiscono e ne identificherà uno con il maggiore effetto. Ogni evento imprevisto presenta un'anomalia della causa radice, ovvero il nodo principale dell'albero.

### <a name="anomaly-grouping"></a>Raggruppamento anomalie

Metrica Advisor fornisce la possibilità di trovare le serie temporali correlate con modelli simili. Può inoltre fornire informazioni approfondite sull'effetto sulle altre dimensioni e correlare le anomalie.

### <a name="time-series-comparison"></a>Confronto tra serie temporali

È possibile scegliere più serie temporali per confrontare le tendenze in un'unica visualizzazione. Questo consente di visualizzare e confrontare le serie correlate in modo chiaro e intuitivo.

## <a name="detection-configuration"></a>Configurazione rilevamento

>[!Note]
>Le configurazioni di rilevamento vengono applicate solo all'interno di una singola metrica.

Nel portale Web di Advisor di metrica, una configurazione di rilevamento (ad esempio sensitivity, auto snooze e Direction) viene elencata nel riquadro sinistro quando si visualizza una metrica. I parametri possono essere ottimizzati e applicati a tutte le serie all'interno di questa metrica.

Una configurazione di rilevamento è necessaria per ogni serie temporale e determina se un punto della serie temporale è un'anomalia. Per la prima onboarding dei dati, in metrica Advisor viene configurata una configurazione predefinita per l'intera metrica. 

È inoltre possibile perfezionare la configurazione applicando parametri di ottimizzazione per un gruppo di serie o uno specifico. Verrà applicata una sola configurazione a una serie temporale:
* Le configurazioni applicate a una serie specifica sovrascriveranno le configurazioni per un gruppo
* Le configurazioni per un gruppo sovrascrivono le configurazioni applicate all'intera metrica.

Metrica Advisor fornisce diversi [metodi di rilevamento](how-tos/configure-metrics.md#anomaly-detection-methods)ed è possibile combinarli usando gli operatori logici.

### <a name="smart-detection"></a>Rilevamento intelligente

Rilevamento di anomalie usando più algoritmi di machine learning.

**Sensitivity**: valore numerico per regolare la tolleranza del rilevamento delle anomalie. Visivamente, maggiore è il valore, più ristretto è il limite superiore e inferiore attorno alla serie temporale.

### <a name="hard-threshold"></a>Soglia rigida

I valori al di fuori dei limiti superiore o inferiore sono le anomalie.

**Min**: limite inferiore

**Max**: limite superiore

### <a name="change-threshold"></a>Modifica soglia

Usare il valore del punto precedente per determinare se questo punto è un'anomalia.

**Percentuale di modifica**: rispetto al punto precedente, il punto corrente è un'anomalia se la percentuale di modifica è maggiore di questo parametro.

**Modificare i punti**: il numero di punti da analizzare.

### <a name="common-parameters"></a>Parametri comuni

**Direction**: un punto è un'anomalia solo quando la deviazione si verifica nella direzione verso l' *alto*, verso il *basso*o *entrambi*.

L' **anomalia non è valida fino a quando**: un punto dati è solo un'anomalia se una percentuale specificata di punti precedenti è anche un'anomalia.

## <a name="alert-settings"></a>Impostazione avviso

Le impostazioni degli avvisi determinano quali anomalie devono attivare un avviso. È possibile impostare più avvisi con impostazioni diverse. Ad esempio, è possibile creare un avviso per le anomalie con un minore effetto aziendale e un altro per gli avvisi di maggiore importanza.

È anche possibile creare un avviso tra le metriche. Ad esempio, un avviso che viene attivato solo se due metriche specificate presentano anomalie.

### <a name="alert-scope"></a>Ambito avviso

Ambito avviso si riferisce all'ambito a cui si applica l'avviso. Sono disponibili quattro opzioni:

**Anomalie di tutte le serie**: gli avvisi verranno attivati per le anomalie in tutte le serie all'interno della metrica.

**Anomalie nel gruppo serie**: gli avvisi verranno attivati solo per le anomalie in dimensioni specifiche del gruppo di serie. Il numero di dimensioni specificate deve essere minore delle dimensioni totali del numero.

**Anomalie nella serie preferiti**: gli avvisi verranno attivati solo per le anomalie che vengono aggiunte come preferiti. È possibile scegliere un gruppo di serie come preferito per ogni configurazione di rilevamento.

**Anomalie nelle prime n di tutte le serie**: gli avvisi verranno attivati solo per le anomalie nelle prime n serie. È possibile impostare i parametri per specificare il numero di timestamp da tenere in considerazione e quante anomalie devono essere presenti per inviare l'avviso.

### <a name="severity"></a>Gravità

Il livello di gravità è un livello che l'Advisor di metrica usa per descrivere la gravità dell'evento imprevisto, tra cui *alto*, *medio*e *basso*.

Attualmente, per misurare la gravità dell'avviso, in Advisor vengono utilizzati i fattori seguenti:
1. La proporzione del valore e la percentuale di anomalie nella metrica.
1. Sicurezza delle anomalie.
1. Le impostazioni preferite contribuiscono anche alla gravità.

### <a name="auto-snooze"></a>Snooze automatico

Alcune anomalie sono problemi temporanei, specialmente per le metriche di granularità ridotte. È possibile *posticipare* un avviso per un numero specifico di punti di tempo. Se si verificano anomalie nel numero di punti specificato, non verrà attivato alcun avviso. Il comportamento di snooze automatico può essere impostato a livello di metrica o di serie.

Il comportamento di snooze può essere impostato a livello di metrica o di serie.

## <a name="data-feed-settings"></a>Impostazioni feed di dati

### <a name="ingestion-time-offset"></a>Offset tempo di inserimento

Per impostazione predefinita, i dati vengono inseriti in base alla granularità (ad esempio, *ogni giorno*). Utilizzando un numero intero positivo, è possibile ritardare l'inserimento dei dati in base al valore specificato. Utilizzando un numero negativo, è possibile avanzare l'inserimento in base al valore specificato.

### <a name="max-ingestion-per-minute"></a>Inserimento massimo al minuto

Impostare questo parametro se l'origine dati supporta la concorrenza limitata. In caso contrario, lasciare le impostazioni predefinite.

### <a name="stop-retry-after"></a>Arresta tentativi dopo

Se l'inserimento dei dati ha esito negativo, la metrica Advisor tenterà automaticamente di riprovare dopo un periodo di tempo. L'inizio del periodo è l'ora in cui si è verificata la prima inserimento di dati. La lunghezza del periodo di ripetizione dei tentativi viene definita in base alla granularità. Se si usa il valore predefinito ( `-1` ), il periodo di ripetizione dei tentativi verrà determinato in base alla granularità:

| Granularità       | Arresta tentativi dopo           |
| :------------ | :--------------- |
| Giornaliero, personalizzato (>= 1 giorno), settimanale, mensile, annuale     | 7 giorni          |
| Ogni ora, personalizzata (< 1 giorno)       | 72 ore |

### <a name="min-retry-interval"></a>Intervallo minimo tra tentativi

È possibile specificare l'intervallo minimo quando si tenta di eseguire il pull dei dati dall'origine. Se si usa il valore predefinito ( `-1` ), l'intervallo tra tentativi verrà determinato in base alla granularità:

| Granularità       | Intervallo minimo tra tentativi           |
| :------------ | :--------------- |
| Giornaliero, personalizzato (>= 1 giorno), settimanale, mensile     | 30 minuti          |
| Ogni ora, personalizzata (< 1 giorno)      | 10 minuti |
| Annuale | 1 giorno          |

### <a name="grace-period"></a>Periodo di tolleranza

> [!Note]
> Il periodo di tolleranza inizia in corrispondenza del tempo di inserimento normale, più l'offset del tempo di inserimento specificato.
    
Un periodo di tolleranza è un periodo di tempo in cui metrica Advisor continuerà a recuperare i dati dall'origine dati, ma non genererà alcun avviso. Se non sono stati inseriti dati dopo il periodo di tolleranza, verrà attivato un avviso di *feed di dati non disponibile* .

### <a name="snooze-alerts-in"></a>Snooze avvisi in

Quando questa opzione è impostata su zero, ogni timestamp con *non disponibile* attiverà un avviso. Se è impostato su un valore diverso da zero, il numero specificato di avvisi *non disponibili* verrà posticipato se non sono stati recuperati dati.

## <a name="data-feed-permissions"></a>Autorizzazioni feed di dati

Per gestire le autorizzazioni per i feed di dati sono disponibili due ruoli: *Administrator*e *Viewer*. 

* Un *amministratore* ha il controllo completo del feed di dati e delle metriche al suo interno. Possono attivare, sospendere, eliminare il feed di dati e apportare aggiornamenti ai feed e alle configurazioni. Un *amministratore* è in genere il proprietario della metrica.

* Un *Visualizzatore* è in grado di visualizzare il feed di dati o le metriche, ma non è in grado di apportare modifiche. 

## <a name="next-steps"></a>Passaggi successivi
- [Panoramica di Advisor metriche](overview.md)
- [Prova il sito demo](quickstarts/explore-demo.md)
- [Usare il portale Web](quickstarts/web-portal.md)