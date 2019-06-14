---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 6016b13fe7d3e1f3b673bd2446d2f68b04878cd6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124742"
---
Bing Statistics offre analisi per le API di ricerca Bing. Le analisi includono il volume delle chiamate, le principali stringhe di query, la distribuzione geografica e altro ancora. Per abilitare Bing Statistics nella sottoscrizione a pagamento di Ricerca Bing, passare al [dashboard di Azure](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), selezionare la sottoscrizione a pagamento e fare clic su Enable Bing Statistics (Abilita Bing Statistics). L'abilitazione di Bing Statistics aumenta leggermente la tariffa della sottoscrizione (vedere i [prezzi](https://aka.ms/bingstatisticspricing)).

> [!NOTE]
> Bing Statistics è disponibile solo con sottoscrizioni a pagamento, non con sottoscrizioni di prova gratuita.

> [!NOTE]
> Non è possibile usare i dati disponibili tramite il dashboard di Bing Statistics per creare applicazioni per la distribuzione a terze parti.

Bing aggiorna i dati delle analisi ogni 24 ore e gestisce una cronologia risalente fino a 13 mesi prima.

## <a name="accessing-your-analytics"></a>Accesso alle analisi

Per accedere al dashboard delle analisi, andare su https://bingapistatistics.com. Verificare di aver effettuato l'accesso usando lo stesso account Microsoft impiegato per ottenere la sottoscrizione a pagamento.

## <a name="filtering-the-data"></a>Filtro dei dati

Per impostazione predefinita, i grafici riflettono tutti i dati delle metriche a cui si ha accesso. È possibile filtrare i dati visualizzati nei grafici selezionando le risorse, i mercati, gli endpoint e il periodo di reporting a cui si è interessati. I grafici cambiano a seconda dei filtri applicati. Di seguito vengono descritti i filtri che è possibile modificare.

- **ID risorsa**: l'ID risorsa univoco che identifica la sottoscrizione di Azure. Se si effettua la sottoscrizione a più di un livello dell'API di ricerca Bing, l'elenco contiene più ID. Per impostazione predefinita, sono selezionate tutte le risorse.  
  
- **Mercati**: i mercati da cui provengono i risultati. Ad esempio, en-us (inglese, Stati Uniti). Per impostazione predefinita, sono selezionati tutti i mercati. Si noti che Bing usa il mercato en-WW se la chiamata non specifica un mercato e Bing non può determinare il mercato dell'utente.  
  
- **Endpoints**: gli endpoint dell'API di ricerca Bing. L'elenco contiene tutti gli endpoint per cui si dispone di una sottoscrizione a pagamento. Per impostazione predefinita, sono selezionati tutti gli endpoint.  

- **Intervallo di tempo**: il periodo di reporting. È possibile specificare:
  - Tutto&mdash;include i dati degli ultimi 13 mesi  
  - Ultime 24 ore&mdash;Include le analisi delle ultime 24 ore  
  - Ultima settimana&mdash;Include le analisi dei sette giorni precedenti  
  - Mese precedente&mdash;Include le analisi dei 30 giorni precedenti  
  - Intervallo di date personalizzato&mdash;Include le analisi a partire dall'intervallo di date specificato, se disponibile  

  > [!NOTE]  
  > La visualizzazione delle metriche nel dashboard potrebbe richiedere fino a 24 ore. Il dashboard mostra la data e l'ora dell'ultimo aggiornamento dei dati.  

  > [!NOTE]  
  > Le metriche sono disponibili dal momento in cui viene abilitato il componente aggiuntivo Bing Statistics.

## <a name="charts-and-graphs"></a>Grafici

Il dashboard mostra i grafici delle metriche disponibili per l'endpoint selezionato. Non tutte le metriche sono disponibili per tutti gli endpoint. I grafici per ogni endpoint sono statici (non è possibile selezionare i grafici da visualizzare). Il dashboard mostra solo i grafici per cui ci sono dati.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Di seguito sono riportate le scelte possibili. Ogni metrica mostra le restrizioni dell'endpoint.

- **Volume chiamate**: mostra il numero di chiamate effettuate durante il periodo di reporting. Se il periodo di reporting è un giorno, il grafico mostra il numero di chiamate effettuate ogni ora. In caso contrario, il grafico mostra il numero di chiamate effettuate al giorno nel periodo di reporting.  
  
  > [!NOTE]
  > Il volume di chiamate può differire dai report di fatturazione, che generalmente includono solo le chiamate con esito positivo.

- **Query principali**: mostra le query principali e il numero di occorrenze di ogni query durante il periodo di reporting. È possibile configurare il numero di query visualizzate. Ad esempio, è possibile visualizzare le prime 25 o 50, 75 query. Query principali non è disponibile per gli endpoint seguenti:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Alcuni termini di query possono essere eliminati per rimuovere informazioni riservate, ad esempio messaggi di posta elettronica, numeri di telefono, codice fiscale e così via.

- **Distribuzione geografica**: i mercati da cui provengono i risultati. Ad esempio, en-us (inglese, Stati Uniti). Bing usa il parametro di query `mkt` per determinare il mercato, se specificato. In caso contrario, Bing usa segnali come l'indirizzo IP del chiamante per determinare il mercato.

- **Distribuzione dei codici di risposta**: i codici di stato HTTP di tutte le chiamate durante il periodo di reporting.

- **Distribuzione dell'origine delle chiamate**: i tipi di browser usati dagli utenti. Ad esempio, Microsoft Edge, Chrome, Safari e FireFox. Le chiamate effettuate dall'esterno di un browser, ad esempio da bot, Postman o usando curl da un'app console, vengono raggruppate in raccolte. L'origine viene determinata tramite il valore dell'intestazione agente utente della richiesta. Se la richiesta non include l'intestazione agente utente, Bing tenta di derivare l'origine da altri segnali.  

- **Distribuzione della ricerca sicura**: la distribuzione dei valori di ricerca sicura. Ad esempio, off, moderate o strict. Il parametro di query `safeSearch` contiene il valore, se specificato. In caso contrario, Bing definisce per impostazione predefinita il valore da moderare.  

- **Distribuzione delle risposte richieste**: le risposte dell'API di ricerca Web richieste nel parametro di query `responseFilter`.  

- **Distribuzione delle risposte restituite**: le risposte che l'API di ricerca Web ha restituito nella risposta.

- **Distribuzione server di risposta**: il server applicazioni che ha fornito le risposte dell'API. I valori possibili sono Bing.com (per il traffico fornito da dispositivi desktop e portatili) e Bing.com-mobile (per il traffico fornito da dispositivi mobili). Il server viene determinato tramite il valore dell'intestazione agente utente della richiesta. Se la richiesta non include l'intestazione agente utente, Bing tenta di derivare il server da altri segnali.

Di seguito vengono mostrate le analisi disponibili per ogni endpoint.

![Matrice di supporto della distribuzione in base all'endpoint](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)
