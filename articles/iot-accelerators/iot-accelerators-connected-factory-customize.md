---
title: Personalizzare la soluzione Connected Factory - Azure | Microsoft Docs
description: Informazioni su come personalizzare il comportamento dell'acceleratore di soluzioni di connected factory.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 7d4e95d066e191e1d5b6d083ede65843dbe73f31
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010073"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalizzare la modalità di visualizzazione dei dati dai server OPC UA da parte della soluzione Connected Factory

La soluzione Connected Factory aggrega e visualizza dati dai server OPC UA connessi alla soluzione. È possibile individuare e inviare comandi ai server OPC UA nella soluzione. Per altre informazioni su OPC UA, vedere le [domande frequenti su Connected Factory](iot-accelerators-faq-cf.md).

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

La soluzione Connected Factory visualizza dati dai server OPC UA connessi alla soluzione. L'installazione predefinita include diversi server OPC UA che eseguono una simulazione di fabbrica. È possibile aggiungere i propri server OPC UA che [si connettono tramite un gateway][lnk-connect-cf] alla soluzione.

È possibile anche esplorare gli elementi dei dati che un server OPC UA connesso può inviare alla soluzione nel dashboard:

1. Scegliere **Browser** per passare alla visualizzazione **Selezionare un server OPC UA**:

    ![Passare alla vista Selezionare un server OPC UA][img-select-server]

1. Selezionare un server e fare clic su **Connetti**. Fare clic su **Procedi** quando viene visualizzato l'avviso di sicurezza.

    > [!NOTE]
    > L'avviso viene visualizzato una sola volta per ciascun server e stabilisce una relazione di trust tra quest'ultimo e il dashboard della soluzione.

1. È ora possibile esplorare gli elementi dei dati che il server può inviare alla soluzione. Gli elementi che vengono inviati alla soluzione hanno un segno di spunta:

    ![Elementi pubblicati][img-published]

1. Gli *amministratori* della soluzione possono scegliere di pubblicare un elemento di dati per renderlo disponibile nella soluzione Connected Factory. L'amministratore può anche modificare il valore degli elementi di dati e chiamare i metodi nel server OPC UA.

## <a name="map-the-data"></a>Eseguire il mapping di dati

La soluzione Connected Factory mappa e aggrega gli elementi di dati pubblicati dal server OPC UA nelle varie visualizzazioni nella soluzione. La soluzione Connected Factory viene distribuita nell'account Azure dell'utente quando viene effettuato il provisioning della soluzione stessa. Le informazioni di mapping sono contenute in un file JSON nella soluzione Connected Factory di Visual Studio. È possibile visualizzare e modificare questo file di configurazione JSON nella soluzione Connected Factory di Visual Studio. È possibile ridistribuire la soluzione dopo aver apportato una modifica.

È possibile usare il file di configurazione per:

- Modificare le stazioni, le linee di produzione e gli stabilimenti simulati esistenti.
- Eseguire il mapping di dati dai server OPC UA effettivi che si connettono alla soluzione.

Per altre informazioni sul mapping e sull'aggregazione dei dati per soddisfare requisiti specifici, vedere [Come configurare l'acceleratore di soluzioni di connected factory](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Distribuire le modifiche

Dopo avere apportato le modifiche al file **ContosoTopologyDescription.json**, è necessario ridistribuire la soluzione Connected Factory al proprio account Azure.

Il repository **azure-iot-connected-factory** include uno script PowerShell **build.ps1** che è possibile usare per ricompilare e distribuire la soluzione.

## <a name="next-steps"></a>Fasi successive

Altre informazioni sull'acceleratore di soluzioni di connected factory sono disponibili negli articoli seguenti:

* [Procedura dettagliata dell'acceleratore di soluzioni di connected factory ][lnk-rm-walkthrough]
* [Distribuire un gateway per Connected Factory][lnk-connect-cf]
* [Autorizzazioni per il sito azureiotsolutions.com][lnk-permissions]
* [Domande frequenti su Connected Factory](iot-accelerators-faq-cf.md)
* [Domande frequenti][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md