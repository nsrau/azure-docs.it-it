---
title: Creare tracce di prestazioni lato client
description: Procedure consigliate per la profilatura delle prestazioni lato client tramite WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681311"
---
# <a name="create-client-side-performance-traces"></a>Creare tracce di prestazioni lato client

Esistono molti motivi per cui le prestazioni del rendering remoto di Azure potrebbero non essere quelle desiderate. Oltre alle prestazioni di rendering pure sul server cloud, in particolare la qualità della connessione di rete ha un impatto significativo sull'esperienza. Per profilare le prestazioni del server, vedere il capitolo [query sulle prestazioni lato server](../overview/features/performance-queries.md).

In questo capitolo viene illustrato come identificare potenziali colli di bottiglia sul lato client tramite le *tracce delle prestazioni*.

## <a name="getting-started"></a>Guida introduttiva

Se non si ha familiarità con la funzionalità di analisi delle prestazioni di Windows, in questa sezione verranno citati i termini e le applicazioni più importanti per iniziare.

### <a name="installation"></a>Installazione

Le applicazioni utilizzate per eseguire la traccia con ARR sono strumenti generici che possono essere utilizzati per lo sviluppo di Windows. Vengono forniti tramite [Windows Performance Toolkit](https://docs.microsoft.com/windows-hardware/test/wpt/). Per ottenere questo Toolkit, scaricare [Windows Assessment and Deployment Kit](https://docs.microsoft.com/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologia

Quando si cercano le informazioni sulle tracce di prestazioni, si passerà inevitabilmente a una serie di termini. Quelli più importanti sono:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** sta per [ **E**Vent **T**Racing per **W**indows](https://docs.microsoft.com/windows/win32/etw/about-event-tracing). Si tratta semplicemente del nome complesso per la funzionalità di traccia a livello di kernel efficiente incorporata in Windows. Viene definita traccia *eventi* , perché le applicazioni che supportano ETW generano eventi per le azioni di registrazione che possono contribuire a tenere traccia dei problemi di prestazioni. Per impostazione predefinita, il sistema operativo emette già eventi per elementi come accessi al disco, commutatori di attività e così via. Le applicazioni come ARR generano inoltre eventi personalizzati, ad esempio i frame eliminati, il ritardo di rete e così via.

**ETL** si basa su **E**Vent **T**Race **L**ogging. Significa semplicemente che una traccia è stata raccolta (registrata) e viene pertanto utilizzata come estensione di file per i file in cui sono archiviati i dati di traccia. Pertanto, quando si esegue una traccia, in un secondo momento \*si disporrà di un file con estensione ETL.

**WPR** sta per [ **W**indows **P**Esplora **R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) e è il nome dell'applicazione che avvia e arresta la registrazione delle tracce dell'evento. WPR accetta un file di profilo\*(con estensione WPRP) che configura gli eventi esatti da registrare. Tale `wprp` file viene fornito con l'SDK arr. Quando si esegue la traccia in un PC desktop, è possibile avviare direttamente WPR. Quando si esegue una traccia in HoloLens, in genere si passa attraverso l'interfaccia Web.

**WPA** è l'acronimo di [ **W**indows **P**Esplora **a**nalyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) e è il nome dell'applicazione GUI utilizzata per aprire \*i file con estensione ETL e analizzare i dati per identificare i problemi di prestazioni. WPA consente di ordinare i dati in base a diversi criteri, visualizzare i dati in diversi modi, approfondire i dettagli e correlare le informazioni.

Sebbene sia possibile creare tracce ETL in qualsiasi dispositivo Windows (PC locale, HoloLens, server cloud e così via), in genere vengono salvati su disco e analizzati con WPA su un PC desktop. I file ETL possono essere inviati ad altri sviluppatori per consentirne l'aspetto. Tenere presente che le informazioni riservate, ad esempio i percorsi di file e gli indirizzi IP, possono essere acquisite nelle tracce ETL. È possibile utilizzare ETW in due modi: per registrare le tracce o per analizzare le tracce. La registrazione delle tracce è semplice e richiede una configurazione minima. L'analisi delle tracce d'altra parte richiede una conoscenza corretta dello strumento WPA e del problema che si sta esaminando. Il materiale generale per l'apprendimento di WPA verrà riportato di seguito, nonché le linee guida per l'interpretazione delle tracce specifiche di ARR.

## <a name="recording-a-trace-on-a-local-pc"></a>Registrazione di una traccia in un PC locale

Per identificare i problemi di prestazioni ARR è preferibile eseguire una traccia direttamente in una HoloLens, perché questo è l'unico modo per ottenere uno snapshot delle caratteristiche reali delle prestazioni. Tuttavia, se si desidera eseguire una traccia in modo specifico senza le restrizioni sulle prestazioni di HoloLens o se si desidera semplicemente apprendere come utilizzare WPA e non è necessaria una traccia realistica, di seguito viene illustrato come eseguire questa operazione.

### <a name="wpr-configuration"></a>Configurazione di WPR

1. Avviare [registrazione prestazioni Windows](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) dal *menu Start*.
1. Espandi **altre opzioni**
1. Fare clic su **Aggiungi profili...**
1. Selezionare il file *AzureRemoteRenderingNetworkProfiling. WPRP*. È possibile trovare questo file in ARR SDK in *Tools/ETLProfiles*.
   Il profilo verrà ora elencato in WPR in *misurazioni personalizzate*. Verificare che sia l'unico profilo abilitato.
1. Espandi *valutazione di primo livello*:
    * Se si vuole solo acquisire una traccia rapida degli eventi di rete ARR, **disabilitare** questa opzione.
    * Se è necessario correlare gli eventi di rete ARR con altre caratteristiche di sistema, ad esempio utilizzo della CPU o della memoria, **abilitare** questa opzione.
    * Se si abilita questa opzione, è probabile che la traccia sia costituita da più gigabyte e che sia necessario molto tempo per il salvataggio e l'apertura in WPA.

In seguito, la configurazione di WPR dovrebbe essere simile alla seguente:

![Configurazione di WPR](./media/wpr.png)

### <a name="recording"></a>Registrazione

Fare clic su **Avvia** per avviare la registrazione di una traccia. È possibile avviare e arrestare la registrazione in qualsiasi momento; prima di procedere, non è necessario chiudere l'applicazione. Come si può notare, non è necessario specificare l'applicazione da tracciare, poiché ETW registrerà sempre una traccia per l'intero sistema. Il `wprp` file specifica i tipi di eventi da registrare.

Fare clic su **Salva** per arrestare la registrazione e specificare dove archiviare il file ETL.

A questo punto si dispone di un file ETL che può essere aperto direttamente in WPA o inviato a un altro utente.

## <a name="recording-a-trace-on-a-hololens"></a>Registrazione di una traccia in un HoloLens

Per registrare una traccia in un HoloLens, avviare il dispositivo e immettere l'indirizzo IP in un browser per aprire il portale del *dispositivo*.

![Portale di dispositivi](./media/wpr-hl.png)

1. A sinistra passare a *prestazioni > traccia delle prestazioni*.
1. Selezione **profili personalizzati**
1. Fare clic su **Sfoglia...**
1. Selezionare il file *AzureRemoteRenderingNetworkProfiling. WPRP*. È possibile trovare questo file in ARR SDK in *Tools/ETLProfiles*.
1. Fare clic su **Avvia traccia**
1. HoloLens sta ora registrando una traccia. Assicurarsi di attivare i problemi di prestazioni che si desidera analizzare. Quindi fare clic su **Arresta traccia**.
1. La traccia verrà visualizzata nella parte inferiore della pagina Web. Fare clic sull'icona del disco sul lato destro per scaricare il file ETL.

A questo punto si dispone di un file ETL che può essere aperto direttamente in WPA o inviato a un altro utente.

## <a name="analyzing-traces-with-wpa"></a>Analisi delle tracce con WPA

### <a name="wpa-basics"></a>Nozioni fondamentali su WPA

Windows Performance Analyzer è lo strumento standard per aprire i file ETL ed esaminare le tracce. Una spiegazione del funzionamento di WPA non rientra nell'ambito di questo articolo. Per iniziare, vedere le risorse seguenti:

* Per una prima panoramica, Guarda i [video introduttivi](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) .
* WPA dispone di una scheda *Introduzione* , che illustra i passaggi comuni. Esaminare gli argomenti disponibili. In particolare, in "Visualizza dati" si ottiene una rapida introduzione come creare grafici per dati specifici.
* Sono disponibili informazioni eccezionali [su questo sito Web](https://randomascii.wordpress.com/2015/09/24/etw-central/), ma non tutte sono rilevanti per i principianti.

### <a name="graphing-data"></a>Grafici dei dati

Per iniziare a usare la traccia ARR, è opportuno conoscere le parti seguenti.

![Grafico prestazioni](./media/wpa-graph.png)

L'immagine precedente mostra una tabella di dati di traccia e una rappresentazione grafica degli stessi dati.

Nella tabella nella parte inferiore, prendere nota della barra gialla (dorata) e della barra blu. È possibile trascinare queste barre e posizionarle in qualsiasi posizione.

Tutte le **colonne a sinistra della barra gialla** vengono interpretate come **chiavi**. Le chiavi vengono usate per strutturare l'albero nella finestra in alto a sinistra. Qui sono disponibili due colonne *chiave* , "provider name" e "Task Name". Di conseguenza, la struttura ad albero nella finestra in alto a sinistra è a due livelli di profondità. Se si riordinano le colonne o si aggiungono o rimuovono colonne dall'area chiave, la struttura nella visualizzazione albero viene modificata.

Le **colonne a destra della barra blu** vengono usate per la **visualizzazione del grafico** nella finestra in alto a destra. Nella maggior parte dei casi viene utilizzata solo la prima colonna, ma alcune modalità grafico richiedono più colonne di dati. Per il corretto funzionamento dei grafici a linee, è necessario impostare la *modalità di aggregazione* su tale colonna. Usare ' AVG ' o ' Max '. La modalità di aggregazione viene utilizzata per determinare il valore del grafico in un determinato pixel, quando un pixel copre un intervallo con più eventi. Questa operazione può essere osservata impostando l'aggregazione su' Sum ' e quindi eseguendo lo zoom avanti e indietro.

Le colonne nella parte centrale non hanno un significato speciale.

![Vista eventi](./media/wpa-event-view.png)

*Nell'Editor visualizzazione eventi generici* è possibile configurare tutte le colonne da visualizzare, la modalità di aggregazione, l'ordinamento e le colonne utilizzate come chiavi o per la rappresentazione grafica. Nell'esempio precedente il **campo 2** è abilitato e il campo 3-6 è disabilitato. Il campo 2 è in genere il primo campo *dati personalizzato* di un evento ETW e pertanto per gli eventi arr "FrameStatistics", che rappresentano un valore di latenza di rete. Abilitare altre colonne "Field" per visualizzare altri valori di questo evento.

### <a name="presets"></a>Impostazioni predefinite

Per analizzare correttamente una traccia, è necessario individuare il proprio flusso di lavoro e la visualizzazione dei dati preferita. Tuttavia, per essere in grado di ottenere una rapida panoramica sugli eventi specifici di ARR, è necessario includere i file del profilo della piattaforma Windows Software Protection e dei set di impostazioni nella cartella *Tools/ETLProfiles*. Per caricare un profilo completo, selezionare *profili > applica...* dalla barra dei menu WPA oppure aprire il pannello *set di impostazioni personali* (*finestra > set di impostazioni personali*) e selezionare *Importa*. Il primo imposta una configurazione WPA completa, come nell'immagine seguente. Quest'ultimo renderà disponibili solo i set di impostazioni per le varie configurazioni di visualizzazione e consente di aprire rapidamente una visualizzazione per esaminare una parte specifica dei dati degli eventi ARR.

![Impostazioni predefinite](./media/wpa-arr-trace.png)

L'immagine precedente mostra le visualizzazioni dei vari eventi specifici di ARR, oltre a una visualizzazione dell'utilizzo complessivo della CPU.

## <a name="next-steps"></a>Passaggi successivi

* [Query sulle prestazioni lato server](../overview/features/performance-queries.md)
