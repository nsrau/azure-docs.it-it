<properties
	pageTitle="CDN - Report HTTP avanzati"
	description="Report HTTP avanzati nella rete CDN di Microsoft Azure. Questi report forniscono informazioni dettagliate sull'attività della rete CDN."
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

# Report HTTP avanzati nella rete CDN di Microsoft Azure

## Panoramica

Questo documento illustra la creazione di report HTTP avanzati nella rete CDN di Microsoft Azure. Questi report forniscono informazioni dettagliate sull'attività della rete CDN.

> [AZURE.NOTE] I report HTTP avanzati sono una funzionalità del piano Premium per la rete CDN. Per un confronto tra le funzionalità del livello Standard e del livello Premium della rete CDN, vedere [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](cdn-overview.md).

## Accesso ai report HTTP avanzati

1. Nel pannello relativo al profilo della rete CDN fare clic sul pulsante **Gestisci**.

	![Pulsante Gestisci del pannello relativo al profilo della rete CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)

	Verrà aperto il portale di gestione della rete CDN.

2. Passare il puntatore sulla scheda **Analisi**, quindi sul riquadro a comparsa **Report HTTP avanzati**. Fare clic su **Piattaforma HTTP grande**.

	Vengono visualizzate le opzioni dei report.

## Report geografici (basati su mappe)

Esistono cinque report che sfruttano una mappa per indicare le aree da cui il contenuto viene richiesto. Questi report sono Mappa del mondo, Mappa Stati Uniti, Mappa Canada, Mappa Europa e Mappa Asia Pacifico.

Ogni report basato su mappa classifica le entità geografiche (ad esempio, paesi, stati e province) in base alla percentuale di accessi originati dall'area specifica. Una mappa consente anche di visualizzare le posizioni da cui il contenuto viene richiesto. A questo scopo, contraddistingue con il colore ogni area in base alla quantità di domanda proveniente da tale area. Le aree con una sfumatura più chiara indicano una domanda minore di contenuto, mentre le aree più scure indicano livelli più alti di domanda di contenuto.

Proprio sotto la mappa vengono fornite informazioni dettagliate sul traffico e sulla larghezza di banda per ogni area. In questo modo è possibile visualizzare il numero totale di riscontri, la percentuale di riscontri, la quantità totale di dati trasferiti (in gigabyte) e la percentuale di dati trasferiti per ogni area. Visualizzare una descrizione per ogni metrica. Infine, quando si passa il puntatore su un'area (ad esempio, un paese, uno stato o una provincia), il nome e la percentuale di riscontri che si sono verificati nell'area verranno visualizzati come descrizione comando.

Di seguito viene fornita una breve descrizione di ogni tipo di report geografico basato su mappa.

Nome report | Descrizione
------------|------------
Mappa del mondo | Questo report consente di visualizzare la domanda mondiale del contenuto della rete CDN. Sulla mappa del mondo ogni paese è contraddistinto dal colore per indicare la percentuale di riscontri originati da tale area.
Mappa Stati Uniti | Questo report consente di visualizzare la domanda del contenuto della rete CDN negli Stati Uniti. Su questa mappa ogni stato è contraddistinto dal colore per indicare la percentuale di riscontri originati da tale area.
Mappa Canada | Questo report consente di visualizzare la domanda del contenuto della rete CDN in Canada. Su questa mappa ogni provincia è contraddistinta dal colore per indicare la percentuale di riscontri originati da tale area.
Mappa Europa | Questo report consente di visualizzare la domanda del contenuto della rete CDN in Europa. Su questa mappa ogni paese è contraddistinto dal colore per indicare la percentuale di riscontri originati da tale area.
Mappa Asia Pacifico | Questo report consente di visualizzare la domanda del contenuto della rete CDN in Asia. Su questa mappa ogni paese è contraddistinto dal colore per indicare la percentuale di riscontri originati da tale area.

## Report geografici (grafici a barre)

Esistono altri due report che forniscono informazioni statistiche basate sulla geografia, Città principali e Paesi principali. Questi report classificano rispettivamente città e paesi, in base al numero di riscontri originati da queste aree. Immediatamente dopo la generazione di questo tipo di report, un grafico a barre indicherà le prime 10 città o i primi 10 paesi che hanno richiesto il contenuto su una piattaforma specifica. Questo grafico a barre consente di valutare rapidamente le aree che generano il numero più alto di richieste per il contenuto.

Il lato sinistro del grafico (asse y) indica quanti riscontri si sono verificati nell'area specificata. Proprio sotto il grafico (asse x) verrà visualizzata un'etichetta per ognuna delle prime 10 aree.

### Uso dei grafici a barre

* Se si passa il puntatore su una barra, il nome e il numero totale di riscontri che si sono verificati nell'area verrà visualizzato come descrizione comando.
* La descrizione comando per il report Città principali identifica una città con nome, stato/provincia e abbreviazione del paese.
* Se la città o l'area (ad esempio, stato/provincia) da cui ha avuto origine una richiesta non può essere determinata, verrà indicata come sconosciuta. Se il paese è sconosciuto, verranno visualizzati due punti di domanda (vale a dire, ??).
* Un report può includere le metriche per l'area "Europa" o "Asia/Pacifico". Tali elementi non servono a offrire informazioni statistiche su tutti gli indirizzi IP in tali aree, ma si applicano solo alle richieste originate dagli indirizzi IP distribuiti in Europa o in Asia/Pacifico invece che in una città o in un paese specifico.

I dati usati per generare il grafico a barre possono essere visualizzati sotto il grafico stesso, dove saranno indicati il numero totale di riscontri, la percentuale di riscontri, la quantità di dati trasferiti (in gigabyte) e la percentuale di dati trasferiti per le prime 250 aree. Visualizzare una descrizione per ogni metrica.

Per entrambi i tipi di report seguenti viene fornita una breve descrizione.

Nome report | Descrizione
------------|------------
Città principali | Questo report classifica le città in base al numero di riscontri originati da una determinata area.
Paesi principali | Questo report classifica i paesi in base al numero di riscontri originati da una determinata area.

## Riepilogo giornaliero

Il report Riepilogo giornaliero consente di visualizzare il numero totale di riscontri e i dati trasferiti ogni giorno su una determinata piattaforma. Queste informazioni possono essere usate per individuare velocemente gli schemi di attività della rete CDN. Questo report, ad esempio, consente di rilevare in quali giorni il traffico è stato superiore o inferiore al previsto.

Immediatamente dopo la generazione di questo tipo di report, un grafico a barre offrirà un'indicazione visiva della quantità di domanda specifica della piattaforma generata ogni giorno durante il periodo di tempo coperto dal report. A questo scopo, nel report verrà visualizzata una barra per ogni giorno. Ad esempio, selezionando il periodo di tempo "Ultima settimana", verrà generato un grafico con sette barre. Ogni barra indicherà il numero totale di riscontri rilevati nel giorno corrispondente.

Il lato sinistro del grafico (asse y) indica quanti riscontri si sono verificati nella data specificata. Proprio sotto il grafico (asse x) verrà visualizzata un'etichetta indicante la data (formato: AAAA-MM-GG) per ogni giorno incluso nel report.

> [AZURE.TIP] Se si passa il puntatore su una barra, il numero totale di riscontri che si sono verificati in tale data verrà visualizzato come descrizione comando.

I dati usati per generare il grafico a barre possono essere visualizzati sotto il grafico stesso, dove saranno indicati il numero totale di riscontri e la quantità di dati trasferiti (in gigabyte) per ogni giorno considerato dal report.

## Per ora

Il report Per ora consente di visualizzare il numero totale di riscontri e i dati trasferiti ogni ora su una determinata piattaforma. Queste informazioni possono essere usate per individuare velocemente gli schemi di attività della rete CDN. Questo report, ad esempio, consente di rilevare i momenti del giorno durante i quali il traffico è superiore o inferiore al previsto.

Immediatamente dopo la generazione di questo tipo di report, un grafico a barre offrirà un'indicazione visiva della quantità di domanda specifica della piattaforma generata ogni ora durante il periodo di tempo coperto dal report. A questo scopo, nel report verrà visualizzata una barra per ogni ora considerata dal report. Se, ad esempio, si seleziona un periodo di tempo di 24 ore, verrà generato un grafico con ventiquattro barre. Ogni barra indicherà il numero totale di riscontri rilevati durante l'ora corrispondente.

Il lato sinistro del grafico (asse y) indica quanti riscontri si sono verificati nell'ora specificata. Proprio sotto il grafico (asse x) verrà visualizzata un'etichetta indicante la data/ora (formato: AAAA-MM-GG hh:mm) per ogni ora inclusa nel report. L'ora viene indicata con il formato di 24 ore e specificata con il fuso orario UTC/GMT.

> [AZURE.TIP] Se si passa il puntatore su una barra, il numero totale di riscontri che si sono verificati durante tale ora verrà visualizzato come descrizione comando.

I dati usati per generare il grafico a barre possono essere visualizzati sotto il grafico stesso, dove saranno indicati il numero totale di riscontri e la quantità di dati trasferiti (in gigabyte) per ogni ora considerata dal report.

## Per file

Il report Per file consente di visualizzare la quantità di domanda e il traffico generato da una determinata piattaforma per la maggior parte degli asset richiesti. Immediatamente dopo la generazione di questo tipo di report, verrà generato un grafico a barre sui 10 asset più richiesti nel periodo di tempo specificato.

> [AZURE.NOTE] Ai fini di questo report, gli URL CNAME perimetrali vengono convertiti negli equivalenti URL CDN. Questo consente un calcolo preciso del numero totale di riscontri associati a un asset indipendentemente dagli URL CNAME perimetrali o CDN usati per richiederlo.

Il lato sinistro del grafico (asse y) indica il numero di richieste per ogni asset nel periodo di tempo specificato. Proprio sotto il grafico (asse x) verrà visualizzata un'etichetta indicante il nome file per ognuno dei primi 10 asset richiesti.

I dati usati per generare il grafico a barre possono essere visualizzati sotto il grafico stesso, dove saranno indicate le informazioni seguenti per ognuno dei primi 250 asset richiesti: il percorso relativo, il numero totale di riscontri, la percentuale di riscontri, la quantità di dati trasferiti (in gigabyte) e la percentuale di dati trasferiti.

## Per dettaglio file

Il report Per dettaglio file consente di visualizzare la quantità di domanda e il traffico generato su una determinata piattaforma per un asset specifico. All'inizio di questo report si trova l'opzione Dettagli file per. Questa opzione fornisce un elenco degli asset più richiesti sulla piattaforma selezionata. Per generare un report Per dettaglio file, sarà necessario selezionare l'asset desiderato dall'opzione Dettagli file per. Un grafico a barre indicherà quindi la quantità di domanda giornaliera generata nel periodo di tempo specificato.

Il lato sinistro del grafico (asse y) indica il numero totale di richieste per un asset in un giorno specifico. Proprio sotto il grafico (asse x) verrà visualizzata un'etichetta indicante la data (formato: AAAA-MM-GG) per cui è stata inserita nel report la domanda della rete CDN per l'asset.

I dati usati per generare il grafico a barre possono essere visualizzati sotto il grafico stesso, dove saranno indicati il numero totale di riscontri e la quantità di dati trasferiti (in gigabyte) per ogni giorno considerato dal report.

## Per tipo di file

Il report Per tipo di file consente di visualizzare la quantità di domanda e il traffico generato in base al tipo di file. Immediatamente dopo la generazione di questo tipo di report, un grafico ad anello indicherà la percentuale di riscontri generati dai primi 10 tipi di file.

> [AZURE.TIP] Se si passa il puntatore su una sezione del grafico ad anello, il tipo di contenuto multimediale Internet di tale tipo di file verrà visualizzato come descrizione comando.

I dati usati per generare il grafico ad anello possono essere visualizzati sotto il grafico stesso, dove saranno indicati il tipo di contenuto multimediale Internet o l'estensione di file, il numero totale di riscontri, la percentuale di riscontri, la quantità di dati trasferiti (in gigabyte) e la percentuale di dati trasferiti per ognuno dei primi 250 tipi di file.

## Per directory

Il report Per directory consente di visualizzare la quantità di domanda e il traffico generato su una determinata piattaforma per il contenuto di una directory specifica. Immediatamente dopo la generazione di questo tipo di report, un grafico a barre indicherà il numero totale di riscontri generati dal contenuto nelle prime 10 directory.

### Uso del grafico a barre

* Passare il puntatore su una barra per visualizzare il percorso relativo della directory corrispondente.
* Il contenuto archiviato in una sottocartella di una directory non viene considerato durante il calcolo della domanda per directory. Questo calcolo si basa esclusivamente sul numero di richieste generate per il contenuto archiviato nella directory vera e propria.
* Ai fini di questo report, gli URL CNAME perimetrali vengono convertiti negli equivalenti URL CDN. Questo consente un calcolo preciso di tutte le statistiche associate a un asset indipendentemente dagli URL CNAME perimetrali o CDN usati per richiederlo.

Il lato sinistro del grafico (asse y) indica il numero totale di richieste per il contenuto archiviato nelle prime 10 directory. Ogni barra del grafico rappresenta una directory. Usare lo schema di codifica a colori per associare una barra a una directory elencata nella sezione Prime 250 directory complete.

I dati usati per generare il grafico a barre possono essere visualizzati sotto il grafico stesso, dove saranno indicate le informazioni seguenti per ognuna delle prime 250 directory: il percorso relativo, il numero totale di riscontri, la percentuale di riscontri, la quantità di dati trasferiti (in gigabyte) e la percentuale di dati trasferiti.

## Per browser

Il report Per browser consente di visualizzare i browser usati per richiedere il contenuto. Immediatamente dopo la generazione di questo tipo di report, un grafico a torta indicherà la percentuale di richieste gestite dai primi 10 browser.

### Uso del grafico a torta

* Passare il puntatore su una sezione del grafico a torta per visualizzare il nome e la versione di un browser.
* Ai fini di questo report, ogni combinazione univoca di browser/versione viene considerata un browser distinto.
* La sezione "Altro" indica la percentuale di richieste gestite da tutti gli altri browser e versioni.

I dati usati per generare il grafico a torta possono essere visualizzati sotto il grafico stesso, dove saranno indicati il tipo di browser/numero di versione, il numero totale di riscontri e la percentuale di riscontri per ognuno dei primi 250 browser.

## Per referrer

Il report Per referrer consente di visualizzare i principali referrer al contenuto sulla piattaforma selezionata. Un referrer indica il nome host da cui è stata generata una richiesta. Immediatamente dopo la generazione di questo tipo di report, un grafico a barre indicherà la quantità di domanda (ad esempio, riscontri) generata dai primi 10 referrer.

Il lato sinistro del grafico (asse y) indica il numero totale di richieste per un asset per ogni referrer. Ogni barra del grafico rappresenta un referrer. Usare lo schema di codifica a colori per associare una barra a un referrer elencato nella sezione Primi 250 referrer.

I dati usati per generare il grafico a barre possono essere visualizzati sotto il grafico stesso, dove saranno indicati l'URL, il numero totale di riscontri e la percentuale di riscontri generati da ognuno dei primi 250 referrer.

## Per download

Il report Per download consente di analizzare i modelli di download per il contenuto più richiesto. La parte iniziale del report contiene un grafico a barre che confronta i tentativi di download con i download completati per i primi 10 asset richiesti. Ogni barra è contraddistinta dal colore per indicare se si tratta di un tentativo di download (blu) o di un download completato (verde).

> [AZURE.NOTE] Ai fini di questo report, gli URL CNAME perimetrali vengono convertiti negli equivalenti URL CDN. Questo consente un calcolo preciso di tutte le statistiche associate a un asset indipendentemente dagli URL CNAME perimetrali o CDN usati per richiederlo.

Il lato sinistro del grafico (asse y) indica il nome file per ognuno dei primi 10 asset richiesti. Proprio sotto il grafico (asse x) verranno visualizzate le etichette indicanti il numero totale di download tentati/completati.

Proprio sotto il grafico a barre verranno elencate le informazioni seguenti per i primi 250 asset richiesti: il percorso relativo (incluso il nome file), il numero di volte in cui un asset è stato scaricato completamente, il numero di volte in cui è stato richiesto e la percentuale di richieste che hanno restituito un download completo.

> [AZURE.TIP] La rete CDN non viene informata da un client HTTP (ad esempio, un browser) quando un asset è stato scaricato completamente. Di conseguenza, è necessario calcolare se un asset è stato completamente scaricato in base ai codici di stato e alle richieste di intervallo di byte. Quando si esegue questo calcolo, prima di tutto è necessario verificare se la richiesta restituisce un codice di stato 200 OK. In questo caso, si esaminano le richieste di intervallo di byte per assicurarsi che coprano l'intero asset. Infine si confronta la quantità di dati trasferiti con le dimensioni dell'asset richiesto. Se i dati trasferiti sono uguali o maggiori alle dimensioni del file e le richieste di intervallo di byte sono appropriate per l'asset, il riscontro verrà calcolato come un download completo.
>
>Data la necessità di interpretare questo report, tenere in considerazione i fattori seguenti che possono influire sulla coerenza e sull'accuratezza del report.
>
>* I modelli di traffico non possono essere previsti con precisione quando gli agenti utente si comportano in modo diverso. Ciò può generare risultati di download completato superiori al 100%.
>* È possibile che gli asset che sfruttano il download progressivo HTTP non vengano rappresentati accuratamente da questo report perché gli utenti cercano posizioni diverse in un video.

## Per errori 404

Il report Per errori 404 consente di identificare il tipo di contenuto che genera il maggior numero di codici di stato 404 Non trovato. La parte iniziale del report contiene un grafico a barre per i primi 10 asset per cui è stato restituito un codice di stato 404 Non trovato. Questo grafico a barre confronta il numero totale di richieste con le richieste che hanno restituito un codice di stato 404 Non trovato per tali asset. Ogni barra è contraddistinta dal colore. Le barre gialle indicano che le richieste hanno restituito un codice di stato 404 Non trovato. Le barre rosse indicano il numero totale di richieste per l'asset.

> [AZURE.NOTE] Ai fini di questo report, tenere presente quanto segue:
>
>* Un riscontro rappresenta una richiesta per un asset indipendentemente dal codice di stato.
>* Gli URL CNAME perimetrali vengono convertiti negli equivalenti URL CDN. Questo consente un calcolo preciso di tutte le statistiche associate a un asset indipendentemente dagli URL CNAME perimetrali o CDN usati per richiederlo.

Il lato sinistro del grafico (asse y) indica il nome file per ognuno dei primi 10 asset richiesti che hanno restituito un codice di stato 404 Non trovato. Proprio sotto il grafico (asse x) verranno visualizzate le etichette indicanti il numero totale di richieste e il numero di richieste che hanno restituito un codice di stato 404 Non trovato.

Proprio sotto il grafico a barre verranno elencate le informazioni seguenti per i primi 250 asset richiesti: il percorso relativo (incluso il nome file), il numero di richieste che hanno restituito un codice di stato 404 Non trovato, il numero totale di volte in cui l'asset è stato richiesto e la percentuale di richieste che hanno restituito un codice di stato 404 Non trovato.

## Vedere anche
* [Panoramica della rete CDN di Azure](cdn-overview.md)
* [Statistiche in tempo reale nella rete CDN di Microsoft Azure](cdn-real-time-stats.md)
* [Override del comportamento HTTP predefinito mediante il motore di regole](cdn-rules-engine.md)
* [Analizzare le prestazioni perimetrali](cdn-edge-performance.md)

<!---HONumber=AcomDC_0302_2016-->