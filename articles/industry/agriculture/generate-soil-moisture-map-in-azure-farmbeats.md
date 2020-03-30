---
title: Genera Heatmap per l'umidità del suolo
description: Descrive come generare Soil Moisture Heatmap in Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482569"
---
# <a name="generate-soil-moisture-heatmap"></a>Genera Heatmap per l'umidità del suolo

L'umidità del suolo è l'acqua che si tiene negli spazi tra le particelle del suolo.Soil Moisture Heatmap ti aiuta a comprendere i dati sull'umidità a qualsiasi profondità e ad alta risoluzione all'interno delle tue aziende agricole. Per generare una mappa di calore dell'umidità del suolo accurata e utilizzabile, è necessaria una distribuzione uniforme di sensori dello stesso fornitore. Diversi fornitori avranno differenze nel modo in cui l'umidità del suolo viene misurata insieme alle differenze nella calibrazione. La Heatmap viene generata per una particolare profondità utilizzando i sensori dispiegati a quella profondità.

In questo articolo viene descritto il processo di generazione di una mappa di calore per l'umidità del suolo per la farm, usando Azure FarmBeats Accelerator.This article describes the process of generating a Soil Moisture Heatmap for your farm, using the Azure FarmBeats Accelerator. In questo articolo verrà spiegato come:

- [Creare farm](#create-a-farm)
- [Assegnare sensori alle aziende agricole](#get-soil-moisture-sensor-data-from-partner)
- [Genera Heatmap per l'umidità del suolo](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>Prima di iniziare

Verificare quanto segue:  

- Una sottoscrizione di Azure.
- Un'istanza in esecuzione di Azure FarmBeats.
- Per l'azienda sono disponibili almeno tre sensori di umidità del suolo.

## <a name="create-a-farm"></a>Creazione di una farm

Una fattoria è un'area geografica di interesse per la quale si desidera creare una mappa termica dell'umidità del suolo. È possibile creare una farm utilizzando [l'API Farms](https://aka.ms/FarmBeatsDatahubSwagger) o nell'interfaccia utente di [FarmsBeats Accelerator](manage-farms-in-azure-farmbeats.md#create-farms)

## <a name="deploy-sensors"></a>Distribuire i sensori

È necessario distribuire fisicamente i sensori di umidità del suolo nella fattoria. È possibile acquistare sensori di umidità del suolo da uno dei nostri partner approvati - [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) e [Teralytic](https://teralytic.com/). È necessario coordinarsi con il provider di sensori per eseguire la configurazione fisica nella farm.

## <a name="get-soil-moisture-sensor-data-from-partner"></a>Ottenere i dati del sensore di umidità del suolo dal partner

Quando i sensori iniziano lo streaming, i dati nel dashboard dei dati del partner abilitano i dati in Azure FarmBeats.As sensors start streaming, the data into the partner data dashboard, they enable the data into Azure FarmBeats. Questa operazione può essere eseguita dall'applicazione partner.

Ad esempio, se sono stati acquistati sensori Davis, si accederà all'account di collegamento meteo e verranno fornite le credenziali necessarie per abilitare lo streaming dei dati in Azure FarmBeats.For example, if you have purchased Davis sensors, you will log into your weather link account, and provide the required credentials to enable the data streaming into Azure FarmBeats. Per ottenere le credenziali necessarie, seguire le istruzioni riportate in Ottenere i dati del [sensore.](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)

Dopo aver immesso le credenziali e selezionato Invia nell'applicazione partner, è possibile fare in modo che i dati scorrino in Azure FarmBeats.Once you enter your credentials and select **Submit** on the partner application, you can have the data flowing into Azure FarmBeats.

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>Assegnare sensori di umidità del suolo all'azienda agricola

Dopo aver collegato l'account del sensore in Azure FarmBeats, è necessario assegnare i sensori di umidità del suolo alla farm di interesse.

1.  Nella home page selezionare **Farms** dal menu, viene visualizzata la pagina elenco **Farms.**
2.  Selezionare **MyFarm** > Add Devices (**Aggiungi dispositivi**).
3.  Viene visualizzata la finestra **Aggiungi dispositivi.** Selezionare qualsiasi dispositivo collegato ai sensori di umidità del suolo per la farm.

    ![Progetto FarmBeats](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selezionare **Aggiungi dispositivi**.     

## <a name="generate-soil-moisture-heatmap"></a>Genera Heatmap per l'umidità del suolo

Questo passaggio consiste nel creare un processo o un'operazione a esecuzione prolungata che genererà Soil Moisture Heatmap per la tua azienda agricola.

1.  Nella home page passare a **Farm dal** menu di spostamento sinistro per visualizzare la pagina delle farm.
2.  Selezionare **MyFarm**.
3.  Nella pagina **Dettagli farm** selezionare Genera mappa di **precisione**.
4.  Dal menu a discesa, selezionare **Umidità del suolo**.
5.  Nella finestra **Umidità suolo,** selezionare **Questa settimana**.
6.  In **Select Soil Moisture** **Sensor Measure**immettere la misura che si desidera utilizzare per la mappa.
    Per trovare la misura del sensore, in **Sensori**, selezionare qualsiasi sensore di umidità del suolo. In **Proprietà sensore**utilizzare il valore **Nome misura.**

    ![Progetto FarmBeats](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  Selezionare **Genera mappe**.
    Viene visualizzato un messaggio di conferma con i dettagli del processo. Per ulteriori informazioni, vedere Stato processo in Processi.For more information, see Job Status in Jobs.

    >[!NOTE]
    > Il completamento del processo richiede da tre a quattro ore.

### <a name="download-the-soil-moisture-heatmap"></a>Scarica la mappa termica dell'umidità del suolo

Eseguire la procedura descritta di seguito:

1. Nella pagina **Processi** controllare **lo stato** del processo creato nell'ultima procedura.
2. Quando lo stato del processo è **Riuscito**, selezionare **Mappe** nel menu.
3. Cercare la mappa per il giorno in cui è stata creata nel formato <> del suolo-moisture_MyFarm_YYYY-MM-DD.
4. Selezionare una mappa nella colonna **Nome,** viene visualizzata una finestra popup con l'anteprima della mappa selezionata.
5. Selezionare **Download**. La mappa viene scaricata e memorizzata nella cartella locale del computer.

    ![Progetto FarmBeats](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che hai generato con successo una Soil Moisture Heatmap, scopri come generare il [posizionamento del sensore](generate-maps-in-azure-farmbeats.md#sensor-placement-map) e [inserire dati di telemetria cronologici.](ingest-historical-telemetry-data-in-azure-farmbeats.md) 
