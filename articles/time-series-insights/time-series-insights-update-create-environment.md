---
title: 'Esercitazione: Configurare un ambiente di anteprima di Azure Time Series Insights | Microsoft Docs'
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
ms.openlocfilehash: 1b09c0e31b217d7d67f936aefe9045d190241389
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633114"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Esercitazione: Configurare un ambiente di anteprima di Azure Time Series Insights

Questa esercitazione illustra il processo di creazione di un ambiente di anteprima con pagamento a consumo di Azure Time Series Insights. In questa esercitazione si apprenderà come:

* Creare un ambiente di anteprima di Azure Time Series Insights.
* Connettere l'ambiente di anteprima di Azure Time Series Insights a un hub eventi in Hub eventi di Azure.
* Eseguire un esempio di acceleratore di soluzione per trasmettere dati nell'ambiente di anteprima di Azure Time Series Insights.
* Eseguire un'analisi di base sui dati.
* Definire un tipo di modello di serie temporale e una gerarchia e associarli alle istanze.

# <a name="create-a-device-simulation"></a>Creare una simulazione di dispositivi

In questa sezione vengono creati tre dispositivi simulati che invieranno dati a un hub IoT.

1. Accedere alla [pagina degli acceleratori di soluzione di Azure IoT](https://www.azureiotsolutions.com/Accelerators). La pagina mostra diversi esempi predefiniti. Accedere tramite l'account di Azure personale. Selezionare quindi **Simulazione dispositivi**.

   ![Pagina degli acceleratori di soluzioni di Azure IoT][1]

   Selezionare **Prova adesso**.

1. Immettere i parametri richiesti nella pagina **Crea soluzione Simulazione dispositivi**:

   | Parametro | Descrizione |
   | --- | --- |
   | **Nome soluzione** |    Immettere un valore univoco per la creazione di un nuovo gruppo di risorse. Le risorse di Azure elencate vengono create e assegnate al gruppo di risorse. |
   | **Sottoscrizione** | Specificare la stessa sottoscrizione usata per la creazione dell'ambiente Time Series Insights. |
   | **Area** |   Specificare la stessa area usata per la creazione dell'ambiente Time Series Insights. |
   | **Distribuisci risorse di Azure facoltative**    | Lasciare selezionato Hub IoT, perché verrà usato dai dispositivi simulati per la connessione e la trasmissione dei dati. |

   Selezionare quindi **Crea soluzione**. Attendere 10-15 minuti che la soluzione venga distribuita.

   ![Pagina Crea soluzione Simulazione dispositivi][2]

1. Nel dashboard dell'acceleratore di soluzione selezionare il pulsante **Avvia**:

   ![Avviare la soluzione Simulazione dispositivi][3]

1. Si verrà reindirizzati alla pagina **Microsoft Azure IoT Device Simulation** (Simulazione dispositivi di Microsoft Azure IoT). Selezionare **+ New simulation** (+ Nuova simulazione) in alto a destra nella pagina.

   ![Pagina di simulazione di Azure IoT][4]

1.  Immettere i parametri obbligatori nel modo seguente:

    ![Parametri da compilare][5]

    |||
    | --- | --- |
    | **Name** (Nome) | Immettere un nome univoco per un simulatore. |
    | **Description** (Descrizione) | Immettere una definizione. |
    | **Simulation duration** (Durata simulazione) | Impostare su **Run indefinitely** (Esecuzione a tempo indefinito). |
    | **Device model** (Modello del dispositivo) | **Name** (Nome): immettere **Chiller** (Refrigeratore). </br>**Amount** (Quantità): immettere **3**. |
    | **Target IoT Hub** (Hub IoT di destinazione) | Impostare su **Use pre-provisioned IoT Hub** (Usa l'hub IoT di cui è già stato eseguito il provisioning). |

    Selezionare quindi **Start simulation** (Avvia simulazione).

1. Nel dashboard di simulazione dei dispositivi esaminare **Active devices** (Dispositivi attivi) e **Messages per second** (Messaggi al secondo).

    ![Dashboard di simulazione di Azure IoT][6]

## <a name="list-device-simulation-properties"></a>Elencare le proprietà della simulazione del dispositivo

Prima di creare un ambiente di Azure Time Series Insights, sono necessari i nomi dell'hub IoT, della sottoscrizione e del gruppo di risorse.

1. Accedere al dashboard dell'acceleratore di soluzioni con lo stesso account di sottoscrizione di Azure. Trovare la simulazione del dispositivo creata nei passaggi precedenti.

1. Selezionare il simulatore di dispositivi e quindi **Launch** (Avvia). Selezionare il collegamento del **portale di gestione di Azure** visualizzato sul lato destro.

    ![Elenchi di simulatori][7]

1. Prendere nota dei nomi dell'hub IoT, della sottoscrizione e del gruppo di risorse.

    ![Portale di Azure][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Creare un ambiente di anteprima di Time Series Insights con pagamento in base al consumo

Questa sezione illustra come creare un ambiente di anteprima di Azure Time Series Insights tramite il [portale di Azure](https://portal.azure.com/).

1. Accedere al portale di Azure usando l'account della sottoscrizione.

1. Selezionare **Crea una risorsa**.

1. Selezionare la categoria **Internet delle cose** e quindi **Time Series Insights**.

   ![Selezionare Internet delle cose e quindi Time Series Insights][9]

1. Compilare i campi nella pagina come segue:

   | | |
   | --- | ---|
   | **Nome ambiente** | Scegliere un nome univoco per l'ambiente di anteprima di Azure Time Series Insights. |
   | **Sottoscrizione** | Immettere la sottoscrizione in cui si desidera creare l'ambiente di anteprima di Azure Time Series Insights. È consigliabile usare la stessa sottoscrizione delle altre risorse di IoT create dal simulatore di dispositivi. |
   | **Gruppo di risorse** | Un gruppo di risorse è un contenitore per le risorse di Azure. Scegliere un gruppo di risorse esistente per la risorsa dell'ambiente di anteprima di Azure Time Series Insights oppure crearne uno nuovo. È consigliabile usare lo stesso gruppo di risorse delle altre risorse di IoT create dal simulatore di dispositivi. |
   | **Posizione** | Scegliere un'area del data center per l'ambiente di anteprima di Azure Time Series Insights. Per evitare un aumento della latenza e dei costi della larghezza di banda, è consigliabile mantenere l'ambiente di anteprima di Azure Time Series Insights nella stessa area delle altre risorse IoT. |
   | **Livello** |  Selezionare **PAYG**, ovvero l'acronimo del pagamento in base al consumo. Si tratta dello SKU per il prodotto di anteprima di Azure Time Series Insights. |
   | **ID proprietà** | Immettere un nome che identifichi in modo univoco la serie temporale. Si noti che questo campo è invariabile e non può essere modificato in un secondo momento. Per questa esercitazione usare **iothub-connection-device-id**. Per altre informazioni sull'ID serie temporale, leggere [Come scegliere un ID serie temporale](./time-series-insights-update-how-to-id.md). |
   | **Nome account di archiviazione** | Immettere un nome univoco globale per un nuovo account di archiviazione da creare. |

   Selezionare quindi **Avanti: Origine evento**.

   ![Pagina per la creazione di un ambiente Time Series Insights][10]

1. Nella pagina relativa all'origine evento compilare i campi come indicato di seguito:

   | | |
   | --- | --- |
   | **Create an event source?** (Creare un origine evento?) | Immettere **Yes** (Sì).|
   | **Name** (Nome) | Immettere un valore univoco che viene usato per assegnare un nome all'origine evento.|
   | **Source type** (Tipo di origine) | Immettere **IoT Hub** (Hub IoT). |
   | **Select a hub?** (Selezionare un hub?) | Immettere **Select existing** (Seleziona esistente). |
   | **Sottoscrizione** | Immettere la sottoscrizione usata per il simulatore di dispositivo. |
   | **Nome hub IoT** | Immettere il nome dell'hub IoT creato per il simulatore di dispositivo. |
   | **Criteri di accesso dell'hub IoT** | Immettere **iothubowner**. |
   | **Gruppo di consumer dell'hub IoT** | È necessario un gruppo di consumer univoco per l'anteprima di Azure Time Series Insights. Selezionare **Nuovo**, immettere un nome univoco e quindi selezionare **Aggiungi**. |
   | **Timestamp property** (Proprietà timestamp) | Questo campo viene usato per identificare la proprietà timestamp nei dati di telemetria in ingresso. Per questa esercitazione, non compilare il campo. Questo simulatore usa il timestamp in arrivo dall'hub IoT predefinito di Time Series Insights.|

   Selezionare quindi **Rivedi e crea**.

   ![Pagina per la configurazione di un'origine evento][13]

1. Esaminare tutti i campi nella pagina di revisione e selezionare **Crea**.

   ![Pagina Rivedi e crea, con pulsante Crea][14]

1. È possibile visualizzare lo stato della distribuzione.

   ![Notifica del completamento della distribuzione][15]

1. Se si è proprietari del tenant, si dovrebbe ricevere l'accesso all'ambiente di anteprima di Azure Time Series Insights. Assicurarsi di avere accesso:

   a. Cercare il gruppo di risorse e selezionare l'ambiente di anteprima di Azure Time Series Insights:

      ![Ambiente selezionato][16]

   b. Nella pagina di anteprima di Azure Time Series Insights passare a **Criteri di accesso ai dati**.

     ![Criteri di accesso ai dati][17]

   c. Verificare che le credenziali siano elencate.

     ![Credenziali elencate][18]

   Se le credenziali non sono elencate, è necessario assegnarsi manualmente l'autorizzazione per accedere all'ambiente. Per altre informazioni sull'impostazione delle autorizzazioni, leggere [Concedere l'accesso ai dati](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analizzare i dati nell'ambiente

In questa sezione verranno eseguite attività di analisi di base sui dati delle serie temporali usando lo [strumento di esplorazione aggiornato dell'anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Passare allo strumento di esplorazione dell'anteprima di Azure Time Series Insights selezionando l'URL nella pagina delle risorse nel [portale di Azure](https://portal.azure.com/).

   ![URL dello strumento di esplorazione dell'anteprima di Time Series Insights][19]

1. Nello strumento di esplorazione selezionare il nodo **Unparented Instances** (Istanze senza padre) per visualizzare tutte le istanze della versione di anteprima di Azure Time Series Insights nell'ambiente.

   ![Elenco di istanze senza padre][20]

1. Nella serie temporale visualizzata selezionare la prima istanza. Selezionare quindi **Show Avg pressure** (Mostra pressione media).

   ![Istanza selezionata con il comando di menu per visualizzare la pressione media][21]

   Viene visualizzato un grafico della serie temporale a destra:

   ![Grafico serie temporale][22]

1. Ripetere il passaggio 3 con le altre due serie temporali. È quindi possibile visualizzare tutte le serie temporali come illustrato in questo grafico:

   ![Grafico relativo a tutte le serie temporali][23]

1. Modificare l'intervallo di tempo per visualizzare le tendenze nelle serie temporali dell'ultima ora. 

   a. Selezionare la casella di opzione **From** (Da):

      ![Casella di opzione From (Da)][24]

   b. Nella casella modificare il tempo entro il quale visualizzare gli eventi dell'ultima ora:

      ![Modifiche apportate al tempo][25]

1. È quindi possibile confrontare la pressione i tutti e tre i dispositivi nell'ultima ora:

   ![Confronto tra tre dispositivi][26]

## <a name="define-and-apply-a-model"></a>Definire e applicare un modello

In questa sezione si applica un modello per strutturare i dati. Per completare il modello, vengono definiti i tipi, le gerarchie e le istanze. Per altre informazioni sulla modellazione dei dati, passare a [Modello Time Series](./time-series-insights-update-tsm.md).

1. Nello strumento di esplorazione selezionare la scheda **Modello**:

   ![Scheda Model (Modello) nello strumento di esplorazione][27]

1. Selezionare **+ Add** (+ Aggiungi) per aggiungere un tipo. A destra verrà aperto un editor di tipi.

   ![Pulsante Add (Aggiungi) per i tipi][28]

1. Definire tre variabili per il tipo, ovvero pressure (pressione), temperature (temperatura) e humidity (umidità). Immettere le seguenti informazioni:

   | | |
   | --- | ---|
   | **Nome** | Immettere **Chiller** (Refrigeratore). |
   | **Descrizione** | Immettere **This is a type definition of Chiller** (Definizione del tipo per Refrigeratore). |

   * Definire la pressione con tre variabili:

      | | |
      | --- | ---|
      | **Nome** | Immettere **Avg Pressure** (Pressione media). |
      | **Valore** | Selezionare **pressione (doppia)**. Tenere presente che il popolamento di questo campo potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi in Azure Time Series Insights. |
      | **Operazione di aggregazione** | Selezionare **AVG** (MEDIA). |

      ![Selezioni per la definizione della pressione][29]

      Selezionare **+ Add Variable** (+ Aggiungi variabile) per aggiungere la variabile successiva.

   * Definire la temperatura:

      | | |
      | --- | ---|
      | **Nome** | Immettere **Avg Temperature** (Temperatura media). |
      | **Valore** | Selezionare **temperatura (doppia)**. Tenere presente che il popolamento di questo campo potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi in Azure Time Series Insights. |
      | **Operazione di aggregazione** | Selezionare **AVG** (MEDIA).|

      ![Selezioni per la definizione della temperatura][30]

   * Definire l'umidità:

      | | |
      | --- | ---|
      | **Nome** | Immettere **Max Humidity** (Umidità massima). |
      | **Valore** | Selezionare **umidità (doppia)**. Tenere presente che il popolamento di questo campo potrebbe richiedere alcuni minuti dopo l'inizio della ricezione di eventi in Azure Time Series Insights. |
      | **Operazione di aggregazione** | Selezionare **MAX** (MASSIMA).|

      ![Selezioni per la definizione della temperatura][31]

   Scegliere quindi **Create** (Crea).

1. È possibile visualizzare il tipo aggiunto:

   ![Informazioni sul tipo aggiunto][32]

1. Il passaggio successivo consiste nell'aggiungere una gerarchia. Nella sezione **Hierarchies** (Gerarchie) selezionare **+ Add** (+ Aggiungi):

   ![Scheda Hierarchies (Gerarchie) con pulsante Add (Aggiungi)][33]

1. Definire la gerarchia. Compilare i campi come indicato di seguito:

   | | |
   | --- | ---|
   | **Nome** | Immettere **Location Hierarchy** (Gerarchia località). |
   | **Livello 1** | Immettere **Country** (Paese). |
   | **Livello 2** | Immettere **City** (Città). |
   | **Livello 3** | Immettere **Building** (Edificio). |

   Scegliere quindi **Create** (Crea).

   ![Campi della gerarchia con pulsante Create (Crea)][34]

1. È possibile visualizzare la gerarchia creata:

   ![Informazioni sulla gerarchia][35]

1. Selezionare **Instances** (Istanze) sulla sinistra. Quando le istanze vengono visualizzate, selezionare la prima e quindi fare clic su **Edit** (Modifica):

   ![Selezione del pulsante Edit (Modifica) per un'istanza][36]

1. A destra verrà aperto un editor di testo. Aggiungere le informazioni seguenti:

   | | |
   | --- | --- |
   | **Tipo** | Selezionare **Chiller** (Refrigeratore). |
   | **Descrizione** | Immettere **Instance for Chiller-01.1** (Istanza per Refrigeratore-01.1). |
   | **Gerarchie** | Abilitare **Location Hierarchy** (Gerarchia località). |
   | **Paese** | Immettere **USA** (Stati Uniti). |
   | **Città** | Immettere **Seattle**. |
   | **Edificio** | Immettere **Space Needle**. |

    Selezionare quindi **Salva**.

   ![Campi dell'istanza con pulsante Save (Salva)][37]

1. Ripetere il passaggio precedente per gli altri sensori. Usare i campi seguenti:

   * Per Chiller 01.2:

     | | |
     | --- | --- |
     | **Tipo** | Selezionare **Chiller** (Refrigeratore). |
     | **Descrizione** | Immettere **Instance for Chiller-01.2** (Istanza per Refrigeratore-01.2). |
     | **Gerarchie** | Abilitare **Location Hierarchy** (Gerarchia località). |
     | **Paese** | Immettere **USA** (Stati Uniti). |
     | **Città** | Immettere **Seattle**. |
     | **Edificio** | Immettere **Pacific Science Center**. |

   * Per Chiller 01.3:

     | | |
     | --- | --- |
     | **Tipo** | Selezionare **Chiller** (Refrigeratore). |
     | **Descrizione** | Immettere **Instance for Chiller-01.1** (Istanza per Refrigeratore-01.1). |
     | **Gerarchie** | Abilitare **Location Hierarchy** (Gerarchia località). |
     | **Paese** | Immettere **USA** (Stati Uniti). |
     | **Città** | Immettere **New York**. |
     | **Edificio** | Immettere **Empire State Building**. |

1. Aprire la scheda **Analizza** e aggiornare la pagina. Espandere tutti i livelli della gerarchia per trovare la serie temporale.

   ![Scheda Analizza][38]

1. Per esplorare le serie temporali dell'ultima ora, modificare **Tempi rapidi** in **Ultima ora**:

   ![Casella Tempi rapidi con opzione Ultima ora selezionata][39]

1. Selezionare la serie temporale in **Pacific Science Center** e quindi **Show Max Humidity** (Mostra umidità massima).

   ![Serie temporale selezionata con selezione del menu Show Max Humidity (Mostra umidità massima)][40]

1. Si aprirà la serie temporale per **Max Humidity** (Umidità massima) con un intervallo di dimensioni pari a 1 minuto. Selezionare un'area per filtrare un intervallo. Fare quindi clic con il pulsante destro del mouse e selezionare **Zoom** per analizzare gli eventi nell'intervallo di tempo:

   ![Intervallo selezionato con comando Zoom in un menu di scelta rapida][41]

1. È anche possibile selezionare un'area e quindi fare clic con il pulsante destro del mouse per visualizzare i dettagli dell'evento:

   ![Elenco dettagliato di eventi][44]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:  

* Creare e usare un acceleratore di simulazione dispositivi.
* Creare un ambiente di anteprima di Azure Time Series Insights con pagamento in base al consumo.
* Connettere l'ambiente di anteprima di Azure Time Series Insights a un hub eventi.
* Eseguire un esempio di acceleratore di soluzione per trasmettere dati nell'ambiente di anteprima di Azure Time Series Insights.
* Eseguire un'analisi di base sui dati.
* Definire un tipo di modello di serie temporale e una gerarchia da associare alle istanze.

Ora che si è appreso come creare un ambiente di anteprima di Azure Time Series Insights, si procederà ad apprendere altre informazioni sui concetti chiave di Azure Time Series Insights.

Leggere informazioni sulla configurazione dell'archiviazione di Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights](./time-series-insights-update-storage-ingress.md)

Leggere altre informazioni sui modelli di serie temporali:

> [!div class="nextstepaction"]
> [Modellazione dei dati nell'anteprima di Azure Time Series Insights](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-seven-dashboard.png
[7]: media/v2-update-provision/device-six-listings.png
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