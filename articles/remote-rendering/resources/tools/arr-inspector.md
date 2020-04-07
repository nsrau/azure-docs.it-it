---
title: Lo strumento di ispezione ArrInspector
description: Manuale utente dello strumento ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680076"
---
# <a name="the-arrinspector-inspection-tool"></a>Lo strumento di ispezione ArrInspector

ArrInspector è uno strumento basato sul Web usato per esaminare una sessione di rendering remoto di Azure in esecuzione. È progettato per essere utilizzato per scopi di debug, per controllare la struttura della scena di cui viene eseguito il rendering, visualizzare i messaggi di log e monitorare le prestazioni in tempo reale sul server.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Connessione ad ArrInspector

Una volta ottenuto il `mixedreality.azure.com`nome host (che termina con ) del server ARR, connettersi utilizzando [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Questa funzione `StartArrInspector.html` crea un nel dispositivo in cui è in esecuzione l'applicazione. Per avviare ArrInspector, apri il file con un browser (Edge, Firefox o Chrome) su un PC. Il file è valido solo per 24 ore.

Se l'app `ConnectToArrInspectorAsync` che chiama è già in esecuzione su un PC:

* Se si utilizza l'integrazione Unity, potrebbe essere avviata automaticamente.
* In caso contrario, il file verrà trovato in *Cartelle\\utente LocalAppData\\[your_app]\\Temp AC\\*.

Se l'app è in esecuzione in un HoloLens:

1. Accedere a HoloLens utilizzando il [portale dei dispositivi Windows](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal).
1. Passare a *Esplora file > di sistema*.
1. Passare a *\\Cartelle utente\\LocalAppData [your_app]\\\\Temp AC*.
1. Salva *StartArrInspector.html* nel PC.
1. Aprire *StartArrInspector.html* per caricare ArrInspector della sessione.

## <a name="the-performance-panel"></a>Il pannello Prestazioni

![Il pannello Prestazioni](./media/performance-panel.png)

Questo pannello mostra i grafici di tutti i valori delle prestazioni per frame esposti dal server. I valori attualmente includono il tempo di fotogramma, FPS, utilizzo della CPU e della memoria, statistiche di memoria come l'utilizzo complessivo della RAM, il numero di oggetti, ecc.

Per visualizzare uno di questi parametri, fare clic sul pulsante **Aggiungi nuovo** e selezionare uno dei valori disponibili visualizzati nella finestra di dialogo. Questa azione aggiunge un nuovo grafico a scorrimento al pannello, tracciando i valori in tempo reale. Sulla sua destra si può vedere il valore *minimo,* *massimo* e *corrente.*

È possibile eseguire la panoramica del grafico, trascinandone il contenuto con il mouse, tuttavia la panoramica orizzontale è possibile solo quando ArrInspector è in stato di pausa.

Tenendo premuto CTRL durante il trascinamento, è possibile eseguire lo zoom. Lo zoom orizzontale può anche essere controllato con il cursore in basso.

L'intervallo verticale viene calcolato per impostazione predefinita in base ai valori attualmente visualizzati e i valori min e max vengono visualizzati nelle caselle di testo a destra. Quando i valori vengono impostati manualmente, digitandoli direttamente nella casella di testo o eseguendo la panoramica o lo zoom, il grafico utilizzerà tali valori. Per ripristinare l'inquadratura verticale automatica, fare clic sull'icona nell'angolo in alto a destra.

![gamma verticale](./media/vertical-range.png)

## <a name="the-log-panel"></a>Il pannello Registro

![Pannello Registro](./media/log-panel.png)

Il pannello di registro mostra un elenco di messaggi di registro generati sul lato server. Al collegamento verranno visualizzati fino a 200 messaggi di registro precedenti e ne stamperà di nuovi man mano che si verificano.

È possibile filtrare l'elenco `[Error/Warning/Info/Debug]` in base al tipo di registro utilizzando i pulsanti nella parte superiore.
![Pulsanti del filtro di registro](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Il pannello Acquisizione dati di tempo

![Acquisizione dati temporizzazione](./media/timing-data-capture.png)

Questo pannello viene utilizzato per acquisire informazioni di temporizzazione dal server e scaricarle. Il file utilizza il [formato JSON Chrome Tracing](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Per esaminare i dati, `Chrome://tracing` apri Chrome sull'URL e trascina il file scaricato nella pagina. I dati di temporizzazione vengono continuamente raccolti in un ring-buffer a dimensione fissa. Quando viene scritto, l'acquisizione include solo informazioni sul passato immediato, ovvero un paio di secondi a pochi minuti.

## <a name="the-scene-inspection-panel"></a>Il pannello Ispezione scena

![Pannello di ispezione scena](./media/scene-inspection-panel.png)

Questo pannello mostra la struttura della scena sottoposta a rendering. La gerarchia degli oggetti si trova a sinistra, il contenuto dell'oggetto selezionato si trova a destra. Il pannello è di sola lettura e viene aggiornato in tempo reale.

## <a name="the-vm-debug-information-panel"></a>Pannello Informazioni di debug della macchina virtuale

![Pannello informazioni di debug DELLA macchina virtuale](./media/state-debugger-panel.png)

Questo pannello offre alcune funzionalità di debug.

### <a name="restart-service"></a>Riavvio del servizio

Il pulsante **Riavvia servizio** consente di riavviare il runtime nella macchina virtuale a cui è connesso arrInspector. Qualsiasi client collegato verrà disconnesso e la pagina arrInspector deve essere ricaricata per connettersi al servizio riavviato.

### <a name="collect-debug-information"></a>Raccogliere informazioni di debugCollect debug information

Il pulsante **Raccogli informazioni di debug per** la macchina virtuale apre una finestra di dialogo che consente di attivare l'istanza ARR per raccogliere informazioni di debug nella macchina virtuale:The Collect Debug Information for VM button opens a dialog that allows you to trigger the ARR instance to collect debug information on the VM:

![Finestra di dialogo Informazioni di debug VM](./media/state-debugger-dialog.png)

Le informazioni di debug consentono al team di Rendering remoto di Azure di analizzare eventuali problemi che si verificano in un'istanza ARR in esecuzione. La finestra di dialogo dispone di un campo di testo per fornire ulteriori dettagli, ad esempio la procedura per riprodurre un problema.

Dopo aver fatto clic sul pulsante **Avvia raccolta,** la finestra di dialogo verrà chiusa e inizia il processo di raccolta. La raccolta delle informazioni nella macchina virtuale può richiedere alcuni minuti.

![Raccolta di informazioni di debug della macchina virtuale in corsoVM Debug Information collection in progress](./media/state-debugger-panel-in-progress.png)

Al termine della raccolta, si riceverà una notifica nella finestra di ArrInspector. Questa notifica contiene un ID che identifica questa raccolta specifica. Assicurarsi di salvare questo ID per passarlo al team di Rendering remoto di Azure.Be sure to save this ID to pass it on to the Azure Remote Rendering team.

![Raccolta di informazioni di debug della macchina virtuale](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Non è possibile scaricare o accedere in altro modo alle informazioni di debug della macchina virtuale. Solo il team di Rendering remoto di Azure ha accesso ai dati raccolti. È necessario contattarci e inviare l'ID di ritiro, per poter indagare sul problema che si sta riscontrando.

## <a name="pause-mode"></a>Modalità Pausa

Nell'angolo in alto a destra, un interruttore consente di mettere in pausa l'aggiornamento in tempo reale dei pannelli. Questa modalità può essere utile per controllare attentamente uno stato specifico.

![Modalità pausa](./media/pause-mode.png)

Quando si riattiva l'aggiornamento in tempo reale, tutti i pannelli vengono reimpostati.

## <a name="host-configuration"></a>Configurazione dell'host

Per impostazione predefinita, lo strumento si connette al server ARR in esecuzione sullo stesso host che serve ArrInspector. Tuttavia, è possibile configurarlo per controllare un altro server, presupponendo che sia in esecuzione un'istanza ARR con la porta degli strumenti aperta.

A tale scopo, accedere al menu principale a sinistra della barra dell'intestazione e selezionare *Configurazione host*. Fare clic su **Aggiungi nuovo host**e immettere il nome e il nome host. Per *il nome host* utilizzare `.mixedreality.azure.com`solo il nome `http://` host che termina con , non includere o una porta.

![Configurazione dell'host](./media/host-configuration.png)

Per passare rapidamente da un host all'altro, utilizzare il menu a discesa in alto a destra.

![Combinazione host](./media/host-switch-combo.png)

L'elenco host viene memorizzato nella memoria locale del browser, quindi verrà mantenuto durante la riapertura dello stesso browser.
