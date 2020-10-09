---
title: Strumento di ispezione ArrInspector
description: Manuale dell'utente dello strumento ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80680076"
---
# <a name="the-arrinspector-inspection-tool"></a>Strumento di ispezione ArrInspector

ArrInspector è uno strumento basato sul Web usato per esaminare una sessione di rendering remota di Azure in esecuzione. È concepito per essere usato a scopo di debug, per controllare la struttura della scena sottoposta a rendering, visualizzare i messaggi di log e monitorare le prestazioni in tempo reale sul server.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Connessione a ArrInspector

Una volta ottenuto il nome host (che termina con `mixedreality.azure.com` ) del server ARR, connettersi usando [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Questa funzione crea un oggetto `StartArrInspector.html` nel dispositivo in cui è in esecuzione l'applicazione. Per avviare ArrInspector, aprire il file con un browser (Edge, Firefox o Chrome) in un PC. Il file è valido solo per 24 ore.

Se l'app che chiama `ConnectToArrInspectorAsync` è già in esecuzione in un computer:

* Se si usa l'integrazione di Unity, è possibile che venga avviata automaticamente.
* In caso contrario, sarà possibile trovare il file in *cartelle utente \\ LocalAppData \\ [your_app] \\ AC \\ Temp*.

Se l'app è in esecuzione in un HoloLens:

1. Accedere a HoloLens tramite il [portale del dispositivo Windows](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal).
1. Passare a *System > file Explorer*.
1. Passare a *cartelle utente \\ LocalAppData \\ [your_app] \\ \\ Temp AC*.
1. Salvare *StartArrInspector.html* nel PC.
1. Aprire *StartArrInspector.html* per caricare il ArrInspector della sessione.

## <a name="the-performance-panel"></a>Pannello prestazioni

![Pannello prestazioni](./media/performance-panel.png)

Questo pannello mostra i grafici di tutti i valori delle prestazioni per fotogramma esposti dal server. I valori attualmente includono tempo di frame, FPS, utilizzo della CPU e della memoria, statistiche della memoria come utilizzo complessivo della RAM, conteggi degli oggetti e così via.

Per visualizzare uno di questi parametri, fare clic sul pulsante **Aggiungi nuovo** e selezionare uno dei valori disponibili visualizzati nella finestra di dialogo. Questa azione aggiunge un nuovo grafico a scorrimento al pannello, tracciando i valori in tempo reale. A destra è possibile visualizzare il valore *minimo*, *massimo* e *corrente* .

È possibile fare il panning del grafico, trascinando il relativo contenuto con il mouse, tuttavia, il panning orizzontale è possibile solo quando ArrInspector è in stato di sospensione.

Tenendo premuto CTRL durante il trascinamento, è possibile eseguire lo zoom. È anche possibile controllare lo zoom orizzontale con il dispositivo di scorrimento in basso.

Per impostazione predefinita, l'intervallo verticale viene calcolato in base ai valori attualmente visualizzati e i valori min e Max vengono visualizzati nelle caselle di testo a destra. Quando i valori vengono impostati manualmente, digitando direttamente nella casella di testo o eseguendo il panning o lo zoom, il grafo utilizzerà tali valori. Per ripristinare la cornice verticale automatica, fare clic sull'icona nell'angolo in alto a destra.

![intervallo verticale](./media/vertical-range.png)

## <a name="the-log-panel"></a>Pannello log

![Pannello log](./media/log-panel.png)

Il pannello log mostra un elenco di messaggi di log generati sul lato server. Per la connessione verrà visualizzato un massimo di 200 messaggi di log precedenti e verranno stampati quelli nuovi appena si verificano.

È possibile filtrare l'elenco in base al tipo di log `[Error/Warning/Info/Debug]` usando i pulsanti nella parte superiore.
![Pulsanti filtro log](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Pannello di acquisizione dati temporizzato

![Acquisizione dei dati di temporizzazione](./media/timing-data-capture.png)

Questo pannello viene usato per acquisire le informazioni di temporizzazione dal server e scaricarle. Il file usa il [formato JSON della traccia di Chrome](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Per esaminare i dati, aprire Chrome sull'URL `Chrome://tracing` e trascinare il file scaricato nella pagina. I dati di temporizzazione vengono raccolti continuamente in un buffer circolare a dimensione fissa. Quando viene scritto, l'acquisizione include solo informazioni sul passato immediato, vale a dire un paio di secondi per pochi minuti.

## <a name="the-scene-inspection-panel"></a>Pannello di ispezione della scena

![Pannello di ispezione della scena](./media/scene-inspection-panel.png)

Questo pannello mostra la struttura della scena di cui è stato eseguito il rendering. La gerarchia di oggetti si trova a sinistra, il contenuto dell'oggetto selezionato è a destra. Il pannello è di sola lettura e viene aggiornato in tempo reale.

## <a name="the-vm-debug-information-panel"></a>Pannello informazioni di debug della macchina virtuale

![Pannello informazioni di debug della macchina virtuale](./media/state-debugger-panel.png)

Questo pannello offre alcune funzionalità di debug.

### <a name="restart-service"></a>Riavvio del servizio

Il pulsante **Riavvia servizio riavvia** il runtime nella macchina virtuale a cui è connesso arrInspector. Eventuali client collegati verranno disconnessi e sarà necessario ricaricare la pagina arrInspector per connettersi al servizio riavviato.

### <a name="collect-debug-information"></a>Raccogli informazioni di debug

Il pulsante **Raccogli informazioni di debug per macchina virtuale** apre una finestra di dialogo che consente di attivare l'istanza ARR per raccogliere informazioni di debug nella macchina virtuale:

![Finestra di dialogo informazioni di debug VM](./media/state-debugger-dialog.png)

Le informazioni di debug consentono al team di rendering remoto di Azure di analizzare gli eventuali problemi che si verificano in un'istanza ARR in esecuzione. La finestra di dialogo contiene un campo di testo per fornire dettagli aggiuntivi, ad esempio i passaggi per riprodurre un problema.

Dopo aver fatto clic sul pulsante **Avvia raccolta** , la finestra di dialogo viene chiusa e viene avviato il processo di raccolta. La raccolta delle informazioni nella macchina virtuale può richiedere alcuni minuti.

![Raccolta di informazioni di debug della macchina virtuale in corso](./media/state-debugger-panel-in-progress.png)

Al termine della raccolta, verrà visualizzata una notifica nella finestra ArrInspector. Questa notifica contiene un ID che identifica questa raccolta specifica. Assicurarsi di salvare questo ID per passarlo al team di rendering remoto di Azure.

![Raccolta informazioni di debug VM riuscita](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Non è possibile scaricare o accedere in altro modo alle informazioni di debug della macchina virtuale. Solo il team di rendering remoto di Azure può accedere ai dati raccolti. È necessario contattarci e inviare l'ID della raccolta per esaminare il problema che si sta osservando.

## <a name="pause-mode"></a>Modalità di sospensione

Nell'angolo superiore destro, un'opzione consente di sospendere l'aggiornamento in tempo reale dei pannelli. Questa modalità può essere utile per esaminare attentamente uno stato specifico.

![Modalità di sospensione](./media/pause-mode.png)

Quando si riattiva l'aggiornamento in tempo reale, tutti i pannelli vengono reimpostati.

## <a name="host-configuration"></a>Configurazione dell'host

Per impostazione predefinita, lo strumento si connette al server ARR in esecuzione nello stesso host che funge da ArrInspector. Tuttavia, è possibile configurarlo in modo da ispezionare un altro server, supponendo che sia in esecuzione un'istanza ARR con la porta degli strumenti aperta.

A tale scopo, accedere al menu principale a sinistra della barra di intestazione e selezionare *Configurazione host*. Fare clic su **Aggiungi nuovo host**e immettere il nome e il nome host. Per *hostname* usare solo il nome host che termina con `.mixedreality.azure.com` , non includere `http://` o una porta.

![Configurazione dell'host](./media/host-configuration.png)

Per passare rapidamente da un host a un altro, usare l'elenco a discesa in alto a destra.

![Casella combinata host](./media/host-switch-combo.png)

L'elenco host viene archiviato nella risorsa di archiviazione locale del browser, quindi verrà mantenuto quando si riapre lo stesso browser.
