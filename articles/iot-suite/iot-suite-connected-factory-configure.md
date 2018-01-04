---
title: Configurare la topologia di factory connesso | Documenti Microsoft
description: Come configurare la topologia di una soluzione di factory connesso preconfigurato.
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 19e0f48ab817428a1f953c80296b2e23effe5a8a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="configure-the-connected-factory-preconfigured-solution"></a>Configurare la soluzione factory connesso preconfigurato

La soluzione factory connesso preconfigurato Mostra un dashboard simulato per una società fittizia Contoso. Società è globale factory in numerose posizioni globali.

In questo articolo Usa Contoso ad esempio per descrivere come configurare la topologia di una soluzione di factory connesso.

## <a name="simulated-factories-configuration"></a>Configurazione di factory simulata

Ogni factory di Contoso è costituita da tre stazioni le righe di produzione. Ogni stazione è un server di OPC UA reale a un ruolo specifico:

* Stazione di assembly
* Stazione di test
* Stazione di creazione di pacchetti

Questi server OPC UA sono nodi OPC UA e [Publisher OPC](https://github.com/Azure/iot-edge-opc-publisher) invia i valori di questi nodi connessi factory. Sono inclusi:

* Stato operativo corrente, ad esempio il consumo di energia corrente.
* Informazioni relative alla produzione, ad esempio il numero di prodotti è stato generato.

È possibile utilizzare il dashboard per analizzare la topologia di factory Contoso da una vista globale fino a una visualizzazione a livello di espansione. Il dashboard di factory connesso consente:

* La visualizzazione delle figure OEE e un indicatore KPI per ogni livello nella topologia.
* La visualizzazione dei valori correnti dei nodi OPC UA le stazioni.
* L'aggregazione delle figure OEE e indicatori KPI dal livello della stazione a livello globale.
* La visualizzazione di avvisi e le azioni da eseguire se i valori raggiungono soglie specifiche.

## <a name="connected-factory-topology"></a>Topologia factory connesso

La topologia della factory, linee di produzione e le stazioni è gerarchica:

* A livello globale include nodi di factory come elementi figlio.
* La factory sono nodi di linea di produzione come elementi figlio.
* Le righe di produzione hanno nodi stazione come elementi figlio.
* Le stazioni (server UA OPC) sono nodi OPC UA come elementi figlio.

Ogni nodo nella topologia è associato un set comune di proprietà che definiscono:

* Identificatore univoco del nodo di topologia.
* Un nome.
* Una descrizione.
* Un'immagine.
* Gli elementi figlio del nodo di topologia.
* Minimo, destinazione e i valori massimi di figure OEE e indicatori KPI e le azioni di avviso per l'esecuzione.

## <a name="topology-configuration-file"></a>File di configurazione della topologia

Per configurare le proprietà elencate nella sezione precedente, la soluzione di factory connesso utilizza un file di configurazione denominato [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Questo file si trova nel codice sorgente soluzione nel `WebApp/Contoso/Topology` cartella.

Il frammento seguente mostra un sommario di `ContosoTopologyDescription.json` file di configurazione:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

Le proprietà comuni di `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`, e `<station_configuration>` sono:

* **Nome** (di tipo stringa)

  Definisce un nome descrittivo, che deve essere solo una parola per il nodo di topologia da visualizzare nel dashboard.

* **Descrizione** (di tipo stringa)

  Viene descritto il nodo di topologia in modo più dettagliato.

* **Immagine** (di tipo stringa)

  Il percorso di un'immagine nella soluzione WebApp da visualizzare quando le informazioni relative al nodo di topologia vengono visualizzate nel dashboard.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (tipo `<performance_definition>`)

  Queste proprietà definiscono minimo, destinazione e i valori massimo di operativo figura utilizzato per generare avvisi. Inoltre, queste proprietà definiscono le azioni da eseguire se viene rilevato un avviso.

Il `<factory_configuration>` e `<production_line_configuration>` elementi presentano una proprietà:

* **GUID** (di tipo stringa)

  Identifica in modo univoco il nodo di topologia.

`<factory_configuration>`dispone di una proprietà:

* **Percorso** (tipo `<location_definition>`)

  Specifica la factory in cui si trova.

`<station_configuration>`dispone di proprietà:

* **OpcUri** (di tipo stringa)

  Questa proprietà deve essere impostata per l'URI di applicazione OPC agente utente del server UA OPC.
  Poiché deve essere globalmente univoco dalla specifica OPC UA, questa proprietà viene utilizzata per identificare il nodo di topologia di stazione.

* **OpcNodes**, che sono una matrice di nodi OPC UA (tipo `<opc_node_description>`)

`<location_definition>`dispone di proprietà:

* **Città** (di tipo stringa)

  Nome di città vicino alla posizione

* **Paese** (di tipo stringa)

  Paese del percorso

* **Latitudine** (tipo double)

  Latitudine della posizione

* **Longitudine** (tipo double)

  Longitudine della località

`<performance_definition>`dispone di proprietà:

* **Minimo** (tipo double)

  Il valore di soglia inferiore può raggiungere. Se il valore corrente è sotto questa soglia, viene generato un avviso.

* **Destinazione** (tipo double)

  Valore di destinazione ideale.

* **Massimo** (tipo double)

  Grado di raggiungere il valore di soglia superiore. Se il valore corrente è superiore a questa soglia, viene generato un avviso.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Definisce il set di azioni che possono essere eseguite come risposta a un avviso minimo.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Definisce il set di azioni che possono essere eseguite come risposta a un avviso massimo.

`<alert_action`> dispone di proprietà:

* **Tipo** (di tipo stringa)

  Tipo dell'azione di avviso. I seguenti tipi sono noti:

  * **AcknowledgeAlert**: lo stato dell'avviso deve cambiare a riconosciuto.
  * **CloseAlert**: tutti gli avvisi precedenti dello stesso tipo non dovrebbero essere visualizzati nel dashboard.
  * **CallOpcMethod**: deve essere chiamato un metodo UA OPC.
  * **OpenWebPage**: una finestra del browser deve essere aperto con informazioni contestuali aggiuntive.

* **Descrizione** (di tipo stringa)

  Descrizione dell'azione nel dashboard.

* **Parametro** (di tipo stringa)

  Parametri necessari per eseguire l'azione. Il valore dipende dal tipo di azione.

  * **AcknowledgeAlert**: nessun parametro richiesto.
  * **CloseAlert**: nessun parametro richiesto.
  * **CallOpcMethod**: le informazioni sul nodo e i parametri del metodo OPC UA da chiamare nel formato "NodeId del nodo padre, NodeId del metodo da chiamare, l'URI del server OPC UA".
  * **OpenWebPage**: l'URL da visualizzare nella finestra del browser.

`<opc_node_description>`contiene informazioni su OPC UA nodi in una stazione (server UA OPC). I nodi che non rappresentano nodi OPC UA esistenti, ma vengono utilizzati come spazio di archiviazione nella logica di calcolo della factory connessi sono validi. Contiene le proprietà seguenti:

* **NodeId** (di tipo stringa)

  Indirizzo del UA OPC spazio degli indirizzi di nodo (server UA OPC) della stazione. La sintassi deve essere come specificato nella specifica OPC UA relativa a un NodeId.

* **Nome simbolico** (di tipo stringa)

  Nome da visualizzare nel dashboard quando viene visualizzato il valore di questo nodo UA OPC.

* **Pertinenza** (matrice di tipo stringa)

  Indica per cui calcolo del OEE o il valore del nodo OPC UA KPI è rilevante. Ogni elemento della matrice può essere uno dei valori seguenti:

  * **OeeAvailability_Running**: il valore è rilevante per il calcolo della disponibilità OEE.
  * **OeeAvailability_Fault**: il valore è rilevante per il calcolo della disponibilità OEE.
  * **OeePerformance_Ideal**: il valore è rilevante per il calcolo delle prestazioni OEE ed è in genere un valore costante.
  * **OeePerformance_Actual**: il valore è rilevante per il calcolo delle prestazioni OEE.
  * **OeeQuality_Good**: il valore è rilevante per il calcolo della qualità OEE.
  * **OeeQuality_Bad**: il valore è rilevante per il calcolo della qualità OEE.
  * **Kpi1**: il valore è rilevante per il calcolo della KPI1.
  * **Kpi2**: il valore è rilevante per il calcolo della KPI2.

* **Codice operativo** (di tipo stringa)

  Indica il valore del nodo OPC UA viene gestito in modo ora serie Insight query e calcoli di OEE/indicatore KPI. Ogni query tempo serie Insight destinato timespan specifico, che è un parametro della query e recapita un risultato. Il codice operativo controlla la modalità con cui il risultato viene calcolato e può essere uno dei valori seguenti:

  * **Diff**: differenza tra il primo valore e l'ultimo intervallo di tempo.
  * **AVG**: la media di tutti i valori di intervallo di tempo.
  * **Somma**: la somma di tutti i valori di intervallo di tempo.
  * **Ultimo**: attualmente non utilizzata.
  * **Conteggio**: il numero di valori nell'intervallo di tempo.
  * **Max**: il valore massimo nell'intervallo di tempo.
  * **Min**: il valore minimo nell'intervallo di tempo.
  * **Const**: il risultato è il valore specificato dalla proprietà ConstValue.
  * **SubMaxMin**: la differenza tra il massimo e il valore minimo.
  * **TimeSpan**: l'intervallo di tempo.

* **Unità** (di tipo stringa)

  Definisce un'unità del valore per la visualizzazione nel dashboard.

* **Visibile** (di tipo booleano)

  Controlla se il valore deve essere visualizzato nel dashboard.

* **ConstValue** (tipo double)

  Se il **OpCode** è **Const**, questa proprietà è il valore del nodo.

* **Minimo** (tipo double)

  Se il valore corrente scende di sotto di questo valore, viene generato un avviso minimo.

* **Massimo** (tipo double)

  Se il valore corrente genera oltre questo valore, viene generato un avviso di massimo.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Definisce il set di azioni che possono essere eseguite come risposta a un avviso minimo.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Definisce il set di azioni che possono essere eseguite come risposta a un avviso massimo.

A livello di stazione, anche vedere **simulazione** oggetti. Questi oggetti vengono utilizzati solo per configurare la simulazione di factory connesso e non devono essere usati per configurare una topologia reale.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Utilizzare i dati di configurazione in fase di esecuzione

Tutte le proprietà utilizzate nel file di configurazione possono essere raggruppate in categorie diverse a seconda della modalità di utilizzo. Tali categorie sono:

### <a name="visual-appearance"></a>Aspetto visivo

In questa categoria definiscono l'aspetto visivo del dashboard connessi factory. Tra gli esempi sono inclusi:

* NOME
* DESCRIZIONE
* Image
* Località
* Unità
* Visible

### <a name="internal-topology-tree-addressing"></a>Topologia interna indirizzamento di struttura ad albero

L'App Web gestisce un dizionario di dati interne che contiene informazioni di tutti i nodi della topologia. Le proprietà **Guid** e **OpcUri** vengono utilizzate come chiavi per accedere a questo dizionario e devono essere univoci.

### <a name="oeekpi-computation"></a>Calcolo OEE/KPI

Le cifre OEE/indicatore KPI per la simulazione di factory connessi vengono parametrizzate da:

* Valori del nodo OPC UA da includere nel calcolo.
* Come nella figura viene calcolata dai valori di dati di telemetria.

Factory connesso utilizza le formule OEE pubblicati dal http://oeeindustrystandard.oeefoundation.org.

Oggetti del nodo OPC UA in stazioni abilitare tag per l'utilizzo di calcolo OEE/indicatore KPI. Il **pertinenza** proprietà indica per quale figura OEE/KPI deve essere utilizzato il valore del nodo UA OPC. Il **OpCode** proprietà definisce la modalità con cui il valore è incluso nel calcolo.

### <a name="alert-handling"></a>Gestione degli avvisi

Factory connessi supporta un meccanismo semplice minimo o massimo in base a soglie generazione dell'avviso. Esistono una serie di azioni predefinite, che è possibile configurare in risposta a tali avvisi. Le proprietà seguenti controllano questo meccanismo:

* Massima
* Minima
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Correlazione di dati di telemetria

Per alcune operazioni, ad esempio l'ultimo valore di visualizzazione o la creazione di query di serie ora una visione, l'App Web è necessario uno schema di indirizzamento per i dati di telemetria acquisiti. Dati di telemetria inviati alla factory connesso deve inoltre essere archiviati nelle strutture dati interne. Le due proprietà, l'abilitazione di queste operazioni sono stazione (server UA OPC) e a livello di nodo OPC UA:

* **OpcUri**

  Identifica la telemetria server OPC UA (GUID) proviene. Nei messaggi acquisiti, questa proprietà viene inviata come **ApplicationUri**.

* **NodeId**

  Identifica il valore del nodo nel server UA OPC. Il formato della proprietà deve essere come specificato nella specifica OPC UA. Nei messaggi acquisiti, questa proprietà viene inviata come **NodeId**.

Controllare [questo](https://github.com/Azure/iot-edge-opc-publisher) pagina GitHub per ulteriori informazioni sulla modalità in cui vengono acquisiti i dati di telemetria per factory connesso utilizzando il server di pubblicazione OPC.

## <a name="example-how-kpi1-is-calculated"></a>Esempio: Come viene calcolata KPI1

La configurazione nel `ContosoTopologyDescription.json` file controlla la modalità di calcolo nelle figure OEE/indicatore KPI. Nell'esempio seguente viene illustrato come proprietà in questo file controllano il calcolo del KPI1.

In connesso factory di che kpi1 viene utilizzato per valutare correttamente il numero prodotto prodotti nell'ultima ora. Ogni stazione (server UA OPC) nella simulazione factory connesso consente a un nodo OPC UA (`NodeId: "ns=2;i=385"`), che fornisce dati di telemetria per calcolare l'indicatore KPI.

La configurazione per questo nodo OPC UA è simile nel frammento seguente:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Questa configurazione consente l'esecuzione di query dei valori di dati di telemetria di questo nodo con tempo serie Insights. Recupera la query ora serie Insights:

* Il numero di valori.
* Il valore minimo.
* Il valore massimo.
* La media di tutti i valori.
* La somma di tutti i valori per tutti univoci **OpcUri** (**ApplicationUri**), **NodeId** coppie in un intervallo di tempo specificato.

Una caratteristica del **NumberOfManufactureredProducts** valore del nodo è solo aumenta. Per calcolare il numero di prodotti nell'intervallo di tempo, connesso factory viene utilizzata la **OpCode** **SubMaxMin**. Il calcolo recupera il valore minimo all'inizio dell'intervallo di tempo e il valore massimo alla fine dell'intervallo di tempo.

Il **OpCode** nella configurazione consente di configurare la logica di calcolo per calcolare il risultato della differenza del valore minimo e massimo. Tali risultati vengono quindi accumulati inferiore fino al livello di radice (globale) e visualizzati nel dashboard.

## <a name="next-steps"></a>Passaggi successivi

Come suggerito il passaggio successivo consiste nell'imparare come [distribuire un gateway in Windows o Linux per la soluzione preconfigurata di fabbrica connessa](iot-suite-connected-factory-gateway-deployment.md).