<properties
	pageTitle="Rete CDN - statistiche in tempo reale"
	description="Statistiche in tempo reale nella rete CDN di Microsoft Azure. Le statistiche in tempo reale forniscono i dati in tempo reale sulle prestazioni della rete CDN durante la distribuzione di contenuti ai client."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>

# Statistiche in tempo reale nella rete CDN di Microsoft Azure

## Panoramica

Questo documento illustra le statistiche in tempo reale nella rete CDN di Microsoft Azure. Questa funzionalità fornisce i dati in tempo reale sulle prestazioni della rete CDN durante la distribuzione di contenuti ai client.

> [AZURE.NOTE] Le statistiche in tempo reale sono una funzionalità del piano Premium della rete CDN. Per un confronto delle funzionalità della rete CDN Standard e Premium, vedere [Panoramica della rete CDN di Azure](cdn-overview.md).

Quando si visualizzano le statistiche in tempo reale per le piattaforme basate su HTTP, sono disponibili i grafici seguenti:

* [Larghezza di banda](#bandwidth)
* [Codici di stato](#status-codes)
* [Stati della cache](#cache-statuses)
* [Connessioni](#connections)

> [AZURE.NOTE] Ognuno dei grafici precedenti visualizza le statistiche in tempo reale per un determinato periodo di tempo. Una volta superato il tempo specificato, viene visualizzata una finestra temporale scorrevole di dati. Ciò significa che i dati precedenti verranno rimossi dal grafico per lasciare spazio ai nuovi dati. Il periodo di tempo per questa finestra temporale scorrevole può essere impostato dall'intervallo di tempo dell'opzione Graph.

## Accesso alle statistiche in tempo reale

1. Dal pannello del profilo della rete CDN fare clic sul pulsante **Gestisci**.

	![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)

	Si aprirà il portale di gestione della rete CDN.

2. Passare il puntatore sulla scheda **Analisi**, quindi sul riquadro a comparsa **Statistiche in tempo reale**. Fare clic su **Piattaforma HTTP grande**.

	Vengono visualizzate le opzioni dei report.

## Larghezza di banda

Il grafico della larghezza di banda visualizza la quantità di larghezza di banda utilizzata per la piattaforma corrente in un periodo di tempo specificato. La parte ombreggiata del grafico indica l'utilizzo della larghezza di banda. La quantità esatta di larghezza di banda attualmente in uso viene visualizzata direttamente sotto il grafico a linee.

> [AZURE.NOTE] Le unità utilizzate per effettuare i report dell’utilizzo della larghezza di banda sono una delle seguenti: bit al secondo (b/s), kilobit al secondo (Kb/s), megabit al secondo (Mb/s) o Gigabit al secondo (Gb/s).

## Codici di stato

Il grafico dei codici di stato è costituito da linee contraddistinte da colori che indicano la frequenza con cui i codici di risposta HTTP si verificano in un periodo di tempo specificato. Il lato sinistro del grafico (asse y) indica la frequenza con cui viene restituito un codice di stato per le richieste, mentre la parte inferiore del grafico (asse x) indica l'avanzamento del tempo.

Direttamente sopra al grafico viene visualizzato un elenco dei codici di stato. Questo elenco indica ogni codice di stato che può essere incluso nel grafico a linee e il numero di occorrenze correnti al secondo per ogni codice di stato. Per impostazione predefinita, viene visualizzata una riga per ognuno di questi codici di stato nel grafico. Tuttavia, è possibile scegliere di monitorare solo i codici di stato che hanno un significato speciale per la configurazione della rete CDN. Questo può essere eseguito solo contrassegnando le opzioni di codice di stato desiderate e cancellando tutte le altre opzioni. Dopo aver stabilito i codici di stato che verranno visualizzati nel grafico, è necessario fare clic su Aggiorna grafico. Questo impedisce che i codici di stato esclusi siano inclusi nel grafico.

> [AZURE.NOTE] L’opzione **Aggiorna grafico** cancellerà il grafico. Dopo questa operazione, verranno visualizzati solo i codici di stato selezionati.

Ogni opzione di codice di stato è descritta di seguito.

Nome | Descrizione
-----|------------
Totale riscontri al secondo | Determina se il numero totale di richieste che si verificano al secondo per la piattaforma corrente verrà visualizzato nel grafico. È possibile utilizzare questa opzione come un indicatore iniziale per visualizzare la percentuale di riscontri totali compresi da un codice di stato specifico.
2xx al secondo | Determina se il numero totale di codici di stato 2xx (ad esempio 200, 201, 202 e così via) che si verificano al secondo per la piattaforma corrente verrà visualizzato nel grafico. Questo tipo di codice di stato indica che la richiesta è stata recapitata correttamente al client.
304 al secondo | Determina se il numero totale di codici di stato 304 che si verificano al secondo per la piattaforma corrente verrà visualizzato nel grafico. Questo codice di stato indica che l’asset richiesto non è stato modificato dall'ultimo recupero dal client HTTP.
3xx al secondo | Determina se il numero totale di codici di stato 3xx (ad esempio 300, 301, 302 e così via) che si verificano al secondo per la piattaforma corrente verrà visualizzato nel grafico. Questa opzione consente di escludere le occorrenze dei codici di stato 304. Questo tipo di codice di stato indica che la richiesta ha determinato un reindirizzamento.
403 al secondo | Determina se il numero totale di codici di stato 403 che si verificano al secondo per la piattaforma corrente verrà visualizzato nel grafico. Questo codice di stato indica che la richiesta è stata considerata non autorizzata. Una delle possibili cause per questo codice di stato è la richiesta da parte di un utente non autorizzato di un asset protetto con l'autenticazione basata su Token.
404 al secondo | Determina se il numero totale di codici di stato 404 che si verificano al secondo per la piattaforma corrente verrà visualizzato nel grafico. Questo codice di stato indica che l’asset richiesto non è stato trovato.
4xx al secondo | Determina se il numero totale di codici di stato 4xx (ad esempio 400, 401, 402, 405 e così via) che si verificano al secondo per la piattaforma corrente verrà visualizzato nel grafico. Questa opzione consente di escludere le occorrenze dei codici di stato 403 e 404. Questo tipo di codice di stato indica che l’asset richiesto non è stato recapitato al client.
5xx al secondo | Determina se il numero totale di codici di stato 5xx (ad esempio 500, 501, 502 e così via) che si verificano al secondo per la piattaforma corrente verrà visualizzato nel grafico.
Altri al secondo | Determina se il totale di occorrenze per tutti gli altri codici di stato verrà segnalato nel grafico.

È inoltre possibile nascondere temporaneamente i dati registrati per un codice di stato specifico. È possibile farlo dall'area direttamente sotto al grafico deselezionando l'opzione di codice di stato desiderata. Il codice di stato selezionato verrà nascosto immediatamente dal grafico. Se si seleziona questa opzione di codice di stato tale opzione verrà visualizzata nuovamente.

> [AZURE.NOTE] Le opzioni contraddistinte da colori direttamente sotto al grafico vengono applicate solo a ciò che viene visualizzato nel grafico. Non influisce sul funzionamento del grafico che tiene traccia di tale codice di stato.

## Stati della cache

Il grafico degli stati della cache è costituito da linee contraddistinte da colori che indicano la frequenza con cui certi tipi di stati della cache si verificano in un periodo di tempo specificato. Il lato sinistro del grafico (asse y) indica la frequenza con cui viene restituito uno stato della cache per le richieste, mentre la parte inferiore del grafico (asse x) indica l'avanzamento del tempo.

Direttamente sopra al grafico viene visualizzato un elenco degli stati della cache. Questo elenco indica ogni stato della cache che può essere incluso nel grafico a linee e il numero di occorrenze correnti al secondo per ogni stato della cache. Per impostazione predefinita, viene visualizzata una riga per ognuno di questi stati della cache nel grafico. Tuttavia, è possibile scegliere di monitorare solo gli stati della cache che hanno un significato speciale per la configurazione della rete CDN. Questo può essere eseguito solo contrassegnando le opzioni di stato della cache desiderate e cancellando tutte le altre opzioni. Dopo aver stabilito gli stati della cache che verranno visualizzati nel grafico, è necessario fare clic su Aggiorna grafico. Questo impedisce che i codici di stato esclusi siano inclusi nel grafico.

> [AZURE.NOTE] L’opzione **Aggiorna grafico** cancellerà il grafico. Dopo questa operazione, verranno visualizzati solo gli stati della cache selezionati.

È inoltre possibile nascondere temporaneamente i dati registrati per un codice di risposta specifico. È possibile farlo dall'area direttamente sotto al grafico deselezionando l'opzione di codice di risposta desiderata. Il codice di risposta selezionato verrà nascosto immediatamente dal grafico. Se si seleziona questa opzione di codice di risposta tale opzione verrà visualizzata nuovamente.

> [AZURE.NOTE] Le opzioni contraddistinte da colori direttamente sotto al grafico vengono applicate solo a ciò che viene visualizzato nel grafico. Non influisce sul funzionamento del grafico che tiene traccia di tale codice di stato.

## Connessioni

Questa rappresentazione grafica del numero medio di connessioni al minuto consente di visualizzare il numero di connessioni ai server. Una connessione è costituita da ogni richiesta per un asset che passa attraverso la rete CDN.

## Vedere anche
* [Panoramica della rete CDN di Azure](cdn-overview.md)
* [Override del comportamento HTTP predefinito mediante il motore di regole](cdn-rules-engine.md)
* [Report HTTP avanzati](cdn-advanced-http-reports.md)
* [Analizzare delle prestazioni edge](cdn-edge-performance.md)

<!---HONumber=AcomDC_0302_2016-->