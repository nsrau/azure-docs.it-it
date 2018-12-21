---
title: 'Configurazione della versione di anteprima di Azure Time Series Insights: esercitazione su come configurare un ambiente di anteprima di Azure Time Series Insights | Microsoft Docs'
description: Informazioni su come configurare l'ambiente nella versione di anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 9ad957d6378b1279f1ca51939eb4802b0ce7d78f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322619"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Esercitazione: Configurare un ambiente di anteprima di Azure Time Series Insights

Questa esercitazione illustra il processo di creazione di un ambiente di anteprima con pagamento a consumo di Azure Time Series Insights. In questa esercitazione si apprenderà come:

* Creare un ambiente di anteprima di Azure Time Series Insights.
* Connettere l'ambiente di anteprima di Azure Time Series Insights a un hub eventi in Hub eventi di Azure.
* Eseguire una simulazione relativa a una centrale eolica per trasmettere dati nell'ambiente di anteprima di Azure Time Series Insights.
* Eseguire un'analisi di base sui dati.
* Definire un tipo di modello di serie temporale e una gerarchia da associare alle istanze.

# <a name="create-a-device-simulation"></a>Creare una simulazione di dispositivi

In questa sezione si creeranno tre dispositivi simulati che invieranno dati a un hub IoT.

1. Accedere alla [home page degli acceleratori di soluzione di Azure IoT](https://www.azureiotsolutions.com/Accelerators). La home page degli acceleratori di soluzione di Azure IoT mostra diversi esempi preesistenti. Accedere tramite l'account di Azure personale. Quindi, selezionare **Simulazione dispositivi**.

   ![Home page degli acceleratori di soluzione di Azure IoT][1]

   Infine, fare clic su **Prova adesso**.

1. Immettere i parametri richiesti nella pagina della soluzione **Crea simulazione dispositivo**:

   | Parametro | DESCRIZIONE |
   | --- | --- |
   | Nome soluzione |    Un valore univoco usato per la creazione di un nuovo gruppo di risorse. Le risorse di Azure elencate vengono | create e assegnate al gruppo di risorse. |
   | Sottoscrizione | Specificare la stessa sottoscrizione usata per la creazione dell'ambiente TSI |
   | Region |   Specificare la stessa area usata per la creazione di TSI. |
   | Distribuire risorse di Azure facoltative    | Lasciare selezionato Hub IoT, che verrà usato dai dispositivi simulati per la connessione/trasmissione dei dati. |

   Dopo aver immesso i parametri obbligatori, fare clic su **Crea soluzione**. Attendere circa 10-15 minuti per la distribuzione della soluzione.

   ![Creare una soluzione Simulazione dispositivi][2]

1. Nel **dashboard dell'acceleratore di soluzione** fare clic sul pulsante **Avvia**:

   ![Avviare la soluzione Simulazione dispositivi][3]

1. Si verrà reindirizzati alla pagina **Simulazione dispositivo IoT di Microsoft Azure**. Fare clic su **+ Nuova simulazione** in alto a destra nella schermata.

   ![Pagina di simulazione di Azure IoT][4]

1.  Immettere i parametri obbligatori nel modo seguente:

    ![Parametri da compilare][5]

    |||
    | --- | --- |
    | **Nome** | Immettere un nome univoco per un simulatore |
    | **Descrizione** | Immettere una definizione |
    | **Durata simulazione** | Impostare su `Run indefinitely` |
    | **Modello del dispositivo** | **Nome**: Immettere `Chiller` **Quantità**: Immettere `3` |
    | **Target IoT Hub** (Hub IoT di destinazione) | Impostare su `Use pre-provisioned IoT Hub` |

    Dopo aver immesso i parametri obbligatori, fare clic su **Avvia simulazione**.

1. Nel dashboard di simulazione del dispositivo, vedere i **Dispositivi attivi** e i **Messaggi al secondo**.

    ![Dashboard di simulazione di Azure IoT][6]

## <a name="list-device-simulation-properties"></a>Elencare le proprietà della simulazione del dispositivo

Prima di creare un ambiente di Azure Time Series Insights, saranno necessari i nomi dell'hub IoT, della sottoscrizione e del gruppo di risorse.

1. Accedere al **dashboard dell'acceleratore di soluzione** con lo stesso account di sottoscrizione di Azure. Trovare la simulazione del dispositivo creata nei passaggi precedenti.

1. Fare clic sul simulatore di dispositivi e quindi fare clic su **Avvia**. Fare clic sul collegamento **Portale di gestione di Microsoft Azure** visualizzato sul lato destro.

    ![Elenchi di simulatori][7]

1. Annotare i nomi dell'hub IoT, della sottoscrizione e del gruppo di risorse.

    ![Portale di Azure][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Creare un ambiente di anteprima di Time Series Insights con pagamento in base al consumo

Questa sezione illustra come creare un ambiente di anteprima di Azure Time Series Insights tramite il [portale di Azure](https://portal.azure.com/).

1. Accedere al portale di Azure usando l'account della sottoscrizione.

1. Selezionare **Crea una risorsa**.

1. Selezionare la categoria **Internet delle cose** e quindi **Time Series Insights**.

   ![Selezionare Crea una risorsa, quindi selezionare Internet delle cose, quindi Time Series Insights][9]

1. Compilare i campi nella pagina come segue:

   | | |
   | --- | ---|
   | **Nome ambiente** | Scegliere un nome univoco per l'ambiente di anteprima di Azure Time Series Insights. |
   | **Sottoscrizione** | Immettere la sottoscrizione in cui si desidera creare l'ambiente di anteprima di Azure Time Series Insights. È consigliabile usare la stessa sottoscrizione delle altre risorse di IoT create dal simulatore di dispositivi. |
   | **Gruppo di risorse** | Un gruppo di risorse è un contenitore per le risorse di Azure. Scegliere un gruppo di risorse esistente per la risorsa dell'ambiente di anteprima di Azure Time Series Insights oppure crearne uno nuovo. È consigliabile usare lo stesso gruppo di risorse delle altre risorse di IoT create dal simulatore di dispositivi. |
   | **Posizione** | Scegliere un'area del data center per l'ambiente di anteprima di Azure Time Series Insights. Per evitare un aumento della latenza e dei costi della larghezza di banda, è consigliabile mantenere l'ambiente di anteprima di Azure Time Series Insights nella stessa area delle altre risorse IoT. |
   | **Livello** |  Selezionare `PAYG`, ovvero l'acronimo del pagamento in base al consumo. Si tratta dello SKU per il prodotto di anteprima di Azure Time Series Insights. |
   | **ID proprietà** | Identifica in modo univoco la serie temporale. Si noti che questo campo è invariabile e non può essere modificato in un secondo momento. Per questa esercitazione impostare il campo su `iothub-connection-device-id`. Per altre informazioni sull'ID serie temporale, leggere [Come scegliere un ID serie temporale](./time-series-insights-update-how-to-id.md). |
   | **Storage Account Name** | Immettere un nome univoco globale per un nuovo account di archiviazione da creare. |

   Dopo aver compilato i campi precedenti, fare clic su **Avanti: Origine evento**.

   ![Fare clic su Avanti: Origine evento][10]

1. Compilare i campi nella pagina come segue:

   | | |
   | --- | --- |
   | **Creare un origine evento?** | Immettere `Yes`|
   | **Nome** | Richiede un valore univoco, usato per assegnare un nome all'origine evento.|
   | **Tipo di origine** | Immettere `IoT Hub` |
   | **Selezionare un hub?** | Immettere `Select Existing` |
   | **Sottoscrizione** | Immettere la sottoscrizione usata per il simulatore di dispositivo. |
   | **Nome hub IoT** | Immettere il nome dell'hub IoT creato per il simulatore di dispositivo. |
   | **Criteri di accesso dell'hub IoT** | Immettere `iothubowner` |
   | **Gruppo di consumer dell'hub IoT** | È necessario un gruppo di consumer univoco per un'anteprima di Azure Time Series Insights. |
   | **Timestamp** | Questo campo viene usato per identificare la proprietà timestamp nei dati di telemetria in ingresso. Per questa esercitazione, non compilare il campo. Questo simulatore usa il timestamp in arrivo dall'hub IoT predefinito di Time Series Insights.|

   Per creare un gruppo di consumer univoco:

   1. Fare clic su **Nuovo** accanto al campo **Gruppo di consumer dell'hub IoT**:

      ![Fare clic su Avanti: Origine evento][11]

   1. Assegnare un nome univoco al gruppo di consumer e fare clic su **Aggiungi**:

      ![Fare clic su Aggiungi.][12]

   Dopo aver compilato i campi precedenti, fare clic su **Rivedi e crea**.

      ![Rivedi e crea][13]

1. Esaminare tutti i campi nella pagina di revisione e fare clic su **Crea**.

   ![Create][14]

1. È possibile visualizzare lo stato della distribuzione.

   ![Distribuzione completata][15]

1. Se si è proprietari del tenant, si dovrebbe ricevere l'accesso per l'ambiente della serie temporale. Assicurarsi di avere accesso:

   * Passare all'ambiente di anteprima di Azure Time Series Insights appena creato. È possibile farlo cercando il gruppo di risorse. Quindi, fare clic sull'ambiente della serie temporale:

      ![Distribuzione completata][16]

   * Nella pagina di anteprima di Azure Time Series Insights, passare a **Criteri di accesso ai dati**.

     ![Criteri di accesso ai dati][17]

   * Verificare che le credenziali siano elencate.

     ![Verificare le credenziali][18]

   Se le credenziali non sono elencate, è necessario assegnarsi manualmente l'autorizzazione per accedere all'ambiente. Leggere [Concedere l'accesso ai dati](./time-series-insights-data-access.md) per altre informazioni sull'impostazione delle autorizzazioni.

## <a name="analyze-data-in-your-environment"></a>Analizzare i dati nell'ambiente

In questa sezione verranno eseguite attività di analisi di base sui dati delle serie temporali usando lo [strumento di esplorazione aggiornato dell'anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Passare allo strumento di esplorazione dell'anteprima di Azure Time Series Insights facendo clic sull'URL nella pagina delle risorse nel [portale di Azure](https://portal.azure.com/).

   ![URL dello strumento di esplorazione di Azure Time Series Insights][19]

1. Nello strumento di esplorazione selezionare i nodi **Unparented Instances** (Istanze senza padre) per visualizzare tutte le istanze della versione di anteprima di Azure Time Series Insights nell'ambiente.

   ![Elenco di istanze senza padre][20]

1. Nella serie temporale visualizzata, fare clic sulla prima istanza. Quindi, fare clic su **Mostra pressione media**.

   ![Mostra pressione media][21]

1. Viene visualizzato un grafico della serie temporale a destra:

   ![Grafico serie temporale][22]

1. Ripetere il **passaggio 3** con le altre due serie temporali. È possibile visualizzare tutte le serie temporali come illustrato di seguito:

   ![Grafico di tutte le serie temporali][23]

1. Modificare l'**intervallo di tempo** per visualizzare le tendenze nelle serie temporali dell'ultima ora. Selezionare la casella dell'opzione **Da** come mostrato di seguito:

   ![Selezionare l'opzione Da][24]

1. Modificare il tempo entro il quale visualizzare gli eventi dell'ultima ora nella casella di opzione **Da**:

   ![Selezionare l'opzione Da][25]

1. È quindi possibile confrontare la pressione i tutti e tre i dispositivi nell'ultima ora:

   ![Selezionare l'opzione Da][26]

## <a name="define-and-apply-a-model"></a>Definire e applicare un modello

In questa sezione si applicherà un modello per strutturare i dati. Per completare il modello, si definiranno i tipi, le gerarchie e le istanze. Per altre informazioni sulla modellazione dei dati, passare a [Modelli Time Series](./time-series-insights-update-tsm.md).

1. Nella finestra di esplorazione, selezionare la scheda **Modello**:

   ![Selezionare la scheda Modello][27]

1. Quindi, fare clic su **+ Aggiungi** per aggiungere un tipo. A destra verrà aperto un editor di tipi.

   ![Fare clic su Aggiungi.][28]

1. Quindi, definire tre variabili: Pressione, Temperatura e Umidità in un tipo. Immettere i campi seguenti:

   | | |
   | --- | ---|
   | **Nome** | Immettere `Chiller` |
   | **Descrizione** | Immettere `This is a type definition of Chiller` |

   * A questo punto, definire la Pressione con tre variabili:

      | | |
      | --- | ---|
      | **Nome** | Immettere `Avg Pressure` |
      | **Valore** | Selezionare **pressione (doppia)**. Potrebbero volerci alcuni minuti per popolare il campo dopo l'avvio della ricezione di eventi in Azure Time Series Insights |
      | **Operazione di aggregazione** | Selezionare `AVG`. |

      ![Aggiungere una variabile][29]

      Fare clic su **+ Variabile** per aggiungere la variabile successiva.

   * A questo punto, definire la Temperatura:

      | | |
      | --- | ---|
      | **Nome** | Immettere `Avg Temperature` |
      | **Valore** | Selezionare **temperatura (doppia)**. Potrebbero volerci alcuni minuti per popolare il campo dopo l'avvio della ricezione di eventi in Azure Time Series Insights |
      | **Operazione di aggregazione** | Selezionare `AVG`.|

      ![Definire la Temperatura][30]

   * Ora, definire l'Umidità:

      | | |
      | --- | ---|
      | **Nome** | Immettere `Max Humidity` |
      | **Valore** | Selezionare **umidità (doppia)**. Potrebbero volerci alcuni minuti per popolare il campo dopo l'avvio della ricezione di eventi in Azure Time Series Insights |
      | **Operazione di aggregazione** | Selezionare `MAX`.|

      ![Definire la Temperatura][31]

   Dopo aver definito le variabili, fare clic su **Crea**.

1. È possibile visualizzare il tipo aggiunto:

   ![Vedere il tipo aggiunto][32]

1. Il passaggio successivo consiste nell'aggiungere una gerarchia. Nella sezione **Gerarchie**, selezionare **+ Aggiungi** per creare una nuova gerarchia:

   ![Aggiungere una gerarchia][33]

1. Definire una gerarchia. Compilare i campi come indicato di seguito:

   | | |
   | --- | ---|
   | **Nome** | Immettere `Location Hierarchy` |
   | **Livello 1** | Immettere `Country` |
   | **Livello 2** | Immettere `City` |
   | **Livello 3** | Immettere `Building` |

   Dopo aver compilato i campi precedenti, fare clic su **Crea**.

   ![Definire una gerarchia][34]

1. È possibile visualizzare la gerarchia creata:

   ![Vedere la gerarchia creata][35]

1. Dopo aver definito la gerarchia, fare clic su **Istanze** a sinistra. Quando le istanze vengono visualizzate, fare clic sulla prima e selezionare **Modifica**:

   ![Modificare un'istanza][36]

1. A destra verrà aperto un editor di testo. Aggiungere i campi seguenti:

   | | |
   | --- | --- |
   | **Tipo** | Selezionare `Chiller`. |
   | **Descrizione** | Immettere `Instance for Chiller-01.1` |
   | **Gerarchie** | Abilitare `Location Hierarchy` |
   | **Paese** | Immettere `USA` |
   | **Città** | Immettere `Seattle` |
   | **Edificio** | Immettere `Space Needle` |

    Dopo aver compilato i campi precedenti, fare clic su **Salva**.

   ![Salvare un refrigeratore][37]

1. Ripetere il passaggio precedente per gli altri sensori. Usare i campi seguenti:

   * Per Chiller 01.2:

     | | |
     | --- | --- |
     | **Tipo** | Selezionare `Chiller`. |
     | **Descrizione** | Immettere `Instance for Chiller-01.2` |
     | **Gerarchie** | Abilitare `Location Hierarchy` |
     | **Paese** | Immettere `USA` |
     | **Città** | Immettere `Seattle` |
     | **Edificio** | Immettere `Pacific Science Center` |

   * Per Chiller 01.3:

     | | |
     | --- | --- |
     | **Tipo** | Selezionare `Chiller`. |
     | **Descrizione** | Immettere `Instance for Chiller-01.1` |
     | **Gerarchie** | Abilitare `Location Hierarchy` |
     | **Paese** | Immettere `USA` |
     | **Città** | Immettere `New York` |
     | **Edificio** | Immettere `Empire State Building` |

1. Aprire la scheda **Analizza** e aggiornare la pagina. Espandere tutti i livelli della gerarchia per trovare la serie temporale.

   ![Visualizzare la scheda Analizza][38]

1. Per esplorare le serie temporali dell'ultima ora, modificare **tempi rapidi** all'ultima ora:

   ![Esplorare l'ultima ora][39]

1. Fare clic sulle serie di tempi in **Pacific Science Center** e fare clic su **Mostra umidità massima**.

   ![Mostra umidità massima][40]

1. Si aprirà la serie temporale per **Umidità massima** con un intervallo di dimensioni pari a 1 minuto. Fare clic su un'area per filtrare un intervallo. Quindi, fare clic con il tasto destro del mouse e fare zoom avanti per analizzare gli eventi nell'intervallo di tempo:

   ![Visualizzare, filtrare e fare zoom avanti][41]

   ![Visualizzare, filtrare e fare zoom avanti][42]

1. È possibile anche fare clic su un'area geografica e quindi fare clic con il tasto destro del mouse per visualizzare i dettagli dell'evento:

   ![Visualizzare, filtrare e fare zoom avanti][43]

   ![Visualizzare, filtrare e fare zoom avanti][44]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:  

* Creare e usare un acceleratore di simulazione dispositivi.
* Creare un ambiente di anteprima di Azure Time Series Insights con pagamento in base al consumo.
* Connettere l'ambiente di anteprima di Azure Time Series Insights a un hub eventi.
* Eseguire una simulazione relativa a una centrale eolica per trasmettere dati nell'ambiente di anteprima di Azure Time Series Insights.
* Eseguire un'analisi di base sui dati.
* Definire un tipo di modello di serie temporale e una gerarchia da associare alle istanze.

Ora che si è appreso come creare un ambiente di anteprima di Azure Time Series Insights, si procederà ad apprendere altre informazioni sui concetti chiave di Azure Time Series Insights.

Leggere informazioni sulla configurazione dell'archiviazione di Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Archiviazione e ingresso nell'anteprima di Azure Time Series Insights](./time-series-insights-update-storage-ingress.md)

Leggere altre informazioni sui modelli di serie temporali:

> [!div class="nextstepaction"]
> [Modellazione dei dati nell'anteprima di Azure Time Series Insights](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png