---
title: Configurare la topologia di fabbrica connessa | Microsoft Docs
description: Come configurare la topologia di una soluzione preconfigurata di fabbrica connessa.
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="configure-the-connected-factory-preconfigured-solution"></a>Configurare la soluzione preconfigurata di fabbrica connessa

La soluzione preconfigurata di fabbrica connessa mostra un dashboard simulato per una società fittizia Contoso. Questa società ha fabbriche in numerose località a livello globale.

Questo articolo usa Contoso come esempio per descrivere come configurare la topologia di una soluzione di fabbrica connessa.

## <a name="simulated-factories-configuration"></a>Configurazione simulata delle fabbriche

Ogni fabbrica Contoso ha linee di produzione costituite da tre stazioni ognuna. Ogni stazione è un server OPC UA reale con un ruolo specifico:

* Stazione di assemblaggio
* Stazione di test
* Stazione di imballaggio

Questi server OPC UA hanno nodi OPC UA e il [server di pubblicazione OPC](https://github.com/Azure/iot-edge-opc-publisher) invia i valori di questi nodi alla fabbrica connessa. Sono inclusi:

* Stato operativo corrente, ad esempio il consumo di energia corrente.
* Informazioni relative alla produzione, ad esempio il numero di articoli prodotti.

È possibile usare il dashboard per esaminare la topologia della fabbrica Contoso partendo da una vista globale fino ad arrivare a una vista a livello di stazione. Il dashboard della fabbrica connessa offre:

* Visualizzazione dei valori OEE (Overall Equipment Efficiency) e KPI per ogni livello nella topologia.
* Visualizzazione dei valori correnti dei nodi OPC UA nelle stazioni.
* Aggregazione dei valori OEE e KPI dal livello di stazione al livello globale.
* Visualizzazione degli avvisi e delle azioni da eseguire se i valori raggiungono soglie specifiche.

## <a name="connected-factory-topology"></a>Topologia di fabbrica connessa

La topologia di fabbriche, linee di produzione e stazioni è gerarchica:

* Il livello globale ha nodi fabbrica come elementi figlio.
* La fabbriche hanno nodi linea di produzione come elementi figlio.
* Le linee di produzione hanno nodi stazione come elementi figlio.
* Le stazioni (server OPC UA) hanno i nodi OPC UA come elementi figlio.

Ogni nodo nella topologia ha un set comune di proprietà che definiscono:

* Un identificatore univoco per il nodo della topologia.
* Un nome.
* Una descrizione.
* Un'immagine.
* Gli elementi figlio del nodo della topologia.
* I valori OEE e KPI minimo, target e massimo e le azioni di avviso da eseguire.

## <a name="topology-configuration-file"></a>File di configurazione della topologia

Per configurare le proprietà elencate nella sezione precedente, la soluzione di fabbrica connessa usa un file di configurazione denominato [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

È possibile trovare questo file nel codice sorgente della soluzione nella cartella `WebApp/Contoso/Topology`.

Il frammento di codice seguente mostra la struttura del file di configurazione `ContosoTopologyDescription.json`:

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

Le proprietà comuni di `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>` e `<station_configuration>` sono:

* **Name** (tipo string)

  Definisce un nome descrittivo, che deve essere costituito da una sola parola per il nodo della topologia da visualizzare nel dashboard.

* **Description** (tipo string)

  Descrive il nodo della topologia in modo più dettagliato.

* **Image** (tipo string)

  Percorso di un'immagine nella soluzione app Web da visualizzare quando le informazioni relative al nodo della topologia vengono visualizzate nel dashboard.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (tipo `<performance_definition>`)

  Queste proprietà definiscono i valori minimo, target e massimo dei dati operativi usati per generare avvisi. Queste proprietà definiscono anche le azioni da eseguire se viene rilevato un avviso.

Gli elementi `<factory_configuration>` e `<production_line_configuration>` hanno una proprietà:

* **Guid** (tipo string)

  Identifica in modo univoco il nodo della topologia.

`<factory_configuration>` ha una proprietà:

* **Location** (tipo `<location_definition>`)

  Specifica la località dove si trova la fabbrica.

`<station_configuration>` ha le proprietà:

* **OpcUri** (tipo string)

  Questa proprietà deve essere impostata sull'URI dell'applicazione OPC UA del server OPC UA.
  Poiché in base alla specifica OPC UA deve essere univoca a livello globale, questa proprietà viene usata per identificare il nodo della topologia di tipo stazione.

* **OpcNodes**, una matrice di nodi OPC UA (tipo `<opc_node_description>`)

`<location_definition>` ha le proprietà:

* **City** (tipo string)

  Nome della città più vicina alla località

* **Country** (tipo string)

  Paese della località

* **Latitude** (tipo double)

  Latitudine della località

* **Longitude** (tipo double)

  Longitudine della località

`<performance_definition>` ha le proprietà:

* **Minimum** (tipo double)

  Soglia inferiore che il valore può raggiungere. Se il valore corrente è sotto questa soglia, viene generato un avviso.

* **Target** (tipo double)

  Valore target ideale.

* **Maximum** (tipo double)

  Soglia superiore che il valore può raggiungere. Se il valore corrente è sopra questa soglia, viene generato un avviso.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Definisce il set di azioni che è possibile eseguire in risposta a un avviso di raggiungimento del valore minimo.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Definisce il set di azioni che è possibile eseguire in risposta a un avviso di raggiungimento del valore massimo.

`<alert_action`> ha le proprietà:

* **Type** (tipo string)

  Tipo di azione di avviso. Di seguito sono elencati i tipi noti:

  * **AcknowledgeAlert**: lo stato dell'avviso deve essere impostato come confermato.
  * **CloseAlert**: tutti gli avvisi precedenti dello stesso tipo non devono più essere visualizzati nel dashboard.
  * **CallOpcMethod**: deve essere chiamato un metodo OPC UA.
  * **OpenWebPage**: deve venire aperta una finestra del browser con informazioni contestuali aggiuntive.

* **Description** (tipo string)

  Descrizione dell'azione indicata nel dashboard.

* **Parameter** (tipo string)

  Parametri necessari per eseguire l'azione. Il valore dipende dal tipo di azione.

  * **AcknowledgeAlert**: non sono necessari parametri.
  * **CloseAlert**: non sono necessari parametri.
  * **CallOpcMethod**: informazioni sul nodo e parametri del metodo OPC UA da chiamare nel formato "NodeId del nodo padre, NodeId del metodo da chiamare, URI del server OPC UA".
  * **OpenWebPage**: URL da visualizzare nella finestra del browser.

`<opc_node_description>` contiene informazioni sui nodi OPC UA in una stazione (server OPC UA). Sono validi anche i nodi che non rappresentano nodi OPC UA esistenti, ma vengono usati come spazio di archiviazione nella logica di calcolo della fabbrica connessa. Le proprietà sono le seguenti:

* **NodeId** (tipo string)

  Indirizzo del nodo OPC UA nello spazio degli indirizzi della stazione (server OPC UA). La sintassi deve essere quella indicata nella specifica OPC UA per un elemento NodeId.

* **SymbolicName** (tipo string)

  Nome da visualizzare nel dashboard quando viene visualizzato il valore di questo nodo OPC UA.

* **Relevance** (matrice di tipo string)

  Indica per quale calcolo del valore OEE o KPI è rilevante il valore del nodo OPC UA. Ogni elemento della matrice può essere uno dei valori seguenti:

  * **OeeAvailability_Running**: il valore è rilevante per il calcolo della disponibilità OEE.
  * **OeeAvailability_Fault**: il valore è rilevante per il calcolo della disponibilità OEE.
  * **OeePerformance_Ideal**: il valore è rilevante per il calcolo delle prestazioni OEE ed è in genere un valore costante.
  * **OeePerformance_Actual**: il valore è rilevante per il calcolo delle prestazioni OEE.
  * **OeeQuality_Good**: il valore è rilevante per il calcolo della qualità OEE.
  * **OeeQuality_Bad**: il valore è rilevante per il calcolo della qualità OEE.
  * **Kpi1**: il valore è rilevante per il calcolo dell'indicatore KPI1.
  * **Kpi2**: il valore è rilevante per il calcolo dell'indicatore KPI2.

* **OpCode** (tipo string)

  Indica come viene gestito il valore del nodo OPC UA nelle query Time Series Insights e nel calcolo dei valori OEE/KPI. Ogni query Time Series Insights viene eseguita su un intervallo di tempo specifico, che è un parametro della query e restituisce un risultato. La proprietà OpCode controlla come viene calcolato il risultato e può essere uno dei valori seguenti:

  * **Diff**: differenza tra l'ultimo valore e il primo valore nell'intervallo di tempo.
  * **Avg**: media di tutti i valori nell'intervallo di tempo.
  * **Sum**: somma di tutti i valori nell'intervallo di tempo.
  * **Last**: attualmente non in uso.
  * **Count**: numero di valori nell'intervallo di tempo.
  * **Max**: valore massimo nell'intervallo di tempo.
  * **Min**: valore minimo nell'intervallo di tempo.
  * **Const**: il risultato è il valore specificato dalla proprietà ConstValue.
  * **SubMaxMin**: differenza tra il valore massimo e il valore minimo.
  * **Timespan**: intervallo di tempo.

* **Units** (tipo string)

  Definisce un'unità del valore per la visualizzazione nel dashboard.

* **Visible** (tipo boolean)

  Controlla se il valore deve essere visualizzato nel dashboard.

* **ConstValue** (tipo double)

  Se **OpCode** è **Const**, questa proprietà è il valore del nodo.

* **Minimum** (tipo double)

  Se il valore corrente scende sotto questo valore, viene generato un avviso di raggiungimento del valore minimo.

* **Maximum** (tipo double)

  Se il valore corrente sale sopra questo valore, viene generato un avviso di raggiungimento del valore massimo.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Definisce il set di azioni che è possibile eseguire in risposta a un avviso di raggiungimento del valore minimo.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Definisce il set di azioni che è possibile eseguire in risposta a un avviso di raggiungimento del valore massimo.

A livello di stazione, sono presenti anche oggetti **Simulation**. Questi oggetti vengono usati solo per configurare la simulazione di fabbrica connessa e non devono essere usati per configurare una topologia reale.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Modalità d'uso dei dati di configurazione in fase di esecuzione

Tutte le proprietà usate nel file di configurazione possono essere raggruppate in categorie diverse a seconda della modalità d'uso. Le categorie sono:

### <a name="visual-appearance"></a>Aspetto visivo

Le proprietà in questa categoria definiscono l'aspetto visivo del dashboard della fabbrica connessa. Tra gli esempi sono inclusi:

* NOME
* DESCRIZIONE
* Image
* Località
* Unità
* Visible

### <a name="internal-topology-tree-addressing"></a>Indirizzamento dell'albero della topologia interna

L'app Web gestisce un dizionario dei dati interno che contiene informazioni su tutti i nodi della topologia. Le proprietà **Guid** e **OpcUri** vengono usate come chiavi per accedere a questo dizionario e devono essere univoche.

### <a name="oeekpi-computation"></a>Calcolo dei valori OEE/KPI

I valori OEE/KPI per la simulazione di fabbrica connessa sono parametrizzati in base a:

* Valori del nodo OPC UA da includere nel calcolo.
* Modalità di calcolo del valore dai valori di telemetria.

La fabbrica connessa usa le formule OEE pubblicate da http://oeeindustrystandard.oeefoundation.org.

Gli oggetti nodo OPC UA nelle stazioni consentono l'uso di tag nel calcolo dei valori OEE/KPI. La proprietà **Relevance** indica per quale valore OEE/KPI deve essere usato il valore del nodo OPC UA. La proprietà **OpCode** definisce come viene incluso il valore nel calcolo.

### <a name="alert-handling"></a>Gestione degli avvisi

La fabbrica connessa supporta un semplice meccanismo di generazione di avvisi basati sulla soglia minima o massima. Ci sono diverse azioni predefinite che è possibile configurare in risposta agli avvisi. Il meccanismo è controllato dalle proprietà seguenti:

* Maximum
* Minimum
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Correlazione ai dati di telemetria

Per alcune operazioni, ad esempio la visualizzazione dell'ultimo valore o la creazione di query Time Series Insight, l'app Web necessita di uno schema di indirizzamento per i dati di telemetria inseriti. I dati di telemetria inviati alla fabbrica connessa devono inoltre essere archiviati nelle strutture dei dati interne. Le due proprietà che consentono queste operazioni sono a livello di stazione (server OPC UA) e a livello di nodo OPC UA:

* **OpcUri**

  Identifica (in modo univoco globale) il server OPC UA da cui provengono i dati di telemetria. Nei messaggi inseriti questa proprietà viene inviata come **ApplicationUri**.

* **NodeId**

  Identifica il valore del nodo nel server OPC UA. Il formato della proprietà deve essere quello indicato nella specifica OPC UA. Nei messaggi inseriti questa proprietà viene inviata come **NodeId**.

Vedere [questa](https://github.com/Azure/iot-edge-opc-publisher) pagina di GitHub per altre informazioni sulla modalità di inserimento dei dati di telemetria nella fabbrica connessa tramite il server di pubblicazione OPC.

## <a name="example-how-kpi1-is-calculated"></a>Esempio: modalità di calcolo dell'indicatore KPI1

La configurazione nel file `ContosoTopologyDescription.json` controlla la modalità di calcolo dei valori OEE/KPI. L'esempio seguente illustra come le proprietà in questo file controllano il calcolo dell'indicatore KPI1.

Nella fabbrica connessa l'indicatore KPI1 viene usato per misurare il numero di articoli prodotti nell'ultima ora. Ogni stazione (server OPC UA) nella simulazione di fabbrica connessa fornisce un nodo OPC UA (`NodeId: "ns=2;i=385"`), che fornisce i dati di telemetria per calcolare l'indicatore KPI.

La configurazione di questo nodo OPC UA è simile al frammento di codice seguente:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Questa configurazione consente l'esecuzione di query sui valori di telemetria di questo nodo con Time Series Insights. La query Time Series Insights recupera:

* Numero di valori.
* Valore minimo.
* Valore massimo.
* Media di tutti i valori.
* Somma di tutti i valori per tutte le coppie **OpcUri** (**ApplicationUri**), **NodeId** univoche in un intervallo di tempo specificato.

Una caratteristica del valore del nodo **NumberOfManufactureredProducts** è che può solo aumentare. Per calcolare il numero di articoli prodotti nell'intervallo di tempo, la fabbrica connessa usa **OpCode** **SubMaxMin**. Il calcolo recupera il valore minimo all'inizio dell'intervallo di tempo e il valore massimo alla fine dell'intervallo di tempo.

**OpCode** nella configurazione consente di configurare la logica di calcolo per calcolare il risultato della differenza tra valore massimo e minimo. Questi risultati vengono quindi accumulati dal basso verso l'altro fino al livello radice (globale) e visualizzati nel dashboard.

## <a name="next-steps"></a>Passaggi successivi

Come suggerito il passaggio successivo consiste nell'imparare come [distribuire un gateway in Windows o Linux per la soluzione preconfigurata di fabbrica connessa](iot-suite-connected-factory-gateway-deployment.md).