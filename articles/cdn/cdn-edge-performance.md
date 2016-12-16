---
title: Analizzare le prestazioni del server perimetrale nella rete CDN di Microsoft Azure | Microsoft Docs
description: Analizzare le prestazioni del nodo perimetrale nella rete CDN di Microsoft Azure. L&quot;analisi delle prestazioni perimetrali fornisce informazioni granulari sul traffico e sull&quot;utilizzo della larghezza di banda per la rete CDN.
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 61fe3f9419b5a6b15beb27d7b3f0782b404cd076


---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analizzare le prestazioni del nodo perimetrale nella rete CDN di Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Panoramica
L'analisi delle prestazioni perimetrali fornisce informazioni granulari sul traffico e sull'utilizzo della larghezza di banda per la rete CDN. Queste informazioni possono essere quindi usate per generare statistiche relative alle tendenze, che consentono di ottenere approfondimenti sul modo in cui gli asset vengono memorizzati nella cache e distribuiti ai client. Ciò consente quindi di definire una strategia per l'ottimizzazione della distribuzione dei contenuti e di determinare quali problemi è necessario affrontare per sfruttare al meglio le capacità della rete CDN. Oltre a migliorare le prestazioni della distribuzione dei dati, sarà quindi possibile ridurre anche i costi relativi alla rete CDN.

> [!NOTE]
> Tutti i report usano la notazione UTC/GMT quando specificano una data/ora.
> 
> 

## <a name="reports-and-log-collection"></a>Report e raccolta di log
I dati dell'attività della rete CDN devono essere raccolti dal modulo di analisi delle prestazioni perimetrali prima che sia possibile generare i report correlati. Questo processo di raccolta viene eseguito una volta al giorno e interessa le attività effettuate nella giornata precedente. Le statistiche di un report rappresentano quindi un campione delle statistiche della giornata nel momento in cui il report è stato elaborato e non contengono necessariamente un set di dati completo relativo alla giornata corrente. La funzione principale di questi report consiste nel valutare le prestazioni. Non devono essere usati per finalità relative alla fatturazione o per estrarre statistiche numeriche.

> [!NOTE]
> I dati non elaborati da cui vengono generati i report dell'analisi delle prestazioni perimetrali sono disponibili per almeno 90 giorni.
> 
> 

## <a name="dashboard"></a>Dashboard
Il dashboard relativo alle analisi delle prestazioni perimetrali tiene traccia del traffico corrente e cronologico della rete CDN tramite un grafico e le statistiche. Usare questo dashboard per rilevare le tendenze recenti e a lungo termine relative alle prestazioni del traffico della rete CDN per l'account specifico.

Il dashboard è costituito da:

* Un grafico interattivo che consente la visualizzazione delle metriche chiave e delle tendenze.
* Una sequenza temporale che fornisce indicazioni relative ai modelli a lungo termine per le metriche chiave e le tendenze.
* Metriche chiave e informazioni statistiche sul modo in cui la rete CDN migliora il traffico del sito, sulla base dei dati complessivi relativi a prestazioni, utilizzo ed efficienza.

### <a name="accessing-the-edge-performance-dashboard"></a>Accesso al dashboard relativo alle prestazioni perimetrali
1. Nel pannello relativo al profilo della rete CDN fare clic sul pulsante **Gestisci** .
   
    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.
2. Passare il puntatore sulla scheda **Analisi** e quindi sul riquadro a comparsa **Analisi delle prestazioni del server perimetrale**.  Fare clic su **Dashboard**.
   
    Verrà visualizzato il dashboard per le analisi relative al nodo perimetrale.

### <a name="chart"></a>Grafico
Il dashboard contiene un grafico che tiene traccia di una metrica nel periodo di tempo selezionato nella sequenza temporale visualizzata direttamente sotto il grafico.  Una sequenza temporale relativa agli ultimi due anni di attività della rete CDN viene visualizzata sotto il grafico.

#### <a name="using-the-chart"></a>Uso del grafico
* Per impostazione predefinita, verrà creato un grafico relativo al livello di efficienza della cache per gli ultimi 30 giorni.
* Questo grafico viene generato dai dati raccolti su base giornaliera.
* Se si passa il puntatore su un giorno nel grafico a linee, verranno indicati una data e il valore della metrica relativa a tale data.
* Fare clic sull'opzione relativa all'evidenziazione dei fine settimana per attivare o disattivare un overlay di barre verticali grigio chiaro che rappresentano i fine settimana sul grafico. Questo tipo di overlay è utile per identificare i modelli di traffico nei fine settimana.
* Fare clic sull'opzione per visualizzare l'anno precedente per attivare o disattivare un overlay delle attività dell'anno precedente per lo stesso periodo di tempo nel grafico. Questo tipo di confronto fornisce approfondimenti relativi ai modelli di utilizzo a lungo termine della rete CDN. L'angolo superiore destro del grafico include una legenda che indica il codice colore per ogni grafico a linee.

#### <a name="updating-the-chart"></a>Aggiornamento del grafico
* Intervallo di tempo: eseguire una delle operazioni seguenti.
  * Selezionare l'area desiderata nella sequenza temporale. Il grafico verrà aggiornato con i dati corrispondenti al periodo di tempo selezionato.
  * Fare doppio clic sul grafico per visualizzare tutti i dati cronologici disponibili, per un massimo di due anni.
* Metrica: fare clic sull'icona del grafico visualizzata accanto alla metrica desiderata. Il grafico e la sequenza temporale verranno aggiornati con i dati per la metrica corrispondente.

### <a name="key-metrics-and-statistics"></a>Metriche chiave e statistiche
#### <a name="efficiency-metrics"></a>Metriche relative all'efficienza
La finalità di queste metriche consiste nel verificare se è possibile migliorare l'efficienza della cache. Ecco i vantaggi principali derivanti dall'efficienza della cache:

* Riduzione del carico sul server di origine, che potrebbe permettere di ottenere:
  * Migliori prestazioni del server Web.
  * Costi operativi ridotti.
* Miglioramento dell'accelerazione della distribuzione dei dati, perché un numero maggiore di richieste verrà gestito direttamente dalla rete CDN.

| Campo | Descrizione |
| --- | --- |
| Efficienza della cache |Indica la percentuale dei dati trasferiti che è stata fornita dalla cache. Questa metrica indica quando una versione del contenuto richiesto memorizzata nella cache è stata fornita direttamente dalla rete CDN (server perimetrali) ai richiedenti (ad esempio un Web browser). |
| Percentuale riscontri |Indica la percentuale di richieste gestite dalla cache. Questa metrica indica quando una versione del contenuto richiesto memorizzata nella cache è stata fornita direttamente dalla rete CDN (server perimetrali) ai richiedenti (ad esempio un Web browser). |
| % di byte remoti - Nessuna cache configurata |Indica la percentuale di traffico fornita dai server di origine alla rete CDN (server perimetrali) che non verrà memorizzata nella cache come risultato della funzionalità di bypass della cache (motore delle regole HTTP). |
| % di byte remoti - Cache scaduta |Indica la percentuale di traffico fornita dai server di origine alla rete CDN (server perimetrali) come risultato di una riconvalida dei contenuti obsoleti. |

#### <a name="usage-metrics"></a>Metriche di utilizzo
Lo scopo di queste metriche consiste nel fornire approfondimenti nelle misure seguenti per la riduzione dei costi:

* Riduzione al minimo dei costi operativi tramite la rete CDN.
* Riduzione delle spese della rete CDN tramite l'efficienza e la compressione della cache.

> [!NOTE]
> I numeri relativi al volume del traffico rappresentano il traffico usato nei calcoli di rapporti e percentuali e può mostrare solo una parte del traffico totale per clienti a volume elevato.
> 
> 

| Campo | Descrizione |
| --- | --- |
| Media byte in uscita |Indica il numero medio di byte trasferiti per ogni richiesta fornita dalla rete CDN (server perimetrali) al richiedente (ad esempio un Web browser). |
| Velocità in byte senza cache configurata |Indica la percentuale di traffico fornita dalla rete CDN (server perimetrali) al richiedente (ad esempio un Web browser) che non verrà memorizzata nella cache a causa della funzionalità di bypass della cache. |
| Velocità in byte con compressione |Indica la percentuale di traffico inviato dalla rete CDN (server perimetrali) al richiedente (ad esempio un Web browser) in formato compresso. |
| Byte in uscita |Indica la quantità di dati, in byte, distribuiti dalla rete CDN (server perimetrali) al richiedente (ad esempio un Web browser). |
| Byte in entrata |Indica la quantità di dati, in byte, inviati dai richiedenti (ad esempio un Web browser) alla rete CDN (server perimetrali). |
| Byte remoti |Indica la quantità di dati, in byte, inviati dalla rete CDN e dai server di origine del cliente alla rete CDN (server perimetrali). |

#### <a name="performance-metrics"></a>Metriche delle prestazioni
Lo scopo di queste metriche consiste nel tenere traccia delle prestazioni complessive della rete CDN per il traffico.

| Campo | Descrizione |
| --- | --- |
| Velocità di trasferimento |Indica la velocità media del trasferimento del contenuto dalla rete CDN a un richiedente. |
| Durata |Indica il tempo medio, in millisecondi, necessario per distribuire un asset a un richiedente (ad esempio un Web browser). |
| Velocità richieste con compressione |Indica la percentuale di riscontri distribuiti dalla rete CDN (server perimetrali) al richiedente (ad esempio un Web browser) in formato compresso. |
| Frequenza degli errori 4xx |Indica la percentuale di riscontri che hanno generato un codice di stato 4xx. |
| Frequenza degli errori 5xx |Indica la percentuale di riscontri che hanno generato un codice di stato 5xx. |
| Riscontri |Indica il numero di richieste di contenuto della rete CDN. |

#### <a name="secure-traffic-metrics"></a>Metriche relative al traffico sicuro
Lo scopo di queste metriche consiste nel tenere traccia delle prestazioni della rete CDN per il traffico HTTPS.

| Campo | Descrizione |
| --- | --- |
| Efficienza della cache sicura |Indica la percentuale dei dati trasferiti per richieste HTTPS che sono state fornite dalla cache. Questa metrica indica quando una versione del contenuto richiesto memorizzata nella cache è stata fornita direttamente dalla rete CDN (server perimetrali) ai richiedenti (ad esempio un Web browser) su HTTPS. |
| Velocità di trasferimento sicuro |Indica la velocità media di trasferimento dei contenuti dalla rete CDN (server perimetrali) ai richiedenti (ad esempio server Web) su HTTPS. |
| Durata media sicura |Indica il tempo medio, in millisecondi, necessario per distribuire un asset a un richiedente (ad esempio un Web browser) su HTTPS. |
| Riscontri sicuri |Indica il numero di richieste HTTPS di contenuto della rete CDN. |
| Byte sicuri in uscita |Indica la quantità di traffico HTTPS, in byte, distribuita dalla rete CDN (server perimetrali) al richiedente (ad esempio un Web browser). |

## <a name="reports"></a>Report
Ogni report in questo modulo contiene un grafico e statistiche relative a larghezza di banda e utilizzo del traffico per diversi tipi di metriche, ad esempio codici di stato HTTP, codici di stato della cache, URL di richiesta e così via. Queste informazioni possono essere usate per ottenere informazioni approfondite sul modo in cui i contenuti vengono forniti ai client e per perfezionare il comportamento della rete CDN per migliorare le prestazioni della distribuzione dei dati.

### <a name="accessing-the-edge-performance-reports"></a>Accesso ai report relativi alle prestazioni perimetrali
1. Nel pannello relativo al profilo della rete CDN fare clic sul pulsante **Gestisci** .
   
    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.
2. Passare il puntatore sulla scheda **Analisi** e quindi sul riquadro a comparsa **Analisi delle prestazioni del server perimetrale**.  Fare clic su **Oggetto di grandi dimensioni HTTP**.
   
    Verrà visualizzata la schermata dei report sulle analisi relative al nodo perimetrale.

| Report | Descrizione |
| --- | --- |
| Riepilogo giornaliero |Consente di visualizzare le tendenza relative al traffico giornaliero per un periodo di tempo specificato. Ogni barra di questo grafico rappresenta una data specifica. Le dimensioni della barra indicano il numero totale di riscontri rilevati nella data specifica. |
| Riepilogo orario |Consente di visualizzare le tendenza relative al traffico orario per un periodo di tempo specificato. Ogni barra di questo grafico rappresenta una singola ora in una data specifica. Le dimensioni della barra indicano il numero totale di riscontri rilevati durante l'ora. |
| Protocolli |Mostra la suddivisione del traffico tra i protocolli HTTP e HTTPS. Un grafico ad anello indica la percentuale di riscontri rilevati per ogni tipo di protocollo. |
| Metodi HTTP |Consente di ottenere rapidamente una indicazione relativa ai metodi HTTP usati per richiedere i dati. In genere i metodi di richiesta HTTP più comuni sono GET, HEAD e POST. Un grafico ad anello indica la percentuale di riscontri rilevati per ogni tipo di metodo di richiesta HTTP. |
| URL |Contiene un grafico che mostra i primi 10 URL richiesti. Viene visualizzata una barra per ogni URL. L'altezza della barra indica il numero di riscontri generati dall'URL specifico nell'intervallo di tempo interessato dal report. Le statistiche relative ai primi 100 URL richiesti sono visualizzate direttamente sotto il grafico. |
| CNAME |Contiene un grafico che mostra i primi 10 CNAME usati per richiedere asset nel periodo di tempo specificato nel report. Le statistiche relative ai primi 100 CNAME richiesti sono visualizzate direttamente sotto il grafico. |
| Origini |Contiene un grafico che mostra i primi 10 server della rete CDN o server di origine dei clienti da cui sono stati richiesti asset nel periodo di tempo specificato. Le statistiche relative ai primi 100 server della rete CDN o server di origine dei clienti sono visualizzate direttamente sotto il grafico. I server di origine dei clienti vengono identificati dal nome definito nell'opzione relativa al nome della directory. |
| POP geografici |Mostra la quantità di traffico che viene indirizzata attraverso un Point of Presence (POP) specifico. L'abbreviazione di tre lettere rappresenta un POP nella rete CDN. |
| Client |Contiene un grafico che mostra i primi 10 client che hanno richiesto asset in un periodo di tempo specificato. Ai fini di questo report, tutte le richieste originate dallo stesso indirizzo IP vengono considerate come provenienti dallo stesso client. Le statistiche relative ai primi 100 client sono visualizzate direttamente sotto il grafico. Questo report è utile per determinare i modelli di attività di download per i client principali. |
| Stati della cache |Fornisce una suddivisione dettagliata del comportamento della cache, che potrebbe rivelare approcci per il miglioramento dell'esperienza utente finale complessiva. Poiché le prestazioni più veloci provengono dai riscontri nella cache, è possibile ottimizzare le velocità di recapito dei dati riducendo al minimo i mancati riscontri nella cache e i riscontri nella cache scaduti. |
| Dettagli NONE |Contiene un grafico che mostra i primi 10 URL per gli asset per cui il livello di aggiornamento dei contenuti della cache non è stato verificato per un periodo di tempo specificato. Le statistiche relative ai primi 100 URL per questi tipi di asset sono visualizzate direttamente sotto il grafico. |
| Dettagli CONFIG_NOCACHE |Contiene un grafico che mostra i primi 10 URL per asset non memorizzati nella cache a causa della configurazione della rete CDN del cliente. Questi tipi di asset sono stati forniti direttamente dal server di origine. Le statistiche relative ai primi 100 URL per questi tipi di asset sono visualizzate direttamente sotto il grafico. |
| Dettagli UNCACHEABLE |Contiene un grafico che mostra i primi 10 URL per asset non memorizzati nella cache a causa dei dati dell'intestazione della richiesta. Le statistiche relative ai primi 100 URL per questi tipi di asset sono visualizzate direttamente sotto il grafico. |
| Dettagli TCP_HIT |Contiene un grafico che mostra i primi 10 URL per asset che vengono forniti direttamente dalla cache. Le statistiche relative ai primi 100 URL per questi tipi di asset sono visualizzate direttamente sotto il grafico. |
| Dettagli TCP_MISS |Contiene un grafico che mostra i primi 10 URL per asset con stato della cache di tipo TCP_MISS. Le statistiche relative ai primi 100 URL per questi tipi di asset sono visualizzate direttamente sotto il grafico. |
| Dettagli TCP_EXPIRED_HIT |Contiene un grafico che mostra i primi 10 URL per asset obsoleti forniti direttamente dal POP. Le statistiche relative ai primi 100 URL per questi tipi di asset sono visualizzate direttamente sotto il grafico. |
| Dettagli TCP_EXPIRED_MISS |Contiene un grafico che mostra i primi 10 URL per asset obsoleti per cui è stato necessario recuperare una nuova versione dal server di origine. Le statistiche relative ai primi 100 URL per questi tipi di asset sono visualizzate direttamente sotto il grafico. |
| Dettagli TCP_CLIENT_REFRESH_MISS |Contiene un grafico a barre che mostra i primi 10 URL per asset recuperati da un server di origine a causa di una richiesta di tipo no cache da parte del client. Le statistiche relative ai primi 100 URL per questi tipi di richieste sono visualizzate direttamente sotto il grafico. |
| Tipi di richiesta client |Indica il tipo di richieste effettuate dai client HTTP, ad esempio i browser. Questo report include un grafico ad anello che fornisce indicazioni sul modo in cui vengono gestite le richieste. Le informazioni sulla larghezza di banda e sul traffico per ogni tipo di richiesta vengono visualizzate sotto il grafico. |
| Agente utente |Contiene un grafico a barre che mostra i primi 10 agenti utente che richiedono contenuti tramite la rete CDN. In genere un agente utente è un Web browser, un lettore multimediale o un browser di un telefono cellulare. Le statistiche per i primi 100 agenti utente sono visualizzate direttamente sotto il grafico. |
| Referrer |Contiene un grafico a barre che mostra i primi 10 referrer a contenuto a cui si accede tramite la rete CDN. In genere un referrer è l'URL della pagina Web o della risorsa che si collega al contenuto. Informazioni dettagliate sono disponibili sotto il grafico per i primi 100 referrer. |
| Tipi di compressione |Contiene un grafico ad anello che suddivide gli asset richiesti in base alla compressione o meno da parte dei server perimetrali. La percentuale di asset compressi viene suddivisa in base al tipo di compressione usata. Informazioni dettagliate sono disponibili sotto il grafico per ogni tipo e stato di compressione. |
| Tipi di file |Contiene un grafico a barre che mostra i primi 10 tipi di file richiesti tramite la rete CDN per l'account specifico. Ai fini di questo report, un tipo di file viene definito dall'estensione del nome file dell'asset e dal tipo di elemento multimediale Internet, ad esempio html \[testo/html\], .htm \[testo/html\], .aspx \[testo/html\], così via. Informazioni dettagliate sono disponibili sotto il grafico per i primi 100 tipi di file. |
| File univoci |Contiene un grafico che traccia il numero totale di asset univoci richiesti in un determinato giorno in un periodo di tempo specificato. |
| Riepilogo autenticazione con token |Contiene un grafico a torta che fornisce una rapida panoramica relativa alla protezione tramite autenticazione con token degli asset richiesti. Gli asset protetti vengono visualizzati nel grafico in base ai risultati dei rispettivi tentativi di autenticazione. |
| Dettagli di autenticazione con token rifiutata |Contiene un grafico a barre che consente di visualizzare le prime 10 richieste rifiutate a causa dell'autenticazione basata su token. |
| Codici di risposta HTTP |Fornisce una suddivisione dei codici di stato HTTP (ad esempio, 200 OK, 403 Accesso negato, 404 Non trovato e così via) recapitati ai client HTTP dai server perimetrali. Un grafico a torta consente di verificare rapidamente il modo in cui sono stati gestiti gli asset. Dati statistici dettagliati per ogni codice di risposta sono disponibili sotto il grafico. |
| Errori 404 |Contiene un grafico a barre che consente di visualizzare le prime 10 richieste che hanno avuto come risultato un codice di risposta di tipo 404 Non trovato. |
| Errori 403 |Contiene un grafico a barre che consente di visualizzare le prime 10 richieste che hanno avuto come risultato un codice di risposta di tipo 403 Accesso negato. Un codice di risposta di tipo 403 Accesso negato viene restituito quando una richiesta viene rifiutata da un server di origine del cliente o da un server perimetrale sul POP. |
| Errori 4xx |Contiene un grafico a barre che consente di visualizzare le prime 10 richieste che hanno avuto come risultato un codice di risposta incluso nell'intervallo 400. I codici di risposta di tipo 403 Accesso negato e 404 Non trovato sono esclusi da questo report. In genere, un codice di risposta di tipo 4xx viene restituito quando una richiesta viene rifiutata a causa di un errore del client. |
| Errori 504 |Contiene un grafico a barre che consente di visualizzare le prime 10 richieste che hanno avuto come risultato un codice di risposta di tipo 504 - Timeout gateway. Un codice di risposta di tipo 504 - Timeout gateway viene restituito quando si verifica un timeout durante un tentativo di comunicazione di un proxy HTTP con un altro server. Nel caso della rete CDN, un codice di risposta di tipo 504 - Timeout gateway viene in genere restituito quando un server perimetrale non riesce a stabilire una comunicazione con un server di origine del cliente. |
| Errori 502 |Contiene un grafico a barre che consente di visualizzare le prime 10 richieste che hanno restituito un codice di risposta di tipo 502 - Gateway non valido. Un codice di risposta di tipo 502 - Gateway non valido viene restituito in caso di errore del protocollo HTTP tra un server e un proxy HTTP. Nel caso della rete CDN, un codice di risposta di tipo 502 - Gateway non valido viene in genere restituito quando un server di origine del cliente restituisce una risposta non valida a un server perimetrale. Una risposta non è valida se non può essere analizzata o se è incompleta. |
| Errori 5xx |Contiene un grafico a barre che consente di visualizzare le prime 10 richieste che hanno avuto come risultato un codice di risposta incluso nell'intervallo 500.  I codici di risposta di tipo 502 - Gateway non valido e 504 - Timeout gateway sono esclusi da questo report. |

## <a name="see-also"></a>Vedere anche
* [Panoramica della rete CDN di Azure](cdn-overview.md)
* [Statistiche in tempo reale nella rete CDN di Microsoft Azure](cdn-real-time-stats.md)
* [Override del comportamento HTTP predefinito mediante il motore di regole](cdn-rules-engine.md)
* [Report HTTP avanzati](cdn-advanced-http-reports.md)




<!--HONumber=Nov16_HO3-->


