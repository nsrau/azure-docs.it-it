---
title: Analizzare i dati del dispositivo nell'applicazione Azure IoT Central Documenti Microsoft
description: Analizzare i dati del dispositivo nell'applicazione Azure IoT Central.Analyze device data in your Azure IoT Central application.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 40460b58ede0ca0da8fe25475906bdbe41bfffe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158283"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Come usare l'analisi per analizzare i dati del dispositivoHow to use analytics to analyze device data

*Questo articolo è rivolto a operatori, autori e amministratori.*



Azure IoT Central offre funzionalità di analisi avanzate per analizzare le tendenze cronologiche e correlare varie telemetrie dai dispositivi. Per iniziare, visita **Analytics** nel riquadro sinistro.

## <a name="understanding-the-analytics-ui"></a>Informazioni sull'interfaccia utente di AnalyticsUnderstanding the Analytics UI
L'interfaccia utente di Analytics è costituita da tre componenti principali:
- **Pannello di configurazione dei dati:** Nel pannello di configurazione, iniziare selezionando il gruppo di dispositivi per il quale si desidera analizzare i dati. Selezionare quindi i dati di telemetria che si desidera analizzare e selezionare il metodo di aggregazione per ogni dati di telemetria. Il controllo **Dividi per** consente di raggruppare i dati usando le proprietà del dispositivo come dimensioni.

- **Controllo del tempo:** Il controllo temporale viene utilizzato per selezionare la durata per la quale si desidera analizzare i dati. È possibile trascinare una delle estremità del dispositivo di scorrimento temporale per selezionare l'intervallo di tempo. Il controllo del tempo dispone anche di un dispositivo di scorrimento **Dimensioni intervallo** che controlla il bucket o la dimensione dell'intervallo utilizzato per aggregare i dati. 

- **Controllo grafico:** Il controllo Chart visualizza i dati come un grafico a linee. È possibile attivare o disattivare la visibilità di linee specifiche interagendo con la legenda del grafico. 


  ![Panoramica dell'interfaccia utente di Analytics](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Query dei dati

È necessario iniziare scegliendo un gruppo di **dispositivi**e i dati di telemetria che si desidera analizzare. Al termine, seleziona **Analizza** per iniziare a visualizzare i dati.

- **Gruppo di dispositivi:** Un [gruppo](tutorial-use-device-groups.md) di dispositivi è un gruppo definito dall'utente dei dispositivi. Ad esempio, tutti i refrigeratori a Oakland o tutte le turbine eoliche della versione 2.0.

- **Telemetria:** Selezionare i dati di telemetria che si desidera analizzare ed esplorare. È possibile selezionare più telemetrie da analizzare insieme. Il metodo di aggregazione predefinito è impostato su Average per numerical e Count rispettivamente per il tipo di dati string. I metodi di aggregazione supportati per i tipi di dati Numeric sono Average, Maximum, Minimum, Count e, Sum.  I metodi di aggregazione supportati per il tipo di dati stringa vengono conteggiati.

- **Dividi per:** Il controllo 'Dividi per' consente di raggruppare i dati usando le proprietà del dispositivo come dimensioni. I valori delle proprietà del dispositivo e del cloud vengono uniti insieme ai dati di telemetria come e quando viene inviato dal dispositivo. Se la proprietà del cloud o del dispositivo è stata aggiornata, i dati di telemetria verranno raggruppati in base a valori diversi nel grafico.

    > [!TIP]
    > Per visualizzare i dati per ogni dispositivo separatamente, selezionare ID dispositivo nel controllo 'Dividi per'.

## <a name="interacting-with-your-data"></a>Interazione con i dati

Dopo aver eseguito una query sui dati, è possibile iniziare a visualizzarlo nel grafico a linee. È possibile mostrare/nascondere i dati di telemetria, modificare la durata, visualizzare i dati di telemetria in una griglia di dati.

- **Pannello editor temporale:** Per impostazione predefinita, recupereremo i dati dell'ultimo giorno. È possibile trascinare una delle estremità del dispositivo di scorrimento temporale per modificare la durata. È inoltre possibile utilizzare il controllo calendario per selezionare uno degli intervalli di tempo predefiniti o selezionare un intervallo di tempo personalizzato. Il controllo del tempo dispone anche di un dispositivo di scorrimento **Dimensioni intervallo** che controlla il bucket o la dimensione dell'intervallo utilizzato per aggregare i dati.

    ![Editor del tempo](media/howto-create-analytics/timeeditorpanel.png)

    - Strumento di **scorrimento dell'intervallo**di date interno: usare i due controlli endpoint trascinandoli nell'intervallo di tempo desiderato. Questo intervallo di date interno è vincolato dal controllo dispositivo di scorrimento dell'intervallo di date esterno.
    
   
    - Controllo dispositivo di **scorrimento dell'intervallo**di date esterno: usare i controlli endpoint per selezionare l'intervallo di date esterno, che sarà disponibile per il controllo dell'intervallo di date interno.

    - **Aumentare e ridurre i pulsanti dell'intervallo**di date : Aumentare o ridurre l'intervallo di tempo selezionando uno dei pulsanti per l'intervallo desiderato.

    - **Cursore dimensione intervallo**: utilizzarlo per ingrandire e ridurre gli intervalli nello stesso intervallo di tempo. Questa azione garantisce un controllo più preciso dello spostamento tra intervalli di tempo di particolare entità. È possibile utilizzarlo per visualizzare visualizzazioni granulari e ad alta risoluzione dei dati, anche fino a millisecondi. Il punto di partenza predefinito del dispositivo di scorrimento viene impostato come la visualizzazione ottimale dei dati della selezione, che consente di bilanciare la risoluzione, la velocità delle query e la granularità.
    
    - **Selezione intervallo**di date : Con questo controllo web, è possibile selezionare facilmente gli intervalli di data e ora desiderati. È anche possibile usare il controllo per passare da una fascia oraria a un'altra. Dopo aver apportato le modifiche da applicare all'area di lavoro corrente, selezionare Salva.

    > [!TIP]
    > La dimensione dell'intervallo viene determinata dinamicamente in base all'intervallo di tempo selezionato. Intervalli di tempo più piccoli consentiranno l'aggregazione dei dati in intervalli molto granulari fino a pochi secondi.


- **Legenda grafico:** La legenda del grafico mostra i dati di telemetria selezionati nel grafico. È possibile passare il mouse su ogni elemento della legenda per mettere a fuoco il grafico. Quando si usa 'Dividi per', i dati di telemetria vengono raggruppati in base ai rispettivi valori della dimensione selezionata. È possibile attivare o disattivare la visibilità di ogni telemetria specifica o dell'intero gruppo facendo clic sul nome del gruppo.  


- **Controllo del formato dell'asse Y:** la modalità dell'asse y scorre le opzioni di visualizzazione dell'asse y disponibili. Questo controllo è disponibile solo quando vengono visualizzate telemetrie diverse. È possibile impostare l'asse y scegliendo una delle tre modalità seguenti:

    - **In pila:** Un grafico per ogni telemetria è impilato e ognuno dei grafici ha il proprio asse y. Questa modalità è impostata come predefinita.
    - **Condiviso:** Un grafico per ogni telemetria viene tracciato sullo stesso asse y.
    - **Sovrapposizione:** Utilizzatelo per impilare più linee sullo stesso asse y, con la modifica dei dati dell'asse y in base alla linea selezionata.

  ![Disporre i dati tra l'asse y con diverse modalità di visualizzazione](media/howto-create-analytics/yaxiscontrol.png)

- **Controllo zoom:** Lo zoom consente di approfondire ulteriormente i dati. Se si trova un periodo di tempo su cui si vuole concentrarsi all'interno del set di risultati, utilizzare il puntatore del mouse per acquisire l'area e trascinarla nel punto finale desiderato. Quindi fare clic con il pulsante destro del mouse sull'area selezionata e fare clic su Ingrandimento.

  ![Ingrandire i dati](media/howto-create-analytics/zoom.png)

Sotto i lipsia, ci sono più controlli del grafico per interagire con i dati.

- **Griglia di visualizzazione:** I risultati sono disponibili in formato tabella, consentendo di visualizzare il valore specifico per ogni punto dati.

- **Rilasciare un marcatore:** Il controllo 'Drop Marker' consente di ancorare determinati punti dati nel grafico. È utile quando si tenta di confrontare i dati per più righe in periodi di tempo diversi.

  ![Passare alla visualizzazione griglia per l'analitica](media/howto-create-analytics/additionalchartcontrols.png)