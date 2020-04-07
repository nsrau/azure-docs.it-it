---
title: Creare tracce delle prestazioni lato clientCreate client-side performance traces
description: Procedure consigliate per la profilatura delle prestazioni lato client tramite WPFBest practices for client-side performance profiling using WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681311"
---
# <a name="create-client-side-performance-traces"></a>Creare tracce delle prestazioni lato clientCreate client-side performance traces

Esistono molti motivi per cui le prestazioni del rendering remoto di Azure potrebbero non essere buone come desiderato. Oltre alle prestazioni di rendering puro sul server cloud, in particolare la qualità della connessione di rete ha un'influenza significativa sull'esperienza. Per profilare le prestazioni del server, fare riferimento al capitolo [Query sulle prestazioni lato server](../overview/features/performance-queries.md).

In questo capitolo viene illustrato come identificare potenziali colli di bottiglia sul lato client tramite *le tracce delle prestazioni.*

## <a name="getting-started"></a>Introduzione

Se non si ha familiarità con la funzionalità di analisi delle prestazioni di Windows, in questa sezione verranno menzionati i termini e le applicazioni fondamentali per iniziare.

### <a name="installation"></a>Installazione

Le applicazioni utilizzate per eseguire la traccia tura con ARR sono strumenti di uso generale che possono essere utilizzati per tutti gli sviluppatori di Windows.The applications used to do tracing with ARR are general purpose tools that can be used for all Windows development. Vengono forniti tramite [Windows Performance Toolkit](https://docs.microsoft.com/windows-hardware/test/wpt/). Per ottenere questo toolkit, scaricare [Windows Assessment and Deployment Kit](https://docs.microsoft.com/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologia

Quando si cercano informazioni sulle tracce delle prestazioni, si sarà inevitabilmente imbattervi in una serie di termini. I più importanti sono:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** sta per [ **E**vent **T**racing per **W**indows](https://docs.microsoft.com/windows/win32/etw/about-event-tracing). È semplicemente il nome generale per l'efficiente funzionalità di tracciamento a livello di kernel incorporata in Windows. Viene *chiamato* traccia degli eventi, perché le applicazioni che supportano ETW genereranno eventi per registrare azioni che possono aiutare a individuare i problemi di prestazioni. Per impostazione predefinita, il sistema operativo genera già eventi per operazioni quali accessi al disco, opzioni di attività e così via. Applicazioni come ARR generano inoltre eventi personalizzati, ad esempio sui frame eliminati, sul ritardo di rete e così via.

**ETL** sta per **E**vent **T**race **L**in gung. Significa semplicemente che una traccia è stata raccolta (registrata) e pertanto viene in genere utilizzata come estensione di file per i file che archiviano i dati di traccia. Così quando si esegue una traccia, \*in genere si avrà un file .etl in seguito.

**WPR** è l'acronimo di [ **W**indows **P**erformance **R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) ed è il nome dell'applicazione che avvia e interrompe la registrazione delle tracce di eventi. WPR accetta un\*file di profilo ( wprp) che configura gli eventi esatti da registrare. Tale `wprp` file viene fornito con L'SDK ARR. Quando si fanno tracce su un PC desktop, è possibile avviare direttamente WPR. Quando si esegue una traccia su HoloLens, in genere passare attraverso l'interfaccia web invece.

**WPA** sta per [ **W**indows **P**erformance **A**nalyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) ed è \*il nome dell'applicazione GUI che viene utilizzato per aprire i file .etl e setacciare i dati per identificare i problemi di prestazioni. WPA consente di ordinare i dati in base a vari criteri, visualizzare i dati in diversi modi, scavare nei dettagli e correlare le informazioni.

Mentre le tracce ETL possono essere create su qualsiasi dispositivo Windows (PC locale, HoloLens, server cloud e così via), vengono in genere salvate su disco e analizzate con WPA su un PC desktop. I file ETL possono essere inviati ad altri sviluppatori per dare un'occhiata. Tenere presente che le informazioni riservate, ad esempio i percorsi di file e gli indirizzi IP, possono essere acquisite nelle tracce ETL. È possibile utilizzare ETW in due modi: per registrare le tracce o per analizzare le tracce. La registrazione delle tracce è semplice e richiede una configurazione minima. L'analisi delle tracce, d'altra parte, richiede una comprensione decente sia dello strumento WPA che del problema che si sta analizzando. Il materiale generale per l'apprendimento di WPA sarà fornito di seguito, nonché linee guida su come interpretare le tracce specifiche di ARR.

## <a name="recording-a-trace-on-a-local-pc"></a>Registrazione di una traccia su un PC locale

Per identificare i problemi di prestazioni ARR è consigliabile eseguire una traccia direttamente in un HoloLens, poiché questo è l'unico modo per ottenere uno snapshot delle caratteristiche di prestazioni reali. Tuttavia, se si desidera eseguire una traccia in particolare senza le restrizioni di prestazioni HoloLens o si desidera semplicemente imparare a utilizzare WPA e non è necessaria una traccia realistica, ecco come farlo.

### <a name="wpr-configuration"></a>Configurazione WPR

1. Avviare [Windows Performance Recorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) dal *menu start.*
1. Espandi **altre opzioni**
1. Fare clic su **Aggiungi profili...**
1. Selezionare il file *AzureRemoteRenderingNetworkProfiling.wprp*. È possibile trovare questo file in ARR SDK in *Tools/ETLProfiles*.
   Il profilo verrà ora elencato in WPR in *Misure personalizzate*. Assicurarsi che sia l'unico profilo abilitato.
1. Espandi *triage di primo livello*:
    * Se si desidera solo acquisire una traccia rapida degli eventi di rete ARR, **disattivare** questa opzione.
    * Se è necessario correlare gli eventi di rete ARR con altre caratteristiche del sistema, ad esempio l'utilizzo della CPU o della memoria, **abilitare** questa opzione.
    * Se si abilita questa opzione, la traccia avrà probabilmente più gigabyte di dimensioni e richiederà molto tempo per salvare e aprire in WPA.

Successivamente la configurazione WPR dovrebbe essere simile alla seguente:Afterwards your WPR configuration should look like this:

![Configurazione WPR](./media/wpr.png)

### <a name="recording"></a>Registrazione

Fare clic su **Avvia** per avviare la registrazione di una traccia. È possibile avviare e interrompere la registrazione in qualsiasi momento; non è necessario chiudere l'applicazione prima di farlo. Come si può vedere non è necessario specificare quale applicazione tracciare, come ETW registrerà sempre una traccia per l'intero sistema. Il `wprp` file specifica quali tipi di eventi registrare.

Fare clic su **Salva** per interrompere la registrazione e specificare dove archiviare il file ETL.

Ora hai un file ETL che puoi aprire direttamente in WPA o inviarlo a qualcun altro.

## <a name="recording-a-trace-on-a-hololens"></a>Registrazione di una traccia in un HoloLensRecording a trace on a HoloLens

Per registrare una traccia in un HoloLens, avviare il dispositivo e immettere il relativo indirizzo IP in un browser per aprire il *portale dei dispositivi*.

![Portale di dispositivi](./media/wpr-hl.png)

1. A sinistra, passare a *Performance > Performance Tracing*.
1. Selezionare **Profili personalizzati**
1. Fare clic su **Sfoglia...**
1. Selezionare il file *AzureRemoteRenderingNetworkProfiling.wprp*. È possibile trovare questo file in ARR SDK in *Tools/ETLProfiles*.
1. Fare clic su **Avvia traccia**
1. Il HoloLens sta registrando una traccia. Assicurarsi di attivare i problemi di prestazioni che si desidera analizzare. Quindi fare clic su **Interrompi traccia**.
1. La traccia verrà elencata nella parte inferiore della pagina Web. Fare clic sull'icona del disco sul lato destro per scaricare il file ETL.

Ora hai un file ETL che puoi aprire direttamente in WPA o inviarlo a qualcun altro.

## <a name="analyzing-traces-with-wpa"></a>Analisi delle tracce con WPAAnalyzing traces with WPA

### <a name="wpa-basics"></a>Nozioni di base su WPA

Windows Performance Analyzer è lo strumento standard per aprire i file ETL ed esaminare le tracce. Una spiegazione del funzionamento di WPA non è in ambito per questo articolo. Per iniziare, dai un'occhiata a queste risorse:

* Guarda i [video introduttivi](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) per una prima panoramica.
* WPA stesso ha una scheda *introduttiva,* che spiega i passaggi comuni. Dai un'occhiata agli argomenti disponibili. Soprattutto in "Visualizza dati" si ottiene una rapida introduzione su come creare grafici per dati specifici.
* Ci sono informazioni eccellenti [su questo sito web](https://randomascii.wordpress.com/2015/09/24/etw-central/), tuttavia, non tutto è rilevante per i principianti.

### <a name="graphing-data"></a>Dati grafici

Per iniziare con la traccia ARR, i seguenti pezzi sono buoni da sapere.

![Grafico delle prestazioni](./media/wpa-graph.png)

L'immagine precedente mostra una tabella di dati di ricalco e una rappresentazione grafica degli stessi dati.

Nella tabella in basso, notare la barra gialla (dorata) e la barra blu. È possibile trascinare queste barre e posizionarle in qualsiasi posizione.

Tutte le **colonne a sinistra della barra gialla** vengono interpretate come **chiavi**. Le chiavi vengono utilizzate per strutturare la struttura nella finestra in alto a sinistra. Qui abbiamo due colonne *chiave,* "Provider Name" e "Task Name". Di conseguenza, la struttura ad albero nella finestra in alto a sinistra è profonda due livelli. Se si riordinano le colonne o si aggiungono o rimuovono colonne dall'area chiave, la struttura nella visualizzazione ad albero cambia.

**Le colonne a destra della barra blu** vengono utilizzate per la visualizzazione del **grafico** nella finestra in alto a destra. Nella maggior parte dei modi viene utilizzata solo la prima colonna, ma alcune modalità grafico richiedono più colonne di dati. Affinché i grafici a linee funzionino, è necessario impostare la modalità di *aggregazione* in tale colonna. Utilizzare 'Avg' o 'Max'. La modalità di aggregazione viene utilizzata per determinare il valore del grafico in corrispondenza di un determinato pixel, quando un pixel copre un intervallo con più eventi. Questo può essere osservato impostando l'aggregazione su 'Sum' e quindi lo zoom avanti e indietro.

Le colonne al centro non hanno un significato speciale.

![Vista eventi](./media/wpa-event-view.png)

*Nell'Editor visualizzazione eventi generici* è possibile configurare tutte le colonne da visualizzare, la modalità di aggregazione, l'ordinamento e le colonne da utilizzare come chiavi o per la creazione di grafici. Nell'esempio precedente, il **campo 2** è abilitato e il campo 3 - 6 sono disabilitati. Il campo 2 è in genere il primo campo *dati personalizzato* di un evento ETW e quindi per gli eventi ARR "FrameStatistics", che rappresentano un valore di latenza di rete. Abilitare altre colonne "Campo" per visualizzare ulteriori valori di questo evento.

### <a name="presets"></a>Impostazioni predefinite

Per analizzare correttamente una traccia, è necessario individuare il proprio flusso di lavoro e la visualizzazione dei dati preferiti. Tuttavia, per poter ottenere una rapida panoramica sugli eventi specifici di ARR, includiamo i file del profilo di Windows Software Protection Platform e i file predefiniti nella cartella *Tools/ETLProfiles*. Per caricare un profilo completo, selezionate *Profili > Applica...* dalla barra dei menu WPA oppure aprite il pannello *I miei predefiniti* *(Finestra > Mie predefiniti*) e selezionate *Importa*. Il primo imposterà una configurazione WPA completa come nell'immagine qui sotto. Quest'ultimo renderà disponibili solo i preset per le varie configurazioni di vista e vi permetterà di aprire rapidamente una vista per esaminare una parte specifica dei dati degli eventi ARR.

![Impostazioni predefinite](./media/wpa-arr-trace.png)

L'immagine precedente mostra le visualizzazioni di vari eventi specifici di ARR e una visualizzazione dell'utilizzo complessivo della CPU.

## <a name="next-steps"></a>Passaggi successivi

* [Query sulle prestazioni lato server](../overview/features/performance-queries.md)
