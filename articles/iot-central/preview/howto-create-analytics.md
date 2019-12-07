---
title: Analizzare i dati dei dispositivi nell'applicazione IoT Central di Azure | Microsoft Docs
description: Analizzare i dati dei dispositivi nell'applicazione IoT Central di Azure.
author: ankitgup
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: e4cf4469833e28a594996b981a47a964131026a7
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895716"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Come usare Analytics per analizzare i dati del dispositivo

*Questo articolo è rivolto a operatori, autori e amministratori.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central offre funzionalità di analisi avanzate per analizzare le tendenze cronologiche e correlare diverse telemetrie dai dispositivi. Per iniziare, visitare **Analytics** nel riquadro sinistro.

## <a name="understanding-the-analytics-ui"></a>Informazioni sull'interfaccia utente di Analytics
L'interfaccia utente di Analytics è costituita da tre componenti principali:
- **Pannello configurazione dati:** Nel pannello configurazione, iniziare selezionando il gruppo di dispositivi per il quale si desidera analizzare i dati. Selezionare quindi i dati di telemetria che si desidera analizzare e selezionare il metodo di aggregazione per ogni telemetria. **Split by** Control consente di raggruppare i dati usando le proprietà del dispositivo come dimensioni.

- **Controllo temporale:** Il controllo del tempo viene usato per selezionare la durata per cui si desidera analizzare i dati. È possibile trascinare entrambe le estremità del dispositivo di scorrimento ora per selezionare l'intervallo di tempo. Il controllo dell'ora dispone anche di un dispositivo di scorrimento **Dimensioni intervallo** che controlla il bucket o la dimensione dell'intervallo utilizzata per aggregare i dati. 

- **Controllo Chart:** Il controllo Chart Visualizza i dati sotto forma di grafico a linee. È possibile impostare la visibilità di linee specifiche interagendo con la legenda del grafico. 


  ![Panoramica dell'interfaccia utente di Analytics](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Query dei dati

È necessario iniziare scegliendo un **gruppo di dispositivi**e i dati di telemetria che si desidera analizzare. Al termine, selezionare **analizza** per iniziare a visualizzare i dati.

- **Gruppo di dispositivi:** Un [gruppo](tutorial-use-device-groups.md) di dispositivi è un gruppo di dispositivi definito dall'utente. Ad esempio, tutti i frigoriferi in Oakland o tutte le turbine eoliche della versione 2,0.

- **Telemetria:** Selezionare i dati di telemetria che si vuole analizzare ed esplorare. È possibile selezionare più telemetrie da analizzare insieme. Il metodo di aggregazione predefinito è impostato su Average per Numerical e Count per il tipo di dati String rispettivamente. I metodi di aggregazione supportati per i tipi di dati numerici sono Average, Maximum, Minimum, Count e, Sum.  I metodi di aggregazione supportati per il tipo di dati String sono Count.

- **Dividi per:** Il controllo ' Split by ' consente di raggruppare i dati usando le proprietà del dispositivo come dimensioni. I valori delle proprietà del dispositivo e del cloud vengono uniti insieme ai dati di telemetria come e quando vengono inviati dal dispositivo. Se la proprietà del cloud o del dispositivo è stata aggiornata, i dati di telemetria vengono raggruppati in base a valori diversi nel grafico.

    > [!TIP]
    > Per visualizzare separatamente i dati per ogni dispositivo, selezionare ID dispositivo nel controllo ' Split by '.

## <a name="interacting-with-your-data"></a>Interazione con i dati

Dopo aver eseguito una query sui dati, è possibile iniziare a visualizzarli sul grafico a linee. È possibile visualizzare/nascondere la telemetria, modificare la durata, visualizzare i dati di telemetria in una griglia di dati.

- **Pannello Editor temporale:** Per impostazione predefinita, recuperiamo i dati dell'ultimo giorno. È possibile trascinare una delle estremità del dispositivo di scorrimento ora per modificare la durata dell'ora. È anche possibile usare il controllo Calendar per selezionare uno dei bucket temporali predefiniti oppure selezionare un intervallo di tempo personalizzato. Il controllo dell'ora dispone anche di un dispositivo di scorrimento **Dimensioni intervallo** che controlla il bucket o la dimensione dell'intervallo utilizzata per aggregare i dati.

    ![Editor del tempo](media/howto-create-analytics/timeeditorpanel.png)

    - **Strumento di scorrimento intervallo di date interno**: usare i due controlli endpoint trascinandoli nell'intervallo di tempo desiderato. Questo intervallo di date interno è vincolato dal controllo dispositivo di scorrimento dell'intervallo di date esterno.
    
   
    - **Controllo dispositivo di scorrimento intervallo di date esterno**: usare i controlli endpoint per selezionare l'intervallo di date esterno, che sarà disponibile per il controllo dell'intervallo di date interno.

    - **Aumentare e ridurre i pulsanti di intervallo di date**: aumentare o ridurre l'intervallo di tempo selezionando uno dei due pulsanti per l'intervallo desiderato.

    - **Dispositivo di scorrimento Dimensioni intervallo**: usarlo per ingrandire e ridurre gli intervalli nello stesso intervallo di tempo. Questa azione garantisce un controllo più preciso dello spostamento tra intervalli di tempo di particolare entità. È possibile usarlo per visualizzare visualizzazioni granulari e ad alta risoluzione dei dati, anche fino a millisecondi. Il punto di partenza predefinito del dispositivo di scorrimento è impostato come visualizzazione ottimale dei dati della selezione, che bilancia la risoluzione, la velocità delle query e la granularità.
    
    - **Selezione intervallo di date**: con questo controllo Web è possibile selezionare facilmente gli intervalli di data e ora desiderati. È anche possibile usare il controllo per passare da una fascia oraria a un'altra. Dopo avere apportato le modifiche da applicare all'area di lavoro corrente, selezionare Salva.

    > [!TIP]
    > La dimensione dell'intervallo viene determinata in modo dinamico in base all'intervallo di tempo selezionato. Gli intervalli di tempo più piccoli consentiranno di aggregare i dati in intervalli molto granulari fino a pochi secondi.


- **Legenda grafico:** Legenda grafico mostra i dati di telemetria selezionati sul grafico. È possibile passare il mouse su ogni elemento della legenda per attivarlo nel grafico. Quando si usa ' Split by ', i dati di telemetria vengono raggruppati in base ai rispettivi valori della dimensione selezionata. È possibile impostare la visibilità di ogni telemetria specifica o l'intero gruppo facendo clic sul nome del gruppo.  


- **Controllo del formato dell'asse y:** la modalità dell'asse y scorre le opzioni di visualizzazione asse y disponibili. Questo controllo è disponibile solo quando vengono visualizzate diverse telemetrie. È possibile impostare l'asse y selezionando una delle tre modalità seguenti:

    - In **Pila:** Un grafico per ogni telemetria è in pila e ognuno dei grafici ha un proprio asse y. Questa modalità è impostata come predefinita.
    - **Condiviso:** Un grafico per ogni telemetria viene tracciato con lo stesso asse y.
    - **Sovrapposizione:** Usarlo per eseguire lo stack di più righe sullo stesso asse y, con la modifica dei dati dell'asse y in base alla riga selezionata.

  ![Disporre i dati tra l'asse y con diverse modalità di visualizzazione](media/howto-create-analytics/yaxiscontrol.png)

- **Controllo zoom:** Zoom consente di eseguire ulteriormente il drill-down dei dati. Se si trova un periodo di tempo in cui si vuole concentrare l'attenzione nel set di risultati, usare il puntatore del mouse per selezionare l'area e trascinarla nell'endpoint desiderato. Fare quindi clic con il pulsante destro del mouse sull'area selezionata e scegliere Zoom.

  ![Ingrandire i dati](media/howto-create-analytics/zoom.png)

Sotto i puntini di sospensione sono presenti più controlli Chart per interagire con i dati.

- **Griglia di visualizzazione:** I risultati sono disponibili in formato tabella, consentendo di visualizzare il valore specifico per ogni punto dati.

- **Rilasciare un marcatore:** Il controllo ' drop marker ' consente di ancorare determinati punti dati nel grafico. È utile quando si tenta di confrontare i dati di più righe tra periodi di tempo diversi.

  ![Passare alla visualizzazione griglia per l'analitica](media/howto-create-analytics/additionalchartcontrols.png)