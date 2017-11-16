---
title: Personalizzare la soluzione di fabbrica connessa - Azure | Microsoft Docs
description: Informazioni su come personalizzare il comportamento della soluzione preconfigurata di fabbrica connessa.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 09b146740413e74e3030bf3a6cb660a3cfabd239
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalizzare la modalità di visualizzazione dei dati dai server OPC UA da parte della soluzione di fabbrica connessa

La soluzione di fabbrica connessa aggrega e visualizza dati dai server OPC UA connessi alla soluzione. È possibile individuare e inviare comandi ai server OPC UA nella soluzione. Per altre informazioni su OPC UA, vedere le [domande frequenti sulla soluzione di connected factory](iot-suite-faq-cf.md).

Gli esempi di dati aggregati nella soluzione includono i valori OEE (Overall Equipment Efficiency) e gli indicatori KPI visualizzabili nel dashboard a livello di fabbrica, linea e stazione. Lo screenshot seguente mostra i valori OEE e gli indicatori KPI per la stazione **Assemblaggio** nella **Linea di produzione 1** di uno stabilimento a **Monaco**:

![Esempio di valori OEE e indicatori KPI nella soluzione][img-oee-kpi]

Questa soluzione consente di visualizzare informazioni dettagliate su dati specifici dai server OPC UA, chiamati *stazioni*. La schermata seguente mostra i tracciati del numero di articoli prodotti da una stazione specifica:

![Tracciati del numero di articoli prodotti][img-manufactured-items]

Facendo clic su uno dei grafici è possibile esplorare ulteriormente i dati tramite Time Series Insights (TSI):

![Esplorare i dati con Time Series Insights][img-tsi]

L'articolo illustra:

- Come i dati vengono resi disponibili per le diverse visualizzazioni nella soluzione.
- Come è possibile personalizzare il modo in cui la soluzione visualizza i dati.

## <a name="data-sources"></a>Origini dati

La soluzione di fabbrica connessa visualizza dati dai server OPC UA connessi alla soluzione. L'installazione predefinita include diversi server OPC UA che eseguono una simulazione di fabbrica. È possibile aggiungere i propri server OPC UA che [si connettono tramite un gateway][lnk-connect-cf] alla soluzione.

È possibile anche esplorare gli elementi dei dati che un server OPC UA connesso può inviare alla soluzione nel dashboard:

1. Passare alla vista **Selezionare un server OPC UA**:

    ![Passare alla vista Selezionare un server OPC UA][img-select-server]

1. Selezionare un server e fare clic su **Connetti**. Fare clic su **Procedi** quando viene visualizzato l'avviso di sicurezza.

    > [!NOTE]
    > L'avviso viene visualizzato una sola volta per ciascun server e stabilisce una relazione di trust tra quest'ultimo e il dashboard della soluzione.

1. È ora possibile esplorare gli elementi dei dati che il server può inviare alla soluzione. Gli elementi che vengono inviati alla soluzione hanno un segno di spunta verde:

    ![Elementi pubblicati][img-published]

1. Se si è un *amministratore* nella soluzione, è possibile scegliere di pubblicare un elemento di dati per renderlo disponibile nella soluzione di fabbrica connessa. L'amministratore può anche modificare il valore degli elementi di dati e chiamare i metodi nel server OPC UA.

## <a name="map-the-data"></a>Eseguire il mapping di dati

La soluzione di fabbrica connessa mappa e aggrega gli elementi di dati pubblicati dal server OPC UA alle varie viste nella soluzione. La soluzione di fabbrica connessa viene distribuita nell'account Azure dell'utente quando ne viene effettuato il provisioning. Le informazioni di mapping sono contenute in un file JSON nella soluzione di fabbrica connessa Visual Studio. È possibile visualizzare e modificare questo file di configurazione JSON nella soluzione di fabbrica connessa Visual Studi. È possibile ridistribuire la soluzione dopo aver apportato una modifica.

È possibile usare il file di configurazione per:

- Modificare le stazioni, le linee di produzione e gli stabilimenti simulati esistenti.
- Eseguire il mapping di dati dai server OPC UA effettivi che si connettono alla soluzione.

Per clonare una copia della soluzione di fabbrica connessa Visual Studio, usare il comando git seguente:

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

Il file **ContosoTopologyDescription.json** definisce il mapping dagli elementi di dati del server OPC UA alle visualizzazioni nel dashboard della soluzione di fabbrica connessa. Il file di configurazione si trova nella cartella **Contoso\Topology** all'interno del progetto **WebApp** soluzione Visual Studio.

Il contenuto del file JSON è organizzato come una gerarchia di nodi di stabilimento, linea di produzione e stazione, la quale definisce la gerarchia di navigazione nel dashboard di fabbrica connessa. I valori in ogni nodo della gerarchia determinano le informazioni visualizzate nel dashboard. Ad esempio, il file JSON contiene i valori seguenti per lo stabilimento di Monaco:

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

Nome, descrizione e posizione vengono visualizzati nel dashboard:

![Dati di Monaco nel dashboard][img-munich]

Ogni stabilimento, linea di produzione e stazione ha una proprietà immagine. Questi file JPEG si trovano nella cartella **Content\img** all'interno del progetto **WebApp** e vengono visualizzati nel dashboard fabbrica connessa.

Ogni stazione include diverse proprietà dettagliate che definiscono il mapping dagli elementi di dati OPC UA. Queste proprietà sono descritte nelle sezioni seguenti:

### <a name="opcuri"></a>OpcUri

Il valore **OpcUri** è l'URI dell'applicazione OPC UA che identifica in modo univoco il server OPC UA. Ad esempio, il valore **OpcUri** per la stazione di assemblaggio nella linea di produzione 1 a Monaco ha l'aspetto seguente: **urn:scada2194:ua:munich:productionline0:assemblystation**.

È possibile visualizzare gli URI dei server OPC UA connessi nel dashboard della soluzione:

![Visualizzare gli URI dei server OPC UA][img-server-uris]

### <a name="simulation"></a>Simulazione

Le informazioni contenute nel nodo **Simulazione** si riferiscono in modo specifico alla simulazione OPC UA eseguita nei server OPC UA di cui è stato eseguito il provisioning per impostazione predefinita. Non vengono usate per un server OPC UA reale.

### <a name="kpi1-and-kpi2"></a>Kpi1 e Kpi2

Questi nodi descrivono il contributo dei dati dalla stazione ai due indicatori KPI nel dashboard. In una distribuzione predefinita, questi indicatori KPI rappresentano unità all'ora e kWh all'ora. La soluzione calcola i valori degli indicatori KPI a livello di stazione e li aggrega ai livelli di linea di produzione e di stabilimento.

Ogni indicatore KPI ha un valore minimo, un valore massimo e un valore di destinazione e può anche definire azioni di avviso che devono essere eseguite dalla soluzione di fabbrica connessa. Il frammento di codice seguente mostra le definizioni degli indicatori KPI per la stazione di assemblaggio nella linea di produzione 1 a Monaco:

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

Lo screenshot seguente mostra i dati KPI nel dashboard.

![Informazioni relative ai KPI nel dashboard][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

I nodi **OpcNodes** identificano gli elementi dei dati pubblicati dal server OPC UA e ne specificano le modalità di elaborazione.

Il valore **NodeId** identifica il NodeID OPC UA specifico dal server OPC UA. Il primo nodo nella stazione di assemblaggio per la linea di produzione 1 a Monaco ha un valore **ns=2;i=385**. Il valore **NodeId** specifica l'elemento dati da leggere dal server OPC UA, mentre il valore **SymbolicName** fornisce un nome descrittivo da usare nel dashboard per tali dati.

Altri valori associati a ogni nodo sono riepilogati nella tabella riportata di seguito:

| Valore | Descrizione |
| ----- | ----------- |
| Pertinenza  | Gli indicatori KPI e i valori OEE cui contribuiscono questi dati. |
| OpCode     | Il modo in cui i dati vengono aggregati. |
| Unità      | Le unità da usare nel dashboard.  |
| Visible    | Indica se il valore viene visualizzato nel dashboard. Alcuni valori vengono usati nei calcoli, ma non visualizzati.  |
| Massima    | Il valore massimo che attiva un avviso nel dashboard. |
| MaximumAlertActions | Azione da intraprendere in risposta a un avviso. Ad esempio, l'invio di un comando a una stazione. |
| ConstValue | Un valore costante usato in un calcolo. |

## <a name="deploy-the-changes"></a>Distribuire le modifiche

Dopo avere apportato modifiche al file **ContosoTopologyDescription.json**, è necessario ridistribuire la soluzione di fabbrica connessa nel proprio account Azure.

Il repository **azure-iot-connected-factory** include uno script PowerShell **build.ps1** che è possibile usare per ricompilare e distribuire la soluzione.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla soluzione preconfigurata di fabbrica connessa sono disponibili nei seguenti articoli:

* [Procedura dettagliata per la soluzione preconfigurata di connected factory][lnk-rm-walkthrough]
* [Distribuire un gateway per fabbrica connessa][lnk-connect-cf]
* [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions]
* [Domande frequenti sulla soluzione di connected factory](iot-suite-faq-cf.md)
* [Domande frequenti][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-faq]: iot-suite-v1-faq.md