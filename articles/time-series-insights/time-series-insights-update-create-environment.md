---
title: Esercitazione su Azure Time Series Insights (anteprima) | Microsoft Docs
description: Informazioni su Azure Time Series Insights (anteprima)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: ed25d03f7c592476b9284790ac12f9954661a42b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872306"
---
# <a name="azure-time-series-insights-preview-tutorial"></a>Esercitazione su Azure Time Series Insights (anteprima)

Questa esercitazione illustra come creare un ambiente Azure Time Series Insights (TSI) in versione di anteprima, popolato con i dati provenienti da dispositivi simulati. In questa esercitazione si apprenderà come:

* Creare un ambiente Time Series Insights (anteprima).
* Connettere l'ambiente Time Series Insights (anteprima) a un hub eventi.
* Eseguire una simulazione relativa a una centrale eolica per trasmettere dati nell'ambiente di anteprima di Time Series Insights.
* Eseguire un'analisi di base sui dati.
* Definire un tipo di modello di serie temporale e una gerarchia da associare alle istanze.

## <a name="create-a-time-series-insights-preview-environment"></a>Creare un ambiente Time Series Insights (anteprima)

Questa sezione illustra come creare un ambiente Time Series Insights (anteprima) usando il [portale di Azure](https://portal.azure.com/).

1. Accedere al portale di Azure usando l'account della sottoscrizione personale
1. Selezionare **+ Crea una risorsa** in alto a sinistra.
1. Selezionare la categoria **Internet delle cose** e quindi **Time Series Insights**.

  ![Esercitazione - fase 1][1]

1. Nella pagina dell'ambiente Time Series Insights inserire i parametri richiesti e fare clic su **Avanti: Origine evento**

  ![Esercitazione - fase 2][2]

1. Nella pagina  **Origine evento** inserire i parametri richiesti e fare clic su **Rivedi e crea**.

  ![Esercitazione - fase 3][3]

1. Esaminare tutti i dettagli e fare clic su **Crea** per avviare il provisioning dell'ambiente.

  ![Esercitazione - fase 4][4]

1. Una volta completata la distribuzione, si riceverà una notifica.

  ![Esercitazione - fase 5][5]

## <a name="send-events-to-your-tsi-environment"></a>Inviare eventi all'ambiente Time Series Insights

In questa sezione si userà un simulatore di dispositivo di tipo mulino a vento per inviare eventi all'ambiente Time Series Insights tramite un hub eventi.

  1. Nel portale di Azure passare alla risorsa hub eventi e connetterla all'ambiente Time Series Insights. Leggere le informazioni su [come connettere la risorsa a un hub eventi esistente](./time-series-insights-how-to-add-an-event-source-eventhub.md).

  1. Nella pagina della risorsa hub eventi passare a **Criteri di accesso condivisi** e quindi a **RootManageSharedAccessKey**. Copiare il valore di **Connection string-primary key**  (Stringa di connessione - chiave primaria) visualizzato.

      ![Esercitazione - fase 6][6]

  1. Passare a [https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html). Questa app Web simula i dispositivi di tipo mulino a vento.
  1. Incollare la stringa di connessione copiata nel passaggio 3 in **Event Hub Connection String** (Stringa di connessione hub eventi)

      ![Esercitazione - fase 7][7]

  1. Fare clic su **Click to Start** (Fare clic per avviare) per eseguire il push degli eventi all'hub eventi. In questa fase verrà scaricato nel computer un file denominato `instances.json`. Salvare il file, che sarà necessario più avanti.

  1. Tornare all'hub eventi. Dovrebbero ora venire visualizzati i nuovi eventi ricevuti dall'hub.

     ![Esercitazione - fase 8][8]

## <a name="analyze-data-in-your-environment"></a>Analizzare i dati nell'ambiente

In questa sezione verranno eseguite attività di analisi di base sui dati delle serie temporali usando lo strumento di esplorazione aggiornato di Time Series Insights.

  1. Passare allo strumento di esplorazione aggiornato di Time Series Insights facendo clic sull'URL nella pagina delle risorse nel portale di Azure.

      ![Esercitazione - fase 9][9]

  1. Nello strumento di esplorazione fare clic sui nodi **Unparented Instances** (Istanze senza padre) per visualizzare tutte le istanze di serie temporali nell'ambiente.

     ![Esercitazione - fase 10][10]

  1. In questa esercitazione verranno analizzati i dati inviati nell'ultimo giorno. A tale scopo, fare clic su **Tempi rapidi** e selezionare l'opzione **Ultime 24 ore**.

     ![Esercitazione - fase 11][11]

  1. Selezionare l'elemento **Sensor_0** e scegliere **Show Avg Value** (Mostra valore medio) per visualizzare i dati inviati da questa istanza di serie temporale.

     ![Esercitazione - fase 12][12]

  1. Analogamente, è possibile tracciare i dati provenienti da altre istanze di serie temporali per eseguire attività di analisi di base.

     ![Esercitazione - fase 13][13]

## <a name="define-a-type--hierarchy"></a>Definire un tipo e una gerarchia 

In questa sezione verranno creati un tipo e una gerarchia, che verranno associati alle istanze di serie temporali. Leggere altre informazioni sui [modelli di serie temporali](./time-series-insights-update-tsm.md).

  1. Nello strumento di esplorazione fare clic sulla scheda **Modello** sulla barra dell'app.

     ![Esercitazione - fase 14][14]

  1. Nella sezione Tipi fare clic su **+ Aggiungi**. Sarà così possibile creare un nuovo tipo di modello di serie temporale.

     ![Esercitazione - fase 15][15]

  1. Nell'editor di tipi immettere **Nome** e **Descrizione** e creare variabili per i valori **Media**, **Min** e **Max**, come illustrato di seguito. Fare clic su **Crea** per salvare il tipo.

     ![Esercitazione - fase 16][16]

     ![Esercitazione - fase 17][17]

  1. Nella sezione **Gerarchie** fare clic su **+ Aggiungi**. Sarà così possibile creare una nuova gerarchia del modello di serie temporale.

     ![Esercitazione - fase 18][18]

  1. Nell'editor di gerarchie immettere un valore per **Nome** e aggiungere i livelli della gerarchia, come illustrato di seguito. Fare clic su **Crea** per salvare la gerarchia.

     ![Esercitazione - fase 19][19]

     ![Esercitazione - fase 20][20]

  1. Nella sezione **Istanze** selezionare un'istanza e fare clic su **Modifica**. Sarà così possibile associare un tipo e una gerarchia a questa istanza.

     ![Esercitazione - fase 21][21]

  1. Nell'editor di istanze scegliere il tipo e la gerarchia definiti nei passaggi 3 e 5 in precedenza, come illustrato.

     ![Esercitazione - fase 22][22]

  1. In alternativa, per eseguire questa operazione per tutte le istanze contemporaneamente, è possibile modificare il file `instances.json` scaricato in precedenza. In questo file sostituire tutti i campi **typeId** e **hierarchyId** con l'ID ottenuto nei passaggi 3 e 5 precedenti.

  1. Nella sezione **Istanze** fare clic su **Carica JSON** e caricare il file `instances.json` modificato, come illustrato di seguito.

     ![Esercitazione - fase 23][23]

  1. Passare alla scheda **Analisi** e aggiornare il browser. Dovrebbero ora venire visualizzate tutte le istanze associate al tipo e alla gerarchia definiti in precedenza.

     ![Esercitazione - fase 24][24]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:  

* Creare un ambiente Time Series Insights (anteprima).
* Connettere l'ambiente Time Series Insights (anteprima) a un hub eventi.
* Eseguire una simulazione relativa a una centrale eolica per trasmettere dati nell'ambiente Time Series Insights (anteprima).
* Eseguire un'analisi di base sui dati.
* Definire un tipo di modello di serie temporale e una gerarchia da associare alle istanze.

Ora che si è appreso come creare un ambiente aggiornato di Time Series Insights, leggere altre informazioni sui concetti principali di Time Series Insights.

Leggere le informazioni sulla configurazione dell'archiviazione di Time Series Insights:

> [!div class="nextstepaction"]
> [Archiviazione e dati in ingresso di Azure Time Series Insights (anteprima)](./time-series-insights-update-storage-ingress.md)

Leggere altre informazioni sui modelli di serie temporali:

> [!div class="nextstepaction"]
> [Modellazione dei dati di Azure Time Series Insights (anteprima)](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/tutorial-one.png
[2]: media/v2-update-provision/tutorial-two.png
[3]: media/v2-update-provision/tutorial-three.png
[4]: media/v2-update-provision/tutorial-four.png
[5]: media/v2-update-provision/tutorial-five.png
[6]: media/v2-update-provision/tutorial-six.png
[7]: media/v2-update-provision/tutorial-seven.png
[8]: media/v2-update-provision/tutorial-eight.png
[9]: media/v2-update-provision/tutorial-nine.png
[10]: media/v2-update-provision/tutorial-ten.png
[11]: media/v2-update-provision/tutorial-eleven.png
[12]: media/v2-update-provision/tutorial-twelve.png
[13]: media/v2-update-provision/tutorial-thirteen.png
[14]: media/v2-update-provision/tutorial-fourteen.png
[15]: media/v2-update-provision/tutorial-fifteen.png
[16]: media/v2-update-provision/tutorial-sixteen.png
[17]: media/v2-update-provision/tutorial-seventeen.png
[18]: media/v2-update-provision/tutorial-eighteen.png
[19]: media/v2-update-provision/tutorial-nineteen.png
[20]: media/v2-update-provision/tutorial-twenty.png
[21]: media/v2-update-provision/tutorial-twenty-one.png
[22]: media/v2-update-provision/tutorial-twenty-two.png
[23]: media/v2-update-provision/tutorial-twenty-three.png
[24]: media/v2-update-provision/tutorial-twenty-four.png