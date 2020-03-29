---
title: Aggiungere analisi all'API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Bing Statistics offre analisi all'API Ricerca immagini Bing. Le analisi includono il volume delle chiamate, le principali stringhe di query, la distribuzione geografica e altro ancora.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 5d1b0b19523eb37aa83aa59b24114be9f76ffa55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882789"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Aggiungere analisi alle API di Ricerca Bing

Statistiche Bing fornisce analisi per le API di Ricerca Bing. Queste analisi includono il volume delle chiamate, le stringhe di query principali, la distribuzione geografica e altro ancora. È possibile abilitare Statistiche Bing nel portale di [Azure](https://ms.portal.azure.com) passando alla risorsa di Azure e facendo clic su **Abilita statistiche Bing**.

> [!IMPORTANT]
> * Statistiche Bing non è disponibile con le sottoscrizioni `F0` di valutazione gratuite o le risorse nel piano tariffario gratuito.
> * Non è possibile usare i dati disponibili tramite il dashboard di Bing Statistics per creare applicazioni per la distribuzione a terze parti.
> * L'abilitazione delle statistiche di Bing aumenta leggermente la frequenza di abbonamento. Vedi [i prezzi](https://aka.ms/bingstatisticspricing) per i dettagli.


L'immagine seguente mostra l'analisi disponibile per ogni endpoint dell'API di Ricerca Bing.The following image shows the available analytics for each Bing Search API endpoint.

![Matrice di supporto della distribuzione in base all'endpoint](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Accedi alle tue analisi

Bing aggiorna i dati di analisi ogni 24 ore e mantiene fino a 13 mesi di cronologia a cui è possibile accedere dal dashboard di [analisi.](https://bingapistatistics.com) Assicurarsi di aver eseguito l'accesso con lo stesso account Microsoft (MSA) utilizzato per iscriversi a Bing Statistics.

> [!NOTE]  
> * La visualizzazione delle metriche nel dashboard potrebbe richiedere fino a 24 ore. Il dashboard mostra la data e l'ora dell'ultimo aggiornamento dei dati.  
> * Le metriche sono disponibili dal momento in cui viene abilitato il componente aggiuntivo Bing Statistics.

## <a name="filter-the-data"></a>Filtrare i dati

Per impostazione predefinita, i grafici e i grafici visualizzano tutte le metriche e i dati a cui si ha accesso. È possibile filtrare i dati visualizzati nei grafici selezionando le risorse, i mercati, gli endpoint e il periodo di reporting a cui si è interessati. È possibile modificare i seguenti filtri:

- **ID risorsa**: l'ID risorsa univoco che identifica la sottoscrizione di Azure. Se si effettua la sottoscrizione a più di un livello dell'API di ricerca Bing, l'elenco contiene più ID. Per impostazione predefinita, sono selezionate tutte le risorse.  
  
- **Mercati**: i mercati da cui provengono i risultati. Ad esempio, en-us (inglese, Stati Uniti). Per impostazione predefinita, sono selezionati tutti i mercati. Il `en-WW` mercato è il mercato utilizzato da Bing se la chiamata non specifica un mercato e Bing non è in grado di determinare il mercato dell'utente.  
  
- **Endpoint**: gli endpoint dell'API di ricerca Bing. L'elenco contiene tutti gli endpoint per cui si dispone di una sottoscrizione a pagamento. Per impostazione predefinita, sono selezionati tutti gli endpoint.  

- **Intervallo di tempo**: il periodo di reporting. È possibile specificare:
  - **Tutti**: Include dati fino a 13 mesi  
  - **Ultime 24 ore**: Include l'analisi delle ultime 24 ore  
  - **Settimana scorsa**: Include l'analisi dei sette giorni precedenti  
  - **Ultimo mese**: Include l'analisi dei 30 giorni precedenti  
  - **Un intervallo**di date personalizzato: include l'analisi dell'intervallo di date specificato, se disponibile  

## <a name="charts-and-graphs"></a>Grafici

Il dashboard mostra i grafici delle metriche disponibili per l'endpoint selezionato. Non tutte le metriche sono disponibili per tutti gli endpoint. I grafici per ogni endpoint sono statici (non è possibile selezionare i grafici da visualizzare). Il dashboard mostra solo i grafici per cui ci sono dati.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Di seguito sono riportate le possibili metriche e le restrizioni degli endpoint.

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

- **Distribuzione geografica**: I mercati in cui hanno origine i risultati della ricerca. Ad esempio, `en-us` (inglese, Stati Uniti). Bing usa il parametro di query `mkt` per determinare il mercato, se specificato. In caso contrario, Bing usa segnali come l'indirizzo IP del chiamante per determinare il mercato.

- **Distribuzione dei codici di risposta**: i codici di stato HTTP di tutte le chiamate durante il periodo di reporting.

- **Distribuzione dell'origine delle chiamate**: i tipi di browser usati dagli utenti. Ad esempio, Microsoft Edge, Chrome, Safari e FireFox. Le chiamate effettuate dall'esterno di un browser (ad esempio bot, Postman o l'uso di curl da un'app console) sono raggruppate in Librerie. L'origine viene determinata tramite il valore dell'intestazione agente utente della richiesta. Se la richiesta non include l'intestazione agente utente, Bing tenta di derivare l'origine da altri segnali.  

- **Distribuzione della ricerca sicura**: la distribuzione dei valori di ricerca sicura. Ad esempio, off, moderate o strict. Il parametro di query `safeSearch` contiene il valore, se specificato. In caso contrario, Bing definisce per impostazione predefinita il valore da moderare.  

- **Distribuzione richiesta risposte**: L'API di `responseFilter` ricerca Web risposte richieste nel parametro di query.  

- **Distribuzione delle risposte restituite**: le risposte che l'API di ricerca Web ha restituito nella risposta.

- **Distribuzione server di risposta**: il server applicazioni che ha fornito le risposte dell'API. I valori possibili sono Bing.com (per il traffico fornito da dispositivi desktop e portatili) e Bing.com-mobile (per il traffico fornito da dispositivi mobili). Il server viene determinato tramite il valore dell'intestazione agente utente della richiesta. Se la richiesta non include l'intestazione agente utente, Bing tenta di derivare il server da altri segnali.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle API di ricerca Bing](bing-api-comparison.md)
* [Requisiti per l'uso e la visualizzazione delle API di ricerca Bing](use-display-requirements.md)
