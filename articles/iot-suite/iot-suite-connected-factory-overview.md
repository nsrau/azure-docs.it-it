---
title: Panoramica della soluzione di connected factory di Azure IoT Suite | Microsoft Docs
description: Descrizione della soluzione preconfigurata di connected factory di Azure IoT Suite.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 97da0f4dc5d5528bfb803eefe9037651939a8efd
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-connected-factory-preconfigured-solution"></a>Introduzione alla soluzione preconfigurata di connected factory

## <a name="introduction"></a>Introduzione

Le [soluzioni preconfigurate][lnk-preconfigured-solutions] di Azure IoT Suite combinano più servizi IoT di Azure per fornire soluzioni end-to-end che implementano scenari aziendali IoT comuni. La soluzione preconfigurata di *connected factory* si connette ai dispositivi industriali e li monitora. È possibile usare la soluzione per analizzare il flusso di dati dei dispositivi e ottimizzare la produttività e la redditività operativa.

Questa esercitazione illustra come effettuare il provisioning della soluzione preconfigurata di connected factory. Ne descrive anche le funzionalità di base. È possibile accedere a molte di queste funzionalità dal *dashboard* distribuito come parte della soluzione preconfigurata:

![Dashboard della soluzione preconfigurata di connected factory][img-cf-home]

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure attiva.

> [!NOTE]
> Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk_free_trial].
> 
> 

## <a name="provision-the-solution"></a>Effettuare il provisioning della soluzione

1. Accedere ad azureiotsuite.com con le credenziali dell'account Azure e fare clic su "**+**" per creare una soluzione.
2. Fare clic su **Seleziona** e sul riquadro **Connected factory**.
3. Immettere un valore in **Nome soluzione** per la soluzione preconfigurata di connected factory.
4. Selezionare la **sottoscrizione** e l'**area** da usare per il provisioning della soluzione.
5. Fare clic su **Crea soluzione** per iniziare il processo di provisioning. In genere il processo richiede alcuni minuti.

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>In attesa del completamento del provisioning

1. Fare clic sul riquadro della soluzione con stato **Provisioning**.
2. Notare gli stati **Provisioning** man mano che i servizi di Azure vengono distribuiti nella sottoscrizione di Azure.
3. Al termine del provisioning, lo stato cambierà in **Pronto**.
4. Fare clic sul riquadro per visualizzare i dettagli della soluzione nel riquadro di destra.

> [!NOTE]
> In caso di problemi di distribuzione della soluzione preconfigurata, vedere [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions] e le [domande frequenti][lnk-faq]. Se i problemi persistono, creare un ticket di servizio nel [portale][lnk-portal].

Se ci sono dettagli importanti non elencati per la soluzione, è possibile inviare suggerimenti sulle funzionalità usando i [suggerimenti degli utenti](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="scenario-overview"></a>Panoramica dello scenario

Nel momento in cui viene distribuita, la soluzione preconfigurata di connected factory viene prepopolata con risorse che consentono di eseguire uno scenario industriale comune. In questo scenario, diversi stabilimenti connessi alla soluzione generano report con i valori dei dati necessari per calcolare l'OEE (Overall Equipment Efficiency) e gli indicatori di prestazioni chiave (KPI). Le sezioni seguenti mostrano come:

* Monitorare stabilimenti, linee di produzione, OEE delle postazioni e valori KPI
* Analizzare i dati di telemetria generati da questi dispositivi usando Azure Time Series Insights
* Intervenire in caso di avviso per risolvere i problemi

Una particolarità di questo scenario consiste nella possibilità di eseguire tutte queste azioni in remoto dal dashboard della soluzione, senza dover accedere fisicamente ai dispositivi.

## <a name="view-the-solution-dashboard"></a>Visualizzare il dashboard della soluzione

Il dashboard della soluzione consente di gestire la soluzione distribuita. Si tratta di una rappresentazione gerarchica della configurazione globale degli stabilimenti. È ad esempio possibile visualizzare OEE e KPI, pubblicare nuovi nodi per la telemetria e attivare avvisi.

1. Al termine del provisioning, quando il riquadro della soluzione preconfigurata indica **Pronto**, scegliere **Avvia** per aprire il portale della soluzione di connected factory in una nuova scheda.

    ![Avviare la soluzione preconfigurata][img-launch-solution]

1. Per impostazione predefinita, il portale della soluzione visualizza il *dashboard*. Usare il menu a sinistra della pagina per passare ad altre aree del portale.

    ![Dashboard della soluzione preconfigurata di connected factory][cf-img-menu]

Il dashboard visualizza le informazioni seguenti:

* Un pannello con l'**elenco degli stabilimenti** che visualizza stato, località e configurazione di produzione corrente nella soluzione. Quando si esegue la soluzione per la prima volta, sono disponibili alcuni dispositivi simulati. La simulazione delle linee di produzione è costituita da tre server OPC UA reali per ogni linea di produzione che eseguono attività simulate e condividono i dati. Per altre informazioni su OPC UA, vedere le [Domande frequenti][lnk-faq].
* Una **mappa** che visualizza la posizione di ogni dispositivo connesso alla soluzione. La soluzione può usare l'API di Bing Mappe per tracciare le informazioni sulla mappa. Se la sottoscrizione è abilitata per l'API di Bing Mappe per le aziende, questa funzionalità viene usata automaticamente. In caso contrario, vedere le [domande frequenti][lnk-faq] per informazioni su come rendere dinamica la mappa.
* Un pannello degli **avvisi** che visualizza gli avvisi generati quando un valore OEE/KPI o di telemetria supera una soglia specifica.
* Un pannello **Overall Equipment Efficiency** che visualizza i valori OEE per l'intera azienda o per lo stabilimento, la linea di produzione o la postazione in esame. Questo valore viene aggregato dalla vista della postazione fino al livello aziendale. Il valore OEE e i relativi elementi costitutivi possono essere analizzati nei dettagli.
* Un pannello **Key Performance Indicators** (Indicatori di prestazioni chiave) che visualizza il numero di unità prodotte e l'energia usata dall'intera azienda o dallo stabilimento, dalla linea di produzione o dalla postazione in esame. Questi valori vengono aggregati dalla vista della postazione fino al livello aziendale.

## <a name="view-factories"></a>Visualizzare gli stabilimenti

Il pannello degli *stabilimenti* visualizza la posizione geografica di tutti gli stabilimenti presenti nella soluzione, con lo stato e la configurazione di produzione corrente. Dall'elenco delle località è possibile passare agli altri livelli della gerarchia della soluzione. Le righe nell'elenco sono collegamenti ipertestuali che consentono di visualizzare i dettagli delle linee di produzione di tale località. È quindi possibile esaminare i dettagli delle linee di produzione fino al livello della postazione. È anche possibile applicare un filtro all'elenco.

![Stabilimenti della soluzione preconfigurata di connected factory][cf-img-factories] 

1. Il **pannello degli stabilimenti** visualizza l'elenco di stabilimenti presenti in questa soluzione.

2. L'elenco degli stabilimenti visualizza inizialmente sei stabilimenti creati dal processo di provisioning. È possibile aggiungere altri dispositivi fisici e simulati alla soluzione.

3. Per visualizzare i dettagli di uno stabilimento, fare clic sulla riga corrispondente nell'elenco degli stabilimenti.

4. Per visualizzare i dettagli di una linea di produzione, fare clic sulla riga corrispondente nell'elenco.

5. Per visualizzare i nodi OPC UA pubblicati di una postazione nella linea di produzione, fare clic sulla riga corrispondente nell'elenco.

6. Per visualizzare i dettagli in un nodo specifico nella postazione, fare clic sulla riga corrispondente nell'elenco. Questa azione consente di avviare il pannello contestuale con visualizzazioni di Time Series Insights. Fare clic su questi grafici per eseguire altre analisi nell'ambiente di esplorazione di Time Series Insights.

## <a name="view-map"></a>Visualizzare la mappa

Se la sottoscrizione ha accesso all'API di Bing Mappe, la mappa degli *stabilimenti* visualizza la posizione geografica e lo stato di tutti gli stabilimenti presenti nella soluzione. Fare clic sulle località visualizzate sulla mappa per esaminare i dettagli.

![Mappa della soluzione preconfigurata di connected factory][cf-img-map]

## <a name="view-alerts"></a>Visualizzare gli avvisi

Il pannello con la cronologia degli **avvisi** indica gli avvisi generati a causa di un valore segnalato o un valore OEE/KPI calcolato superiore alla soglia configurata. Questo pannello visualizza gli avvisi per ogni livello della gerarchia, dalla vista a livello di postazione fino alla vista globale. Gli avvisi contengono descrizione dell'avviso, data, ora, località e numero di occorrenze. È possibile ottenere informazioni dettagliate sulle cause dell'avviso usando i dati di Time Series Insights. I dati di Time Series Insights vengono visualizzati negli avvisi, se applicabile. Gli amministratori possono eseguire azioni predefinite sugli avvisi, ad esempio:

* Chiudere l'avviso.
* Confermare l'avviso.

È anche possibile eseguire operazioni più complesse. Ad esempio, per il nodo OPC UA relativo alla pressione del gruppo di componenti è possibile:

* Caricare una pagina Web in una nuova finestra del browser per visualizzare informazioni di supporto.
* Chiamare un metodo OPC UA nel dispositivo per attenuare la causa dell'avviso.
* Rendere indisponibili le azioni predefinite.

    ![Avvisi della soluzione preconfigurata di connected factory][cf-img-alerts]

> [!NOTE]
> Questi avvisi vengono generati da regole specificate in un file di configurazione della soluzione preconfigurata. Queste regole possono generare avvisi quando i valori di OEE o KPI o del nodo OPC UA superano la soglia configurata.

1. Il **pannello degli avvisi** indica gli avvisi generati in questa soluzione.

2. Per visualizzare i dettagli di un avviso, fare clic su di esso nel pannello.

3. Per eseguire altre analisi sui dati dell'avviso, fare clic sul grafico nel pannello degli avvisi per aprire l'ambiente di esplorazione di Time Series Insights.

4. Per risolvere l'avviso, il pannello mette a disposizione diverse azioni. Scegliere l'opzione appropriata e fare clic sul pulsante di comando per l'esecuzione dell'azione.

## <a name="view-overall-equipment-efficiency"></a>Visualizzare il valore di OEE (Overall Equipment Efficiency)

Il valore di OEE valuta l'efficienza del processo di produzione usando parametri operativi chiave correlati alla produzione. Il valore di OEE è una misura standard di settore calcolata moltiplicando la disponibilità, le prestazioni e la qualità: OEE = disponibilità x prestazioni x qualità.

![OEE della soluzione preconfigurata di connected factory][cf-img-oee]

1. Per visualizzare il valore di OEE per qualsiasi livello della gerarchia, passare alla vista specifica. Il valore di OEE per la vista verrà visualizzato nel pannello insieme a tutti gli elementi che costituiscono la percentuale OEE.

2. Per eseguire altre analisi sul valore di OEE per qualsiasi livello della gerarchia di dati, fare clic sulla percentuale OEE oppure sulla percentuale della disponibilità, delle prestazioni o della qualità. Si aprirà un pannello contestuale con visualizzazioni di Time Series Insights che indicano i dati dell'ultima ora, delle ultime 24 ore e degli ultimi 7 giorni.

    ![Visualizzazione TSI della soluzione preconfigurata di connected factory][cf-img-tsi-visualization]

3. Per eseguire altre analisi sui dati dell'avviso, fare clic sul grafico nel pannello degli avvisi. Si aprirà così l'ambiente di esplorazione di Time Series Insights.

    ![Ambiente di esplorazione TSI della soluzione preconfigurata di connected factory][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>Visualizzare gli indicatori di prestazioni chiave

La soluzione offre due indicatori di prestazioni chiave, *unità per ogni ora* ed *energia usata in kWh*.

![KPI della soluzione preconfigurata di connected factory][cf-img-kpi]

1. Per visualizzare le unità per ogni ora o l'energia usata per qualsiasi livello della gerarchia, passare alla vista specifica. Le unità per ogni ora e l'energia usata verranno visualizzate nel pannello.

2. Per eseguire altre analisi sulle unità per ogni ora o sull'energia usata per qualsiasi livello della gerarchia dei dati, fare clic sul misuratore delle unità prodotte o dell'energia usata nel pannello **Key Performance Indicators** (Indicatori di prestazioni chiave). Si aprirà un pannello contestuale con visualizzazioni di Time Series Insights che indicano i dati dell'ultima ora, delle ultime 24 ore e degli ultimi 7 giorni.

## <a name="scenario-review"></a>Analisi dello scenario

In questo scenario sono stati monitorati i valori di OEE e KPI nel dashboard. È stato quindi usato Time Series Insights per ottenere altre informazioni e analizzare i dati di telemetria per OEE e KPI al fine di rilevare le anomalie. È stato anche usato il pannello degli avvisi per visualizzare i problemi degli stabilimenti e le azioni disponibili per risolvere l'avviso.

## <a name="other-features"></a>Altre funzionalità

Le sezioni seguenti descrivono alcune funzionalità aggiuntive della soluzione preconfigurata di connected factory non descritte nell'ambito dello scenario precedente.

## <a name="apply-filters"></a>Applicare filtri

1. Fare clic sulla **freccia di espansione** per visualizzare un elenco dei filtri disponibili nel pannello delle località degli stabilimenti o nel pannello degli avvisi.

2. Verrà visualizzato il pannello dei filtri. 

    ![Filtri della soluzione preconfigurata di connected factory][cf-img-alert-filter]

3. Scegliere il filtro necessario. È anche possibile digitare testo libero nei campi di filtro, se necessario.

4. Il filtro verrà quindi applicato. Lo stato del filtro viene visualizzato nel dashboard anche tramite un imbuto visibile nelle tabelle degli stabilimenti e degli avvisi.

    ![Filtri della soluzione preconfigurata di connected factory][cf-img-alert-filter-funnel]

    > [!NOTE]
    > Un filtro attivo non ha effetto sui valori di OEE e KPI visualizzati, filtra solo il contenuto dell'elenco.

5. Per cancellare un filtro, fare clic sull'imbuto e quindi sul filtro nel pannello contestuale dei filtri. Il testo **All** (Tutti) viene visualizzato nelle tabelle degli stabilimenti e degli avvisi.

## <a name="browse-an-opc-ua-server"></a>Esplorare un server OPC UA

Quando si distribuisce la soluzione preconfigurata, viene effettuato automaticamente il provisioning dei server OPC UA simulati che è possibile esplorare tramite il browser della soluzione. Questi server sono *server OPC UA simulati* . I server simulati consentono di provare facilmente la soluzione preconfigurata senza la necessità di distribuire server fisici reali. Per connettere un server OPC UA reale alla soluzione, vedere l'esercitazione [Connettere il dispositivo OPC UA alla soluzione preconfigurata di connected factory][lnk-connect-cf].

1. Fare clic sull'**icona dello stabilimento** nella barra di spostamento del dashboard.

    ![Browser dei server della soluzione preconfigurata di connected factory][cf-img-server-browser]

2. Scegliere uno dei server dall'elenco preconfigurato. Questo elenco visualizza i server distribuiti nella soluzione preconfigurata.

    ![Selezione del server della soluzione preconfigurata di connected factory][cf-img-server-choice]

3. Fare clic su **Connect** (Connetti). Verrà visualizzata una finestra di dialogo di sicurezza. Per la simulazione è possibile fare clic su **Proceed** (Continua)

4. Fare clic su uno dei nodi dell'albero dei server per espanderlo. I nodi che pubblicano i dati di telemetria hanno un segno di spunta accanto.

    ![Albero dei server della soluzione preconfigurata di connected factory][cf-img-server-tree]

5. Fare clic con il pulsante destro del mouse su un elemento per leggere, scrivere, pubblicare o chiamare il nodo. Le azioni disponibili variano a seconda delle autorizzazioni e degli attributi del nodo. L'opzione di lettura visualizza un pannello contestuale con il valore del nodo specifico. L'opzione di scrittura visualizza un pannello contestuale in cui è possibile immettere un nuovo valore. L'opzione di chiamata visualizza un nodo in cui è possibile immettere i parametri per la chiamata.

## <a name="publish-a-node"></a>Pubblicare un nodo

Quando si esplora un *server OPC UA simulato* è anche possibile scegliere di pubblicare nuovi nodi. È possibile analizzare i dati di telemetria da questi nodi nella soluzione. Questi *server OPC UA simulati* consentono di provare facilmente la soluzione preconfigurata senza la necessità di distribuire dispositivi fisici reali.

1. Passare a un nodo da pubblicare nell'albero del browser dei server OPC UA.

2. Fare clic con il pulsante destro del mouse sul nodo.

3. Scegliere **Publish** (Pubblica).

    ![Pubblicare un nodo nella soluzione di connected factory][cf-img-publish-node]

4. Verrà visualizzato un pannello contestuale che indica che la pubblicazione ha avuto esito positivo. Il nodo viene visualizzato al livello delle postazioni con un segno di spunta accanto.

    ![Pubblicazione eseguita nella soluzione preconfigurata di connected factory][cf-img-publish-success]

## <a name="command-and-control"></a>Comando e controllo

La soluzione di connected factory consente di comandare e gestire i dispositivi industriali direttamente dal cloud. È possibile usare questa funzionalità per rispondere agli avvisi generati dal dispositivo. È ad esempio possibile inviare un comando al dispositivo dal cloud. È possibile trovare i comandi disponibili nel nodo **StationCommands** dell'albero del browser dei server OPC UA. In questo scenario si sta aprendo una valvola di sfiato nella postazione di un gruppo di componenti di una linea di produzione situata a Monaco di Baviera. Per usare la funzionalità di comando e controllo è necessario il ruolo **Amministratore** per la distribuzione della soluzione preconfigurata.

1. Passare al nodo **StationCommands** nell'albero del browser dei server OPC UA.

2. Scegliere il comando che si vuole usare.

3. Fare clic con il pulsante destro del mouse sul nodo.

4. Scegliere **Call** (Chiama).

    ![Comando di chiamata della soluzione preconfigurata di connected factory][cf-img-call-command]

5. Viene visualizzato un pannello contestuale che indica il metodo che si sta per chiamare e i dettagli dei parametri applicabili.

6. Scegliere **Call** (Chiama).

    ![Contesto di chiamata della soluzione preconfigurata di connected factory][cf-img-call-context]

7. Il pannello contestuale viene aggiornato per indicare che la chiamata al metodo ha avuto esito positivo. È possibile verificare che la chiamata abbia avuto esito positivo leggendo il valore del nodo della pressione aggiornato in seguito alla chiamata.

    ![Chiamata eseguita nella soluzione preconfigurata di connected factory][cf-img-call-success]


## <a name="behind-the-scenes"></a>Dietro le quinte

Quando si distribuisce una soluzione preconfigurata, il processo di distribuzione crea più risorse nella sottoscrizione di Azure selezionata. È possibile visualizzare queste risorse nel [portale][lnk-portal] di Azure. Il processo di distribuzione crea un **gruppo di risorse** con un nome basato sul nome scelto per la soluzione preconfigurata:

![Soluzione preconfigurata nel portale di Azure][img-cf-portal]

È possibile visualizzare le impostazioni di ogni risorsa selezionandola nell'elenco di risorse nel gruppo di risorse.

È anche possibile visualizzare il codice sorgente per la soluzione preconfigurata. Il codice sorgente della soluzione preconfigurata di connected factory si trova nel repository GitHub [azure-iot-connected-factory][lnk-cfgithub]:

Al termine, è possibile eliminare la soluzione preconfigurata dalla sottoscrizione di Azure nel sito [azureiotsuite.com][lnk-azureiotsuite]. Questo sito consente di eliminare facilmente tutte le risorse di cui è stato effettuato il provisioning quando si è creata la soluzione preconfigurata.

> [!NOTE]
> Per assicurarsi di eliminare tutti gli elementi correlati alla soluzione preconfigurata, eseguire l'eliminazione dal sito [azureiotsuite.com][lnk-azureiotsuite]. Non eliminare il gruppo di risorse nel portale.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito una soluzione preconfigurata è possibile proseguire con l'introduzione a IoT Suite vedendo gli articoli seguenti:

* [Procedura dettagliata per la soluzione preconfigurata di connected factory][lnk-rm-walkthrough]
* [Connettere il dispositivo alla soluzione preconfigurata di connected factory][lnk-connect-cf]
* [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions]

[img-cf-home]:media/iot-suite-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-suite-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-suite-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-suite-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-suite-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-suite-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-suite-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-suite-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-suite-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-suite-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-suite-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-suite-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-suite-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-suite-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-suite-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-suite-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-suite-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-suite-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-suite-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-suite-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-suite-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
